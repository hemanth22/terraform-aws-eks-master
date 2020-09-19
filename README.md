# Terraform AWS EKS Module

Terraform configuration to create an AWS EKS cluster

Adapted from https://github.com/terraform-providers/terraform-provider-aws

For details, see https://www.terraform.io/docs/providers/aws/guides/eks-getting-started.html

Configures an AWS account with the following resources:

* EKS Kubernetes API server
* VPC with:
  * "10.0.0.0/16" CIDR block
  * 2 subnets, each in a different availability zone with:
    * "10.0.x.0/24" CIDR block
  * Internet gateway
* EC2 autoscaling group for worker nodes, with:
  * 1-2 m4.large instances
  * 2 subnets

## Variables

### Required

#### account_id (string)

The AWS account ID that should be used to automatically map IAM users into the Kubernetes cluster

### Optional

#### cluster_name (string)

The Kubernetes cluster name (defaults to "cluster")

#### availability_zones (list)

List of exactly 2 availability zones in which
to create the cluster (defaults to ["us-east-1a", "us-east-1b"])

## Outputs

#### config_map_aws_auth (string)

Kubernetes config map contents in YAML format, used to configure cluster authentication for users and worker nodes

#### kubeconfig (string)

Kubeconfig file contents, used to configure cluster access for Kubernetes client libraries


## Getting started

1. Apply the configuration:
```
$ terraform apply
```

2. Create a kubeconfig file:
```
$ terraform output kubeconfig > kubeconfig
```

3. Create and apply the config map:
```
$ terraform output config_map_aws_auth > config_map_aws_auth.yaml && \
KUBECONFIG=kubeconfig kubectl apply -f config_map_aws_auth.yaml
```

4. Verify that the worker nodes have joined the cluster:
```
KUBECONFIG=kubeconfig kubectl get nodes
```
