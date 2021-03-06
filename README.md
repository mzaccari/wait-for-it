This is a fork of [vishnubob/wait-for-it](https://github.com/vishnubob/wait-for-it) to support [Alpine based Docker images](https://hub.docker.com/_/alpine/). It was originally modified for use in RMS project images at the University of Calgary. Note that this fork of the script has more limited support for arguments than the original script.

`wait-for-it.sh` is a script that will wait on the availability of a host and TCP port.  It is useful for synchronizing the spin-up of interdependent services, such as linked docker containers.  It depends on nc to determine availability.

## Usage

```
wait-for-it.sh host:port [-s] [-t timeout] [-- command args]
-h HOST                     Host or IP under test
-p PORT                     TCP port under test
-s                          Only execute subcommand if the test succeeds
-q                          Don't output any status messages
-t TIMEOUT                  Timeout in seconds, zero for no timeout
-- COMMAND ARGS             Execute command with args after the test finishes
```

## Examples

For example, let's test to see if we can access port 80 on www.google.com, and if it is available, echo the message `google is up`.

```
$ ./wait-for-it.sh -h www.google.com -p 80 -- echo "google is up"
wait-for-it.sh: waiting 15 seconds for www.google.com:80
wait-for-it.sh: www.google.com:80 is available after 0 seconds
google is up
```

You can set your own timeout with the `-t` option.  Setting the timeout value to 0 will disable the timeout:

```
$ ./wait-for-it.sh -t 0 -h www.google.com -p 80 -- echo "google is up"
wait-for-it.sh: waiting for www.google.com:80 without a timeout
wait-for-it.sh: www.google.com:80 is available after 0 seconds
google is up
```

The subcommand will be executed regardless if the service is up or not.  If you wish to execute the subcommand only if the service is up, add the `-s` argument. In this example, we will test port 81 on www.google.com which will fail:

```
$ ./wait-for-it.sh -h www.google.com -p 81 -t 1 -s -- echo "google is up"
wait-for-it.sh: waiting 1 seconds for www.google.com:81
wait-for-it.sh: timeout occurred after waiting 1 seconds for www.google.com:81
wait-for-it.sh: strict mode, refusing to execute subprocess
```

If you don't want to execute a subcommand, leave off the `--` argument.  This way, you can test the exit condition of `wait-for-it.sh` in your own scripts, and determine how to proceed:

```
$ ./wait-for-it.sh -h www.google.com -p 80
wait-for-it.sh: waiting 15 seconds for www.google.com:80
wait-for-it.sh: www.google.com:80 is available after 0 seconds
$ echo $?
0
$ ./wait-for-it.sh -h www.google.com -p 81
wait-for-it.sh: waiting 15 seconds for www.google.com:81
wait-for-it.sh: timeout occurred after waiting 15 seconds for www.google.com:81
$ echo $?
124
```
