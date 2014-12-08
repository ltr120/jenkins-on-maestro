This readme file is written by Yi Huang (ltr120).

A few things you need to know:
  a. VM on port 31800 is Jenkins master. Please be careful about it. (The name of that VM is yi-jenkins. It doesn't look like a Jenkins master's name. You may find slaves are not named very well. Sorry about the naming)
  b. Please create VM using VMAPI tools developed by Junxiao (see "Useful knowledge" below).
  c. Please only create VM within port range of 31800-31999. One special case is that Jenkins master runs on host port 8080.
  d. ~/vmports is my own version of keeping track of VM name and host port mapping. There is no strict formatting requirements.

1. Introduction
Jenkins slaves are stateless. We are using labels on slaves and when a job is required to be built under a label, Jenkins master will assign the job to an idle slave with that perticular label. One slave being down probably won't impact the performance of the entire Jenkins very much. This fact gives us time to fix a problematic slave.

Useful knowledge:
http://nrl.cs.arizona.edu/internal/maestro
http://yoursunny.com/p/vmapi/

2. Maintaining Jenkins slaves
Slave status can be tracked on Jenkins website: http://jenkins.named-data.net/computer/

If the slave is offline for any reason, it needs to be fixed. The reason of failure can be vary. Since slaves are all VMs and can be replaced. The worst case is that we found one slave is not fixable or it does not worth the time to fix it (fixing takes more time than provisioning a new slave). In this case, we destroy the slave using Junxiao's VMAPI command and recreate one with same settings.

In order to manage Jenkins slaves, you will need to become an admin of Jenkins master. We currently don't have any automation to do that since when this readme file is written, there are only two Jenkins admin: Alex and Yi. To become an admin of Jenkins, please contact Alex at afanasev@cs.ucla.edu.

3. Creating new Jenkins slave
On Maestro, we can only create Ubuntu slaves. All OS X slaves are in UCLA. This section will introduce how one can create and set up a Jenkins Ubuntu slave for our Jenkins. 

  a. run "create-jenkins-slave" script to create a Ubuntu VM with a 20GB attached VDI and 2GB memory. I am not very confident with this script because it does not have any error handling. Once the script done running without any problem, we should have a VM ready to connect. If anything is wrong and you need to re-run the script, don't forget to reset ~/.ssh/config and ~/vmports since this script add contents to these two files everytime it creates a slave.
  b. If there is a slave created by "create-jenkins-slave" script, or manually created with the similar steps described in "create-jenkins-slave", it is ready to be connected with Jenkins master (i.e. Jenkins master should be able to ssh to the new slave without typing any password). To do this, ssh to Jenkins master node (yi-jenkins). Edit ~/.ssh/config on master node and add entry according to the newly created slave. Then run "ssh-copy-id <vmname>" to copy master's key to slave.
  Also, don't forget to try ssh to that slave manually to make sure it does not need any password to connect to the slave.
  c. Once the above steps are done, it is the time to add the slave via the Jenkins website. On the page http://jenkins.named-data.net/computer/, click on "New Node". Then fill out the node name according to the name pattern (You can figure it out easily by just looking at other nodes' name. I recommend to copy configuration from an existing node. On next page, make sure you edit labels and port number accordingly.
  d. Now you can check log of that slave. You should see that Jenkins master is copying slave.jar to it and trying to run it. If the node does not have Java installed, Jenkins will install Java for you. This step is fully automatic. If nothing goes wrong, the new slave should be up in a few minutes.

4. Patch all VMs
VMs needs to be patched from time to time. The easiest way to patch them all is to run the "patch-all-vms" script.

5. Destroy a slave
Destroy a slave is easy. There are only two steps for that:
  a. Remove that slave from Jenkins website.
  b. Destroy the VM using VMAPI and clean up ~/.ssh/config on related machines.

6. Configuration backup
All valuable configurations are backed up using a Jenkins plugin called "SCM Sync Configuration Plugin". This plugin backs up configuration files to https://github.com/ltr120/ndn-jenkins-config. Please see documentation for this plugin to get more information.

7. Other scripts
I have written other scripts to update/change things for each VM. For example, "check-mem-size" is a simple script which prints out how much memory is assigned to each VM under my account. You probably need to create script to apply changes to all VMs if we found a better way to run the slaves. The existing scripts are always a good reference.
