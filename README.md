Created a private repo as new-repo

Created a terraform file terraform.tf 
      provider "google"{
  project ="gcp_id"
  region ="us-central1"
  zone ="us-central1-c"
}
resource "google_compute_instance" "vm_instance"{
  name="jtheres-terraform-instance"
  machine_type="f1-micro"

  boot_disk{
    initialize_params{
      image="debian-cloud/debian-9"
    }
  }

  network_interface{
    network="${google_compute_network.vpc_network.self_link}"
  }
}

resource "google_compute_network" "vpc_network"{
  name="jtheres-terraform"

}
resource "google_compute_subnetwork" "vpc_subnet" {
  name = "jtheres-subnet1"
  region = "us-central1"
  ip_cidr_range="10.26.1.0/24"
  network= "${google_compute_network.vpc_network.self_link}"
}

Created the workflow:
  name: 'Terraform CI'

on:
  push:
    branches:
    - main
  pull_request:

jobs:
  terraform:
    name: 'Terraform'
    runs-on: ubuntu-latest

    # Use the Bash shell regardless whether the GitHub Actions runner is ubuntu-latest, macos-latest, or windows-latest
    defaults:
      run:
        shell: bash

    steps:
    # Checkout the repository to the GitHub Actions runner
    - name: Checkout
      uses: actions/checkout@v2

    # Install the latest version of Terraform CLI and configure the Terraform CLI configuration file with a Terraform Cloud user API token
    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v1


    # Initialize a new or existing Terraform working directory by creating initial files, loading any remote state, downloading modules, etc.
    - name: Terraform Init
      run: terraform init
      env:
        GOOGLE_CREDENTIALS: ${{ secrets.GOOGLE_CREDENTIALS }}
        

    # Generates an execution plan for Terraform
    - name: Terraform Plan
      run: terraform plan
      env:
        GOOGLE_CREDENTIALS: ${{ secrets.GOOGLE_CREDENTIALS }}

      # On push to main, build or change infrastructure according to Terraform configuration files
      # Note: It is recommended to set up a required "strict" status check in your repository for "Terraform Cloud". See the documentation on "strict" required status checks for more information: https://help.github.com/en/github/administering-a-repository/types-of-required-status-checks
    - name: Terraform Apply
      if: github.ref == 'refs/heads/main' && github.event_name == 'push'
      run: terraform apply -auto-approve
      env:
        GOOGLE_CREDENTIALS: ${{ secrets.GOOGLE_CREDENTIALS }}


Created the Service Account and downloaded the json key to Secrets in github and saved as GOOGLE_CREDENTIALS
Created the new branch as dev and made some changes to the code and commited
Created Pull Request and merged both the brach
Now event is triggered and compute instance is created using terraform code.
