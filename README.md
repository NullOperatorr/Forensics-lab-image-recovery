# Forensics-lab-image-recovery  

CyberLab-08


# Corrupted Image Forensics Investigation

<img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/e061c4b9-0797-4f12-bba6-3ca8fd62a26a" />


## Overview

You are a forensic investigator assigned to examine two image files suspected of being tampered with. The files are corrupted and cannot be opened normally.  
Your task is to determine the cause of the corruption and recover the original images without modifying the original evidence.  

- **Note:** All analysis and recovery attempts must be performed on copies of the evidence to preserve its integrity.


## Case Summary

| Item | Details |
|---|---|
| **Case** | Forensic Image Corruption Recovery |
| **Evidence** | Two corrupted image files |
| **Tools** | FTK Imager, 010 Editor |
| **Objective** | Identify the corruption and recover the original images |
| **Result** | Images successfully recovered and verified |



## Objectives

- Analyze file headers and metadata.
- Identify corruption using hexadecimal analysis.
- Recover the images when possible.
- Verify the recovered images.

## Tools Used

- FTK Imager
- 010 Editor

## Investigation Process

### Step 1: Initial Examination

- Loaded the files into FTK Imager.  
- Checked the file size and timestamps.  
- Examined the available metadata.  
- Confirmed that the images could not be opened normally.  

### Step 2: Hex Analysis

- Opened the corrupted images in 010 Editor.  
- Examined the file headers and hexadecimal data.  
- Located corrupted or missing bytes.  

### Step 3: Recovery

- Repaired the damaged header where possible.  
- Corrected invalid hexadecimal values.  
- Verified that the recovered image could be opened successfully.  


## Conclusion

- The investigation successfully identified corruption within the two image files. Using FTK Imager for the initial examination and 010 Editor for hexadecimal analysis, the damaged file structures were identified and recovery was attempted successfully.

##  Lessons Learned  

- Always preserve the original evidence before starting any analysis.
- File headers are important for identifying and recovering corrupted files.
- Hexadecimal analysis can reveal corruption that is not visible through normal file examination.
- Recovery should always be performed on a copy, never on the original evidence.
- After recovery, the resulting files should be verified to confirm that they are usable and consistent with the investigation findings.


---
---


