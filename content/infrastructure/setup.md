+++
title = "Setup"
weight = 2
sort_by = "weight"
+++
## Turnup environment setup
### Pre-requisites
Please follow instructions of your operation system to setup the following software:
  * [Docker](http://docker.com)
  * [Wireguard](http://wireguard.com)
  * Control cluster credentials
    * Google cloud credentials file
	* Admin's Public&Private RSA keys

#### Docker

For the sake of turnup environment unification, all operations are done from docker container running specific docker image. 

#### Wireguard

Machines in all clusters are protected by dedicated VPNs. Only ports exposed by public services are available from the outside.  Wireguard is used to setup access from administrator's machine to the internal services running in remote environments.

#### Control cluster credentials
It is expected that files with all required credentials are mapped as a **shadow** volume for docker container with a following structure:
```
/shadow
   |- google_cloud_credentials.json
   |- admin_rsa.pub
   |- admin_rsa
```

**google_cloud_credentials.json**

Provisioning of the cluster machines is done using Google Cloud API. To let automation do the thing, it is required to create Application account with a specific roles:
  * Compute Admin
  * DNS Administrator
  * Compute Instance Admin (v1)
  * Service Account User

**admin_rsa.pub, admin_rsa**

Pubic and Private RSA keys are required to configure services on control cluster machines after turnup, such as ssh, kubernetes, gitea, drone. Since there is no direct ssh access availble for all machines, only via VPN, it is not required to use super strong RSA keys pair. Feel free to use the following command for the rsa key pair creation:
```
$ ssh-keygen -t rsa -C "your_email@example.com"
```

### Run docker container
1. Checkout sources of the infrastructure repository.
1. Update `docker-compose.yml` file with a proper paths for `shadow` and `src` volumes.
