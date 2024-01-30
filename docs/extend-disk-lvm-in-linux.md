---
layout: default
title: Extend disk lvm in linux
nav_order: 5
---

# Extend disk lvm in linux
{: .no_toc}

## table of content
{: .no_toc .text-delta}

1. TOC
{:toc}

---

## Introduction

LVM (Logical Volume Management) is a technology that helps manager data storage devices on the Linux OS. This technology allows users to group physical hard drives together and partition them into smaller sections, easily expanding these partitions when necessary.

Some advantages of LVM:
- Easily manage a large number of hard drives.
- Flexibly adjust hard drive partitions.
- Backup the system by snapshotting hard drive partitions (real-time).
- Easily migrate data.

## Operating model

Some concepts to understand clearly when dealing with LVM:
- **Physical Volume – PV:** Physical storage devices in the system (hard drives, partitions, iSCSI LUNs, SSDs, etc.) are the basic units used by LVM to create Volume Groups. Each Physical Volume (PV) contains about a 1 MB header that records data about the distribution of the Volume Group that contains it. This header greatly assists in data recovery in the event of a failure.
- **Volume Group – VG:** It is the aggregation of physical hard drives (PVs) into a common storage pool with the total capacity of the individual disks. Whenever a PV is added to a VG, LVM automatically divides the capacity on the PV into multiple Physical Extents of equal size. From the VG, multiple Logical Volumes can be created and their sizes easily adjusted.
![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/volume-group-vg.png)
- **Logical Volume – LV:** These are logical partitions created from a VG. Logical Volumes are similar to normal hard drive partitions but more flexible, as the size of an LV can be easily changed in real time without disrupting the system. The ease of resizing LVs is due to their division into multiple Logical Extents, each of which is mapped correspondingly to a Physical Extent on the drives.
![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/logical-volume-lv.png)
- **Extent:** An extent is the smallest unit of a VG. Each volume created from a VG contains many small extents of equal fixed size. The extents on an LV do not necessarily have to be contiguous on the underlying physical hard drive but can be scattered across different drives. Extents are the foundation of LVM technology, allowing LVs to be expanded or reduced by adding or removing extents from the volume.

In summary, with LVM technology, we can combine multiple physical hard drives, Physical Volumes, into a Volume Group to consolidate all the necessary storage resources. Subsequently, the administrator can divide the Volume Group into various Logical Volumes as desired and flexibly. Each Logical Volume consists of many extents, and when expanding a Logical Volume, we add some extents, and when reducing, we remove some extents.