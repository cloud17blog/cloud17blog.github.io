---
layout: post
title:  "Azure Hints and Tips: Application Gateway Configuration with Multiple Backend Pools"
date:   2024-07-08 14:32:21 +0100
modified_date: 2024-07-08 14:32:21 +0100
categories: blog hints-and-tips
---

## Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Step 1: Create an Application Gateway with Initial Backend Pool and Listener](#step-1-create-an-application-gateway-with-initial-backend-pool-and-listener)
- [Step 2: Add Additional Backend Pools and Listeners](#step-2-add-additional-backend-pools-and-listeners)
- [Extracting the SSL Certificate from an Existing Virtual Machine Web Server](#extracting-the-ssl-certificate-from-an-existing-virtual-machine-web-server)

## Introduction

When you are working with Azure Application Gateway, you may need to configure multiple backend pools to route traffic to different backend services, for example to web apps hosted on different VMs or App Services etc.

This quick guide will show you how to configure an Application Gateway with multiple backend pools and listeners, including using separate SL certificates for each listener. Using separate certificates is not necessarily the best practice for all scenarios, as a wildcard certificate can be used to secure multiple subdomains, but if that is not possible due to different domain names being used for each backend service, or, as in this case, simply because this is how the customer does things, then this guide will give a high-level overview of how to configure it!

## Prerequisites

- An Azure account
- An Azure VNet with a subnet dedicated to the Application Gateway
- Backend services that you want to route traffic to, such as a VM, Azure Function, or App Service
- SSL certificates for each backend service (see [below](#extracting-the-ssl-certificate-from-an-existing-virtual-machine-web-server) for how to extract an SSL certificate from an existing web server)
- A domain name and access to the DNS records

## Step 1: Create an Application Gateway with Initial Backend Pool and Listener

In the Azure portal, follow the basic steps to create a new Application Gateway:

1. In the Basics tab, select the subscription, resource group, name and region, and choose a **Standard V2** tier. If this is a training, PoC or other non-Production environment, you can remove the autoscaling configuration and reduce the instance count to 1:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-1.png)

1. In the Frontends tab, create a new frontend IP address for the Frontends configuration:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-2-pip.png)

1. In the Backends tab, create a new backend pool and add the backend service you want to route traffic to (e.g. the private IP address of a virtual machine):
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-3-be-pool.png)
<br />&nbsp;<br />
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-3-be-pool2.png)

1. In the Configuration tab, enter details for a new Listener, ensuring to use the "Multi site" type and add the domain name you want to use for this backend service:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-4-routing-listener.png)
<br />&nbsp;<br /><br />NOTE: Not setting the type to "Multi site" will restrict you to only being able to set up a single listener per incoming port, which in turn makes it not possible to route to different hosted domains with individual SSL certificates.
<br /><br />&nbsp;<br />

1. In the "Backend targets" tab, select the backend pool you created in the previous step and create a new "Backend Settings" entry:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-4-routing-target.png)
<br />&nbsp;<br />
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-4-routing-setting.png)

1. In the final tab, Review + Create, review the configuration and click Create to deploy the Application Gateway:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-5-create.png)

1. Finally, add a DNS record in your domain admin portal for the domain name you used in the listener configuration. The exact steps will vary depending on your DNS provider, but you will need to create an A record that points to the public IP address of the Application Gateway. (If a DNS record already exists for the web server, i.e. because it was previously hosted on a VM with a public IP address, you can simply update the record to point to the Application Gateway's public IP address instead):
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-5-dns.png)

## Step 2: Add Additional Backend Pools and Listeners

1. Once the Application Gateway is deployed, navigate to the Backend pools tab and add a new backend pool for the second backend service you want to route traffic to:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-6-add-be-pool2.png)
<br />&nbsp;<br />
<br />You should now see two backend pools listed:<br />
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-7-add-be-pool2.png)

1. You can then optionally add a new backend settings configuration, although the settings created above are likely to be suitable for the new backend pool:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-7-add-settings2.png)

1. Next, create a new Health probe for the new backend pool; this enables the Application Gateway to check the health of the backend service to ensure that traffic is only routed to healthy instances. Pick the new or original backend settings configuration as required:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-8-add-healthprobe2.png)

1. In the Listeners tab, add a new listener for the second backend service, ensuring to use the "Multi site" type and add the domain name you want to use for this backend service. Again you will need to supply the relevant SSL certificate for the domain:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-9-add-listener-2.png)

1. Finally, add the routing rules that will direct traffic to the correct backend pool based on the domain name in the request:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-10-add-routing-2-listener.png)
<br />&nbsp;<br />
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-10-add-routing-2-backends.png)

1. Once you have added the new backend pool and listener, you can check that the backend health is OK for both backend pools:
![Image](../../../../../assets/images/blog/2024/07/08/azure-app-gateway/agw-config-11-backend-health.png)


## Extracting the SSL Certificate from an Existing Virtual Machine Web Server

If the backend service is a virtual machine running Apache (as per the instructions [here](../../../../hints-and-tips/2024/07/08/azure-apache-webserver.html)) or Nginx, you can extract the SSL certificate from the server using the following steps:

1. SSH into the virtual machine
2. Run the following command to extract the SSL certificate and private key:

```bash
sudo openssl pkcs12 -export -out www1-example-com.pfx -inkey /etc/letsencrypt/live/www1.example.com/privkey.pem -in /etc/letsencrypt/live/www1.example.com/cert.pem -certfile /etc/letsencrypt/live/www1.example.com/chain.pem
```

3. Copy the `www1-example-com.pfx` file to your local machine:

- If you have SSH access to the VM, you can use `scp` to copy the file to your local machine
- First, make the file readable by the current user:

```bash
sudo chmod +r www1-example-com.pfx
```

... then, from your local machine, use SCP to pull the file down:

```bash
scp username@xxx.xxx.xxx.xxx://home/username/www1-example-com.pfx www1-example-com.pfx
```

where values for username and IP address are those of the VM.
