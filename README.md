# eephttpd

So, basically everything I put on i2p is a bunch of static files. Until now, I
tried to host them using darkhttpd(a fork of lighttpd from Alpine which
functions as a static Web Server) and by adding tunnel configuration information
to tunnels.conf for i2pd. This is easier than manipulating a web interface, but
still tedious and kind of error-prone. So instead, this serves simple static
sites directly to i2p via the SAM API.

to build:

        git clone https://github.com/eyedeekay/eephttpd && cd eephttpd
        go get -u "github.com/eyedeekay/sam-forwarder"
        go get -u "github.com/eyedeekay/sam-forwarder/config"
        go build

to run:

        ./eephttpd

will serve the files from ./www, and store i2p keys in the working directory.

## Docker

First, create a volume helper:

        docker run -i -t -d \
            --name eephttpd-volume \
            --volume eephttpd:/home/eephttpd/ \
            eyedeekay/eephttpd

Then, copy files you wish to serve into the volume folder:

        docker cp www/* eephttpd-volume:/home/eephttpd/www

Stop the volume helper:

        docker stop eephttpd-volume

and run the container:

        docker run -i -t -d \
            --env samhost=$(samhost) \
            --env samport=$(samport) \
            --env args=$(args) \
            --network-alias eephttpd \
            --hostname eephttpd \
            --name eephttpd \
            --restart always \
            --volumes-from eephttpd-volume \
            eyedeekay/eephttpd
