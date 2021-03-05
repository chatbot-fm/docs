+++
title = "Control cluster setup"
weight = 2
sort_by = "weight"
+++

## Pre-requisites
The only pre-requisite is the Tunrup environment. To continue with setup of control cluster, the turnup environment must be configured meaning: 
*   You should be able to access docker container via ssh and be able to operate there
*   You should have a project in Google cloud with properly linked valid billing account.
*   Shadow volume mounted to docker container including:
    *   Valid pair of RSA keys
	*   Valid gcloud key file

If anything of it is missed, please follow instruction to setup turnup environment first before going further.

All further steps assumes you have `github.com/chatbot-fm/ops` repository checked out locally and you are in root of it.

#### Generate htpasswd
htpasswd file is required to configure docker registry. Set the following env variables:
```bash
export DOCKER_LOGIN='your login'
export DOCKER_PASSWORD='your password'
```

Generate the htpasswd file using the following command:
```bash
htpasswd -Bbn $DOCKER_LOGIN $DOCKER_PASSWORD > /home/admin/shadow/htpasswd
```

## Stage 0: images creation

All required software needed for compute instances is getting pre-installed and persisted as snapshots of the instances which we use later to setup required amount of machines of different type including:
*   Bastion host
*   CI/CD
*   GIT repository
*   Kubernetes cluster

For this please:
*   Change current directory to `control_cluster_env/stage_00_images`
*   Set Google cloud project name as environment variable `PROJECT_ID="yourprojectid"`
*   Run `$ packer build -var 'project_id=$PROJECT_ID' bastion.pkr.hcl`
*   Run `$ packer build -var 'project_id=$PROJECT_ID' drone.pkr.hcl`
*   Run `$ packer build -var 'project_id=$PROJECT_ID' gitea.pkr.hcl`
*   Run `$ packer build -var 'project_id=$PROJECT_ID' docker-registry.pkr.hcl`
*   Run `$ packer build -var 'project_id=$PROJECT_ID' k8s-control-install.pkr.hcl`

## Stage 1: Provisioning
TODO
```bash
$ export GOOGLE_APPLICATION_CREDENTIALS=/home/admin/shadow/key.json
$ terraform apply -v 'project=$PROJECT_ID' -auto-approve'
```
