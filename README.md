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
  
