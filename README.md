# Micoservice Monitoring Box (Kubernetes & ECS)


## To start using Micoservice Monitoring Box

- [Getting Started](https://github.com/vikrant-kumar1/mybox1#getting-started)
  - [Introduction](https://github.com/vikrant-kumar1/mybox1#introduction)
  - [Components](https://github.com/vikrant-kumar1/mybox1#components)
  - [Deployment](https://github.com/vikrant-kumar1/mybox1#deployment)
    - [Infrastructure Deployment](https://github.com/vikrant-kumar1/mybox1#infrastructure-deployment)
    - [Service Deployment](https://github.com/vikrant-kumar1/mybox1#service-deployment)
    - [Addons services Deployment](https://github.com/vikrant-kumar1/mybox1#addons-services-deployment)
    - [Centralized Logging Deployment](https://github.com/vikrant-kumar1/mybox1#centralized-logging-deployment)
  - [Monitoring](https://github.com/vikrant-kumar1/mybox1#monitoring)
    - [Kubernetes Cluster Monitoring](https://github.com/vikrant-kumar1/mybox1#kubernetes-cluster-monitoring)
    - [ECS Cluster Monitoring](https://github.com/vikrant-kumar1/mybox1#ecs-cluster-monitoring)
    - [Kafka Monitoring](https://github.com/vikrant-kumar1/mybox1#kafka-monitoring)
    - [CouchbaseDB Monitoring](https://github.com/vikrant-kumar1/mybox1#couchbasedb-monitoring)
    - [JVM and Application Monitoring](https://github.com/vikrant-kumar1/mybox1#jvm-and-application-monitoring)
    - [Continer and Node Monitoring](https://github.com/vikrant-kumar1/mybox1#container-and-node-monitoring)
    - [LoadBalancer Monitoring](https://github.com/vikrant-kumar1/mybox1#loadbalancer-monitoring)
  - [Alerting](https://github.com/vikrant-kumar1/mybox1#alerting)
    - [Email Channel](https://github.com/vikrant-kumar1/mybox1#emal-channel)
    - [Hipchat Channel](https://github.com/vikrant-kumar1/mybox1#hipchat-channel)
    - [Jira Integration Channel](https://github.com/vikrant-kumar1/mybox1#jira-integration-channel)
  - [Logging](https://github.com/vikrant-kumar1/mybox1#logging)
    - [Fluentd](https://github.com/vikrant-kumar1/mybox1#emal-channel)
    - [AWSLog Driver](https://github.com/vikrant-kumar1/mybox1#hipchat-channel)
  - [Additional features](https://github.com/vikrant-kumar1/mybox1#additional-features)
    - [High Availability](https://github.com/vikrant-kumar1/mybox1#high-availability)
    - [Persistant Storage](https://github.com/vikrant-kumar1/mybox1#persistant-storage)
    - [Jira Integration](https://github.com/vikrant-kumar1/mybox1#jira-integraion)
- [Release](https://github.com/vikrant-kumar1/mybox1#release)
  - [V1.0](https://github.com/vikrant-kumar1/mybox1#v1.0)
- [Road map](https://github.com/vikrant-kumar1/mybox1#road-map)
- [License](https://github.com/vikrant-kumar1/mybox1#license)
- [Credits](https://github.com/vikrant-kumar1/mybox1#credits)

## Getting Started

To deploy this Monitoring Box you should have AWSCli ,Kubectl on your system. This Monitor Box is designed for Kubernetes and ECS based Miroservice Infrastructure.

## Introduction

Microservices give you flexibility , hyperscale  and room to grow and we observed some highlighted a series of challenges for monitoring cloud-native container-based systems, and introduced microservice monitoring , alerting and visualisation tool, which may assist devops with testing microservice monitoring at scale.

This Code will deploy a monitoring box on AWS-ECS environment using CloudFormation .Through this Box you can Monitor Kubernetes & ECS cluster Monitoring along with Kafka Monitoring ,CouchbaseDB Monitoring , LoadBalancer Monitoring and JVM & Application Monitoring.


##  Deployment

- Infrastructure Deployment
- Service Deployment
- Addons Service Deployment
- Centralized Logging Deployment

## Infrastructure Deployment

- Setup a new VPC with public and private subnets .
- A highly available Monitoring-Box deployed on ECS Cluster across two [Availability Zones] with persitance storage (EBS).
- An Application Load Balancer to the public subnets to handle inbound traffic.
- ALB host based routes for each ECS service to route the inbound traffic to the correct service.
- Centralized Monitoring-Box logging with Amazon CloudWatch Logs .
- Setup SecurityGroup for ALB and ECS Node to manage inbound traffic.

The templates below are included in this repository and reference architecture:

| Template | Description |
| --- | --- |
| [master.yaml](master.yaml) | This is the master template - deploy it to CloudFormation and it includes all of the others automatically. |
| [infrastructure/vpc.yaml](infrastructure/vpc.yaml) | This template deploys a VPC with a pair of public and private subnets spread across two Availability Zones. It deploys an [Internet gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html), with a default route on the public subnets. It deploys a pair of NAT gateways (one in each zone), and default routes for them in the private subnets. |
| [infrastructure/security-groups.yaml](infrastructure/security-groups.yaml) | This template contains the [security groups](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) this will create a SecurityGroup for ALB and one SecurityGroup for ECS Nodes. |
| [infrastructure/load-balancers.yaml](infrastructure/load-balancers.yaml) | This template deploys an ALB to the public subnets, which exposes the various ECS services. It is created in in a separate nested template, so that it can be referenced by all of the other nested templates and so that the various ECS services can register with it. |
| [infrastructure/ecs-cluster.yaml](infrastructure/ecs-cluster.yaml) | This template deploys an ECS cluster to the private subnets using an Auto Scaling group and installs the AWS SSM agent with related policy requirements. |


| Template | Description |
| --- | --- |
| [master.yaml](master.yaml) | This is the master template - deploy it to CloudFormation and it includes all of the others automatically. |
| [infrastructure/vpc.yaml](infrastructure/vpc.yaml) | This template deploys a VPC with a pair of public and private subnets spread across two Availability Zones. It deploys an [Internet gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html), with a default route on the public subnets. It deploys a pair of NAT gateways (one in each zone), and default routes for them in the private subnets. |
| [infrastructure/security-groups.yaml](infrastructure/security-groups.yaml) | This template contains the [security groups](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) this will create a SecurityGroup for ALB and one SecurityGroup for ECS Nodes. |
| [infrastructure/load-balancers.yaml](infrastructure/load-balancers.yaml) | This template deploys an ALB to the public subnets, which exposes the various ECS services. It is created in in a separate nested template, so that it can be referenced by all of the other nested templates and so that the various ECS services can register with it. |
| [infrastructure/ecs-cluster.yaml](infrastructure/ecs-cluster.yaml) | This template deploys an ECS cluster to the private subnets using an Auto Scaling group and installs the AWS SSM agent with related policy requirements. |




## How do I...?

### Get started and deploy monitoring-box into my AWS account

You can launch this CloudFormation stack in your account:

## Build your images and Push to ECR

1. Push your service images to a registry somewhere (e.g., [Amazon ECR](https://aws.amazon.com/ecr/)).
2. Update `Image` in recpective service template .
3. GO to your AWS `CloudFormation` console and run master template.

### Setup centralized container logging

By default, the containers in your ECS tasks/services are already configured to send log information to CloudWatch Logs and retain them for 365 days. Within each service's template (in [services/*](services/)), a LogGroup is created that is named after the CloudFormation stack. All container logs are sent to that CloudWatch Logs log group.

You can view the logs by looking in your [CloudWatch Logs console](https://console.aws.amazon.com/cloudwatch/home?#logs:) (make sure you are in the correct AWS region).

ECS also supports other logging drivers, including `syslog`, `journald`, `splunk`, `gelf`, `json-file`, and `fluentd`. To configure those instead, adjust the service template to use the alternative `LogDriver`. You can also adjust the log retention period from the default 365 days by tweaking the `RetentionInDays` parameter.

For more information, see the [LogConfiguration](http://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_LogConfiguration.html) API operation.

### Change the ECS host instance type

This is specified in the [master.yaml](master.yaml) template.

By default, [t2.large](https://aws.amazon.com/ec2/instance-types/) instances are used, but you can change this by modifying the following section:

```
ECS:
  Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: ...
      Parameters:
        ...
        InstanceType: t2.large
        InstanceCount: 4
        ...
```

### Adjust the Auto Scaling parameters for ECS hosts and services

The Auto Scaling group scaling policy provided by default launches and maintains a cluster of 4 ECS hosts distributed across two Availability Zones (min: 4, max: 4, desired: 4).

It is ***not*** set up to scale automatically based on any policies (CPU, network, time of day, etc.).

If you would like to configure policy or time-based automatic scaling, you can add the [ScalingPolicy](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-as-policy.html) property to the AutoScalingGroup deployed in [infrastructure/ecs-cluster.yaml](infrastructure/ecs-cluster.yaml#L69).

As well as configuring Auto Scaling for the ECS hosts (your pool of compute), you can also configure scaling each individual ECS service. This can be useful if you want to run more instances of each container/task depending on the load or time of day (or a custom CloudWatch metric). To do this, you need to create [AWS::ApplicationAutoScaling::ScalingPolicy](http://docs.aws.amazon.com/pt_br/AWSCloudFormation/latest/UserGuide/aws-resource-applicationautoscaling-scalingpolicy.html) within your service template.

### Deploy multiple environments (e.g., dev, test, pre-production)

Deploy another CloudFormation stack from the same set of templates to create a new environment. The stack name provided when deploying the stack is prefixed to all taggable resources (e.g., EC2 instances, VPCs, etc.) so you can distinguish the different environment resources in the AWS Management Console.

### Change the VPC or subnet IP ranges

This set of templates deploys the following network design:

| Item | CIDR Range | Usable IPs | Description |
| --- | --- | --- | --- |
| VPC | 10.180.0.0/16 | 65,536 | The whole range used for the VPC and all subnets |
| Public Subnet | 10.180.8.0/21 | 2,041 | The public subnet in the first Availability Zone |
| Public Subnet | 10.180.16.0/21 | 2,041 | The public subnet in the second Availability Zone |
| Private Subnet | 10.180.24.0/21 | 2,041 | The private subnet in the first Availability Zone |
| Private Subnet | 10.180.32.0/21 | 2,041 | The private subnet in the second Availability Zone |

You can adjust the CIDR ranges used in this section of the [master.yaml](master.yaml) template:

```
VPC:
  Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: !Sub ${TemplateLocation}/infrastructure/vpc.yaml
      Parameters:
        EnvironmentName:    !Ref AWS::StackName
        VpcCIDR:            10.180.0.0/16
        PublicSubnet1CIDR:  10.180.8.0/21
        PublicSubnet2CIDR:  10.180.16.0/21
        PrivateSubnet1CIDR: 10.180.24.0/21
        PrivateSubnet2CIDR: 10.180.32.0/21
```

### Update an ECS service to a new Docker image version

ECS has the ability to perform rolling upgrades to your ECS services to minimize downtime during deployments. For more information, see [Updating a Service](http://docs.aws.amazon.com/AmazonECS/latest/developerguide/update-service.html).

To update one of your services to a new version, adjust the `Image` parameter in the service template (in [services/*](services/) to point to the new version of your container image. For example, if `1.0.0` was currently deployed and you wanted to update to `1.1.0`, you could update it as follows:

```
TaskDefinition:
  Type: AWS::ECS::TaskDefinition
  Properties:
    ContainerDefinitions:
      - Name: your-container
        Image: registry.example.com/your-container:1.1.0
```

After you've updated the template, update the deployed CloudFormation stack; CloudFormation and ECS handle the rest.

To adjust the rollout parameters (min/max number of tasks/containers to keep in service at any time), you need to configure `DeploymentConfiguration` for the ECS service.

For example:

```
Service:
  Type: AWS::ECS::Service
    Properties:
      ...
      DesiredCount: 4
      DeploymentConfiguration:
        MaximumPercent: 200
        MinimumHealthyPercent: 50
```
