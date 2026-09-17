
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

---

## Important Notes


Before we start, you need to understand what the tools we are using do.

- **FTK Imager** is a digital forensics tool used to **acquire, examine, and verify digital evidence** without modifying the original evidence.  
  In this investigation, FTK Imager is used to examine the corrupted image files and verify their basic properties.


- **010 Editor** is a hex editor that allows investigators to **view and edit the raw bytes of a file**.  
  In this investigation, 010 Editor is used to examine the image files at the hexadecimal level and identify corrupted or modified data.


  ---

 ## Investigation

 **Step 1: Initial Approach**

1. Extract the `.rar` file using **WinRAR**.
2. Open **FTK Imager** and attempt to load the forensic image.

  <img width="641" height="553" alt="image" src="https://github.com/user-attachments/assets/ed1e1fdd-8b01-427b-a57d-b3636c6cd2fd" />
  <img width="498" height="375" alt="image" src="https://github.com/user-attachments/assets/dc86a699-1863-4789-90d0-0f514e8ac9b6" />
  <img width="1365" height="1105" alt="image" src="https://github.com/user-attachments/assets/2b575dc5-6e00-402f-ae17-8bdf4abf5f9f" />
  <img width="782" height="591" alt="image" src="https://github.com/user-attachments/assets/42cbdf5d-bed6-4d62-aca7-804ea0b3e38b" />
  <img width="684" height="632" alt="image" src="https://github.com/user-attachments/assets/8d6f09ca-2464-4722-bd00-f7fb272612f1" />



3. FTK Imager crashes while attempting to load the image, indicating that the image may contain corrupted or malformed data.

  <img width="1441" height="763" alt="image" src="https://github.com/user-attachments/assets/6e267285-6091-4472-8394-9913473bab1d" />



 **Step 2: Hexadecimal Analysis**

1. Open the image file in **010 Editor**.

<img width="866" height="593" alt="image" src="https://github.com/user-attachments/assets/83c11603-77ed-40a9-a1ec-e3c1c275985e" />

 
    

<img width="1059" height="245" alt="image" src="https://github.com/user-attachments/assets/d2afb204-522f-423d-ab47-06d5ae95a3b8" />  
<img width="1177" height="846" alt="image" src="https://github.com/user-attachments/assets/a93917aa-3102-45e1-89a1-73aadbdf9b75" />
<img width="987" height="566" alt="image" src="https://github.com/user-attachments/assets/922c9b45-b680-4ab8-8b9c-a0c3799113ad" />
<img width="868" height="402" alt="image" src="https://github.com/user-attachments/assets/9cb8980c-ff66-4124-a348-ba9d02c3d7cb" />
<img width="1331" height="954" alt="image" src="https://github.com/user-attachments/assets/4eba6af6-3b8e-4a6a-9f64-0639a2f13555" />


  

2. Examining the Raw Data.

To make the investigation easier, right-click on the sector numbers and select:  
**Addresses → Display Format → Sector Number (Decimal)**  
This displays the file offset as sector numbers instead of hexadecimal addresses. Since disk images are organized into sectors, viewing the data this way makes it easier to identify the location of the **MBR, partitions, VBR, and other filesystem structures**.

<img width="938" height="453" alt="image" src="https://github.com/user-attachments/assets/36f3d38e-dac4-4e56-a579-ca39302cb641" />

3. Examining the First 512 Bytes & Partition table.

To make the MBR structure easier to understand, download the `drive.bt` binary template from the template repository and load it into **010 Editor**.  
The template helps interpret the first **512 bytes** according to the MBR structure instead of viewing the bytes only as raw hexadecimal values.

<img width="1363" height="843" alt="image" src="https://github.com/user-attachments/assets/c0350c25-bb28-4479-844d-319bf987b268" />

After applying the template, we can identify:

* **Boot Code:** 446 bytes
* **Partition Table:** 64 bytes
* **Boot Signature:** 2 bytes (`55 AA`)

The `55 AA` value appears at the end of the sector and indicates the expected MBR boot signature.


The MBR partition table contains **four partition entries**, with each entry occupying **16 bytes**.

After examining the four entries, we can see that:

* **Partition 0** contains the relevant partition information.
* The other partition entries contain `00` values and do not describe an active partition in this image.

Each partition entry contains several important fields, including the partition type, starting location, and size.

<img width="559" height="381" alt="image" src="https://github.com/user-attachments/assets/42010b6d-3b52-4afc-a38c-64828665eda9" />
<img width="1211" height="861" alt="image" src="https://github.com/user-attachments/assets/918cef3e-e40c-43f0-884b-35be5cb01315" />


### Relative Sector

One important field is **Relative Sector**.

**Relative Sector** specifies the starting sector of the partition relative to the beginning of the disk. It tells the system where the partition begins so that the filesystem data can be located correctly.

In our case, the Relative Sector value is:

```text
1094795585
```

This value does not point to the expected location of the partition.

During the investigation, we identified that the first NTFS filesystem structure begins at **sector 128**.

Therefore, the incorrect Relative Sector value appears to be preventing the partition from being located correctly.

### Total Sectors

Another important field is **Total Sectors**.

**Total Sectors** specifies the number of sectors allocated to the partition. Together with the Relative Sector value, it defines the partition's location and size on the disk.

Conceptually:

```text
Partition Start = Relative Sector
Partition Size  = Total Sectors
Partition End   = Relative Sector + Total Sectors - 1
```

These values are important when reconstructing or validating a damaged partition entry.

### Searching for NTFS Structures

We can also use **Ctrl + F** in 010 Editor to search for the string:

```text
NTFS
```

The search can reveal multiple occurrences of the NTFS filesystem identifier within the image.

It is important to note that finding multiple `NTFS` strings does **not necessarily mean that there are multiple NTFS partitions**. NTFS contains several metadata structures, and filesystem information can appear in different locations.

In this case, the first relevant NTFS structure was identified at:

```text
Sector: 128
```

This provides a useful reference when investigating the incorrect partition start sector.

### Correcting the Partition Entry

Based on the hexadecimal analysis, the **Relative Sector** value appears to be incorrect.

The current value is:

```text
1094795585
```

The expected starting sector identified during the investigation is:

```text
128
```

We therefore change the Relative Sector value from:

```text
1094795585 → 128
```

The modified image is then saved as a **separate working copy**.

> **Important:** The original evidence is not modified. The change is made only to the forensic working copy.

### Testing the Recovery

After saving the modified working copy, load the image again in **FTK Imager**.

The purpose of this step is to determine whether correcting the partition start location allows FTK Imager to correctly recognize and access the partition.

After loading the modified image, we can verify whether:

* The partition is recognized.
* The filesystem is identified correctly.
* The image contents can be accessed.
* The expected files or forensic artifacts are available.

In this case, the modification allows the image to be successfully accessed, and the expected **flag** is recovered.



