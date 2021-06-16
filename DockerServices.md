https://docs.docker.com/engine/reference/commandline/service/

In swarm mode to deploy an application 
	we create a service
	
	Generally service is the image for a microservice 
		within the context of some larger application. 
	
When you create a service
	specify which container image to use 
	which commands to execute inside running containers. 
	You also define following options for the service:
		1. port where the swarm makes the service available outside the swarm
		2. overlay network for the service to connect to other services in the swarm
		3. CPU and memory limits and reservations
		4. Rolling update policy
		5. Number of replicas of the image to run in the swarm
		6. Services, tasks, and containers
		7. While deploying service to the swarm, 
			swarm manager 
				accepts your service definition as the desired state 
				schedules the service on nodes 
				tasks run independently of each other on nodes in the swarm.

	
	
	Tasks and scheduling
	--------------------
	Task 
		atomic unit of scheduling within a swarm. 
		Declare a desired service state by 
			creating or 
			updating a service, 
		orchestrator realizes the desired state by scheduling tasks. 
		For e.g., 
			if we define a service with three instances  
			The orchestrator creates three tasks. 
			Each task is a slot that the scheduler fills by spawning a container. 
			The container is the instantiation of the task. 
			If an HTTP listener task subsequently fails its health check or crashes, 
			the orchestrator creates a new replica task that spawns a new container.
		Task is one-directional mechanism. 
		It progresses monotonically through a series of states: 
			assigned, 
			prepared, 
			running, 
			etc. 
		If the task fails 
			orchestrator removes the task and its container 
			then creates a new task to replace it 
				according to the desired state specified by the service.

	The underlying logic of Docker swarm is 
		general purpose scheduler 
		and 
		orchestrator.
		
		Reference: Second diagram in 
		https://docs.docker.com/engine/swarm/how-swarm-mode-works/services/
		
		
	Pending services
	----------------
	Service can be configured such that no node runs its tasks. 
		this service remains in state pending. 
	
	For e.g
		Scale the service to 0.
	or
		All nodes are paused or drained
		create a service, 
			it is pending until a node becomes available. 
		N.B: Don't do this on production because once a node becomes available, first node to become available gets all of the tasks.

		You can reserve a specific amount of memory for a service. 
		If no node in the swarm has the required amount of memory, 
			the service remains in a pending state until a node is available which can run its tasks. 
			If you specify a very large value, such as 500 GB, the task stays pending forever, unless you really have a node which can satisfy it.

You can impose placement constraints on the service, and the constraints may not be able to be honored at a given time.

This behavior illustrates that the requirements and configuration of your tasks are not tightly tied to the current state of the swarm. As the administrator of a swarm, you declare the desired state of your swarm, and the manager works with the nodes in the swarm to create that state. You do not need to micro-manage the tasks on the swarm.

Replicated and global services
There are two types of service deployments, replicated and global.

For a replicated service, you specify the number of identical tasks you want to run. For example, you decide to deploy an HTTP service with three replicas, each serving the same content.

A global service is a service that runs one task on every node. There is no pre-specified number of tasks. Each time you add a node to the swarm, the orchestrator creates a task and the scheduler assigns the task to the new node. Good candidates for global services are monitoring agents, an anti-virus scanners or other types of containers that you want to run on every node in the swarm.


		


Create a service with nginx image

    $ docker service create nginx

Create nginx service with a name
  
    $ docker service create --name my_web nginx
    
List services:

    $ docker service ls

Login to a private registry 

    $ docker login registry.example.com
    $ docker service  create   --with-registry-auth   --name my_service registry.example.com/acme/my_image:latest

Update a service 

    $ docker service update --publish-add 80 my_zekelabs      ## Adding a port 80 with the service for outside world

Create a service with various options 

    $ docker service create --name  zekeCalling   --env MYVAR=myvalue   --workdir /tmp   --user my_user   alpine ping docker.com

Create a service with 3 containers with port forwarding 

    $ docker service create --name my_web --replicas 3 --publish published=8080, target=80 nginx
