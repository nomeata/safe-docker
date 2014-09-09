safe-docker
===========

This script runs a given command in a docker image,
and tries to enforce the following security measures:

 * The container is fresh, and removed after execution
 * The command is run as the user and group invoking sudo; it fails to
   run if it not invoked via sudo or by root.
 * Only the current directory is writable
 * Only the additional paths given on the command line are readable
 * Changes to the current directory are preserved
 * Upon SIGTERM (or any other signal), the container is killed

The docker image name is hardcoded in the script (`safe-docker`). If it were a
command line flag, the userwho can run this script could start a malicious image
downloaded from the internet.

This script should be copied to, say, `/usr/local/bin` and made read-only for the
user running it. Then the targetting user must get sudo permissions to run it.

