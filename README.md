# thunderbird-caddy

## Building and Running Thunderbird

The next step is to build your container and set it to run at startup. You’ll also set up a volume to preserve the application data between restarts and updates.

First build your container. Make sure to run these commands in the ~/thunderbird directory:

    docker build -t thunderbird .

Now create a new network that will be shared between the app’s containers:

    docker network create thunderbird-net

Then create a volume to store the application data:

    docker volume create thunderbird-data

Finally, run it and set it to restart automatically:

    docker run --detach --restart=always --volume=thunderbird-data:/data --net=thunderbird-net --name=thunderbird-app thunderbird

Note that if you want, you can replace the thunderbird-app after the --name option with a different name. Whatever you have chosen, your application is now containerized and running. Now let’s use the Caddy web server to secure it and remotely connect to it.

## Building and Running Caddy

Now build the container:

    docker build -t thunderbird-caddy .

Caddy v.2 requires you to hash your desired password. Run the following command and remember to replace mypass with a strong password of your choosing:

    docker run --rm -it thunderbird-caddy caddy hash-password -plaintext 'mypass'

This command will output a string of characters. Copy this to your clipboard in preparation of running the next command.

Now you are ready to run the container. Make sure to replace myuser with a username of your choosing, and replace mypass-hash with the output of the command you ran in the previous step. You can also change the port (8080 here) to access your server on a different port:

    docker run --detach --restart=always --volume=thunderbird-data:/data --net=thunderbird-net --name=thunderbird-web --env=APP_USERNAME="myuser" --env=APP_PASSWORD_HASH="mypass-hash" --publish=8080:8080 thunderbird-caddy

We are now ready to access and test our application.
