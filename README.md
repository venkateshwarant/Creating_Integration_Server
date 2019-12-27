# Creating Integration Server

Follow the below steps to create the integration server
1. Run the Vagrant file
2. You can see that following task are done by the vagrant file
  * The VM is automatically provisioned using Ansible playbooks.
  * These playbooks install GitLab and Docker.
  * GitLab is used as VCS and CI, whereas Docker is used to handle the integration environments.
  * We have downloaded the katalon Runtime Engine and unzipped it in the home directory, changed 
  * Downloaded JavaRunTime environment to run the TestNG automation test cases
3. Follow instruction in tutorial https://github.com/acapozucca/devops/tree/master/pipeline/s2-automate-build to setup our integration server.

4. Follow tutorial in https://github.com/venkateshwarant/UAT to Create a product and write automation for our product and integrate those in our integration server pipeline.
