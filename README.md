# Forensics-lab-image-recovery  

CyberLab-08


# Corrupted Image Forensics Investigation

<img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/e061c4b9-0797-4f12-bba6-3ca8fd62a26a" />


## Overview

This project documents the forensic analysis and recovery of two corrupted image files using **FTK Imager** and **010 Editor**.  
You are a forensic investigator assigned to examine two image files suspected of being tampered with. The files are corrupted and cannot be opened normally.  
Your task is to determine the cause of the corruption and recover the original images without modifying the original evidence.  

- **Note:** All analysis and recovery attempts must be performed on copies of the evidence to preserve its integrity.


## Objectives

- Analyze file headers and metadata.
- Identify corruption using hexadecimal analysis.
- Recover the images when possible.

## Tools Used

- FTK Imager
- 010 Editor

## Investigation Process

### Step 1: Initial Examination

- Loaded the files into FTK Imager.
- Verified file size and timestamps.
- Examined metadata.

### Step 2: Hex Analysis

- Opened images in 010 Editor.
- Located corrupted bytes and missing headers.

### Step 3: Recovery

- Repaired the damaged header.
- Corrected invalid hexadecimal values.
- Saved a recovered copy.
- Verified that the image could be opened successfully.





