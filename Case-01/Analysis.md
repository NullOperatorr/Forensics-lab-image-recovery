
## Technical Background

Before analyzing the corrupted image, it is important to understand how disks are structured and where partition and filesystem information is stored.

### Partitioning Schemes

Two common partitioning schemes are:

* **MBR (Master Boot Record)** — an older partitioning scheme.
* **GPT (GUID Partition Table)** — a newer partitioning scheme that supports larger disks and more partitions.

The partitioning scheme determines how partitions are described on a disk and where important disk information is stored.

### MBR

The **Master Boot Record (MBR)** is located in the **first sector of a disk**.

The traditional MBR sector is **512 bytes** and contains:

```text
+---------------------------+
| Boot Code      | 446 bytes|
+---------------------------+
| Partition Table|  64 bytes|
+---------------------------+
| Signature      |   2 bytes|
+---------------------------+
| Total          | 512 bytes|
+---------------------------+
```

The last two bytes contain the MBR signature:

```text
55 AA
```

This signature is commonly used to identify a valid MBR.

### VBR

The **Volume Boot Record (VBR)** is located at the beginning of a partition/volume and contains information about the filesystem.

For example, a **FAT32 VBR** contains important information such as:

* Bytes per sector
* Sectors per cluster
* Reserved sectors
* Number of FATs
* FAT size
* Root directory information
* Filesystem-related metadata

A simplified FAT32 structure can be represented as:

```text
Disk
│
├── MBR / Partition Information
│
└── Partition
    │
    └── VBR
        ├── FAT
        ├── FAT
        └── Data Area
```

### First 512 Bytes

The first **512 bytes** are especially important when analyzing an MBR-partitioned disk because they contain the MBR.

When investigating corruption, examining these bytes in a hex editor can help identify:

* Missing or modified boot code
* Damaged partition information
* Invalid values
* A missing or incorrect `55 AA` signature

However, the meaning of the first 512 bytes depends on the storage layout. With **GPT**, the first sector contains a **protective MBR**, while the GPT header is stored in the following sector.

In this investigation, hexadecimal analysis will be used to examine these structures and determine whether partition or filesystem information has been modified.






## Important Notes


Before we start, you need to understand what the tools we are using do.

- **FTK Imager** is a digital forensics tool used to **acquire, examine, and verify digital evidence** without modifying the original evidence.  
  In this investigation, FTK Imager is used to examine the corrupted image files and verify their basic properties.


- **010 Editor** is a hex editor that allows investigators to **view and edit the raw bytes of a file**.  
  In this investigation, 010 Editor is used to examine the image files at the hexadecimal level and identify corrupted or modified data.


