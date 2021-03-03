+++
title = "Turnup environment setup"
weight = 2
sort_by = "weight"
+++
Main idea behind turnup environment is to have it fully configured and isolated as a docker container. So you don't need to configure and setup much of tools and scripts required to manage clusters, instead you ssh to the docker container and have everythin in place available.

Please follow the instructions below to configure and build docker container.

Turnup environment consist of the following parts:
  * [Docker](http://docker.com)
  * [Wireguard](http://wireguard.com)
  * RSA keys pair  
  * Google cloud project with attached billing account
  * Docker container with all tools configured

### Docker

For the sake of turnup environment unification, all operations are done from docker container running specific docker image. Please follow instructions for your operation system to setup Docker.

### Wireguard

Machines in all clusters are protected by dedicated VPNs. Only ports exposed by public services are available from the outside.  Wireguard is used to setup access from administrator's machine to the internal services running in remote environments. Please follow instructions for your operation system to setup Wireguard.

### RSA keys pair

RSA keys pair is required for authentication of the admin in all services of control cluster including VPN. Feel free to use the following command for the rsa key pair creation:
```bash
$ ssh-keygen -t rsa -C "your_email@example.com"
```

### Google cloud setup
As of now, environment is getting configured in Google cloud, so you need to have a project with linked valid billing account.

After this is done, follow instructions to your operation system and install gcloud command line tool. This we will use to setup service account with required permissions needed to configure environment.

#### Setup env vars
Setup environment variables with name of the project and admin service account to be created to manage control cluster.
```bash
$ PROJECT_NAME="name of the project"
$ ADMIN_ACCOUNT_NAME="admin-svc-account"
```

#### Auth gcloud
Bypass Google cloud OAuth2 by executing the following command with account you plan to use for the project. Account should already have project linked to the valid billing account.
```bash
$ gcloud auth login
```

#### Create service account
Following commands will create service account and grant required permissions.

```bash
$ gcloud services enable compute.googleapis.com
$ gcloud iam service-accounts create $ADMIN_ACCOUNT_NAME
$ gcloud projects add-iam-policy-binding $PROJECT_NAME --member "serviceAccount:$ADMIN_ACCOUNT_NAME@$PROJECT_NAME.iam.gserviceaccount.com" --role="roles/compute.admin"
$ gcloud projects add-iam-policy-binding $PROJECT_NAME --member "serviceAccount:$ADMIN_ACCOUNT_NAME@$PROJECT_NAME.iam.gserviceaccount.com" --role="roles/compute.instanceAdmin.v1"
$ gcloud projects add-iam-policy-binding $PROJECT_NAME --member "serviceAccount:$ADMIN_ACCOUNT_NAME@$PROJECT_NAME.iam.gserviceaccount.com" --role="roles/iam.serviceAccountUser"
```

#### Generate keyfile
The keyfile is to be used by ansible and terraform to perform actions required for provisioning and initial configuration of the cloud services.

```bash
$ gcloud iam service-accounts keys create key.json --iam-account="$ADMIN_ACCOUNT_NAME@$PROJECT_NAME.iam.gserviceaccount.com"
```

### Docker container setup
1.  Checkout sources of the ops repository from: https://github.com/chatbot-fm/ops.
1.  Prepare `shadow` directory:
	1.   Copy `key.json` gcloud credentials file to the directory
    1.   Copy `id_rsa` and `id_rsa.pub` key pair to the directory
1.  Copy `id_rsa.pub` file to `chatbot-fm/ops/turnup-local-env/conf` 
1.  Prepare `src` directory:
    1.   This directory might be used later for the work, as of now keep it empty.
1.  Update `chatbot-fm/ops/turnup-local-env/.env` with UID and GID of your local user to avoid permissions issues with files shared via `src` dir.
1. Update `chatbot-fm/ops/tunrup-local-env/docker-compose.yml` file with a proper paths for `shadow` and `src` volumes. 
1. While in `chatbot-fm/ops/tunrup-local-env` directory:
    1.   Build docker container by launching `$ docker build .`
    1.   Run docker container by executing `$ ./run.sh`

Now you can ssh to the docker container. This is a fully ready environment to setup control cluster.

