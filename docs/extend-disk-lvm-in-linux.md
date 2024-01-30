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

**Example:** according to the illustration below, we have a VG created from 3 PVs. On it, we create 3 LVs, and one of the LVs runs on 2 different PVs.

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/lvm.png)

Some basic features of LVM:
- Moving LV between different PVs.
- Changing the size of VG online by attaching or detaching PVs.
- Changing the size of LV by altering the number of extents in the PV.
- Creating snapshots of the LVs (preserving the entire state of the LV at that moment)."

## LVM Thin provisioning

Thin Provisioning is a feature of allocating hard drive space based on the flexibility of LVM. Suppose we have a **Volume Group**, we will create **a Thin Pool** from this **VG** with a capacity of 20GB for multiple clients to use. Let's say we have 3 clients, each allocated 6GB of storage. Thus, we have 3 x 6GB which is 18GB. With traditional allocation techniques, we would only be able to allocate an additional 2GB to the fourth client.

![](https://vdigital-cloud.github.io/vdigital-docs/assets/image/what-is-thin-provisioning.png)

_Comparison between traditional allocation and **Thin Provisioning**_

However, with Thin Provisioning technique, we can still allocate an additional 6GB to the fourth client. This means 4 x 6GB = 24GB, which is greater than the initial 20GB. The reason we can do this is because although each user is allocated 6GB, they usually do not use up all of this capacity (if all four clients use up their allocation, it would lead to a situation of Over Provisioning). We assume that if they don't use up all the allocated capacity, then nominally each person gets 6GB, but in reality, they only use as much capacity as they need, and the system allocates more as required.

With the normal allocation mechanism, LVM allocates a continuous block of storage each time a user creates a new volume. But with the thin pool mechanism, LVM only allocates hard drive blocks (which are a collection of pointers pointing to the hard drive) when actual data is written to it. This approach helps to save storage space for the system and optimizes storage capacity utilization. However, the drawback is that it can cause system fragmentation and lead to Over Provisioning, as mentioned above.

## Guide to extending LVM

