# AWS Infrastructure as Code

This project contains CloudFormation templates to deploy a secure, scalable web application infrastructure on AWS.

## Architecture

The infrastructure consists of:
- VPC with public and private subnets
- Auto Scaling EC2 instances behind an Application Load Balancer
- RDS MySQL database
- Bastion host for secure access
- HTTPS encryption with SSL certificate
- Route53 DNS records

See [architecture.txt](architecture.txt) for detailed architecture documentation.

## Prerequisites

- AWS CLI configured with appropriate credentials
- AWS Certificate Manager (ACM) certificate in ap-southeast-2 region
- Route53 hosted zone
- S3 bucket for CloudFormation templates

## Deployment

1. Create S3 bucket and upload templates:
```bash
aws s3 mb s3://your-bucket-name
aws s3 cp *.yml s3://your-bucket-name/
```

2. Deploy VPC:
```bash
aws cloudformation deploy \
  --template-file 1.vpc.yml \
  --stack-name vpc-stack \
  --capabilities CAPABILITY_IAM
```

3. Deploy RDS:
```bash
aws cloudformation deploy \
  --template-file 4.RDS.yml \
  --stack-name rds-stack \
  --parameter-overrides \
    DBUsername=admin \
    DBPassword=your-secure-password
```

4. Deploy Bastion Host:
```bash
aws cloudformation deploy \
  --template-file 3.bastionhost.yml \
  --stack-name bastion-stack \
  --capabilities CAPABILITY_IAM
```

5. Deploy Application:
```bash
aws cloudformation deploy \
  --template-file 2.Instance.yml \
  --stack-name app-stack \
  --parameter-overrides \
    CertificateArn=your-acm-certificate-arn
```

## Accessing Resources

- Web Application: https://alb.tonythomasm.com
- RDS Endpoint: rds.tonythomasm.com
- Bastion Host: Use the public IP from SSM Parameter Store (JUMPBOX_IP)

## Security

- All instances are in private subnets
- HTTPS encryption for web traffic
- Bastion host for secure access
- IAM roles with least privilege
- Secrets Manager for sensitive data
- Security Groups with minimal access

## Monitoring

- Health checks for ALB
- CloudWatch metrics for instances
- RDS performance insights

## Cleanup

To delete all resources:
```bash
aws cloudformation delete-stack --stack-name app-stack
aws cloudformation delete-stack --stack-name bastion-stack
aws cloudformation delete-stack --stack-name rds-stack
aws cloudformation delete-stack --stack-name vpc-stack
```

## License

This project is licensed under the MIT License - see the LICENSE file for details.
