
## Technical Background

Before analyzing the corrupted image, it is important to understand how disks are structured and where partition and filesystem information is stored.

### Partitioning Schemes

Two common partitioning schemes are:

* **MBR (Master Boot Record):** An older partitioning scheme.
* **GPT (GUID Partition Table):** A newer partitioning scheme designed to overcome some of the limitations of MBR.

The partitioning scheme determines how partitions are described on a disk and where important disk information is stored.

### 1. MBR

The **Master Boot Record (MBR)** is located in the **first sector of a disk**. The traditional MBR sector is **512 bytes**.

- Main characteristics:

1. Supports up to **4 primary partitions**.  
2. Traditional MBR partitioning is limited to approximately **2 TiB**.  
3. It is an older partitioning scheme that has been widely used since the 1980s.

The first **512 bytes** are especially important when analyzing an MBR-partitioned disk because they contain the MBR.

<img width="706" height="454" alt="MBR Structure" src="https://github.com/user-attachments/assets/e1f02e99-02e7-46a3-937c-9a487130f2ff" />

The MBR contains three main areas:

* **Code Area:** (446 bytes) Contains the bootloader code responsible for initiating the boot process and locating the active partition.
* **Master Partition Table:** (64 bytes)  Contains **Four** 16-byte per partition entries, describing the partition type, starting location, size, and boot status.
* **Boot Record Signature:** (2 bytes)  Contains the hexadecimal value `55 AA`, which marks the sector as a valid MBR.

The last two bytes contain the MBR signature `(55 AA)`. This signature is commonly used to identify a valid MBR.

When investigating corruption, examining these bytes in a hex editor can help identify:

* Missing or modified boot code
* Damaged partition information
* Invalid values
* A missing or incorrect `55 AA` signature

 **VBR:**

The **Volume Boot Record (VBR)** is located at the beginning of a partition or volume and contains information about the filesystem.

<img width="887" height="221" alt="VBR Structure" src="https://github.com/user-attachments/assets/e750dbe1-0dbd-4acf-8eb7-b2207899dc3b" />

For example, a **VBR** contains important information such as:

* Bytes per sector
* Sectors per cluster
* Reserved sectors
* Number of FATs
* Filesystem-related metadata

The VBR is important during forensic analysis because damage to its values can prevent the operating system or forensic tools from correctly recognizing the filesystem.

### 2. GPT

The **GUID Partition Table (GPT)** is a modern partitioning scheme commonly used with **UEFI** systems.

Main characteristics:

1. Supports a large number of partitions. Windows commonly supports up to **128 partitions** on a GPT disk.
2. Supports disks larger than **2 TiB**.
3. Stores partition information using **GUIDs**.
4. Includes redundant partition information, with a backup GPT structure at the end of the disk.

<img width="1026" height="419" alt="GPT Structure" src="https://github.com/user-attachments/assets/5357ab9e-9bfa-4a4f-a667-6d93548d9688" />

### Lab Focus

For this investigation, we will focus on **MBR-partitioned images** and examine the MBR and filesystem structures to identify the source of the corruption.

## Important Notes


Before we start, you need to understand what the tools we are using do.

- **FTK Imager** is a digital forensics tool used to **acquire, examine, and verify digital evidence** without modifying the original evidence.  
  In this investigation, FTK Imager is used to examine the corrupted image files and verify their basic properties.


- **010 Editor** is a hex editor that allows investigators to **view and edit the raw bytes of a file**.  
  In this investigation, 010 Editor is used to examine the image files at the hexadecimal level and identify corrupted or modified data.


