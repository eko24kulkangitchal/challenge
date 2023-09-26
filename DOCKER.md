Docker Support for IPP Sample Code
==================================

This repository includes a sample Dockerfile for compiling and running
`ippserver` in a Docker container, and a docker-compose.yml file for running
commands via "docker compose".


Building and Running on Docker
------------------------------

From a shell prompt in the directory (on Windows 10|2016, macOS, or Linux)
containing this docker file run:

    docker build -t ippsample .

You now can run the container with a bash terminal and go to the
`/root/ippsample` folder manually:

    docker run -it ippsample bash

Similarly, you can run the ippsample test suite with:

    docker run -it ippsample make test

You can also run one of the IPP binaries instead of the bash terminal, for
example:

    docker run -it ippsample ipptool -tv ipp://example.local/ipp/print get-printer-attributes.test


Building and Running with Docker Compose
----------------------------------------

To start the IPP server:

    docker compose up ippserver

Run the IPP Everywhere test:

    docker compose up ipptest

To list all IPP printers:

    docker compose up ippfind

To run the IPP proxy:

    docker compose up ippproxy


Fixing The Builds / Debugging Docker Problems
---------------------------------------------

If building from the Dockerfile is failing, you can debug it by running it in a
shell.  But you won't be able to use the tag since the build failed. So to run
a shell in its container, you first need to look up the image number with the
"ps" sub-command:

    docker ps -a

Copy the latest image number from the list, then do a "docker run" specifying
that image, and specify the container should be run with a shell like bash:

    docker run -it IMAGENUMBER bash

Now you can "cd" into the folder and re-run the commands interactively, inspect
the contents of the image, etc.


Starting the IPP Server
-----------------------

Run the IPP server with all its arguments:

    docker run -it ippsample ippserver -M byMyself -l rightHere -m coolPrinter -n myHost -p 631 -s 72 -vvvv myPrintService


*or* to run the server in debug mode using `gdb`:

    docker run -it ippsample gdb ippserver
    run -M byMyself -l rightHere -m coolPrinter -n myHost -p 631 -s 72 -vvvv myPrintService


Running the IPP Client
----------------------

First find all IPP printers from other Docker containers:

    docker run --rm ippsample ippfind

> Note the URL returned, e.g., `ipp://f8a365cfc7ec.local:631/ipp/print`.

Next run the IPP tool in a new container in the `/root/ippsample/examples`
directory with the IPP Server running, run:

    docker run --rm -it -w /root/ippsample/examples ippsample ipptool [URL returned] identify-printer-display.test

> Note the `IDENTIFY from 172.17.0.4: Hello, World!` message in stdout on the
> `ippserver` container.

To run the IPP everywhere tests on the IPP Client using setup from the previous
commands, run:

    docker run --rm -it -w /root/ippsample/examples ippsample ipptool -V 2.0 -tf document-letter.pdf [URL returned] ipp-everywhere.test
