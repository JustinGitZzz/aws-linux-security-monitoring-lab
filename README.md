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

## SSH Server Hardening

• Reviewed the effective OpenSSH configuration
• Backed up the existing configuration before modification
• Disabled password-based SSH authentication
• Disabled direct root SSH login
• Explicitly retained public-key authentication
• Validated the configuration before reloading SSH
• Verified key-based access from a second SSH session
• Tested that password authentication and root login were rejected

```text
PasswordAuthentication no
```

If SSH accepts passwords, an attacker can repeatedly try username/password combinations against the server. Disabling password authentication removes that login method, so knowing or guessing the account's password isn't enough to SSH into the server. Your server instead relies on possession of the appropriate private key.

```text
PermitRootLogin no
```

`root` has essentially unrestricted administrative control over a Linux system. Disabling direct root SSH login means someone cannot remotely authenticate directly as `root`. You instead log in using a normal account such as `ubuntu` and elevate privileges with `sudo` when necessary.

```text
PubkeyAuthentication yes
```

The EC2 server accepts SSH public-key authentication. The server has the authorized public key, while you keep the corresponding private `.pem` key on your computer. When you connect, SSH uses cryptography to prove you possess the private key without transmitting the private key to the server.

This setup essentially changes remote administration from:

Internet
   ↓
SSH
   ↓
Username + password ❌
Direct root login   ❌

to:

Your authorized IP (/32)
          ↓
       TCP 22
          ↓
         SSH
          ↓
Public-key authentication ✅
          ↓
     ubuntu account
          ↓
     sudo when needed