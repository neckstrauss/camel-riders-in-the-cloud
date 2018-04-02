# Camel riders in the Cloud

Two microservices using Spring Boot and WildFly Swarm with Apache Camel running on Kubernetes.

There are three Maven projects:

* client - Spring Boot application with Camel that triggers every 2nd second to call the hello service and log the response. The client uses Camel client side retry for error handling. 
* client-hystrix - A client that uses Hystrix as circuit breaker for error handling.
* helloswarm - WildFly Swarm application hostin a hello service which returns a reply message.

The diagram below illustrates this:

![Overview](diagram.png?raw=true "Overview")


### Slides and Video

This source code is used for the DevNation Live webinar, and you can find the slides for the talks in the [slides](slides) directory.

The webinar was recorded in March 2018 and the video is posted on posted on [youtube](https://developers.redhat.com/video/youtube/zuEYtMvHN6g/)

### Prepare shell

When using Maven tooling you want to setup your command shell for docker/kubernetes which can be done by

    minikube docker-env

Which tells you how to setup using eval

    eval $(minikube docker-env)


### Deploying WildFly Swarm (server)

You can deploy the WildFly Swarm application which hosts the hello service.

    cd helloswarm
    mvn install

If the build is success you can deploy to kubernetes using:

    mvn fabric8:deploy


### Deploying Spring Boot (client)

You can deploy the Spring Boot application which is the client calling the hello service

    cd client
    mvn install

If the build is success you can deploy to kubernetes using:

    mvn fabric8:deploy

You should then be able to show the logs of the client, by running `oc get pods` and find the name of the pod that runs the client, and then use `oc logs -f pod-name` to follow the logs.

However you can also run the application from the shell and have logs automatic tailed using

    mvn fabric8:run

And then when you press `cltr + c` then the application is undeployed. This allows to quickly run an application and stop it easily as if you are using `mvn spring-boot:run` or `mvn wildfly-swarm:run` etc.



### Installing Hystrix Dashboard on Kubernetes

The `client-hystrix` application which uses Hystrix can be viewed from the Hystrix Dashboard.

To install the dashboard you first need to install a hystrix stat collector which is called Turbine:

    kubectl create -f http://repo1.maven.org/maven2/io/fabric8/kubeflix/turbine-server/1.0.28/turbine-server-1.0.28-kubernetes.yml

Then you can install the Hystrix Dashboard:

    kubectl create -f http://repo1.maven.org/maven2/io/fabric8/kubeflix/hystrix-dashboard/1.0.28/hystrix-dashboard-1.0.28-kubernetes.yml

You should then be able to open the Hystrix Dashboard via

    minikube service hystrix-dashboard

