## Graphite + Carbon

An all-in-one image running graphite and carbon-cache. **Version**: 0.9.12.

This image contains a sensible default configuration of graphite and
carbon-cache. Starting this container will, by default, bind the the following
host ports:

- `80`: the graphite web interface
- `2003`: the carbon-cache line receiver (the standard graphite protocol)
- `2004`: the carbon-cache pickle receiver
- `7002`: the carbon-cache query port (used by the web interface)

With this image, you can get up and running with graphite by simply running:

    docker run -d nickstenning/graphite

If you already have services running on the host on one or more of these ports,
you may wish to allow docker to assign random ports on the host. You can do this
easily by running:

    docker run -p 80 -p 2003 -p 2004 -p 7002 -d nickstenning/graphite

You can log into the administrative interface of graphite-web (a Django
application) with the username `admin` and password `admin`. These passwords can
be changed through the web interface.

**N.B.** Please be aware that by default docker will make the exposed ports
accessible from anywhere if the host firewall is unconfigured.

### Data volumes

Graphite data is stored at `/var/lib/graphite/storage/whisper` within the
container. If you wish to store your metrics outside the container (highly
recommended) you can use docker's data volumes feature. For example, to store
graphite's metric database at `/data/graphite` on the host, you could use:

    docker run -v /data/graphite:/var/lib/graphite/storage/whisper \
               -d nickstenning/graphite

**N.B.** You will need to run the container with suitable permissions to write
to the data volume directory. Carbon and the graphite webapp run as `www-data`
inside the container, but this UID/GID may be mapped inconsistently on the host.

### Technical details

By default, this instance of carbon-cache uses the following retention periods
resulting in whisper files of approximately 2.5MiB.

    10s:8d,1m:31d,10m:1y,1h:5y

For more information, see [the
repository](https://github.com/nickstenning/dockerfiles/tree/master/graphite).

### Running on boot2docker
If you want to store data in your home directory on the host you need to do the following

* Create a directory to store the data (e.g. `/Users/<you>/docker/graphite`)
* Set the permissions in boot2docker to allow www-run to access (comment by @sanros on https://github.com/boot2docker/boot2docker/issues/581)
	* `boot2docker ssh`
	* `sudo umount /Users`
	* `sudo mount -t vboxsf -o uid=33,gid=33 Users /Users`
* Start with the volume: `docker run --name graphite -p 8080:80 -p 2003:2003 -v /Users/<you>/docker/graphite:/var/lib/graphite/storage/whisper -d tilman/graphite`

