# [Amazon Elastic Container Service (ECS) Primer](https://miro.com/app/board/uXjVP_LMhzQ=/?share_link_id=924732886799)

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


## Task Placement Strategies

How Amazon ECS actually places the tasks on the container instances in the cluster when you are using the EC2 launch type

<img width="800" src="https://user-images.githubusercontent.com/70295997/227667866-f0c2c4ab-cf78-4b7a-aaec-bb7817335da1.png">

### Task placement

This scenario has 10 container instances. You are making a request to run some tasks or to create a service. As part of that request, you specify CPU, memory, and networking requirements.

You also provide other constraints, such as a specific Availability Zone, Amazon Machine Image (AMI), or instance type.

Lastly, you specify the strategy you prefer ECS to use when starting the tasks; for example, spreading across multiple container instances to maximize availability or consolidating across a smaller number of instances to improve utilization.

At the end of that process, ECS identifies a set of instances that satisfies the requirements for the task you want to run and will place those tasks across your cluster based on the criteria specified.

![image](https://user-images.githubusercontent.com/70295997/227667932-68e3760d-44eb-4b9e-870f-de5d25fdd7d1.png)


### Strategies and constraints

Remember that each task is an instance of a task definition. The task definition specifies the resources required in addition to the task placement information, such as placement strategies and placement constraints.

Placement strategies are best-effort: ECS attempts to place tasks even when the most optimal placement option is unavailable.

The supported placement strategy types are:
• **Random**: Places tasks randomly across Container instances.
• **Binpack**: Places tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use and gets the most utilization out of every instance.
• **Spread**: Places tasks evenly based on a specified value, such as Availability Zone. Service tasks are spread based on the tasks from that service.

Placement constraints are binding, and they can prevent task placement. The supported constraints are:
•  **distinctInstance**: Places each task on a different container instance.
•  **memberOf**: Places tasks based on an expression; for example, group membership.

![image](https://user-images.githubusercontent.com/70295997/227668446-b404ecae-f171-4221-b8df-c1422657b6fa.png)


### Targeting instance type and zone

Placement strategy examples. In this first example, the task definition contains a placement constraint targeting a specific instance type (t2.small) and excluding a specific Availability Zone (us-east-1d).

![image](https://user-images.githubusercontent.com/70295997/227668467-8ae3fb65-8d82-4bf1-9327-04533e740f46.png)

### Spread across zone and binpack

In this example, the task definition is using two placement strategies: Spreading tasks across Availability Zones and then bin packing based on memory.

 The binpack strategy gets its name from the bin packing problem in the physical world, where containers of different sizes must be packed into bins by using the smallest number of bins possible.
 
 ### ![image](https://user-images.githubusercontent.com/70295997/227668490-aa657314-7be7-4996-b634-d1fb85287802.png)

Affinity and anti-affinity 

In this example, we have two task definitions, both of which are using placement constraints.

The first definition requires that its tasks be placed on instances that are part of the webserver group, which is a requirement for these applications to function properly.

The second task definition uses a constraint to specify the opposite: requires the scheduler to place its tasks anywhere except instances that are part of the webserver group. Remember that constraints are binding. If there had been no instances in the webserver group with sufficient resources available, then task placement for the first definition would have failed.

![image](https://user-images.githubusercontent.com/70295997/227668530-f2a3adac-479a-4f84-b588-546e830b8e98.png)

### Running a service

Services can take advantage of the same placement strategies and constraints while maintaining the desired number of tasks. In this example service definition, the scheduler places tasks only on T2 instances, spreads those tasks across Availability Zones, and bin packs the tasks onto the fewest number of instances in each zone by memory.

![image](https://user-images.githubusercontent.com/70295997/227668554-e494f6ed-19c3-4c66-912e-6b40374a55aa.png)

### Multiple services on a cluster

Let's look at a couple of service examples. In this example, you have multiple services running on the same cluster. The first one is bin packing on memory, and the second is spread acrosS Availability Zones.

![image](https://user-images.githubusercontent.com/70295997/227668577-2264d1ff-286f-4484-babd-61af10ea7acc.png)

### Services-distinct instances

Services can also use the distinctInstance placement constraint to ensure landing on specific instance types, such as GPU- accelerated instances or instances with specific CPU and memory requirements.

### Knowledge Check

How do task placement strategies and task placement constraints affect how tasks are placed tasks on instances?
- [x] Strategies are best-effort. Constraints are binding.
- [ ] Strategies are binding. Constraints are best-effort.
- [ ] Both strategies and constraints are best-effort.
- [ ] Both strategies and constraints are binding.

Which ECS task placement strategy minimizes the number of instances in use?
- [x] Binpack
- [ ] Random
- [ ] Spread
- [ ] Overload

## Integrate ECS with other AWS services

![image](https://user-images.githubusercontent.com/70295997/227737021-5599f893-9f33-4399-8978-269c85a7cc44.png)

### Containers within AWS

One of the strengths of running container-based workloads with ECS is the tight integration with other AWS services.

You can use:
- Amazon Simple Notification (Amazon SNS) and Amazon Simple Queue (Amazon SQS) services for decoupling.
- Classic, Application, and Network load balancers.
- Route 53 for DNS and service discovery.
- Identity and Access Management (IAM) for authentication and authorization.
- Secrets Manager for managing encrypted passwords, credentials, tokens, and other secrets.
- API Gateway to expose the services to the outside world.
- AWS Developer tools such as CodePipeline for continuous integration and continuous development (CI/CD).
- Amazon CloudWatch for monitoring, logging, and alerting.

![image](https://user-images.githubusercontent.com/70295997/227737057-fe4e87e3-22bb-422b-bcf9-8162d8864bd8.png)

### ECR: Elastic Container Registry

Let's look at some of the most common integration patterns in ECS. Earlier, we briefly mentioned Amazon ECR: the Elastic Container Registry.

 Remember: After images are built, they are immutable.

When launching a container, ECS pulls images from a public or private image registry. ECR is a fully managed, cloud-based Docker image registry that is fully integrated with Amazon ECS and the Docker CLI. ECR is scalable, highly available, and secure images are encrypted at rest, with IAM-based access and authorization controls.

![image](https://user-images.githubusercontent.com/70295997/227737113-5b9f32cc-ba84-4301-8c29-5c08d3760001.png)

### Microservice architecture for integration

Here is a basic microservice architecture that highlights ECS integration with other AWS services. Clients access the containerized application through DNS, using Route 53, or by using APl calls from a front-end application through the Amazon API Gateway. A load balancer manages traffic to ECS clusters and a database instance from Amazon RDS handles persistent data storage. Other AWS services at work here include ECR that manages the container images, IAM that handles authentication and authorization, and CloudWatch that collects metrics.

<img width="800" src="https://user-images.githubusercontent.com/70295997/227737371-06a574ec-c5b5-4334-9c79-3ef7343f1105.png">

### Service discovery

ECS includes integrated service discovery. This makes it possible for an ECS service to automatically register itself with a predictable and friendly DNS name in Amazon Route 53.

In this architecture, we have a number of container-powered microservices, all in their own Auto Scaling groups to respond to changing loads. As services scale up or down in response to load or container health, ECS keeps the records in Route 53 up-to-date, allowing other services to look up where they need to make connections based on the state of each service.

<img width="1000" src="https://user-images.githubusercontent.com/70295997/227748282-1af95e54-d48b-4d40-943c-63dbbcca3e84.png">

### Continuous deployment

Here is one way of doing continuous deployment (CD) using the AWS CodeStar group of services.
1. Developers continually integrate their changes together into a main branch hosted in AWS CodeCommit.
2. AWS CodePipeline uses CloudWatch Events to detect changes in your AWS CodeCommit Source repository and triggers an execution of the CD pipeline when a new revision is found.
3. AWS CodePipeline sends the new revision to AWS CodeBuild, which builds a Docker container image from the source Code.
4. AWS CodeBuild pushes the newly built Docker container image tagged with the build ID to an Amazon ECR repository.
5. CodePipeline initiates an update of the AWS CloudFormation stack, which defines the Amazon ECS task definition and service.
6. AWS CloudFormation creates a new task definition revision referencing the newly built image, and updates ECS
7. ECS fetches the new container from ECR and replaces the old task with the new one, which completes the deployment.

![image](https://user-images.githubusercontent.com/70295997/227748323-11f135ca-1aac-4edc-a768-bf09b5819108.png)

### Blue-Green deployments

Blue/green deployments are used to deploy software updates with less risk by creating two separate environments, blue and green. "Blue" is the current running version of your application, and "green" is the new version you will deploy. This type of deployment gives you an opportunity to test features in the green environment without impacting the current running version of your application. When you're satisfied that the green version is working properly, you can gradually reroute the traffic from the old blue environment to the new green environment. By following this method you can update and roll back features with near-zero downtime.

Containers can ease the adoption of blue/green deployments because they're easily packaged and behave consistently as they're moved between environments. This example shows how ECS can be used for blue/green deployments, with AWS CodeDeploy handling the switchover.
1. First you define your ECS service and specify blue/green deployment (powered by AWS CodeDeploy) as the deployment type.
2. The service should include an Application Load Balancer that is configured to listen on two ports.
3. ECS creates an application and deployment group in AWS CodeDeploy for your service.
4. When a task definition for one of the tasks in the service is updated, CodeDeploy deploys the new (green) copy of the service, and traffic for the green service is directed to the second port; in this example port 8080. If there are any problems during deployment, you can stop and roll back to the blue service with no impact to users.
5. You can now perform automated or manual testing on the green service. When the green service has been validated, the deployment is completed by swapping the listener rules on the Application Load Balancer and sending user traffic to the green service.

![image](https://user-images.githubusercontent.com/70295997/227748360-eec6cef5-c5d8-410f-80ef-644de617a1a6.png)

### Service autoscaling: CloudWatch

You can configure ECS to use Service Auto Scaling to adjust its desired count up or down in response to CloudWatch alarms. ECS publishes CloudWatch metrics with your service's average CPU and memory usage. You can use these service utilization metrics to Scale your service out to deal with high demand at peak times, and to scale your service in to reduce costs during periods of low utilization.

### Knowledge Check

Which AWS service facilitates service discovery through DNS in a microservices architecture?
- [ ] Amazon SNS
- [ ] Amazon API Gateway
- [ ] Amazon CloudWatch
- [x] Amazon Route53

## Security features of ECS

![image](https://user-images.githubusercontent.com/70295997/227748460-68842a06-2b49-42d9-a405-6ffa001d7868.png)

### Access management: IAM Roles

Each task can have its own IAM role, providing granular permissions for service access. In this example, Task A has been granted access to data in a DynamoDB table. A new task, Task B, needs to access data in an S3 bucket. To allow this, you create a policy in IAM that grants permission to retrieve objects from a specific S3 bucket.

Next, you create a role in IAM with this policy attached. The IAM role you created is added to the task definition for Task B, allowing any instance of that task to retrieve objects from the S3 bucket. Task B does not have permission to access data in Amazon DynamoDB, and Task A does not have permission to access the S3 bucket unless you specifically attach the appropriate policy to the role assigned to Task A.

![image](https://user-images.githubusercontent.com/70295997/227748472-cc2991d0-ee83-4bcf-a2fc-36b660652e10.png)

### Managing secrets

Tasks can also retrieve secrets from the Parameter Store, which is integrated with Secrets Manager. Secrets Manager helps you organize and manage important configuration data such as credentials, passwords, and license keys. By using Parameter Store to reference Secrets Manager secrets, you create a consistent and secure process for calling and using secrets and reference data in your code and configuration scripts. Parameter Store functions as a pass-through service for references to Secrets Manager secrets. No data or metadata about secrets is retained in Parameter Store - the reference is stateless.

Secrets Manager encrypts the protected text of a secret by using the AWS Key Management Service (AWS KMS), a key storage and encryption service that's used by many AWS services. This helps ensure that your secret is securely encrypted when it's at rest.

You can attach IAM policies to a role that grants or denies access to specific secrets, and restrict what can with those secrets. In this example: granting read-only permission to the one secret that Task B needs to run. Alternatively, you can attach a policy directly to the secret to grant permissions that specify who is allowed to read or modify the secret and its versions. There are many other uses for Secrets Manager in ECS; for example, accessing the secure credentials to a private registry that contains the container image you want to pul.

![image](https://user-images.githubusercontent.com/70295997/227748486-f966d1cb-ccea-41fa-9c64-f6bb284f71d3.png)

### Daemon scheduling strategy

All of the examples of scheduling services we have looked at so far use the REPLICA scheduling strategy. This strategy places and maintains the desired number of tasks across your cluster based on the task placement strategies and constraints you specify.

When using the EC2 launch type, you also can use the DAEMON scheduling strategy. DAEMON scheduling deploys exactly one task on each active container instance that meets all of the task placement constraints specified in your cluster. This is useful to provide common supporting functionality, Such as logging, monitoring, or backups, for the tasks running your application code. This scheduling strategy automatically tracks the changes in the cluster and deploys the required daemon tasks when new instances join the cluster. This helps you optimize for the CPU and memory required by your applications, enabling you to deploy one daemon task to support all application tasks running on each instance.

### Knowledge Check

How can access to a secret in Secrets Manager be controlled?
- [ ] A. Each task can have its own IAM role with a policy attached.
- [ ] B. A policy can be attached directly to the secret.
- [x] C. Both A and B.
- [ ] D. None of the above.


