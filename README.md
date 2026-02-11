# GoPhish lab
A hands-on phishing simulation lab built on Azure using GoPhish and Postfix. Demonstrates end-to-end phishing email delivery in an isolated cloud environment for cybersecurity training and awareness testing.
# Phishing Simulation Lab - Azure Environment
Overview:
This project sets up two Azure VMs:
  **Attacker VM**: Running GoPhish for creating and sending phishing campaigns
  
 **Target VM**: Running Postfix mail server to receive and store phishing emails
 
Azure Setup Screenshots:

**Resource Group Creation**:
  Creating the Azure Resource Group (gophish-rg) in East US region to contain all lab resources

**Virtual Network Setup**:
  Configuring the virtual network (gophish-vnet) to enable communication between attacker and
target VMs

**Network Security Group (NSG)**:
  Creating and configuring the Network Security Group (lab-nsg) with restricted SSH access for enhanced security

**Inbound Security Rule**:
  Adding SSH inbound rule restricted to my public IP address (CIDR /32) - following security best practices by limiting attack surface

**Attacker VM Creation**:
  Deploying the GoPhish attacker VM (Ubuntu 24.04 LTS, Standard B1s) with SSH key authentication

**Target VM Creation**:
  Deploying the target VM that will run Postfix mail server to receive phishing test emails

# GoPhish Setup Screenshots
SSH and GoPhish Installation
  SSH session into Azure attacker VM and starting GoPhish service
  Creating SSH tunnel (port 3333) to securely access GoPhish web UI from local machine
  GoPhish admin interface accessible at https://localhost:3333

**GoPhish Initial Login**:
  First login to GoPhish web UI - default credentials are shown on first startup in the terminal
  
**Mail Server Setup Screenshots**:
  Target VM SSH Session:
    SSH connection to the target VM (gophish-target) for Postfix mail server configuration


**Postfix Configuration**:
  Configuring Postfix to listen on all interfaces and accept mail from the virtual network subnet
  Key settings: inet_interfaces=all, mynetworks includes Azure subnet, local_recipient_maps disabled for testing


**Connectivity Test**:
  Successfully testing SMTP connectivity from attacker VM to target VM on port 25 using netcat
  Connection established - mail server is ready to receive phishing emails

# Email Campaign Screenshots:
Sending Profile Configuration:
  Configuring GoPhish sending profile to use the target Postfix server
    SMTP Server: Target VM's private IP (172.16.0.5:25)
    From Address: support@lab.local (spoofed sender)
    No authentication required (internal network)

**Test Email Setup**:
  Creating test recipient (John Doe - johndoe@target.local) and sending first phishing email through GoPhish

**Email Delivery Success**:
  Successful email delivery confirmation! Email received in /var/mail/johndoe on the target VM

**Viewing Received Email**:
  Checking the mailbox on target VM using 'sudo cat /var/mail/johndoe' - phishing email successfully delivered from support@lab.local

# Security Considerations
This is an isolated lab environment - follow these practices:
  1. **SSH Access**: Restricted to specific IP addresses only (not open to the internet)
  2. **Mail Server**: Configured to accept mail only from the virtual network subnet
  3. **Temporary Setup**: VMs should be deleted after testing to avoid unnecessary costs
  4. **No Real Credentials**: Never use real email addresses or credentials in testing
  5. **Isolated Networks**: All testing confined to Azure virtual network

**Never use this setup to send phishing emails to real people or organizations**

# What This Lab Demonstrates
✅ **Azure Cloud Infrastructure**: Resource groups, virtual networks, NSGs, and VM deployment  
✅ **Network Security**: Properly configured firewall rules and IP restrictions  
✅ **Mail Server Administration**: Postfix installation and SMTP configuration  
✅ **Phishing Platform**: GoPhish setup with sending profiles and campaign management  
✅ **Email Delivery**: End-to-end SMTP communication between isolated VMs  
✅ **Security Best Practices**: SSH key authentication, IP whitelisting, isolated lab environment  

## Skills Developed
- Cloud infrastructure deployment (Microsoft Azure)
- Linux system administration
- Mail server configuration (Postfix)
- Network security and firewall management
- Phishing simulation and awareness training
- SSH tunneling and remote access
