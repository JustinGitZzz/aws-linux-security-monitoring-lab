## AWS EC2 Linux Server Deployment

An Ubuntu Linux server was deployed using Amazon EC2 to provide
the base environment for the security monitoring lab.

### EC2 Configuration

- **Operating System:** Ubuntu Server 26.04 LTS (HVM)
- **AMI:** ami-0e5497a77ef21b5ac
- **Architecture:** x86-64
- **Instance Type:** t3.micro
- **AWS Region:** us-east-2
- **Storage:** 8 GiB EBS General Purpose SSD (gp3)
- **Authentication:** SSH public-key authentication
- **SSH Port:** TCP 22
- **SSH Source:** Restricted to administrator public IP (/32)

### Security Considerations

SSH access was restricted at the AWS Security Group level to
the administrator's public IP address rather than allowing
connections from `0.0.0.0/0`.

Public-key authentication was used for remote administrative
access. The EC2 private key is stored locally outside the Git
repository, and private key file types are excluded through
`.gitignore`.

### Evidence

#### EC2 Instance

![EC2 Instance](screenshots/ec2-deployment/ec2-instance-running.png)

#### Security Group

![Security Group](screenshots/ec2-deployment/security-group-ssh-rule.png)