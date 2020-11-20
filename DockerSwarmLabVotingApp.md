Access the voting-app code at 
https://github.com/dockersamples/example-voting-app

## on Master Node :

	$yum install docker
	$service docker  start
	$docker swarm init --advertise-addr $(hostname -i)

Download the code

	$yum install git -y
	$git clone https://github.com/docker/example-voting-app

	$cd example-voting-app
	$cat docker-stack.yml

Deploy the stack 

	$docker stack deploy --compose-file=docker-stack.yml voting_stack
	$docker stack ls

For each services do ---

	$docker stack services voting_stack
	$docker service ps voting_stack_vote

scale a service:
	
	$docker service scale voting_stack_vote=5

	$docker service ls

	$docker service update --replicas 2 voting_stack_vote

	$docker service update --replicas 2 voting_stack_redis
 
	http://<master ip>:5000/
	
	docker stack rm voting_stack

##########################
To create swarm using Hyper V container
https://www.cardinalsolutions.com/blog/2017/06/docker_swarm_mode_part_1

