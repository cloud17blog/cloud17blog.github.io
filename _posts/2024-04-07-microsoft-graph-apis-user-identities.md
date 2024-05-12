---
layout: post
title:  "User Identities in Microsoft Entra Id"
date:   2024-04-07 20:42:32 +0100
categories: blog article
---

## Contents

- [Introduction](#introduction)
- [Users and Service Principals](#users-and-service-principals)
- [Enterprise Applications and App Registrations](#enterprise-applications-and-app-registrations)
  - [App Registrations: The Global Blueprint](#app-registrations-the-global-blueprint)
  - [Enterprise Applications: The Local Implementation](#enterprise-applications-the-local-implementation)
- [App Registration Secrets and Certificates](#app-registration-secrets-and-certificates)
- [Further Information](#further-information)
{:.toc}

## Introduction

In an upcoming blog article, I will explain the use of Microsoft Graph
APIs to query and interact with Microsoft services and data.
That article will provide an overview of the different Graph APIs available,
including the Azure AD Graph API and the Microsoft Graph API.
It will also touch on the different user identities and permission models
used to access resources within the Microsoft Enterprise environment.

As a precursor to that article, it is important to understand the different
types of user identities and service principals that can access Azure
and other Microsoft resources, especially Enterprise Applications and
App Registrations.

### Users and Service Principals

There are three types of identity within Entra Id that can access Azure
and other Microsoft resources:

- **Users**: Individual accounts typically associated with people.
This is the most direct and common way users interact with resources
within Azure and Entra Id.

- **Managed Identities**: A type of service principal that is automatically
managed by Azure AD and is used to authenticate to services that support
Azure AD authentication. These are mainly used to give a specific service or
resource in Azure an identity to authenticate against other resources with.

- **Enterprise Application**: A type of service principal manually created
by an administrator that represents an application or service within Azure
AD. It acts as an identity for the application or service to authenticate
and access resources within Azure AD.<br>
Enterprise Applications are the most common type of service principal
used in Azure AD, particularly in automated or unattended scenarios,
providing a secure and controlled way to manage access and permissions for
various applications and services.

### Enterprise Applications and App Registrations

Before looking at the two permission models that these can be authorised via,
we need to understand a bit more about the *Enterprise Applications* and
the role they play in the Entra Id permissions model, and in particular
their relationship to the similar looking *App Registrations* also seen in
the tenant.

#### App Registrations: The Global Blueprint

![Image](../../../../../assets/images/blog/2024/04/07/microsoft-graph-apis/image10-app-registrations.png)

To summarise as briefly as possible, an *App Registration* is the *global*
representation of an application as seen from the application
developer’s perspective, where by *global* we mean that it can be
published into a single tenant, can be “multi-tenant” such that *any*
Microsoft “work or school” account users can access it, or even be
extended to allow Microsoft personal accounts to access it too.

It is effectively a blueprint for how the application will authenticate
and what permissions it requests. Further, and perhaps of most significance
to most Azure users, it can be configured with an application certificate or
secret that can subsequently be used in a similar manner to a password,
allowing users in possession of it to effectively log into the
Entra Id directory via the app and to then access further resources via
or within it.

Simple applications acting just as an authentication and authorisation
entry-point can be created via scripting or via the portal and added
directly to a tenant (as is commonly done to create a "Service Principal"
with which to run unattended or automated tasks with), but more
complex ones designed for multi-tenant deployment and with significant
additional functionality of their own can be published to the Microsoft
Entra Gallery and made available just your organisation or made available
to the wider public too.

Microsoft Entra Gallery Apps:

![Image](../../../../../assets/images/blog/2024/04/07/microsoft-graph-apis/image2.png)

#### Enterprise Applications: The Local Implementation

![Image](../../../../../assets/images/blog/2024/04/07/microsoft-graph-apis/image11-enterprise-applications.png)

Conversely, an *Enterprise Application* represents an *instance* of the
App Registration in a *given tenant*, and is used for management and
security within that. It contains settings such as who in that tenant
can use or access the application, necessary conditional access policies
and other security-related configurations that are applied tenant-wide.
Essentially, it's the application from the perspective of an
administrator managing how the application is used within *their
organisation*:

![Image](../../../../../assets/images/blog/2024/04/07/microsoft-graph-apis/image13-app-reg-enterprise-app.png)

This design enables a separation of concerns where app developers focus
on how the app is *built*, including its authentication flow and
required permissions, and administrators focus on managing how the app
is *deployed* and who can access it.

### App Registration Secrets and Certificates

When you create an App Registration, you can create a secret or certificate with which
to authenticate the application to the Entra Id directory.

![Image](../../../../../assets/images/blog/2024/04/07/microsoft-graph-apis/image12-app-registration-secrets.png)

At first glance, it may appear contradictory that **secrets and certificates**
are created for the **App Registration** rather than the **Enterprise Application**,
but when you consider that the App Registration is used to define the allowed
access to resources in the tenant that it is created in
*potentially from any other tenant* or even from the wider public via REST
API calls, it makes sense!

For convenience, when you create an App Registration directly in an Entra
Id directory, an Enterprise Application is also automatically created,
as is the Service Principal Identity object that is used to perform the
actual authentication.
{:.text-box}

### Further Information

App Registrations:
<https://learn.microsoft.com/en-us/entra/identity-platform/quickstart-register-app>

Enterprise Applications: <https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/add-application-portal>

User Consent: <https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/configure-user-consent>
