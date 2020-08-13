# Learning resource for Docker certified associate 2020 with Kubernetes (k8s)

##### Docker certified associate exam 2020 has changed its exam content has made some changes on exam procedires. Make sure you are aware of the changes.
##### Since docker certified associate 2020 exam also asks k8s questions as well. I have included k8s topics which will be asked in the exam.

## Make sure you are aware about the DOMC exam type and format (~ 33 DOMC question can't be changed)
#### Practice DOMC exam from here: https://sei.caveon.com/launchpad/docker-domc-practice-exam-world-geography/domc-practice
## ~13 normal MCQ question which can be changed
#### MCQs are same as previous

# Docker cCertified Associate exam format
#### Orchestration (25% of the Exam)
#### Image Creation, Management, and Registry (20% of the Exam)
#### Installation and Configuration (15% of the Exam)
#### Networking (15% of the Exam)
#### Security (15% of the Exam)
#### Storage and Volumes (10% of the Exam)

# Domain 1: Orchestration (25% of exam)
### Content may include the following:
● Complete the setup of a swarm mode cluster, with managers and worker nodes

Set up manager node
```
ifconfig INTERFACE
docker swarm init --advertise-addr MANAGER_NODE_IP
```

Setup worker node and manager node
```
docker swarm join-token manager
docker swarm join-token worker
```

list nodes
```
docker node ls
```

● Describe and demonstrate how to extend the instructions to run individual containers into
running services under swarm.

```
docker service create --name CONTAINER_NAMW --replicas 1 IMAGE_NAME
```

List service running on swarm
```
docker service ls
```

List containers associated with the service
```
docker service ps SERVICE_NAME
```

● Describe the importance of quorum in a swarm cluster.

Quorum helps to maintain docker swarm state in case when you lose manager nodes

If you have 3 manager node, you can maintain quorum in case you lose 1 manager nodes.

In order to maintain quorum, you can still maintain quorum if you lose (N-1)/2 manager nodes where N is the total number of master node.

```
If the swarm loses the quorum of managers, the swarm cannot perform management tasks. If your swarm has multiple managers, always have more than two. To maintain quorum, a majority of managers must be available. An odd number of managers is recommended, because the next even number does not make the quorum easier to keep. For instance, whether you have 3 or 4 managers, you can still only lose 1 manager and maintain the quorum. If you have 5 or 6 managers, you can still only lose two.
Even if a swarm loses the quorum of managers, swarm tasks on existing worker nodes continue to run. However, swarm nodes cannot be added, updated, or removed, and new or existing tasks cannot be started, stopped, moved, or updated.

The best way to recover from losing the quorum is to bring the failed nodes back online. If you can't do that, the only way to recover from this state is to use the --force-new-cluster action from a manager node. This removes all managers except the manager the command was run from. The quorum is achieved because there is now only one manager. Promote nodes to be managers until you have the desired number of managers.

docker swarm init --force-new-cluster --advertise-addr node01:2377
```

● Describe the difference between running a container and running a service.

Container runs a single instance of image.

Service can run single or multiple inatance of image depending upon the number of replicas provided.

● Interpret the output of “docker inspect” commands.

docker inspect provides detail information about docker objects

```
docker service inspect SERVICE_NAME --pretty
docker container inspect CONTAINER_NAME --pretty
docker node inspect NODE_ID --pretty
```

● Convert an application deployment into a stack file using a YAML compose file with "docker
stack deploy"

Docker compose yaml file can be used to deploy stack into swarm

Deploy stack
```
docker stack deploy --compose-file COMPOSE_FILE.yaml STACK_NAME
docker stack deploy -c COMPOSE_FILE.yaml STACK_NAME
```

Remove stack
```
docker stack rm STACK_NAME
```

● Manipulate a running stack of services.

Show all list of running service]
```
docker stack services STACK_NAME
```

Filtering
```
docker stack services --filter name=SERVICE1_NAME --filter STACK_NAME
```

Formating
```
docker stack services --format "{{.ID}}: {{.Mode}} {{.Replicas}}"
```

● Describe and demonstrate orchestration activities.

THere are different orchestation activities in swarm:
1. Deploy stack
2. Create service
3. Scale service
4. Remove service
5. remove stack

● Increase the number of replicas.

```
docker service scale SERVICE_NAME=3
docker service update --replicas=4 SERVICE_NAME
```

● Add networks, publish ports.

Add networks
```
docker network create my-net
docker network create --driver overlay NETWORK_NAME
docker network create --driver overlay --attachable NETWORK_NAME 
docker network create --driver bridge NETWORK_NAME
docker network create --driver maclvan --subnet 192.168.0.0/24 --gateway 192.168.0.1 -o parent=eth0 NETWORK_NAME
```

Create container and add network to container
```
docker container run -d --rm --name busybox --network NETWORK_NAME radian/busyboxplus
docker container run -d --rm --name nginx --net host nginx
```

Connect my-nginx container to network
```
docker network ls
docker network connect NETWORK_NAME nginx
```

Disconnect a running container from an existing network
```
docker network disconnect NETWORK_NAME CONTAINER_NAME
```

Inspect network
```
docker network inspect NETWORK_NAME
```

Delete network
```
docker network rm NETWORK_NAME
```

Publish port 
```
docker container run --it --name nginx -p 8080:80 nginx
```

List port mapping of container
```
docker port CONAINTER_ID
```

Display port
```
docker ps
```

● Mount volumes.

Creating volume
```
docker volume create VOLUME_NAME
```

Get information baout volume
```
docker volume inspect VOLUME_NAME
```

Deleting volume
```
docker volume rm VOLUME_NAME
```

```
docker container run -dt --name mynginx -v /root/index:/usr/share/nginx/html nginx
```
```
docker container run -dt --name mynginx --mount type=bind,source=/root/index,target=/usr/share/nginx/html nginx
```

```
docker container run -dt --name mynginx --mount type=volume,source=my-volume,target=/usr/share/nginx/html nginx
```

● Describe and demonstrate how to run replicated and global services.
```
docker service create --name SERVICE_NAME --mode=global IMAGE
```


● Apply node labels to demonstrate placement of tasks.
```
docker node update --label-add LABEL=VALUE NODE
```

```
docker node ls
```

Adding lable to node
```
docker node update --label-add LABEL=VALUE NODE
```

```
docker node update --label-add availability_zone=east <NODE_NAME>
docker node update --label-add availability_zone=west <NODE_NAME>
```

```
 docker node update --label-add availability_zone=west node2
 docker node update --label-add availability_zone=west node2
```

View existing node lables
```
docker node inspect --pretty NODE
```

```

# apply node label (key w/no value or type)
docker node update --label-add foo --label-add bar worker1
docker node update --label-add type=queue worker1

# placement of service using node
docker service create \
--replicas 9 \
--name redis_2 \
--placement-pref 'spread=node.labels.datacenter' \
--placement-pref 'spread=node.labels.rack' \ redis:3.0.6
```

● Describe and demonstrate how to use templates with “docker service create”.
It is use to provide dynamic values during service creation. Templates can be used to give somewhat dynamic values to some flags with docker service create

--hostname
--env
--mount

```
# pass in --hostname, --mount, or --env & use Go syntax
docker service create --name hosttempl \
--hostname="{{.Node.Hostname}}-{{.Node.ID}}-{{.Service.Name}}" \
busybox top
```

```
docker service create --name node-hostname --env NODE_HOSTNAME="{{.Node.Hostname}}" --replicas 3 nginx
```


● Identify the steps needed to troubleshoot a service not deploying.
```
First of all, make sure at least one node is not paused or drained, there is enough memory available to meet the value defined in the service, and check the service constraints defined (could make a service be in "pending" state).

Then get a task ID with
docker service ps <service-name>.

Next, it is useful to check meta data with
docker inspect <task-id>

In particular, Error message before container start is in the status field, and then reconfirm whether it was started with the intended parameters.

If the task has container ID, it was abnormally exited after starting the container, so check the log of the container with
docker logs <container-id>
```


● Describe how a Dockerized application communicates with legacy systems.
```
Using container name port, network, ENDS (container name and alias)
```
```
# publish ports via --publish or -p to map container port to host port
-p 8080:80 #80 for container & 8080 for host

# attach it to a network on creation or after
--network
docker network connect

# inherits DNS of docker daemon unless you set
--dns
--dns-search
--dns-opt
```


● Describe how to deploy containerized workloads as Kubernetes pods and deployments.

● Describe how to provide configuration to Kubernetes pods using configMaps and secrets.
```

```

# Domain 2: Image Creation, Management, and Registry (20% of exam)
### Content may include the following:
● Describe the use of Dockerfile.
```
docker build -t myimage .
docker tag IMAGE_NAME:TAG IMAGE_ID
```

● Describe options, such as add, copy, volumes, expose, entry point.
```
ADD and COPY : Add tar and url 
VOLUME : mount volume
EXPOSE: Intent to use port
ENTRYPOINT: It helps to make container executable

```
● Identify and display the main parts of a Dockerfile.
```
docker image history IMAGE_ID

FROM Sets the Base Image for subsequent instructions build stage (must be first line and at least once).
RUN execute any commands in a new layer on top of the current image and commit the results.
CMD provide defaults for an executing container.
EXPOSE informs Docker that the container listens on the specified network ports at runtime. NOTE: does not actually make ports accessible.
ENV sets environment variable.
ADD copies new files, directories or remote file to container. Invalidates caches. Avoid ADD and use COPY instead.
COPY copies new files or directories to container. Note that this only copies as root, so you have to chown manually regardless of your USER / WORKDIR setting.
ENTRYPOINT configures a container that will run as an executable.
VOLUME creates a mount point for externally mounted volumes or other containers.
USER sets the user name for following RUN / CMD / ENTRYPOINT commands.
WORKDIR sets the working directory.
ARG defines a build-time variable.
ONBUILD adds a trigger instruction when the image is used as the base for another build.
STOPSIGNAL sets the system call signal that will be sent to the container to exit.
LABEL apply key/value metadata to your images, containers, or daemons.
```
● Describe and demonstrate how to create an efficient image via a Dockerfile.
```
Multi-stage build

Should generate containers that are as ephemeral as possible. Image should be as small as possible. Build context should be within a given folder. String RUN commands together with backslashes.
```

● Describe and demonstrate how to use CLI commands to manage images, such as list,
delete, prune, rmi.
```
docker image ls
docker image rm IMAGE_ID
docker image prune -a
```
● Describe and demonstrate how to inspect images and report specific attributes using filter
and format


● Describe and demonstrate how to tag an image.
```
docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```

● Describe and demonstrate how to apply a file to create a Docker image.
● Describe and demonstrate how to display layers of a Docker image
```
docker image history IMAGE_NAME
docker inspect 
```
● Describe and demonstrate how to modify an image to a single layer.
```
docker export CONTAINER_NAME | docker import - IMAGE_NAME
```
```
#Without rebuilding
-create a container from the image: docker create
-export the container to a flattened tar: docker container export
-load the image back: docker image import

#With rebuilding
See the --squash option of docker build
```

● Describe and demonstrate registry functions.
● Deploy a registry.
```
Docker Trusted Registry (DTR) is a commercial product that enables complete image management workflow, featuring LDAP integration, image signing, security scanning, and integration with Universal Control Plane. DTR is offered as an add-on to Docker Enterprise subscriptions of Standard or higher.

docker run -d -p 5000:5000 --name registry registry:2
```
● Log into a registry.
```
docker login REGISTRY_URL
```

● Utilize search in a registry.
```
docker search IMAGE_NAME

docker search --filter "is-official=true" --filter "stars=3" busybox
```
● Push an image to a registry.
```
docker push REPOSITORY/IMAGE:TAG

docker image push [OPTIONS] NAME[:TAG]
# NAME including registry endpoint (also built with it in the name)
```

● Sign an image in a registry.
```
DCT = Docker Content Trust

export DOCKER_CONTENT_TRUST=1
docker push <dtr-domain>/<repository>/<image>:<tag>
```

● Pull and delete images from a registry.
```
docker login REGISTRY_URL
docker pull IMAGE:TAG

You can delete an image from the Docker Hub by deleting individual tags in your repository in the tags area.
```

# Domain 3: Installation and Configuration (15% of exam)
### Content may include the following:
● Describe sizing requirements for installation.
● Describe and demonstrate the setup of repo, selection of a storage driver, and installation of the Docker engine on multiple platforms.
● Describe and demonstrate configuration of logging drivers (splunk, journald, etc.).

You can use log drive using flags --log-driver and --log-opt
OR
/etc/docker/daemon.json
```
{
	"log-driver":"json-file",
	"log-opts":{
		"max-size":"15m"
	}
}
```
● Describe and demonstrate how to set up swarm, configure managers, add nodes, and
setup the backup schedule.

Backup swarm
Stop docker
backup content of /var/lib/docker/swarm
start docker


● Describe and demonstrate how to create and manage user and teams.
● Describe and demonstrate how to configure the Docker daemon to start on boot.
```
systemctl enable dockerd
```
● Describe and demonstrate how to use certificate-based client-server authentication to
ensure a Docker daemon has the rights to access images on a registry.
● Describe the use of namespaces, cgroups, and certificate configuration.
● Describe and interpret errors to troubleshoot installation issues without assistance.
● Describe and demonstrate the steps to deploy the Docker engine, UCP, and DTR on AWS
and on-premises in an HA configuration.
● Describe and demonstrate how to configure backups for UCP and DTR. 

# Domain 4: Networking (15% of exam)
### Content may include the following:
● Describe the Container Network Model and how it interfaces with the Docker engine and
network and IPAM drivers.
● Describe the different types and use cases for the built-in network drivers.
● Describe the types of traffic that flow between the Docker engine, registry and UCP
controllers.
● Describe and demonstrate how to create a Docker bridge network for developers to use for
their containers.
● Describe and demonstrate how to publish a port so that an application is accessible
externally.
● Identify which IP and port a container is externally accessible on.
● Compare and contrast “host” and “ingress” publishing modes.
● Describe and demonstrate how to configure Docker to use external DNS.
● Describe and demonstrate how to use Docker to load balance HTTP/HTTPs traffic to an
application (Configure L7 load balancing with Docker EE).
● Understand and describe the types of traffic that flow between the Docker engine, registry,
and UCP controllers
● Describe and demonstrate how to deploy a service on a Docker overlay network.
● Describe and demonstrate how to troubleshoot container and engine logs to resolve
connectivity issues between containers.
● Describe how to route traffic to Kubernetes pods using ClusterIP and NodePort services.
● Describe the Kubertnetes’ container network model.

# Domain 5: Security (15% of exam)
### Content may include the following:
● Describe security administration and tasks.
● Describe the process of signing an image.
● Describe default engine security.
● Describe swarm default security.
● Describe MTLS.
● Describe identity roles.
● Compare and contrast UCP workers and managers.
● Describe the process to use external certificates with UCP and DTR.
● Describe and demonstrate that an image passes a security scan.
● Describe and demonstrate how to enable Docker Content Trust.
● Describe and demonstrate how to configure RBAC with UCP.
● Describe and demonstrate how to integrate UCP with LDAP/AD. 
● Describe and demonstrate how to create UCP client bundles.

# Domain 6: Storage and Volumes (10% of exam)
### Content may include the following:
● Identify the correct graph drivers to uses with various operating systems.
● Describe and demonstrate how to configure devicemapper.
● Compare and contrast object and block storage and when they should be used.
● Describe how an application is composed of layers and where these layers reside on the
filesystem.
● Describe the use of volumes are used with Docker for persistent storage.
● Identify the steps to take to clean up unused images on a filesystem and DTR.
● Describe and demonstrate how storage can be used across cluster nodes.
● Describe how to provision persistent storage to a Kubernetes pod using persistentVolumes.
● Describe the relationship between container storage interface drivers, storageClass,
persistentVolumeClaim and volume objects in Kubernetes. 