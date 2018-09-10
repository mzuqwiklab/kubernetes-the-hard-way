# Kubernetes The Hard Way With Terraform

## Table Of Contents

[Project Status](#project-status)

[Getting Started](#getting-started)

[Contributing](#contributing)

[Motivation](#motivation)

## Project Status

**PRE-ALPHA**. Please don't use the project for any production workloads. Also, be sure to spin your cluster down if you aren't using it as it will cost you--or your company--money.

## Getting Started

1. If you don't already have one, sign up for a [Google Cloud Platform account](https://cloud.google.com/).
1. Download the `gcloud` [command-line tool](https://cloud.google.com/sdk/gcloud/).
1. Create a project in GCP named `k8s-the-hard-way-tf`.
1. On the command line, run: `gcloud init` to set up your account credentials/project details to point at the newly created project.
1. [Create a service account](https://cloud.google.com/iam/docs/creating-managing-service-accounts) (`IAM & admin` > `Service accounts` > `+ CREATE SERVICE ACCOUNT`) in the `k8s-the-hard-way-tf` project with `Owner` permissions, create/download the `*.json` credentials generated by the service account, and place them in a safe location on your local machine.
1. Set your Google Application credentials using: `export GOOGLE_APPLICATION_CREDENTIALS=<path-to-your-credentials-json>`.
1. Go to the `certs` directory, and run the `create-ca.sh` script, followed by the `gen-certs.sh` script. This will create a self-signed CA, and create/sign all of the generated certs with that CA.
1. Go to the `configs` directory, and run the `gen-config.sh` script. This will create the encryption configuration for the new Kubernetes cluster.
1. Add a public `ssh` key to your project metadata (`Compute Engine` > `Metadata` > `SSH Keys`) using the format `<protocol> <key-blob> <username@example.com>`. The virtual machines you spin up will inherit this public key and allow the `remote-exec` provisioners to work.
1. Fill in the variables in `variables.tf` that have `TODO` next to them.
1. **OPTIONAL**: Edit the GitHub usernames in `scripts/add-ssh-keys.sh` to give trusted colleagues/friends access to the machines in your Kubernetes cluster (see the [Motivation](#motivation) section for more details).
1. Run: `terraform init` to initialize Terraform.
1. Run: `terraform plan` to see the planned changes that Terraform will make.
1. Run: `terraform apply` to create your Kubernetes cluster. **NOTE**: The first `terraform apply` might fail because the `Compute Engine API has not been used in project` before. Follow the link provided in the error message (should be something like `https://console.developers.google.com/apis/api/compute.googleapis.com/overview?project=0123456789`) and click `ENABLE`. After the API is enabled, rerun `terraform apply`.
1. Configure `kubectl` to use the new cluster by running the `configure-kubectl` script.
1. Run `kubectl get cs` (or another related command) to test the cluster's component statuses.

## Contributing

Make a PR and we'll go from there!

## Motivation

This cluster was designed for the purpose of enabling effective study for the [Certified Kubernetes Administrator (CKA) exam](https://www.cncf.io/certification/cka/). The vision was to define a custom, from-scratch Kubernetes cluster in Terraform (using Kelsey Hightower's [kubernetes-the-hard-way tutorial](https://github.com/kelseyhightower/kubernetes-the-hard-way) as a reference point--thanks Kelsey), so that you could spin up a Kubernetes cluster with access to all of its internal components--including the Control Plane--on-demand, and destroy it if the cluster became unusable, or you just want to start over. This code enabled the majority of the Cloud Engineers at [Nebulaworks](https://nebulaworks.com/) to effectively study for, and get their CKA through hands-on practice in a CKA-exam-like environment (see below).

How we would practice creating/using [Kubernetes objects](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) and completing [tasks](https://kubernetes.io/docs/tasks/) for the CKA:
1. Run `terraform apply` to create your Kubernetes cluster.
1. Scour the [Kubernetes reference docs](https://kubernetes.io/docs/tasks/) and try to create the objects/complete the tasks that you see.

How we would practice for things breaking in the CKA:
1. Edit `scripts/add-ssh-keys.sh` to give trusted colleagues/friends access to the machines in your Kubernetes cluster.
1. Run `terraform apply` to create your Kubernetes cluster.
1. Have your trusted colleagues/friends `ssh` into your cluster and break things [you'll need to give them the username for your machines (run `whoami`), and the IP addresses of your machines (run `terraform output`)].
1. Fix the things that are broken.