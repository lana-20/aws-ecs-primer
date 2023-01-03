# [Amazon Elastic Container Service (ECS) Primer](https://explore.skillbuilder.aws/learn/course/91/play/202/amazon-elastic-container-service-ecs-primer;lp=83)

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


__One host, multiple containers__

Running one or two containers on a single host is simple.

![image](https://user-images.githubusercontent.com/70295997/210416205-9018361a-d659-4603-a0fc-54f9d01b33f9.png)

__Tens of hosts, hundreds of containers__

 What happens when you move into a staging and testing environment where you have tens of hosts with possibly hundreds of containers?
 
 ![image](https://user-images.githubusercontent.com/70295997/210416319-9919aa9a-de50-48c3-983d-d61dd43015c6.png)

__Hundreds of hosts, thousands of containers__

Now imagine a full production environment with hundreds of hosts and maybe thousands of containers. Now you are managing an enterprise-scale clustered environment, and cluster management is hard. You need a way to place your containers intelligently on instances to maximize availability, resilience, and performance. That means you must know the state of everything in your system.

What instances have available resources like memory and ports? How do you know when a container dies? How do you hook into load balancers? How do you facilitate continuous integration and continuous delivery (CI/CD)? In other words, how do you manage your containers at scale?

<img src="https://user-images.githubusercontent.com/70295997/210416491-8f934829-5160-4e6a-a8ba-4c329d7290f7.png" width=500>

__Container management platforms__

This is where _container management platforms_ come in. They handle the scheduling and placement of containers based on thee underlying hardware infrastructure and the application needs. They provide integration with other services such as networking, persistent storage, security, monitoring, and logging.

<img src="https://user-images.githubusercontent.com/70295997/210416663-fbd8eb1d-d123-4d22-a532-1464ad251c07.png" width=700>

__Container management examples__

Numerous options exist, such as the Amazon ECS, native tools like Docker Swarm mode, or open source platforms, Such as Kubernetes. The management platform is arguably the most important choice you will make when architecting a container-based workload.

<img src="https://user-images.githubusercontent.com/70295997/210416816-0fbaa207-b2f2-4206-963e-bfdbe5b0dd19.png" width=700>

__Amazon ECS: Docker containers__

 ECS is a highly scalable, highly performing container orches service that supports Docker containers. The service enables you to easily run and scale containerized applications on AWS. You can use ECS to schedule the placement of containers across a managed cluster of Amazon EC2 instances. ECS provides its own schedulers but can also integrate with third-party schedulers to meet business- or application-specific requirements. ECS is also tightly integrated with other AWS services such as IAM, Amazon CloudWatch, and Amazon Route 53.

![image](https://user-images.githubusercontent.com/70295997/210417183-f40ee080-3e78-4db8-b4e7-ee3199da8c81.png)

__ECS solutions architecture__

Here is a high-level Overview of ECS.

First, container images are pulled from a registry. These container images can come from Amazon Elastic Container Registry (Amazon ECR), which is one of many AWS services that integrate with ECS, or they can be pulled from a third-party or private registry.

Next, you define your application: Customize the Container images with the necessary code and resources, and then create the appropriate configuration files to group and define your containers as short running tasks or long- running services within ECS. We'll use services in this example.

__Launch Types__

When you are ready to bring your services online, you select one of two launch types.

The _Fargate launch type_ provides a near-serverless experience where the infrastructure supporting your containers is Completely managed by AWS as a service. AWS manages the placement of your tasks on instances and makes sure that each task has the appropriate CPU and memory. with Fargate, you can focus on the tasks themselves and the application architecture instead of worrying about the infrastructure.

The _EC2 launch type_ is useful when you want more control over the infrastructure supporting your tasks. When using the EC2 launch type, you create and manage clusters of EC2 instances to support your containers and you define the placement of containers across your cluster based on your resource needs, isolation policies, and availability requirements. You have more granular control over your environment without needing to operate your own cluster management and configuration management systems, or worry about scaling your management infrastructure.

You can mix and match the two launch types as needed within your application. For example, you can launch services with more predictable resource requirements using EC2 and launch other services that are Subject to wider swings in demand in Fargate. Regardless of the launch type you use, ECS manages your containers for availability and can scale your application as necessary to meet demand.
