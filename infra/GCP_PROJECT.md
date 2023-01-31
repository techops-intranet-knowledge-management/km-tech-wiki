# GCP Infra through Terraform

## Required Gcloud setup

Install Google Cloud CLI on your local machine:

```
https://cloud.google.com/sdk/docs/install-sdk
```

Make sure you are logged in on your terminal:

```
gcloud auth login
```



## Creating a new GCP Project

If you are here, you are about to create a new project on GCP. Remember that the current guidelines from DI team is to have projects per environment and domain (eg: 
`new-domain-dev`; `new-domain-stg`; `new-domain-prod`). That facilitates cost analysis in the future.

To be able to run terraform (with basic Cloud Run applications) and deploy through CircleCI on your new project, follow the steps below:

1. Create a new project - check with the team which Billing account you should use (probably `Pythians`)

2. Enable the following services:

   - Cloud Run API
   - Cloud Build API
   - Service Usage API (a credential required warning message may appear, ignore it)
   - Identity and Access Management (IAM) API
   - Cloud Resource Manager API
   - Compute Engine API
   - Secret Manager API
   - Artifact Registry API


3. Add the km-tech-team in the new project with `Owner` role ([IAM & Admin](https://console.cloud.google.com/iam-admin/iam))

#### 4. Terraform Role and Account permission

4.1. Create a new Terraform Infra Role - needed to create/edit infrastructure with Terraform (based on the default setup we have on KM-ecossystem)
```
gcloud iam roles copy --source-project=intranetlumapps --source="infra.terraform" --destination="infra.terraform" --dest-project=[your-new-project-id]
```

4.2. Create a new Service Account with name `terraform` and apply the new Role you just created `Infra Terraform` ([Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts))

4.3. Generate a new key for the Terraform Service Account and save it on Secret Manager - you will need this variable for CircleCI usage (`$[ENV]_GCLOUD_SERVICE_KEY_TERRAFORM`), where `[ENV]` should be replaced by `DEVELOPMENT`,`STAGING`or `PRODUCTION`

#### 5. GCP Editor Role and Account permission

5.1. Create a new GCP Editor Role - needed to deploy applications with CircleCI (based on the default setup we have on KM-ecossystem)
```
gcloud iam roles copy --source-project=intranetlumapps --source="gcp.editor" --destination="gcp.editor" --dest-project=[your-new-project-id]
```

5.2. Create a new Service Account with name `new-domain-editor` and apply the new Role you just created `GCP Editor` ([Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts))

5.3. Generate a new key for the Editor Service Account and save it on Secret Manager - you will need this variable for CircleCI usage (`$[ENV]_GCLOUD_SERVICE_KEY_EDITOR`)

_________________

That's all, with those two keys generated you should be ready to run your `-infra` project and deploy your application with CircleCI pipeline.
