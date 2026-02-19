AWS Application Migration Service (MGN) is AWS’s lift-and-shift migration service that performs block-level continuous replication from source servers (on-prem, VMware, Azure, etc.) to AWS.

It uses:

* An agent installed on source server

* A Replication Server (staging area in AWS)

* EBS volumes

* Launch templates for target EC2

<img width="5444" height="2604" alt="image" src="https://github.com/user-attachments/assets/69c517e1-ab6d-43c9-a82c-0e3a9b729808" />


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

## Step 1: Agent Installation

* Installed on source OS (Windows/Linux)

* Runs as a service

* Requires outbound access to AWS

## Step 2: Initial Full Sync

 * Agent scans all disks

 * Sends full block-level copy

 * Data sent to Replication Server

 * Replication Server writes to EBS

## Step 3: Continuous Data Replication (CDR)

 * Agent hooks into OS disk driver

 * Captures only changed blocks

 * Sends incremental changes

 * Near real-time replication

## Step 4: Encryption & Compression

 * TLS encrypted

 * Optimized bandwidth usage

⚠ Important: It is block-level, not file-level replication.

### How Replication Server Works

The Replication Server:

 * Auto-launched in staging subnet

 * Small EC2 (t3.small typically)

 * Acts as a data receiver

 * Converts data stream → writes to EBS

 * Handles buffering & retries

It does NOT:

 - Run application

 - Boot OS

 - Process application logic

It is purely a replication engine.

## Detailed Replication Flow Diagram

<img width="362" height="432" alt="image" src="https://github.com/user-attachments/assets/ddd74a7a-679f-467e-a217-ed3d4c80ade4" />


## What Happens During Cutover
When you click Launch Test Instance / Cutover Instance:

 1: MGN stops replication momentarily

 2: Creates latest EBS snapshot

 3: Launches EC2 using launch template

 4: Attaches replicated EBS volumes

 5: Instance boots in AWS

No reinstallation needed. It’s a full machine clone.

<img width="1453" height="1183" alt="image" src="https://github.com/user-attachments/assets/4cd3a477-bc4e-4d53-81c5-be4e58761d44" />


## Network Requirements
 - Outbound TCP 1500

 - HTTPS 443 to AWS endpoints

 - No inbound required on source

If port 1500 is blocked → replication stuck

<img width="1201" height="758" alt="image" src="https://github.com/user-attachments/assets/1704f524-fce5-467b-b1e8-d8ced9d720c0" />


## Key Technical Concepts
| Concept                     | Explanation                |
| --------------------------- | -------------------------- |
| Block-level replication     | Copies raw disk blocks     |
| Continuous Data Replication | Near real-time sync        |
| Staging Area                | Temporary replication zone |
| Crash-consistent copy       | No application awareness   |
| No downtime migration       | Until cutover              |



