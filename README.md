# kubeflow-pipeline-as-AWS-sc-product
The product is build from [aws-quickstart quide](https://github.com/aws-quickstart/quickstart-amazon-eks)

This repository will give you step by step guide for building a service catalog product for kubeflow pipelines.
The pipeline software runs on amazon EKS over separate hardware called system. This ensures that your UI does not suffer from scaling of nodes.

Other tools include
- grafana visualizations over prometheus logs
- bastion host to carry out kubectl commands
- spot handler
- dedicated autoscaling groups for gpu machines
- ALB controller
- cluster autoscaler.
- [optional] Docker registry connectivity by hosts
- EFS provisioner for pod volumes




### Getting started

##### Things to Know.
1. Automatic VPC cidr assignment
2. UI runs on dedicated host
3. kubeflow pipeline works off mysql server on aws.
4. tested for 60000 pods provisioning



To create a provisioned product you will need 2 things.

1. Create a [provisioned product](https://docs.aws.amazon.com/servicecatalog/latest/adminguide/getstarted-product.html) by uploading the [CF file](templates/astro-kf-product.yaml) 
2. Copy the whole library to s3 bucket. Refer bucket and the path in the QSS3BucketName & QSS3KeyPrefix parameter of the CF file.

#### List of resources that will get created are

- VPC with CIDR/16
- 3 Private subnet and 3 public subnet
- 7 ASG groups with mostly spot instances
   * System ASG
   * bastion Asg
   * GPU groups 1,2,4,8
   * general node groups (on spot)
- RDS (mysql server)
- 2 Loadbalancer
   * pipeline LB (note: this lb is open to all, recommended to add your own ip to Ingress in [kubeflow installation](templates/astro-install-kubeflow.yaml))
   * grafana
- EFS mount for pod volumes


#### Known issues

1. if you get an error in VPCStack `Template error: Fn::Select cannot select nonexistent value at index 2`
   - Solution: need atleast 3 availability zones.
   

#### NOTE

It take upto 40 - 50 minutes to provision the entire stack for the first time.
   
   
