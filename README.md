
# PROJECT_NAME

*...*

## usage

*...*

### deps

*...*

### docker setup

First, we must prepare our local environment to suit this
project by maintaining a local environment file called `.env`. This file must
never be checked into revision control! If you accidently push the file onto
a public medium, **all bits** considered sensitive must be discarded in full
and regenerated from scratch (by following this guide again).

So, without further ado, let us begin by creating our local environment file!

```sh
cp -av .env.dist .env
```

Next, use your favorite text editor and begin filling in the
variables inside of each file to your liking. However you choose
to manage your credentials for this sort of thing, be sure that the
passwords are kept somewhere safe and accessible in the case that they may
need to be revoked at some point in the future.

```sh
PROJECT_SITE_URL=project.fs1.home
```

As I already have a private DNS setup on my LAN, I simply
visit my authoritative DNS panel and add an entry to the
**fs1.home** zone. Feel free to start off by using
**localhost** for this, orby adding an entry to your hosts
file at `/etc/hosts`.

```sh
# /etc/hosts
# Note that if you are using a reverse proxy that is hosted
# elsewhere, you may want to update the IP to reflect the
# address of the proxy host.
<hostIP> project.localhost
```

Finally, we can bootstrap the project! Cross your fingers, say
your prayers or however your typical ritual for this goes:

```sh
docker compose up -d
docker compose down
docker compose up -d --force-recreate
docker compose up -d service-server --force-recreate
```

```sh
# Check status of each service
docker compose logs #--since=5m -f

```sh
# Start and stop an individual service
docker start service-db # service name
docker stop service-proxy # service name
```

- bind mounts
  * `./mounts/...`

## reference documents

[0]: https://github.com/i8degrees-dockerfiles
