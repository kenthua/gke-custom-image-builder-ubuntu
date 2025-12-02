# GKE Custom Image Builder - Ubuntu

This repository provides a template for building custom Ubuntu images for GKE using Google Cloud Build. The examples provided utilize HashiCorp Packer, a popular open-source tool well-suited for creating machine images, run within a Cloud Build pipeline. This pipeline helps you create repeatable, automated, and version-controlled custom images.

**This is not an officially supported Google product.** This project is not eligible for the Google Open Source Software Vulnerability Rewards Program. The templates and scripts in this repository are provided as a starting point. You are responsible for the security, correctness, and compatibility of any images built using this pipeline. Google does not provide support for HashiCorp Packer.

## Overview

This template sets up an automated pipeline using:

*   **Google Cloud Build:** To run the image creation process in a managed environment.
*   **Terraform:** To provision the necessary Cloud Build triggers, service accounts, and Artifact Registry repository.
*   **Example Configurations:** The examples use configuration files (`*.pkr.hcl`) designed for use with HashiCorp Packer. The Cloud Build pipeline is configured to use a Packer container image from your Artifact Registry.

## Prerequisites

*   An active Google Cloud project with billing enabled.
*   The following APIs enabled: Compute Engine, Cloud Build, IAM, Cloud Storage, Artifact Registry.
*   Google Cloud SDK (`gcloud`) installed and authenticated on your local machine (for deploying Terraform and interacting with GCP).
*   Terraform installed and authenticated on your local machine (for deploying the pipeline infrastructure).

## Hosting Packer in Artifact Registry (Recommended)

For enhanced security and version control, it is recommended to host the Packer builder image in your own Artifact Registry. By default, Cloud Build might use public builder images.

This template assumes you have a Packer container image available in your Artifact Registry. You can follow the guide in the [TODO update once available: Ubuntu User Guide - Hosting Packer in Artifact Registry] to build and push a Packer image. The `cloudbuild.yaml` should be updated to reference this image.

**Example `cloudbuild.yaml` step using Packer from AR:**
```yaml
steps:
  - name: 'us-central1-docker.pkg.dev/YOUR_PROJECT_ID/packer/packer:latest' # Example path
    args: ['build', 'scripts/ubuntu/customize_ubuntu.pkr.hcl']
    # ... other options
```

## How to Use This Template

1.  **Create Your Repository:** Click the "Use this template" button on GitHub to create a new repository under your own organization or user account.
2.  **Clone Your Repository:** Clone your newly created repository to your local machine.
    ```bash
    git clone https://github.com/YOUR_USERNAME/YOUR_REPOSITORY_NAME.git
    cd YOUR_REPOSITORY_NAME
    ```
3.  **Configure `main.tf`:**
    *   Open `main.tf` and update the `project_id` variable to your Google Cloud project ID.
    ```terraform
    variable "project_id" {
      description = "The GCP project ID"
      type        = string
      # Replace with your project ID
      # default     = "your-gcp-project-id"
    }
    ```
4.  **Customize the Build:**
    *   **`scripts/ubuntu/customize_ubuntu.pkr.hcl`:** This is the main Packer template. Modify the `provisioner` blocks to add your desired customizations.
    *   **`scripts/ubuntu/`:** Add any shell scripts referenced by your Packer template in this directory.
    *   **Base Image:** Update the `source_image` in `main.tf` to the GKE Ubuntu base image you want to build upon. See [TODO update once available How to Choose a Base Image] in the main user guide.
    *   **`cloudbuild.yaml`:** Verify the Packer image path matches your Artifact Registry location.

5.  **Deploy the Cloud Build Trigger:**
    Initialize and apply Terraform. This will set up the necessary resources, including the Artifact Registry repository if it doesn't exist (as defined in your Terraform code) and the Cloud Build trigger.
    ```bash
    terraform init
    terraform apply
    ```

6.  **Run the Build:**
    *   Navigate to the Cloud Build > Triggers page in the Google Cloud Console.
    *   Find the trigger named `gke-ubuntu-custom-image-build`.
    *   Click "Run" to manually trigger the build.

## Disclaimer

This repository provides templates and examples. You are responsible for any modifications and the resulting images. Google does not provide support for third-party tools like HashiCorp Packer.