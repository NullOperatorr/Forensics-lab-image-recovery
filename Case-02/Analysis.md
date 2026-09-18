
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
  <img width="1172" height="974" alt="image" src="https://github.com/user-attachments/assets/7ab63d4d-ea17-4811-81ee-5cba8cc7f799" />
 <img width="1038" height="791" alt="image" src="https://github.com/user-attachments/assets/c1ecf68e-d127-4eff-8427-5725fd8f37c9" />
  <img width="684" height="632" alt="image" src="https://github.com/user-attachments/assets/8d6f09ca-2464-4722-bd00-f7fb272612f1" />



3. FTK Imager crashes while attempting to load the image, indicating that the image may contain corrupted or malformed data.

  <img width="1441" height="763" alt="image" src="https://github.com/user-attachments/assets/6e267285-6091-4472-8394-9913473bab1d" />


---


 **Step 2: Hexadecimal Analysis**

- **1. Open the image file in **010 Editor**.**

<img width="866" height="593" alt="image" src="https://github.com/user-attachments/assets/83c11603-77ed-40a9-a1ec-e3c1c275985e" />

- **2. Examining the Raw Data.**

To make the investigation easier, right-click on the sector numbers and select:  
**Addresses → Display Format → Sector Number (Decimal)**  
This displays the file offset as sector numbers instead of hexadecimal addresses. Since disk images are organized into sectors, viewing the data this way makes it easier to identify the location of the **MBR, partitions, VBR, and other filesystem structures**.

<img width="938" height="453" alt="image" src="https://github.com/user-attachments/assets/36f3d38e-dac4-4e56-a579-ca39302cb641" />

- **3. Examining the First 512 Bytes & Partition table.**

To make the MBR structure easier to understand, download the `drive.bt` binary template from the template repository and load it into **010 Editor**.  
The template helps interpret the first **512 bytes** according to the MBR structure instead of viewing the bytes only as raw hexadecimal values.

<img width="1363" height="843" alt="image" src="https://github.com/user-attachments/assets/c0350c25-bb28-4479-844d-319bf987b268" />
<img width="559" height="381" alt="image" src="https://github.com/user-attachments/assets/42010b6d-3b52-4afc-a38c-64828665eda9" />

After applying the template, we can identify:

* **Boot Code:** 446 bytes
* **Partition Table:** 64 bytes
* **Boot Signature:** 2 bytes (`55 AA`)

The `55 AA` value appears at the end of the sector and indicates the expected MBR boot signature.


The MBR partition table contains **four partition entries**, with each entry occupying **16 bytes**.

After examining the four entries, we can see that:

* **Partition 0** contains the relevant partition information.
* The other partition entries contain `00` values and do not describe an active partition in this image.

<img width="1004" height="840" alt="image" src="https://github.com/user-attachments/assets/9e2e30a9-721c-4c98-81c5-2f1e1ca81e60" />



- **4. Relative Sector & Total Sector**

**Relative Sector** specifies the starting sector of the partition relative to the beginning of the disk. It tells the system where the partition begins so that the filesystem data can be located correctly.    
In our case, the Relative Sector value is `1094795585` and this value does not point to the expected location of the partition.   
**Total Sectors** specifies the number of sectors allocated to the partition. Together with the Relative Sector value, it defines the partition's location and size on the disk.

```text
Partition Start = Relative Sector
Partition Size  = Total Sectors
```

<img width="764" height="427" alt="image" src="https://github.com/user-attachments/assets/2dd3e9eb-8770-4bda-ba7a-12322e7b1c2a" />


- **5. Searching for NTFS Structures**

We can also use **Ctrl + F** in 010 Editor to search for the string `NTFS`
The search can reveal multiple occurrences of the NTFS filesystem identifier within the image.
In this case, the first relevant NTFS structure was identified at sector `128` and this provides a useful reference when investigating the incorrect partition start sector.

<img width="948" height="786" alt="image" src="https://github.com/user-attachments/assets/5f0d99cb-3793-455e-a592-2e604c2b35cc" />



- **6.Correcting the Partition Entry**

Based on the hexadecimal analysis, the **Relative Sector** value appears to be incorrect and the expected starting sector identified during the investigation is `128` therefore change the Relative Sector value from `1094795585 → 128`

<img width="904" height="953" alt="image" src="https://github.com/user-attachments/assets/4548855c-98a7-41d2-86de-7d17b1e06652" />

Since the Total Sectors current value is incorrect, we use the **4th NTFS structure** to identify the end of the partition and calculate the correct number of sectors using `Total Sectors = Ending Sector - Relative Sector `.

<img width="1277" height="960" alt="image" src="https://github.com/user-attachments/assets/17b00e91-e096-45f9-9309-1a5801187c1f" />

We found the 4th NTFS structure at sector `202,879`. Using the partition's Relative Sector `128`, we calculate the partition size as `202,879 - 128  = 202,751` Total Sectors, and update the value.

<img width="816" height="549" alt="image" src="https://github.com/user-attachments/assets/8cae6154-c5e3-4d96-a223-db7567eb2d33" />



**7. Testing the Recovery**

After saving the modified working copy, load the image again in **FTK Imager**.  

<img width="712" height="657" alt="image" src="https://github.com/user-attachments/assets/d1502d84-2c3a-44f7-9dbb-9849142d78d8" />
<img width="634" height="593" alt="image" src="https://github.com/user-attachments/assets/706d89fe-8674-4c43-8760-33550d91a3e5" />



After loading the modified image, we can verify whether:

* The partition is recognized.
* The filesystem is identified correctly.
* The image contents can be accessed.
* The expected files or forensic artifacts are available.

In this case, the modification allows the image to be successfully accessed.



