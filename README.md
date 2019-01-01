# Confluence on EC2 Micro Free Tier

## Create the EC2 instance

1. Create an AWS account at http://aws.amazon.com/
1. Launch the AWS EC2 Management Console at https://console.aws.amazon.com/ec2/v2/home
1. Click on `Instances`
1. Click on `Launch Instance`
1. Click on `Select` for the Amazon Linux AMI SSD Volume Type (Select
64-bit (x86) Amazon Linux AMI 2018.03.0 (HVM), SSD Volume Type - ami-01e24be29428c15b2)
1. On the `Instance Type` screen, select the `t2.micro` type and click `Next: Configure Instance Details`

## Security Groups

7. Create a new security group Called "Confluence" by adding the necessary rules:

| Type            | Protocol | Port Range  | Source    |
| --------------- | -------- | -----------:| --------- |
| HTTP            | TCP      | 80          | 0.0.0.0/0 |
| HTTPS           | TCP      | 443         | 0.0.0.0/0 |
| SSH             | TCP      | 22          | 0.0.0.0/0 |
| Custom TCP Rule | TCP      | 8005        | 0.0.0.0/0 |
| Custom TCP Rule | TCP      | 8090        | 0.0.0.0/0 |
| Custom TCP Rule | TCP      | 8443        | 0.0.0.0/0 |

## Launch EC2 instance

8. Click the `Launch` button
1. Create a new key pair named `ec2-confluence`
1. Download the key pair file `ec2-confluence.pem` and save it somewhere secure
1. Click `Launch Instance`
1. Click `View Instances`
1. Select the newly created instance and grab it's Public IP

## Connect to and setup your EC2 instance

12. From a terminal, ssh in to your machine using the command: 

```sh
ssh -i /path/to/ec2-confluence.pem ubuntu@<IP ADDRESS>
```

13. Run `sudo yum update` after your initial login, and update the software
1. Create a swap file:

```sh
[ec2-user@ip ~]$ sudo dd if=/dev/zero of=/var/swapfile bs=1M count=2048
[ec2-user@ip ~]$ sudo chmod 600 /var/swapfile
[ec2-user@ip ~]$ sudo mkswap /var/swapfile
[ec2-user@ip ~]$ echo /var/swapfile none swap defaults 0 0 | sudo tee -a /etc/fstab
[ec2-user@ip ~]$ sudo swapon -a
```

15. Reboot the EC2 instance to ensure the swap is in use:

```sh
[ec2-user@ip ~] sudo reboot now
```

Reconnect to the machine via ssh using the command from step 12. The instance may not be immediately available as it reboots.

## Download and install Confluence

During this tutorial, the latest version of Confluence is 6.13.0 and is available at `https://www.atlassian.com/software/confluence/downloads/binary/atlassian-confluence-5.10.0-x64.bin`. To find the URL for the latest version of Confluence, see the download page at `https://www.atlassian.com/software/confluence/download`, select Linux, and copy the `Trial download` destination link for the 64bit installer.

16. Download Confluence to a downloads directory, and execute the installer

```sh
[ec2-user@ip ~]$ mkdir downloads; cd downloads
[ec2-user@ip ~]$ wget -O confluence.bin https://www.atlassian.com/software/confluence/downloads/binary/atlassian-confluence-6.13.0-x64.bin
[ec2-user@ip ~]$ chmod u+x confluence.bin
[ec2-user@ip ~]$ sudo ./confluence.bin
```

17. Run through the Confluence setup wizard, default/express install is fine
1. Add the hostname to the hosts file so Confluence can reliably configure the local database, followed by a last reboot of the machine
```sh
[ec2-user@ip ~]$ sudo echo `cat /etc/hosts` $HOSTNAME > /etc/hosts
[ec2-user@ip ~]$ sudo reboot now
```

19. Once the machine has rebooted, you will be able to start configuring COnfluence via the web based setup wizard by via the IP address you got at step 13 using `http://<IP ADDRESS>:8090/`. The intial setup of Confluence can take a long time - this process can take well over 10 minutes. Be patient and allow it to complete loading.

## Notes

If you are connecting Confluence for using Jira authentication; you will need to configure the User Group to use jira-software-users since jira-users does not exist by default.
