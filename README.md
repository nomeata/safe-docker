safe-docker
===========

This script runs a given command in a docker image,
and tries to enforce the following security measures:

 * The container is fresh, and removed after execution
 * The command is run as the user and group invoking sudo; it fails to
   run if it not invoked via sudo or by root.
 * Only the current directory is writable
 * Only the additional paths given on the command line are readable (`--dir`)
 * Changes to the current directory are preserved
 * Upon SIGTERM (or any other signal), the container is killed

The docker image name is hardcoded in the script (`safe-docker`). If it were a
command line flag, the userwho can run this script could start a malicious image
downloaded from the internet.

The option `--timeout` specifies a timeout in seconds, after which the container is
killed, and the special return value of 23 is returned.

The option `--memory` does the same for memory usage. On Debian machines, this
requires the kernel parameters `cgroup_enable=memory swapaccount=1`.

Not much is expected from the docker image. But some programs do not like it
when the current user id has no name (e.g. those using `whoami`). If you need
to run these, you need to add the user to your image’s `/etc/passwd`, and
ensure that it has the same uid as the calling user.


Environment variables are not passed through (which would also depend on `sudo`
doing that), but you can prefix the command with `env VAR=value cmd`.

This script should be copied to, say, `/usr/local/bin` and made read-only for the
user running it. Then the targetting user must get sudo permissions to run it,
e.g. by adding the following lines via `visudo`:

    # Praktomat may use safe-docker
    praktomat ALL= NOPASSWD: /usr/local/bin/safe-docker

It requires `libipc-run-perl` to be installed. On Debian and Ubuntu, run

    apt-get install libipc-run-perl

