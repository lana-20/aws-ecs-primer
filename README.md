# Amazon Elastic Container Service (ECS) Primer

## AWS ECS:

• Definition

• EC2 vs Fargate launch types

• Microservices architectures that integrate with other Services via ECS

• Enforce security on ECS tasks

### What is Amazon ECS?

<img src="https://user-images.githubusercontent.com/70295997/210414900-07a0068e-af1e-4cb8-99ef-2790f28a9dc1.png" width=500>

In the digital world, containers are a form of virtualization that happens at the operating-system level.
Containers are similar to virtual machines but much more lightweight, containing only application code and the libraries needed to run that code.
Each running container is an instance of a container image, which is an immutable object that can be stored in a public or private registry and customized as needed.

![image](https://user-images.githubusercontent.com/70295997/210415137-aa21aa91-b379-4f59-894f-dbf125e54112.png)

Containers are strongly associated with microservice architectures and the benefits of leveraging both technologies are huge.
Microservice architectures decompose traditional, monolithic architectures into independent components that run as services and communicate by using lightweight APls.
Microservice environments enable for quicker iteration and increase resilience, efficiency, and overall agility.

### Scalability and microarchitectures

Scalability is a factor to  consider with microservices.

<img src="https://user-images.githubusercontent.com/70295997/210415410-785f346a-e906-4842-8f94-444a373fa64f.png" width=500>


#### One host, multiple containers

Running one or two containers on a single host is simple.

![image](https://user-images.githubusercontent.com/70295997/210416205-9018361a-d659-4603-a0fc-54f9d01b33f9.png)

#### Tens of hosts, hundreds of containers

 What happens when you move into a staging and testing environment where you have tens of hosts with possibly hundreds of containers?
 
 ![image](https://user-images.githubusercontent.com/70295997/210416319-9919aa9a-de50-48c3-983d-d61dd43015c6.png)

#### Hundreds of hosts, thousands of containers

Now imagine a full production environment with hundreds of hosts and maybe thousands of containers. Now you are managing an enterprise-scale clustered environment, and cluster management is hard. You need a way to place your containers intelligently on instances to maximize availability, resilience, and performance. That means you must know the state of everything in your system.

What instances have available resources like memory and ports? How do you know when a container dies? How do you hook into load balancers? How do you facilitate continuous integration and continuous delivery (CI/CD)? In other words, how do you manage your containers at scale?

<img src="https://user-images.githubusercontent.com/70295997/210416491-8f934829-5160-4e6a-a8ba-4c329d7290f7.png" width=500>

#### Container management platforms

This is where _container management platforms_ come in. They handle the scheduling and placement of containers based on thee underlying hardware infrastructure and the application needs. They provide integration with other services such as networking, persistent storage, security, monitoring, and logging.

<img src="https://user-images.githubusercontent.com/70295997/210416663-fbd8eb1d-d123-4d22-a532-1464ad251c07.png" width=700>

#### Container management examples

Numerous options exist, such as the Amazon ECS, native tools like Docker Swarm mode, or open source platforms, Such as Kubernetes. The management platform is arguably the most important choice you will make when architecting a container-based workload.

<img src="https://user-images.githubusercontent.com/70295997/210416816-0fbaa207-b2f2-4206-963e-bfdbe5b0dd19.png" width=700>

#### Amazon ECS: Docker containers

 ECS is a highly scalable, highly performing container orches service that supports Docker containers. The service enables you to easily run and scale containerized applications on AWS. You can use ECS to schedule the placement of containers across a managed cluster of Amazon EC2 instances. ECS provides its own schedulers but can also integrate with third-party schedulers to meet business- or application-specific requirements. ECS is also tightly integrated with other AWS services such as IAM, Amazon CloudWatch, and Amazon Route 53.

![image](https://user-images.githubusercontent.com/70295997/210417183-f40ee080-3e78-4db8-b4e7-ee3199da8c81.png)

#### ECS solutions architecture

Here is a high-level Overview of ECS.

First, container images are pulled from a registry. These container images can come from Amazon Elastic Container Registry (Amazon ECR), which is one of many AWS services that integrate with ECS, or they can be pulled from a third-party or private registry.

Next, you define your application: Customize the Container images with the necessary code and resources, and then create the appropriate configuration files to group and define your containers as short running tasks or long- running services within ECS. We'll use services in this example.

#### Launch Types

When you are ready to bring your services online, you select one of two launch types.

The _Fargate launch type_ provides a near-serverless experience where the infrastructure supporting your containers is Completely managed by AWS as a service. AWS manages the placement of your tasks on instances and makes sure that each task has the appropriate CPU and memory. with Fargate, you can focus on the tasks themselves and the application architecture instead of worrying about the infrastructure.

The _EC2 launch type_ is useful when you want more control over the infrastructure supporting your tasks. When using the EC2 launch type, you create and manage clusters of EC2 instances to support your containers and you define the placement of containers across your cluster based on your resource needs, isolation policies, and availability requirements. You have more granular control over your environment without needing to operate your own cluster management and configuration management systems, or worry about scaling your management infrastructure.

You can mix and match the two launch types as needed within your application. For example, you can launch services with more predictable resource requirements using EC2 and launch other services that are Subject to wider swings in demand in Fargate. Regardless of the launch type you use, ECS manages your containers for availability and can scale your application as necessary to meet demand.

## ECS components: Tasks and Services

Two Core components that make up ECS are *tasks* and *services*.

![image](https://user-images.githubusercontent.com/70295997/227643181-8646098b-8ccf-433b-86e7-4930205b1c05.png)

**Tasks** are the atomic unit of deployment within ECS. Tasks are made up of one or more tightly coupled containers.

A task may run standalone, or it may be part of a service. A **service** is an abstraction on top of a task. A service runs a specified number of tasks and can include a load balancer to distribute traffic across the tasks that are associated with the service. If any of your tasks should fail or stop, the service scheduler launches another instance of your task to replace it and maintain the specified count of tasks.

How do you decide whether to deploy tasks or services? Tasks are managed by the ECS task scheduler and are ideal for on-demand workloads. You can run tasks once or at intervals, they are great for batch jobs, and you can use the *RUNTASK* APl or even a custom *STARTASK* command. For long-running applications, you want to use the ECS service scheduler. It automatically performs health management, scales out and scales in, is Availability Zone aware, and allows tor groups of containers.

![image](https://user-images.githubusercontent.com/70295997/227643629-60ffd7d2-abe4-4a72-953d-85d9e135b92d.png)

### Task definition (1 of 2)

Tasks, whether running standalone or as part of a service, are defined in a task definition. The task definition is a text file, in JSON format, which describes one or more containers that together form an application. Task definitions can be thought of as a blueprint for your application. They specify the name and image used for the containers, CPU and memory requirements, container image repositories, networking ports, storage, and other metadata about the containers and the task itself.

In this example task definition, a request is made for 10 CPU units (1024 is one full vCPU) and 300 MB of memory for this container. It is exposing port 80 in the container to port 80 on the host. This container is flagged as essential to the task. Host paths are also mounted as volumes in the container itself.

![image](https://user-images.githubusercontent.com/70295997/227643719-85ccd498-61c2-41f6-94e0-f536edbb9210.png)

### Task definition (2 of 2)

Here's the second half of the task definition. This task has two containers running: here we identify the name, location, and required resources for the second container. Next, we mount a volume from the first container, run a shell script when launched, and mount a named volume from the host itself.

Each task is an instance of a task definition, created at runtime, and hosted by either an EC2 cluster or by AWS Fargate technology.

![image](https://user-images.githubusercontent.com/70295997/227644266-325e94e4-75b2-498f-9af3-e4c5d02a8dd1.png)

### Fargate task definition

This is another example of a task definition, which is specifically designed to use the Fargate launch type.

A couple of things that are different in this task definition compared to the previous example: It specifies the Fargate launch type instead of EC2, which would normally be the default. Also that CPU and memory are specified for the task as a whole instead ot as resources tor each individual container within the task. This makes it easier to control resource consumption at a task level. Task size fields are required for the Fargate launch type and can optionally be used for non-Windows containers using the EC2 launch type.

![image](https://user-images.githubusercontent.com/70295997/227644347-d04de665-e8b5-4e6d-9b63-b08f13bd39ed.png)

### Task hosting: EC2 launch type

Let's take a closer look at how tasks are hosted in ECS. When using the EC2 launch type, tasks are hosted by EC2 instances, whether they are standalone tasks or long-running services. EC2 instances are grouped into clusters. Clusters are region-specific, but can span Availability Zones to increase resilience.

Each EC2 instance in the cluster runs Docker to support the containers that make up your tasks and also runs the ECS agent. The ECS agent is installed automatically as long as you are using an ECS-optimized AMI. The agent starts and stops tasks based on requests from ECS and sends telemetry data about the instance's tasks and resource utilization to the Amazon ECS backplane. The backplane is the singular control plane for all of ECS; it is well scaled, robust, and fault tolerant

An EC2 instance that is running the ECS agent and has been registered into a cluster is referred to as an ECS container instance. When you run tasks with Amazon ECS, your tasks using the EC2 launch type are placed on your active container instances. Proper placement strategies are key to maximizing your efficiency and availability, as we will discuss later in this lesson.

This example is specific to the EC2 launch type. When using the Fargate launch type, it is not necessary to manage clusters of EC2 instances or task placement. Fargate manages the infrastructure for you as a service, allowing you to focus on your tasks and services.

### Knowledge Check

In which task launch type is the infrastructure that supports your containers completely managed by AWS as a service?
- [ ] Amazon ECS
- [ ] Amazon EKS
- [ ] Amazon EC2
- [x] Fargate

Which type of workload would be best to deploy as a service rather than a task?
- [ ] Batch jobs
- [x] Long-running apps
- [ ] Lambda functions
- [ ] None of the above
