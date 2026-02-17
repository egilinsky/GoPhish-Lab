
1. Create a Resource Group
		Resource group name: gophish-rg
		Region: East US
	![[Screenshot 2026-02-10 at 7.47.24 PM.png]]
2. Create a Virtual Network
		Virtual network: gophish-vnet
	![[Screenshot 2026-02-10 at 7.48.17 PM.png]]
3. Create a Network Security Group (NSG) and restrict management ports
		Network security group: lab-nsg
	![[Screenshot 2026-02-10 at 7.49.28 PM.png]]
4. Add Inbound security rules in NSG
Add
Source: IP Addresses
Source IP addresses: Put the machine's ip that you will be using to ssh into your vms
Source port ranges: *
Destination: Any
Service: SSH
Destination port ranges: 22
Protocol: TCP
Priority: 300![[Screenshot 2026-02-10 at 7.57.26 PM.png]]
5. Create the Attacker VM (Marketplace)
Create: Virtual Machine
Resource Group: gophish-rg
Virtual Machine Name: gophish-attacker
Region: East US or wherever you are located
Image: Ubuntu Server 24.04 LTS - Gen 2
Location: EAST US or where you are located
Size: Standard B1s
Authentication Type: SSH public key
Username: azureuser
SSH public key source: Generate new SSH key pair
Review + Create

**Move SSH KEY**
	Move the downloaded key into ~/.ssh and rename it
	mv ~/Downloads/gophish-server_key.pem ~/.ssh/gophish-server_key.pem
	Restrict permissions so only you can read it
		chmod 600 ~./ssh/gophish-server_key.pem
		
**SSH into Azure Attacker VM**
	`ssh -i ~/.ssh/gophish-server_key.pem azureuser@gophish-attacker-ip

**Set up Azure Attacker**
	sudo apt update
	sudo apt install wget unzip -y
	`wget https://github.com/gophish/gophish/releases/download/v0.12.1/gophish-v0.12.1-linux-64bit.zip`
	`unzip gophish-v0.12.1-linux-64bit.zip`
	`sudo chmod +x gophish
	`sudo ./gophish

**Create SSH Tunnel for Web UI (Admin Access)**
In a new terminal window
	On your mac (not SSH session)
		ssh -i ~/.ssh/gophish-server_key.pem -L 3333:localhost:3333 azureuser@20.110.89.211
		
**Start GoPhish on the VM**
`cd /home/azureuser
`sudo ./gophish

		
Go to GoPhish Admin UI
	https://127.0.0.1:3333
	![[Screenshot 2025-10-18 at 1.26.54 PM.png]]
Caption:
`Top Right: SSH into the Azure VM and start gophish`
`Bottom Right: Creating an SSH tunnel to let me use GoPhish UI
Left: GoPhish WebUI
![[Screenshot 2026-02-09 at 9.22.39 PM.png]]

1. Create the Victim VM 
![[Screenshot 2025-11-06 at 11.52.22 PM.png]]

**SSH into Azure Target VM**
	`ssh -i ~/.ssh/gophish-server_key.pem azureuser@20.81.145.52
![[Screenshot 2026-02-09 at 9.28.19 PM.png]]

In Target VM shell:
`sudo apt update && sudo apt upgrade -y`
`sudo apt install postfix -y`
`sudo postconf -e 'inet_interfaces = all`
Use only in your isolated lab:
`sudo postconf -e 'local_recipient_maps ='
``sudo postconf -e 'mynetworks = 10.0.0.0/8, 127.0.0.0/8'
`sudo postconf -e 'mydestination = $myhostname, localhost.$mydomain, localhost'`
set $myhostname to target and $mydomain to target.local

`sudo systemctl start posfix
`sudo systemctl enable postfix`
Verify Postfix is ready:
`sudo ss -tlnp | grep :25
To restart Postfix:
`sudo systemctl restart postfix`

![[Screenshot 2026-02-10 at 7.18.24 PM.png]]

Test connectivity from attacker VM:
`nc -zv target-private-ip 25`
![[Screenshot 2026-02-10 at 7.28.23 PM.png]]

Create John Doe User Account on Target VM:
`sudo useradd -m johndoe`
`sudo passwd johndoe

Sending First Relay Email:
	Left sidebar --> Sending Profiles --> New Profile
Name: Target Postfix
SMTP From: support@lab.local
Host:  gophish-target-private-ip:25
Username/Password: blank
Ignore Certs
Email Headers: support@lab.local
![[Screenshot 2026-02-10 at 7.29.43 PM.png]]
Send Test Email
First Name: John (fake)
Last Name: Doe (fake)
Email: johndoe@target.local
Position: Employee (fake)
Send
![[Screenshot 2026-02-10 at 7.32.16 PM.png]]
![[Screenshot 2026-02-10 at 7.36.01 PM.png]]

Check mailbox on target-VM:
1st Method: Check the mailbox file directly
`sudo cat /var/mail/johndoe`
2nd Method: Check the mail logs
`sudo tail -50 /var/log/mail.log`
3rd Method: Check as the user
`sudo su - johndoe`
`sudo apt install mailutils`
`mail
4th Method: List all mailboxes
`sudo ls -lh /var/mail/`
![[Screenshot 2026-02-10 at 7.42.53 PM.png]]

What I have accomplished:
1. Configured Azure VMs with proper networking
2. Installed and configured Postfix mail server
3. Set up Gophish sending profiles
4. Successfully delivered a phishing test email
5. Verified email delivery in the mailbox

Going ahead!
