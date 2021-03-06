---
title: Deploy Image
order: 11
---

Now that we have our transaction processor image on the Dockerhub, we can try using Sextant to deploy it to a Kubernetes cluster running Sawtooth.

First - the trainer will need to create you an account on the [sextant instance](http://train-publi-tal3pn0iwu1k-225148631.ap-southeast-1.elb.amazonaws.com/) and allocate you a region to deploy to.

## Create Kubernetes Cluster

Once you have a username and password - login to the [sextant instance](http://train-publi-tal3pn0iwu1k-225148631.ap-southeast-1.elb.amazonaws.com/)

Then - click the `Add` button to make a new cluster.

Name your cluster after your group or username.

Pick `dev.catenasys.com` as the domain.

Pick `1` master and `3` nodes.

Leave the instance types for masters and nodes on `m4.large`.

Pick the region allocated to you by the trainer.

Choose one zone for the master and 3 zones for the nodes.

Click `Generate Keypair` - you don't need to copy the private key.

Click `Create Cluster`.

## Create Sawtooth Network

Once the cluster has created - you need to enter your new transaction processor in the `Custom Transaction Processors` section.

You should have the image name from the previous section.

It will look something like this:

```
binocarlos/xotp
```

For the command - it should be: `node`

For the arguments - it should be: `src/tp/index.js --validator tcp://localhost:4004`

```
node src/tp/index.js --validator tcp://validator:4004
```

Click deploy - after some time - your sawtooth network should have deployed.

## Use the local client against your production tp

Now we have our tp running on a Kubernetes cluster - we can use the rest-api endpoint to connect our local CLI against it.

Get yourself into the `xo` container:

```bash
docker exec -ti sawtooth-xo-tp bash
```

> If you need to spin up the cluster again - first change directory to `code/compose`, then use the `docker-compose -f sawtooth.yaml -f xo.yaml up` command

First - copy the address of the `sawtooth-rest-api` service `ExternalIP` from the sextant UI.

Then export it so our CLI will communicate to our live TP:

```bash
export URL=<address of rest api you copied from the sextant UI>:8080
```

Now - we should be able to use our CLI against our running production transaction processor:

```bash
./src/cli/index.js list
```