# Micoservice Monitoring Box (Kubernetes & ECS)

## To start using Micoservice Monitoring Box

- [Getting Started](https://github.com/vikrant-kumar1/mybox1#getting-started)
  - [Some rules of thumb](https://github.com/vikrant-kumar1/mybox1#some-rules-of-thumb)
  - [Creating a Terminal](https://github.com/vikrant-kumar1/mybox1#creating-a-terminal)
  - [Working with Screens](https://github.com/vikrant-kumar1/mybox1#working-with-screens)
  - [Components](https://github.com/vikrant-kumar1/mybox1#components)
  - [Additional features](https://github.com/vikrant-kumar1/mybox1#additional-features)
    - [Layering](https://github.com/vikrant-kumar1/mybox1#layering)
    - [Input handling](https://github.com/vikrant-kumar1/mybox1#input-handling)
    - [Shape and box drawing](https://github.com/vikrant-kumar1/mybox1#shape-and-box-drawing)
    - [Fonts and tilesets](https://github.com/vikrant-kumar1/mybox1#fonts-and-tilesets)
    - [REXPaint file loading](https://github.com/vikrant-kumar1/mybox1#rexpaint-file-loading)
    - [Color themes](https://github.com/vikrant-kumar1/mybox1#color-themes)
    - [Animations (BETA)](https://github.com/vikrant-kumar1/mybox1#animations-beta)
    - [The API](https://github.com/vikrant-kumar1/mybox1#the-api)
- [A little Crash Course](https://github.com/vikrant-kumar1/mybox1#a-little-crash-course)
  - [Terminal](https://github.com/vikrant-kumar1/mybox1#terminal)
  - [Colors and StyleSets](https://github.com/vikrant-kumar1/mybox1#colors-and-stylesets)
  - [Modifiers](https://github.com/vikrant-kumar1/mybox1#modifiers)
  - [TextImages](https://github.com/vikrant-kumar1/mybox1#textimages)
  - [Screens](https://github.com/vikrant-kumar1/mybox1#screens)
- [Road map](https://github.com/vikrant-kumar1/mybox1#road-map)
- [License](https://github.com/vikrant-kumar1/mybox1#license)
- [Credits](https://github.com/vikrant-kumar1/mybox1#credits)

## Getting Started

- Setup a new VPC with public and private subnets .
- A highly available Monitoring-Box deployed on ECS Cluster across two [Availability Zones] with persitance storage (EBS).
- An Application Load Balancer to the public subnets to handle inbound traffic.
- ALB host based routes for each ECS service to route the inbound traffic to the correct service.
- Centralized Monitoring-Box logging with Amazon CloudWatch Logs .
- Setup SecurityGroup for ALB and ECS Node to manage inbound traffic.

##  Monitoring-Box Infrastructure Deployment

The templates below are included in this repository and reference architecture:

| Template | Description |
| --- | --- |
| [master.yaml](master.yaml) | This is the master template - deploy it to CloudFormation and it includes all of the others automatically. |
| [infrastructure/vpc.yaml](infrastructure/vpc.yaml) | This template deploys a VPC with a pair of public and private subnets spread across two Availability Zones. It deploys an [Internet gateway](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Internet_Gateway.html), with a default route on the public subnets. It deploys a pair of NAT gateways (one in each zone), and default routes for them in the private subnets. |
| [infrastructure/security-groups.yaml](infrastructure/security-groups.yaml) | This template contains the [security groups](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html) this will create a SecurityGroup for ALB and one SecurityGroup for ECS Nodes. |
| [infrastructure/load-balancers.yaml](infrastructure/load-balancers.yaml) | This template deploys an ALB to the public subnets, which exposes the various ECS services. It is created in in a separate nested template, so that it can be referenced by all of the other nested templates and so that the various ECS services can register with it. |
| [infrastructure/ecs-cluster.yaml](infrastructure/ecs-cluster.yaml) | This template deploys an ECS cluster to the private subnets using an Auto Scaling group and installs the AWS SSM agent with related policy requirements. |
| [services/prometheus/service.yaml](services/prometheus/service.yaml) | This is an example of a long-running ECS service that serves a JSON API of products. For the full source for the service, see [services/prometheus/src](services/prometheus/src).|
| [services/grafana/service.yaml](services/grafana/service.yaml) | This is an example of a long-running ECS service that needs to connect to another service (prometheus) via the load-balanced URL. We use an environment variable to pass the prometheus URL to the containers. For the full source for this service, see [services/grafana/src](services/grafana/src). |

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
