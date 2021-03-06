# 02-replicated-pair

This is an example of multiple PingDirectory instances replicating between one another.

[docker-compose.yaml](https://raw.githubusercontent.com/pingidentity/pingidentity-devops-getting-started/master/11-docker-compose/02-replicated-pair/docker-compose.yaml)

## Getting started

Please refer to the [Docker Compose Overview](../README.md) for details on how to start, stop, cleanup stacks.

To stand up multiple containers with a single command add the `--scale` argument to `docker-compose up`:

`docker-compose up --detach --scale pingdirectory=2`

Watch the directories initialize with:

`docker-compose logs -f`

## Using the containers

To see the PingDirectory management console

* Go to [https://localhost:8443/console](https://localhost:8443/console)
* Log in with `Administrator / 2FederateM0re`

> Note: The admin console is running in the pingdataconsole_1 container, not pingdirectory.

Make a change to a user entry on one of the containers. To look at the containers that docker-compose started:

`docker-compose ps`

Then:

```text
docker container exec -it 02-replicated-pair_pingdirectory_1 /opt/out/instance/bin/ldapmodify
dn: uid=user.0,ou=people,dc=example,dc=com
changetype: modify
replace: description
description: this modify was effected on the first container

<Ctrl-D>
```

Note: the white line, followed by the `<Ctrl-D>` are important here; that's how entries are separated in LDIF

On the other container, the matching entry will have the same description:

```text
docker container exec -it 02-replicated-pair_pingdirectory_2 /opt/out/instance/bin/ldapsearch -b uid=user.0,ou=people,dc=example,dc=com -s base '(&)' description

######OUTPUTS######
# dn: uid=user.0,ou=people,dc=example,dc=com
# description: this modify was effected on the first container
###################
```

## Cleaning up

To bring the stack down:

`docker-compose down`

