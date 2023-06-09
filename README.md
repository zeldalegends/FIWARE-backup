[![FIWARE Banner](https://fiware.github.io/tutorials.PEP-Proxy/img/fiware.png)](https://www.fiware.org/developers)
[![ENG Logo](https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/Engineering_logo.png/320px-Engineering_logo.png)](https://www.eng.it)
[![BD4NRG Logo](https://www.bd4nrg.eu/themes/custom/bd4nrg/logo.png)](https://www.bd4nrg.eu)
[![HYPERRIDE Logo](https://hyperride.eu/wp-content/uploads/2021/03/HYPERRIDE_4C_transp.png)](https://hyperride.eu)

[![FIWARE Security](https://nexus.lab.fiware.org/repository/raw/public/badges/chapters/security.svg)](https://github.com/FIWARE/catalogue/blob/master/security/README.md)
[![License: MIT](https://img.shields.io/github/license/fiware/tutorials.PEP-Proxy.svg)](https://opensource.org/licenses/MIT)
[![Support badge](https://img.shields.io/badge/tag-fiware-orange.svg?logo=stackoverflow)](https://stackoverflow.com/questions/tagged/fiware)
<br/> [![Documentation](https://img.shields.io/readthedocs/fiware-tutorials.svg)](https://fiware-tutorials.rtfd.io)

This repository contains the FIWARE stack used by ENG for EU Projects. It uses the FIWARE [Wilma](https://fiware-pep-proxy.rtfd.io/) PEP Proxy combined with **Keyrock** to secure
access to endpoints exposed by FIWARE generic enablers. Users (or other actors) must log-in and use a token to gain
access to services. The application code created in the
[FIWARE tutorial](https://github.com/FIWARE/tutorials.Securing-Access) is expanded to authenticate users throughout a
distributed system. The design of FIWARE Wilma - a PEP Proxy is discussed, and the parts of the Keyrock GUI and REST API
relevant to authenticating other services are described in detail.

[cUrl](https://ec.haxx.se/) commands are used throughout to access the **Keyrock** and **Wilma** REST APIs -
[Postman documentation](https://fiware.github.io/tutorials.PEP-Proxy/) for these calls is also available.

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/6b143a6b3ad8bcba69cf)
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/engsep/FIWARE/tree/pre-production)

## Contents

<details>
<summary><strong>Details</strong></summary>

-   [Securing Microservices with a PEP Proxy](#securing-microservices-with-a-pep-proxy)
    -   [Standard Concepts of Identity Management](#standard-concepts-of-identity-management)
    -   [:arrow_forward: Video : Introduction to Wilma PEP Proxy](#arrow_forward-video--introduction-to-wilma-pep-proxy)
-   [Prerequisites](#prerequisites)
    -   [Docker](#docker)
    -   [Cygwin](#cygwin)
-   [Architecture](#architecture)
-   [Start Up](#start-up)
    -   [Dramatis Personae](#dramatis-personae)
    -   [Logging In to Keyrock using the REST API](#logging-in-to-keyrock-using-the-rest-api)
        -   [Create Token with Password](#create-token-with-password)
        -   [Get Token Info](#get-token-info)
-   [Managing PEP Proxies and IoT Agents](#managing-pep-proxies-and-iot-agents)
    -   [:arrow_forward: Video : Wilma PEP Proxy Configuration](#arrow_forward-video--wilma-pep-proxy-configuration)
    -   [Managing PEP Proxies and IoT Agents - Start Up](#managing-pep-proxies-and-iot-agents---start-up)
    -   [PEP Proxy CRUD Actions](#pep-proxy-crud-actions)
        -   [Create a PEP Proxy](#create-a-pep-proxy)
        -   [Read PEP Proxy details](#read-pep-proxy-details)
        -   [Reset Password of a PEP Proxy](#reset-password-of-a-pep-proxy)
        -   [Delete a PEP Proxy](#delete-a-pep-proxy)
    -   [IoT Agent CRUD Actions](#iot-agent-crud-actions)
        -   [Create an IoT Agent](#create-an-iot-agent)
        -   [Read IoT Agent details](#read-iot-agent-details)
        -   [List IoT Agents](#list-iot-agents)
        -   [Reset Password of an IoT Agent](#reset-password-of-an-iot-agent)
        -   [Delete an IoT Agent](#delete-an-iot-agent)
-   [Securing the Orion Context Broker](#securing-the-orion-context-broker)
    -   [Securing Orion - PEP Proxy Configuration](#securing-orion---pep-proxy-configuration)
    -   [Securing Orion - Application Configuration](#securing-orion---application-configuration)
    -   [Securing Orion - Start up](#securing-orion---start-up)
        -   [:arrow_forward: Video : Securing A REST API](#arrow_forward-video--securing-a-rest-api)
    -   [User Logs In to the Application using the REST API](#user-logs-in-to-the-application-using-the-rest-api)
        -   [PEP Proxy - No Access to Orion without an Access Token](#pep-proxy---no-access-to-orion-without-an-access-token)
        -   [Keyrock - User Obtains an Access Token](#keyrock---user-obtains-an-access-token)
        -   [PEP Proxy - Accessing Orion with an Access Token](#pep-proxy---accessing-orion-with-an-access-token)
        -   [PEP Proxy - Accessing Orion with an Authorization: Bearer](pep-proxy---accessing-orion-awith-an-authorization-bearer)
    -   [Securing Orion - Sample Code](#securing-orion---sample-code)
-   [Securing an IoT Agent South Port](#securing-an-iot-agent-south-port)
    -   [Securing an IoT Agent South Port - PEP Proxy Configuration](#securing-an-iot-agent-south-port---pep-proxy-configuration)
    -   [Securing an IoT Agent South Port - Application Configuration](#securing-an-iot-agent-south-port---application-configuration)
    -   [Securing South Port Traffic - Start up](#securing-south-port-traffic---start-up)
    -   [IoT Sensor Logs In to the Application using the REST API](#iot-sensor-logs-in-to-the-application-using-the-rest-api)
        -   [Keyrock - IoT Sensor Obtains an Access Token](#keyrock---iot-sensor-obtains-an-access-token)
        -   [PEP Proxy - Accessing IoT Agent with an Access Token](#pep-proxy---accessing-iot-agent-with-an-access-token)
    -   [Securing South Port Traffic - Sample Code](#securing-south-port-traffic---sample-code)
-   [Securing an IoT Agent North Port](#securing-an-iot-agent-north-port)
    -   [Securing an IoT Agent North Port - IoT Agent Configuration](#securing-an-iot-agent-north-port---iot-agent-configuration)
    -   [Securing an IoT Agent North Port - Start up](#securing-an-iot-agent-north-port---start-up)
        -   [Keyrock - Obtaining a permanent token](#keyrock---obtaining-a-permanent-token)
        -   [IoT Agent - provisioning a trusted service group](#iot-agent---provisioning-a-trusted-service-group)
        -   [IoT Agent - provisioning a sensor](#iot-agent---provisioning-a-sensor)

</details>

# Securing Microservices with a PEP Proxy

> "Oh, it's quite simple. If you are a friend, you speak the password, and the doors will open."
>
> — Gandalf (The Fellowship of the Ring by J.R.R Tolkien)

The [FIWARE tutorial](https://github.com/FIWARE/tutorials.Securing-Access) demonstrated that it is possible to Permit
or Deny access to resources based on an authenticated user identifying themselves within an application. It was simply a
matter of the code following a different line of execution if the `access_token` was not found (Level 1 -
_Authentication Access_), or confirming that a given `access_token` had appropriate rights (Level 2 - _Basic
Authorization_). The same method of securing access can be applied by placing a Policy Enforcement Point (PEP) in front
of other services within a FIWARE-based Smart Solution.

A **PEP Proxy** lies in front of a secured resource and is an endpoint found at "well-known" public location. It serves
as a gatekeeper for resource access. Users or other actors must supply sufficient information to the **PEP Proxy** to
allow their request to succeed and pass through the **PEP proxy**. The **PEP proxy** then passes the request on to the
real location of the secured resource itself - the actual location of the secured resource is unknown to the outside
user - it could be held in a private network behind the **PEP proxy** or found on a different machine altogether.

FIWARE [Wilma](https://fiware-pep-proxy.rtfd.io/) is a simple implementation of a **PEP proxy** designed to work with
the FIWARE [Keyrock](https://fiware-idm.readthedocs.io/en/latest/) Generic Enabler. Whenever a user tries to gain access
to the resource behind the **PEP proxy**, the PEP will describe the user's attributes to the Policy Decision Point
(PDP), request a security decision, and enforce the decision. (Permit or Deny). There is minimal disruption of access
for authorized users - the response received is the same as if they had accessed the secured service directly.
Unauthorized users are simply returned a **401 - Unauthorized** response.

## Standard Concepts of Identity Management

The following common objects are found with the **Keyrock** Identity Management database:

-   **User** - Any signed up user able to identify themselves with an eMail and password. Users can be assigned rights
    individually or as a group
-   **Application** - Any securable FIWARE application consisting of a series of microservices
-   **Organization** - A group of users who can be assigned a series of rights. Altering the rights of the organization
    effects the access of all users of that organization
-   **OrganizationRole** - Users can either be members or admins of an organization - Admins are able to add and remove
    users from their organization, members merely gain the roles and permissions of an organization. This allows each
    organization to be responsible for their members and removes the need for a super-admin to administer all rights
-   **Role** - A role is a descriptive bucket for a set of permissions. A role can be assigned to either a single user
    or an organization. A signed-in user gains all the permissions from all of their own roles plus all of the roles
    associated to their organization
-   **Permission** - An ability to do something on a resource within the system

Additionally two further non-human application objects can be secured within a FIWARE application:

-   **IoTAgent** - a proxy between IoT Sensors and the Context Broker
-   **PEPProxy** - a middleware for use between generic enablers challenging the rights of a user.

The relationship between the objects can be seen below - the entities marked in red are used directly within this
tutorial:

![](https://fiware.github.io/tutorials.PEP-Proxy/img/entities.png)

## :arrow_forward: Video : Introduction to Wilma PEP Proxy

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=8tGbUI18udM "Introduction")

Click on the image above to see an introductory video

# Prerequisites

## Docker

To keep things simple both components will be run using [Docker](https://www.docker.com). **Docker** is a container
technology which allows to different components isolated into their respective environments.

-   To install Docker on Windows follow the instructions [here](https://docs.docker.com/docker-for-windows/)
-   To install Docker on Mac follow the instructions [here](https://docs.docker.com/docker-for-mac/)
-   To install Docker on Linux follow the instructions [here](https://docs.docker.com/install/)

**Docker Compose** is a tool for defining and running multi-container Docker applications. A
[YAML file](https://raw.githubusercontent.com/Fiware/tutorials.Identity-Management/master/docker-compose.yml) is used
configure the required services for the application. This means all container services can be brought up in a single
command. Docker Compose is installed by default as part of Docker for Windows and Docker for Mac, however Linux users
will need to follow the instructions found [here](https://docs.docker.com/compose/install/)

## Cygwin

We will start up our services using a simple bash script. Windows users should download [cygwin](http://www.cygwin.com/)
to provide a command-line functionality similar to a Linux distribution on Windows.

# Architecture

This application protects access to the existing Orion v2 and LD by adding PEP Proxy
instances around the services created in FIWARE tutorials and uses data pre-populated into the **MySQL** database used
by **Keyrock**. It will make use of four FIWARE components - the
[Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/), the
[IoT Agent for UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/), the
[Keyrock](https://fiware-idm.readthedocs.io/en/latest/) Generic enabler and adds one or two instances
[Wilma](https://fiware-pep-proxy.rtfd.io/) PEP Proxy dependent upon which interfaces are to be secured. Usage of the
Orion Context Broker is sufficient for an application to qualify as _“Powered by FIWARE”_.

Both the Orion Context Broker and the IoT Agent rely on open source [MongoDB](https://www.mongodb.com/) technology to
keep persistence of the information they hold. We will also be using the dummy IoT devices created in the
[FIWARE tutorial](https://github.com/FIWARE/tutorials.IoT-Sensors/). **Keyrock** uses its own
[MySQL](https://www.mysql.com/) database.

Therefore the overall architecture will consist of the following elements:

-   The FIWARE [Orion Context Broker](https://fiware-orion.readthedocs.io/en/latest/) which will receive requests using
    [NGSI-v2](https://fiware.github.io/specifications/OpenAPI/ngsiv2)
-   The FIWARE [IoT Agent for UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/) which will receive
    southbound requests using [NGSI-v2](https://fiware.github.io/specifications/OpenAPI/ngsiv2) and convert them to
    [UltraLight 2.0](https://fiware-iotagent-ul.readthedocs.io/en/latest/usermanual/index.html#user-programmers-manual)
    commands for the devices. **The current version is developed in Node-RED as Custom NGSI Agent.**
-   FIWARE [Keyrock](https://fiware-idm.readthedocs.io/en/latest/) offer a complement Identity Management System
    including:
    -   An OAuth2 authentication system for Applications and Users
    -   A site graphical frontend for Identity Management Administration
    -   An equivalent REST API for Identity Management via HTTP requests
-   FIWARE [Wilma](https://fiware-pep-proxy.rtfd.io/) is a PEP Proxy securing access to the **Orion** and/or **IoT
    Agent** microservices
-   The underlying [MongoDB](https://www.mongodb.com/) database :
    -   Used by the **Orion Context Broker** to hold context data information such as data entities, subscriptions and
        registrations
    -   Used by the **IoT Agent** to hold device information such as device URLs and Keys
-   A [MySQL](https://www.mysql.com/) database :
    -   Used to persist user identities, applications, roles and permissions

Since all interactions between the elements are initiated by HTTP requests, the entities can be containerized and run
from exposed ports.

The specific architecture of each section of the tutorial is discussed below.

# Start Up

To start the installation, do the following:

```console
docker-compose up```

> **Note** The initial creation of Docker images can take up to three minutes

## Dramatis Personae

The following people at `test.com` legitimately have accounts within the Application

-   Alice, she will be the Administrator of the **Keyrock** Application

<details>
  <summary>
   For more details <b>(Click to expand)</b>
  </summary>

| Name       | eMail                       | Password |
| ---------- | --------------------------- | -------- |
| alice      | `alice-the-admin@test.com`  | `test`   |

</details>

Two organizations have also been set up by Alice:

| Name       | Description                         | UUID                                   |
| ---------- | ----------------------------------- | -------------------------------------- |
| Security   | Security Group for Store Detectives | `security-team-0000-0000-000000000000` |
| Management | Management Group for Store Managers | `managers-team-0000-0000-000000000000` |

One application, with appropriate roles and permissions has also been created:

| Key           | Value                                  |
| ------------- | -------------------------------------- |
| Client ID     | `tutorial-dckr-site-0000-xpresswebapp` |
| Client Secret | `tutorial-dckr-site-0000-clientsecret` |
| URL           | `http://localhost:3000`                |
| RedirectURL   | `http://localhost:3000/login`          |

To save time, the data creating users and organizations from the
[FIWARE tutorial](https://github.com/FIWARE/tutorials.Roles-Permissions) has been downloaded and is automatically
persisted to the MySQL database on start-up so the assigned UUIDs do not change and the data does not need to be entered
again.

The **Keyrock** MySQL database deals with all aspects of application security including storing users, password etc.;
defining access rights and dealing with OAuth2 authorization protocols. The complete database relationship diagram can
be found [here](https://fiware.github.io/tutorials.Securing-Access/img/keyrock-db.png)

To refresh your memory about how to create users and organizations and applications, you can log in at
`http://localhost:3005/idm` using the account `alice-the-admin@test.com` with a password of `test`.

![](https://fiware.github.io/tutorials.PEP-Proxy/img/keyrock-log-in.png)

and look around.

## Logging In to Keyrock using the REST API

Enter a username and password to enter the application. The default super-user has the values `alice-the-admin@test.com`
and `test`. The URL `https://localhost:3443/v1/auth/tokens` should also work in a secure system.

### Create Token with Password

The following example logs in using the Admin Super-User:

#### :one: Request:

```console
curl -iX POST \
  'http://localhost:3005/v1/auth/tokens' \
  -H 'Content-Type: application/json' \
  -d '{
  "name": "alice-the-admin@test.com",
  "password": "test"
}'
```

#### Response:

The response header returns an `X-Subject-token` which identifies who has logged on the application. This token is
required in all subsequent requests to gain access

```
HTTP/1.1 201 Created
X-Subject-Token: d848eb12-889f-433b-9811-6a4fbf0b86ca
Content-Type: application/json; charset=utf-8
Content-Length: 138
ETag: W/"8a-TVwlWNKBsa7cskJw55uE/wZl6L8"
Date: Mon, 30 Jul 2018 12:07:54 GMT
Connection: keep-alive
```

```json
{
    "token": {
        "methods": ["password"],
        "expires_at": "2018-07-30T13:02:37.116Z"
    },
    "idm_authorization_config": {
        "level": "basic",
        "authzforce": false
    }
}
```

### Get Token Info

Once a user has logged in, the presence of a (time-limited) token is sufficient to find out more information about the
user.

You can use the long-lasting `X-Auth-token=aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa` to pretend to be Alice throughout this
tutorial. Both `{{X-Auth-token}}` and `{{X-Subject-token}}` can be set to the same value in the case that Alice is
making an enquiry about herself.

#### :two: Request:

```console
curl -X GET \
  'http://localhost:3005/v1/auth/tokens' \
  -H 'Content-Type: application/json' \
  -H 'X-Auth-token: {{X-Auth-token}}' \
  -H 'X-Subject-token: {{X-Subject-token}}'
```

#### Response:

The response will return the details of the associated user

```json
{
    "access_token": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "expires": "2036-07-30T12:04:45.000Z",
    "valid": true,
    "User": {
        "id": "aaaaaaaa-good-0000-0000-000000000000",
        "username": "alice",
        "email": "alice-the-admin@test.com",
        "date_password": "2018-07-30T11:41:14.000Z",
        "enabled": true,
        "admin": true
    }
}
```

# Managing PEP Proxies and IoT Agents

User accounts have been created in a [FIWARE tutorial](https://github.com/FIWARE/tutorials.Identity-Management).
Non-human actors such as a PEP Proxy can be set up in the same manner. The account for each PEP Proxy, IoT Agent or IoT
Sensor will merely consist of a Username and password linked to an application within Keyrock. PEP Proxy and IoT Agents
accounts can be created by using either the Keyrock GUI or by using the REST API.

## :arrow_forward: Video : Wilma PEP Proxy Configuration

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=b4sYU78skrw "PEP Proxy Configuration")

Click on the image above to see a video about configuring the Wilma PEP Proxy using the **Keyrock** GUI

## Managing PEP Proxies and IoT Agents - Start Up

To start the system run the following command:

```console
./services orion
```

This will start up **Keyrock** with a series of users. There are already two existing applications and an existing PEP
Proxy Account associated with the application.

## PEP Proxy CRUD Actions

#### GUI

Once signed-in, users are able to create and update PEP Proxies associated to their applications for themselves.

![](https://fiware.github.io/tutorials.PEP-Proxy/img/create-pep-proxy.png)

#### REST API

Alternatively, the standard CRUD actions are assigned to the appropriate HTTP verbs (POST, GET, PATCH and DELETE) under
the `/v1/applications/{{application-id}}/pep_proxies` endpoint.

### Create a PEP Proxy

To create a new PEP Proxy account within an application, send a POST request to the
`/v1/applications/{{application-id}}/pep_proxies` endpoint along with the `X-Auth-token` header from a previously logged
in administrative user.

#### :three: Request:

```console
curl -iX POST \
  'http://localhost:3005/v1/applications/{{application-id}}/pep_proxies' \
  -H 'Content-Type: application/json' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

Provided there is no previously existing PEP Proxy account associated with the application, a new account will be
created with a unique `id` and `password` and the values will be returned in the response.

```json
{
    "pep_proxy": {
        "id": "pep_proxy_ac80aaf8-0ac3-4bd8-8042-5e8f587679b7",
        "password": "pep_proxy_23d805e7-1b93-434a-8e69-0798dcdd6726"
    }
}
```

### Read PEP Proxy details

Making a GET request to the `/v1/applications/{{application-id}}/pep_proxies` endpoint will return the details of the
associated PEP Proxy Account. The `X-Auth-token` must be supplied in the headers.

#### :four: Request:

```console
curl -X GET \
  'http://localhost:3005/v1/applications/{{application-id}}/pep_proxies/' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

```json
{
    "pep_proxy": {
        "id": "pep_proxy_f84bcba2-3300-4f13-a4bb-7bdbd358b201",
        "oauth_client_id": "tutorial-dckr-site-0000-xpresswebapp"
    }
}
```

### Reset Password of a PEP Proxy

To renew the password of a PEP Proxy Account, make a PATCH request to the
`/v1/applications/{{application-id}}/pep_proxies` endpoint will return the details of the associated PEP Proxy Account.
The `X-Auth-token` must be supplied in the headers.

#### :five: Request:

```console
curl -X PATCH \
  'http://localhost:3005/v1/applications/{{application-id}}/pep_proxies' \
  -H 'Content-Type: application/json' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

The response returns a new password for the PEP Proxy Account

```json
{
    "new_password": "pep_proxy_2bc8996e-29bf-4195-ac39-d1116e429602"
}
```

### Delete a PEP Proxy

An existing PEP Proxy Account can be deleted by making a DELETE request to the
`/v1/applications/{{application-id}}/pep_proxies` endpoint. The `X-Auth-token` must be supplied in the headers.

#### :six: Request:

```console
curl -X DELETE \
  'http://localhost:3005/v1/applications/{{application-id}}/pep_proxies' \
  -H 'Content-Type: application/json' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

## IoT Agent CRUD Actions

#### GUI

In a similar manner to PEP Proxy creation, signed-in, users are able to create and update IoT Sensor Accounts associated
to their applications.

![](https://fiware.github.io/tutorials.PEP-Proxy/img/create-iot-sensor.png)

#### REST API

Alternatively, the standard CRUD actions are assigned to the appropriate HTTP verbs (POST, GET, PATCH and DELETE) under
the `/v1/applications/{{application-id}}/iot_agents` endpoint.

### Create an IoT Agent

To create a new IoT Agent account within an application, send a POST request to the
`/v1/applications/{{application-id}}/iot_agents` endpoint along with the `X-Auth-token` header from a previously logged
in administrative user.

#### :seven: Request:

```console
curl -X POST \
  'http://localhost:3005/v1/applications/{{application-id}}/iot_agents' \
  -H 'Content-Type: application/json' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

A new account will be created with a unique `id` and `password` and the values will be returned in the response.

```json
{
    "iot": {
        "id": "iot_sensor_f1d0ca9e-b519-4a8d-b6ae-1246e443dd7e",
        "password": "iot_sensor_8775b438-6e66-4a6e-87c2-45c6525351ee"
    }
}
```

### Read IoT Agent details

Making a GET request the `/v1/applications/{{application-id}}/iot_agents/{{iot-agent-id}}` endpoint will return the
details of the associated IoT Agent Account. The `X-Auth-token` must be supplied in the headers.

#### :eight: Request:

```console
curl -X GET \
  'http://localhost:3005/v1/applications/{{application-id}}/iot_agents/{{iot-agent-id}}' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

```json
{
    "iot": {
        "id": "iot_sensor_00000000-0000-0000-0000-000000000000",
        "oauth_client_id": "tutorial-dckr-site-0000-xpresswebapp"
    }
}
```

### List IoT Agents

A list of all IoT Agents associated with an application can be obtained by making a GET request the
`/v1/applications/{{application-id}}/iot_agents` endpoint. The `X-Auth-token` must be supplied in the headers.

#### :nine: Request:

```console
curl -X GET \
  'http://localhost:3005/v1/applications/{{application-id}}/iot_agents' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

```json
{
    "iots": [
        {
            "id": "iot_sensor_00000000-0000-0000-0000-000000000000"
        },
        {
            "id": "iot_sensor_c0fa0a77-ea9e-4a82-8118-b4d3c6b230b1"
        }
    ]
}
```

### Reset Password of an IoT Agent

#### :one::zero: Request:

To renew the password of an individual IoT Agent Account, make a PATCH request to the
`/v1/applications/{{application-id}}//iot_agents/{{iot-agent-id}}` endpoint. The `X-Auth-token` must be supplied in the
headers.

```console
curl -iX PATCH \
  'http://localhost:3005/v1/applications/{{application-id}}/iot_agents/{{iot-agent-id}}' \
  -H 'Content-Type: application/json' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

#### Response:

The response returns a new password for the IoT Agent account.

```json
{
    "new_password": "iot_sensor_114cb79c-bf69-444a-82a1-e6e85187dacd"
}
```

### Delete an IoT Agent

An existing IoT Agent Account can be deleted by making a DELETE request to the
`/v1/applications/{{application-id}}/iot_agents/{{iot-agent-id}}` endpoint. The `X-Auth-token` must be supplied in the
headers.

#### :one::one: Request:

```console
curl -X DELETE \
  'http://localhost:3005/v1/applications/{{application-id}}/iot_agents/{{iot-agent-id}}' \
  -H 'X-Auth-token: {{X-Auth-token}}'
```

# Securing the Orion Context Broker

![PEP Proxy Orion](pep-proxy-orion.png)

## Securing Orion - PEP Proxy Configuration

The `orion-proxy` container is an instance of FIWARE **Wilma** listening on port `1027`, it is configured to forward
traffic to `orion` on port `1026`, which is the default port that the Orion Context Broker is listening to for NGSI
Requests.

```yaml
orion-proxy:
    image: fiware/pep-proxy
    container_name: fiware-orion-proxy
    hostname: orion-proxy
    networks:
        default:
            ipv4_address: 172.18.1.10
    depends_on:
        - keyrock
    ports:
        - "1027:1027"
    expose:
        - "1027"
    environment:
        - PEP_PROXY_APP_HOST=orion
        - PEP_PROXY_APP_PORT=1026
        - PEP_PROXY_PORT=1027
        - PEP_PROXY_IDM_HOST=keyrock
        - PEP_PROXY_HTTPS_ENABLED=false
        - PEP_PROXY_AUTH_ENABLED=false
        - PEP_PROXY_IDM_SSL_ENABLED=false
        - PEP_PROXY_IDM_PORT=3005
        - PEP_PROXY_APP_ID=tutorial-dckr-site-0000-xpresswebapp
        - PEP_PROXY_USERNAME=pep_proxy_00000000-0000-0000-0000-000000000000
        - PEP_PASSWORD=test
        - PEP_PROXY_PDP=idm
        - PEP_PROXY_MAGIC_KEY=1234
```

The `PEP_PROXY_APP_ID` and `PEP_PROXY_USERNAME` would usually be obtained by adding new entries to the application in
**Keyrock**, however, in this tutorial, they have been predefined by populating the **MySQL** database with data on
start-up.

The `orion-proxy` container is listening on a single port:

-   The PEP Proxy Port - `1027` is exposed purely for tutorial access - so that cUrl or Postman can requests directly to
    the **Wilma** instance without being part of the same network.

| Key                       | Value                                            | Description                                            |
| ------------------------- | ------------------------------------------------ | ------------------------------------------------------ |
| PEP_PROXY_APP_HOST        | `orion`                                          | The hostname of the service behind the PEP Proxy       |
| PEP_PROXY_APP_PORT        | `1026`                                           | The port of the service behind the PEP Proxy           |
| PEP_PROXY_PORT            | `1027`                                           | The port that the PEP Proxy is listening on            |
| PEP_PROXY_IDM_HOST        | `keyrock`                                        | The hostname for the Identity Manager                  |
| PEP_PROXY_HTTPS_ENABLED   | `false`                                          | Whether the PEP Proxy itself is running under HTTPS    |
| PEP_PROXY_AUTH_ENABLED    | `false`                                          | Whether the PEP Proxy is checking for Authorization    |
| PEP_PROXY_IDM_SSL_ENABLED | `false`                                          | Whether the Identity Manager is running under HTTPS    |
| PEP_PROXY_IDM_PORT        | `3005`                                           | The Port for the Identity Manager instance             |
| PEP_PROXY_APP_ID          | `tutorial-dckr-site-0000-xpresswebapp`           |                                                        |
| PEP_PROXY_USERNAME        | `pep_proxy_00000000-0000-0000-0000-000000000000` | The Username for the PEP Proxy                         |
| PEP_PASSWORD              | `test`                                           | The Password for the PEP Proxy                         |
| PEP_PROXY_PDP             | `idm`                                            | The Type of service offering the Policy Decision Point |
| PEP_PROXY_MAGIC_KEY       | `1234`                                           |                                                        |

For this example, the PEP Proxy is checking for Level 1 - _Authentication Access_ not Level 2 - _Basic Authorization_ or
Level 3 - _Advanced Authorization_.

## Securing Orion - Start up

To start the system with a PEP Proxy protecting access to **Orion**, run the following command:

```console
./services orion
```

### :arrow_forward: Video : Securing A REST API

[![](https://fiware.github.io/tutorials.Step-by-Step/img/video-logo.png)](https://www.youtube.com/watch?v=coxFQEY0_So "Securing a REST API")

Click on the image above to see a video about securing a REST API using the Wilma PEP Proxy

## User Logs In to the Application using the REST API

### PEP Proxy - No Access to Orion without an Access Token

Secured Access can be ensured by requiring all requests to the secured service are made indirectly via a PEP Proxy (in
this case the PEP Proxy is found in front of the Context Broker). Requests must include an `X-Auth-Token`, failure to
present a valid token results in a denial of access.

#### :one::two: Request:

If a request to the PEP Proxy is made without any access token as shown:

```console
curl -X GET \
  http://localhost:1027/v2/entities/urn:ngsi-ld:Store:001?options=keyValues
```

#### Response:

The response is a **401 Unauthorized** error code, with the following explanation:

```
Auth-token not found in request header
```

### Keyrock - User Obtains an Access Token

#### :one::three: Request:

To log in to the application using the user-credentials flow send a POST request to **Keyrock** using the `oauth2/token`
endpoint with the `grant_type=password`. For example to log-in as Alice the Admin:

```console
curl -iX POST \
  'http://localhost:3005/oauth2/token' \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic dHV0b3JpYWwtZGNrci1zaXRlLTAwMDAteHByZXNzd2ViYXBwOnR1dG9yaWFsLWRja3Itc2l0ZS0wMDAwLWNsaWVudHNlY3JldA==' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data "username=alice-the-admin@test.com&password=test&grant_type=password"
```

#### Response:

The response returns an access code to identify the user:

```json
{
    "access_token": "a7e22dfe2bd7d883c8621b9eb50797a7f126eeab",
    "token_type": "Bearer",
    "expires_in": 3599,
    "refresh_token": "05e386edd9f95ed0e599c5004db8573e86dff874"
}
```

This can also be done by entering the Tutorial Application on http:/localhost and logging in using any of the OAuth2
grants on the page. A successful log-in will return an access token.

### PEP Proxy - Accessing Orion with an Access Token

If a request to the PEP Proxy is made including a valid access token in the `X-Auth-Token` header as shown, the request
is permitted and the service behind the PEP Proxy (in this case the Orion Context Broker) will return the data as
expected.

#### :one::four: Request:

```console
curl -X GET \
  http://localhost:1027/v2/entities/urn:ngsi-ld:Store:001?options=keyValues \
  -H 'X-Auth-Token: {{X-Access-token}}'
```

### PEP Proxy - Accessing Orion with an Authorization: Bearer

The standard `Authorization: Bearer` header can also be used to identity the user, the request from an authorized user
is permitted and the service behind the PEP Proxy (in this case the Orion Context Broker) will return the data as
expected.

#### :one::five: Request:

```console
curl -X GET \
  http://localhost:1027/v2/entities/urn:ngsi-ld:Store:001?options=keyValues \
  -H 'Authorization: Bearer {{X-Access-token}}'
```

#### Response:

```json
{
    "id": "urn:ngsi-ld:Store:001",
    "type": "Store",
    "address": {
        "streetAddress": "Bornholmer Straße 65",
        "addressRegion": "Berlin",
        "addressLocality": "Prenzlauer Berg",
        "postalCode": "10439"
    },
    "location": {
        "type": "Point",
        "coordinates": [13.3986, 52.5547]
    },
    "name": "Bösebrücke Einkauf"
}
```

## Securing Orion - Sample Code

When a User logs in to the application using the User Credentials Grant, an `access_token` is obtained which identifies
the User. The `access_token` is stored in session:

```javascript
function userCredentialGrant(req, res) {
    debug("userCredentialGrant");

    const email = req.body.email;
    const password = req.body.password;

    oa.getOAuthPasswordCredentials(email, password).then((results) => {
        req.session.access_token = results.access_token;
        return;
    });
}
```

For each subsequent request, the `access_token` is supplied in the `X-Auth-Token` Header

```javascript
function setAuthHeaders(req) {
    const headers = {};
    if (req.session.access_token) {
        headers["X-Auth-Token"] = req.session.access_token;
    }
    return headers;
}
```

In the following example, two requests are made. The same `X-Auth-Token` Header must be added to each request -
therefore the User can be identified and access granted.

```javascript
async function uodateItem(req, res) {
    const inventory = await retrieveEntity(
        req.params.inventoryId,
        {
            options: "keyValues",
            type: "InventoryItem"
        },
        setAuthHeaders(req)
    );
    const count = inventory.shelfCount - 1;

    await updateExistingEntityAttributes(
        req.params.inventoryId,
        { shelfCount: { type: "Integer", value: count } },
        {
            type: "InventoryItem"
        },
        setAuthHeaders(req)
    );
    res.redirect(`/app/store/${inventory.refStore}/till`);
}
```
---

## License

[MIT](LICENSE) © 2022-2024 Engineering Ingegneria Informtica S.p.A.

[MIT](LICENSE) © 2018-2020 FIWARE Foundation e.V.
