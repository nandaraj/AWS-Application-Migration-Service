AWS Application Migration Service (MGN) is AWS’s lift-and-shift migration service that performs block-level continuous replication from source servers (on-prem, VMware, Azure, etc.) to AWS.

It uses:

* An agent installed on source server

* A Replication Server (staging area in AWS)

* EBS volumes

* Launch templates for target EC2


### Components Explained:

1: Source Server

- Physical / VM / Cloud VM

- Has MGN Agent installed

2: MGN Agent

- Captures disk writes at block level

- Compresses & encrypts data

- Sends data over TCP 1500 to AWS

3: Replication Server (Staging Area)

- Lightweight EC2 instance

- Receives data from agent

- Writes to EBS volumes

4: EBS Volumes

- Continuous updated replica disks

5: Launch Template

 - Used to launch final EC2 during cutover


How MGN Agent Works Internally
----------------------------------
Step-by-step flow:

Step 1: Agent Installation

* Installed on source OS (Windows/Linux)

* Runs as a service

* Requires outbound access to AWS
