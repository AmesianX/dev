### My developer image for docker

```./build.sh``` builds the image

Normally you would build an image like this:

```docker build -t johnae/dev .```

You MUST place an encrypted id_rsa key in the repo (call it "id_rsa"). This key is used when building the image and is also put in the user account inside the image. You may place additional keys here.
You MUST also place "authorized_keys" within this repository (ignored in .gitignore) for any keys you wish to have access to the user account within the image.

The docker container should be run something like this:

First we need a volume:

```docker run --name="john-dev-data" -v /home/john busybox```

Then we use that volume for the mounted /home/<user>

```docker run -t -d -p 2222:22 -p 3000:3000/udp -p 3001:3001/udp -p 3002:3002/udp -p 3003:3003/udp -p 3004:3004/udp -p 3005:3005/udp -v /var/run/docker.sock:/var/run/docker.sock --volumes-from=john-dev-data --name="johnae-dev" johnae/dev```

The default entrypoint is /entrypoint.sh which will also setup any env vars set starting with DOCKER_ using the file /etc/profile.d/docker-env.sh (created on container startup). Thus no entrypoint is needed as the image already has the default one set.

We're mounting the user home from a previously created data container. That way the container may be thrown away while keeping the important data. We also mount the hosts docker.sock inside the container which enables us to use docker from within the container to launch additional containers on the host. This is very useful in a dev environment. The ports we're forwarding are mostly udp ports, except for the ssh port. The udp ports are for mosh which I highly recommend using instead of ssh.
