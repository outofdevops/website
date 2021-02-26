---
title: "How to run containers on GCP"
date: 2021-01-03T11:41:11Z
description: "This is meta description"
type: "post"
image: "images/how-to-run-containers-on-gcp/how-to-run-containers-on-gcp.jpg"
categories: 
  - "devops"
tags:
  - "GCP"
  - "CloudRun"
  - "CloudSQL"
  - "Artifact Registry"
---

Today we are going to see how to run docker containers on Google Cloud Platform using Cloud Run and Cloud SQL

## Pre-requisite

Before we start you need to have the following:

1. `Docker` installed on your machine (Click here to download and install)
2. A Google Cloud Platform Account (Click [here](https://gcpsignup.page.link/doL9) to get 350$ for Trials)
3. `Gcloud` installed 

## Implementation

To proceed with the implementation we have to prepare the project first, then we can configure  Artifact Registry, the database and Cloud Run.

### Project preparation

If your project has just been created the first thing to do is enable the api needed:

- Artifact Registry API
- Cloud SQL Admin API
- Cloud Run API
- Compute Engine API

To enable the APIs click on the Menu → APIs & Services → Library

![../../images/how-to-run-containers-on-gcp/cloud-run-0.png](../../images/how-to-run-containers-on-gcp/cloud-run-0.png)

Now search for each of the APIs listed above and click the `ENABLE` button.

![../../images/how-to-run-containers-on-gcp/cloud-run-1.png](../../images/how-to-run-containers-on-gcp/cloud-run-1.png)

### Artifact Registry

Artifact registry will contain the docker image of our application, to host it in Artifact Registry we need to create a docker repository first.

I called it docker-ar as you can see from the screenshot below.

![../../images/how-to-run-containers-on-gcp/cloud-run-2.png](../../images/how-to-run-containers-on-gcp/cloud-run-2.png)

Now we need to retag and push our image to Artifact Registry, first let's pull our image from docker hub:

```bash
> docker pull outofdevops/simple-go-service:cloud-run
> export PROJECT_ID=your-gcp-project-id
> docker tag outofdevops/simple-go-service:cloud-run europe-west1-docker.pkg.dev/${PROJECT_ID}/docker-ar/simple-go-service:cloud-run
```

Before pushing we need to configure the access to the new registry in our docker config:

```bash
> gcloud auth configure-docker europe-west1-docker.pkg.dev
> docker push europe-west1-docker.pkg.dev/${PROJECT_ID}/docker-ar/simple-go-service:cloud-run
```

This completes the Artifact Registry configuration.

### Cloud SQL

The database for our application will be managed by the Cloud SQL service, so let's create one named sgs make sure you are using a public ip in the configuration options (it is set to public by default but double check just in case).

![../../images/how-to-run-containers-on-gcp/cloud-run-3.png](../../images/how-to-run-containers-on-gcp/cloud-run-3.png)

The instance creation will take few minutes, after that we can proceed with creation of a database and a user.

Menu → Cloud SQL → Databases → CREATE DATABASE

Give a name to the Database, I called it `sgs`

![../../images/how-to-run-containers-on-gcp/cloud-run-4.png](../../images/how-to-run-containers-on-gcp/cloud-run-4.png)

Menu → Cloud SQL → Users → ADD USER ACCOUNT

- Username: sgs
- Password: SuperSecretPassword

![../../images/how-to-run-containers-on-gcp/cloud-run-5.png](../../images/how-to-run-containers-on-gcp/cloud-run-5.png)

The Database is now configured and ready, let's copy the database Connection Name from the overview as we need it for Cloud Run.

![../../images/how-to-run-containers-on-gcp/cloud-run-6.png](../../images/how-to-run-containers-on-gcp/cloud-run-6.png)

### Cloud Run

Everything is in place we just need to create our Cloud Run service: Menu → Cloud Run → Create Serviceand select Cloud Run (fully managed), give a name to the service (e.g. simple-go-service) and click NEXT.

![../../images/how-to-run-containers-on-gcp/cloud-run-7.png](../../images/how-to-run-containers-on-gcp/cloud-run-7.png)

Now we have to select the docker image we want to run, so click on select in the input box under deploy one revision from an existing container image and pick the image we pushed to Artifact Registry.

![../../images/how-to-run-containers-on-gcp/cloud-run-8.png](../../images/how-to-run-containers-on-gcp/cloud-run-8.png)

Now we have to open the Advanced Settings Panel and add the env variables to configure the Database Connection. Under VARIABLES you need to expose the following:

- APP_DB_USERNAME=sgs
- APP_DB_PASSWORD=SuperSecretPassword
- APP_DB_NAME=sgs
- APP_DB_HOST=/cloudsql/your:connnetion:string

Note that we copied the connection string from the previous step if you lost it you can find it in the Cloud SQL Instance overview page.

![../../images/how-to-run-containers-on-gcp/cloud-run-9.png](../../images/how-to-run-containers-on-gcp/cloud-run-9.png)

Since we have to enable the connectivity with the database click on the CONNECTIONS tab and select the database connection from the list:

![../../images/how-to-run-containers-on-gcp/cloud-run-10.png](../../images/how-to-run-containers-on-gcp/cloud-run-10.png)

Finally the last step is to configure Ingress and Authentication:

![../../images/how-to-run-containers-on-gcp/cloud-run-11.png](../../images/how-to-run-containers-on-gcp/cloud-run-11.png)

If you followed all the steps you should see a nice green tick and the URL of the service. 

![../../images/how-to-run-containers-on-gcp/cloud-run-12.png](../../images/how-to-run-containers-on-gcp/cloud-run-12.png)

Copy the URL and export it as environment variable:

`export SERVICE_URL=put-your-service-url-here`

Run the following commands and you should have a similar output:

```bash
> curl $SERVICE_URL/events
[]
> curl --header "Content-Type: application/json" --request POST --data '{"name": "video", "state": "recording1"}' ${SERVICE_URL}/events
{"id":"c05cd71b-b907-4bc1-91ac-f530a00cf8b5","name":"video","state":"recording1"}
> curl $SERVICE_URL/events
[
  {
    "id": "c05cd71b-b907-4bc1-91ac-f530a00cf8b5",
    "name": "video",
    "state": "recording1"
  }
]
```
