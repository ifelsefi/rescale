
Rescale	
===============

Deploys Singularity container that runs an Ansible role which deploys 2 EC2 **t3.micro** nodes with 1 **m5.4xlarge** NFS head node.

SSH keys are configured within the container and deployed into AWS.

A quick OpenFoam simulation, [PitzDaily](https://openfoamwiki.net/index.php/PitzDaily), will then be ran across all nodes with results, including log files, copied back to local **/tmp/FOAM_RUN** directory for optional post processing with paraFoam.

Requirements
------------
Install [Singularity](https://github.com/hpcng/singularity/releases) locally.  Working with version 3.5.3 on Linux Mint.

Create an AWS account, [IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) user, and generate your own API keys.

Subscribe to [CentOS 8](https://aws.amazon.com/marketplace/pp/B086WQ6ZPP?qid=1593192867373&sr=0-1&ref_=srh_res_product_title) AMI **ami-0a5bd0847d7badbbf**.

Usage
--------------

After installing Singularity, clone this project then change to working directory that contains the code.

Edit **ansible-aws.def** replacing AWS variables, **AWS_ACCESS_KEY_ID**, etc, with appropriate API keys.

Edit **ansible.aws.def** and change **SINGULARITY_USER** with user name that will run container.

Then build container:

`sudo singularity build /tmp/ansible-aws.sif ansible-aws.def`


Run the container: 

`singularity run /tmp/ansible-aws.sif`


Compiling OpenMPI and OpenFoam will take a while.

Results for the simulation are copied back to host executing the container.

EC2 instances, along with attached storage, are terminated upon completion.

Optional
------------

Ansible playbook output will be printed to stdout so you can see progress.

If you like it's possible to SSH into nodes while Ansible's being ran ideally after RAID and NFS roles are completed.

In separate terminal window enter the container while it's being ran:


`singularity shell /tmp/ansible-aws.sif`


Activate Python Virtual Environment:


`source /venv/ansible/bin/activate`


Configure SSH alias:


`alias ssh="ssh -F /ssh/config"`


Check Ansible dynamic inventory:

`ansible-inventory --list`


Then SSH into head node, ideally as **mpiuser**, with **tag_Type_NFS**.  You could also connect as **ec2-user** then **sudo su - mpiuser.**

Note that passwordless ssh authentication has only been configured across the cluster for **mpiuser** not **ec2-user.**


ParaView
------------

Install [Paraview](https://openfoam.org/download/7-ubuntu/), and run **paraFoam**, to visualize results which were copied to /tmp

TODO
------------

Configure secondary private network in EC2 cluster.

Restrict public SSH access to only head node.

Compile OpenMPI and OpenFoam using gcc march flags.

Install OpenFoam via Singularity container on NFS head node given OpenFoam takes a long time to compile.

Add some more computationally intensive simulations.

