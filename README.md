# Anthos-on-BM-install-on-GCE
Installation steps for anthos on BM on a GCE VM
This details steps for a test installation of Anthos on a Google Compute engine VM.
Pre-requisites:
     A Google cloud compute engine VM
     gcloud with owner/editor permissions in the VM
 Step 1:
Create the VM instance in GCE:
	 gcloud compute instances create abm-gce \
              --image-family=ubuntu-2004-lts --image-project=ubuntu-os-cloud \
              --zone=us-west1-a \
              --boot-disk-size 200G \
              --boot-disk-type pd-ssd \
              --can-ip-forward \
              --network default \
              --tags http-server,https-server \
              --min-cpu-platform "Intel Haswell" \
              --scopes cloud-platform \
              --machine-type n1-standard-32
							
Step 2:
SSH in to the VM:
gcloud compute ssh abm-gce

Step 3:
Clone the repository:
git clone https://github.com/rkanakam2020/Anthos-on-BM-install-on-GCE.git

