---
layout: post
title:  "Azure Hints and Tips: Quick Web Server with Apache and LetsEncrypt"
date:   2024-07-08 14:32:21 +0100
modified_date: 2024-07-08 14:32:21 +0100
categories: blog hints-and-tips
---

## Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Step 1: Create a Virtual Machine](#step-1-create-a-virtual-machine)
- [Step 2: Create DNS Record](#step-2-create-dns-record)
- [Step 3: Install Apache](#step-3-install-apache)
- [Step 4: Create a Virtual Host and Deploy a Website](#step-4-create-a-virtual-host-and-deploy-a-website)
- [Step 5: Create a LetsEncrypt SSL Certificate](#step-5-create-a-letsencrypt-ssl-certificate)

## Introduction

Sometimes when you are working with Azure, you need to quickly set up a web server to host a simple website in order to test other services. This guide will show you how to set up a web server using Apache and secure it with a free SSL certificate from LetsEncrypt using your own custom domain.

## Prerequisites

- An Azure account
- A domain name and access to the DNS records
- An Azure VNet with a subnet and a Network Security Group (NSG) that allows HTTP and HTTPS traffic requests from the internet and SSH access from your IP address.

## Step 1: Create a Virtual Machine

1. In the Azure portal, follow the basic steps to create a new Linux virtual machine with a public IP address. Since this will be a short-lived test VM, we can simplify the configuration as much as possible:

- In Availability Options, select No infrastructure redundancy required.
- In Security Type, select Standard
- In Authentication Type, select Password and provide a username and password
- In Public inbound ports, select Allow selected ports and choose SSH from the list
- In Networking, select the VNet and subnet you want to use and ensure you have an NSG attached that allows SSH and HTTP/HTTPS traffic as per the prerequisites

1. Once the VM is created, note the public IP address and SSH into the VM using the username and password you provided.

## Step 2: Create DNS Record

1. In your domain registrar's DNS settings, create an A record that points to the public IP address of the VM you created in Step 1 for the domain you want to use to connect to this web server, e.g. www1.example.com.

- It is worth completing this step as soon as you have the public IP address of the server as it can take some time for the DNS record to propagate.

## Step 3: Install Apache

1. Update the package list and install Apache:

    ```bash
    sudo apt update
    sudo apt install apache2
    ```

1. Start the Apache service and enable it to start on boot (not required for VMs deployed via the Azure portal as the service appears to be started automatically):

    ```bash
    sudo systemctl start apache2
    sudo systemctl enable apache2
    ```

1. Test that Apache is running by navigating to the public IP address of the VM in a web browser. You should see the default Apache page:

    ![Image](../../../../../assets/images/blog/2024/07/08/azure-vm/default-apache2-page.png)

## Step 4: Create a Virtual Host and Deploy a Website

1. Create a new directory for your website and set the permissions:

    ```bash
    sudo mkdir /var/www/www1.example.com
    sudo chown -R www-data:www-data /var/www/www1.example.com
    sudo chmod -R 755 /var/www/www1.example.com
    ```

1. Create a simple HTML file to test the website:

    ```bash
    echo "<html><head><title>www1.example.com</title></head><body><h1>Welcome to www1.example.com</h1></body></html>" > /var/www/www1.example.com/index.html
    ```

1. Create a new virtual host configuration file:

    ```bash
    sudo vi /etc/apache2/sites-available/www1.example.com.conf

    # Copy the following code into the file, then save and exit: 

    <VirtualHost *:80>
        ServerName www1.example.com
        # Server Alias is not required if setting up a sub domain www1.example.com
        # but it would be used to automatically redirect www.example.com to example.com, for example!
        # ServerAlias www.example.com
        DocumentRoot /var/www/www1.example.com

        ErrorLog ${APACHE_LOG_DIR}/example.com_error.log
        CustomLog ${APACHE_LOG_DIR}/example.com_access.log combined
    </VirtualHost>
    ```

1. Disable the default website and enable the virtual host for the new website and restart Apache:

    ```bash
    sudo a2dissite 000-default.conf
    sudo a2ensite www1.example.com.conf
    sudo systemctl restart apache2
    ```

1. Check that the website is accessible by navigating to http://www1.example.com in a web browser. You should see the simple HTML page you created above.

## Step 5: Create a LetsEncrypt SSL Certificate

1. Install Certbot:

    ```bash
    sudo apt install certbot python3-certbot-apache
    ```

1. Obtain a certificate for the domain:

    ```bash
    sudo certbot --apache -d www1.example.com
    ```

1. Follow the prompts to create the certificate. Once complete, Certbot will update the Apache configuration to use the certificate.

1. Test that the website is now accessible via HTTPS by navigating to <https://www1.example.com> in a web browser. You should see the same simple HTML page you created above but served via HTTPS.
