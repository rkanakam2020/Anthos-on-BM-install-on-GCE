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

Step 4:
Configure the network with vxlan
Run the shell script vxlan.sh in the repo

Step 5:
Generate ssh keys

ssh-keygen # don't enter any passphrase, use default file paths 
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
# verify you can login without password
ssh [your-username]@[ip address of your machine] # check setenv.sh script for the ip

Step 6:
Generate SSH keys for root:

sudo bash
ssh-keygen # don't enter any passphrase, use default file paths 
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
# verify you can login without password
ssh root@[ip address of your machine] # check setenv.sh script for the ip

Step 7:
Install docker and make sure it is accessible as non-root user

sudo apt-get install -y docker
sudo apt-get install -y docker.io
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker 
docker run hello-world

Step 8:
setup bmctl

cd ~
gsutil cp gs://anthos-baremetal-release/bmctl/1.8.2/linux-amd64/bmctl bmctl
chmod a+x bmctl

Step 9:
Generate the cluster config and create the cluster

cd $HOME
./bmctl create config -c [cluster-name] \
  --enable-apis --create-service-accounts --project-id=$PROJECT_ID
  
  Step 10:
  cp Anthos-on-BM-install-on-GCE/single-gce-abm-with-vxlan.yaml bmctl-workspace/[cluster-name]/[cluster-name].yaml
  
Step 11:
Create the cluster

sudo bash
gcloud auth application-default login
. ./Anthos-on-BM-install-on-GCE/setenv.sh
./bmctl create cluster -c gce-abm-cluster

Step 12:
Generate cluster login by running the script cluster-login-token.sh
