---
title: Migrating Windows to a new SSD with Clonezilla
date: 2023-07-06
tags: ["tech", "general"]
---

Recently I have found myself lacking space on my system drive. Luckily large, fast NVMe M.2 SSDs are cheap now.
Not so luckily, my system has a single M.2 slot, and my Windows installation has so much configured/installed
just _the way I like it_ that I could not fathom the thought of throwing it all away and starting from scratch. (Yeah, yeah, I know, backups and stuff.)

So, the high-level solution was somewhat obvious - dump the drive to an image on my SATA Hard Drive that has plenty of space, swap SSDs, and flash the image. 

Of course, your drive `C:\` on Windows is only _one_ of the partitions that Windows creates during 
its installation. In my case, my SSD had 4 partitions in total. So now I have to not only create/flash
the image but recreate the partition table manually and `dd` each of those partitions separately. The thought of so much manual work was just unbearable.

## Enter Clonezilla!

Here are the basic steps for migrating (images)

1. Download the ISO from the [Clonezilla website](https://clonezilla.org/downloads/download.php?branch=alternative) and create a bootable USB with [Rufus](https://rufus.ie/en/) for example.

2. Before you turn off your Windows machine and boot into Clonezilla, you need to set up a few things first:

    2.1. Disable **hibernation** - Modern Windows systems do not actually _fully_ shutdown - they hibernate. Meaning it's not safe for Clonezilla to mount partitions from them because there still might be processes that are reading/writing. For instructions on how to turn on/off hibernation, check: [How to disable and re-enable hibernation on a computer that is running Windows
](https://learn.microsoft.com/en-us/troubleshoot/windows-client/deployment/disable-and-re-enable-hibernation).

    2.2. Create a folder on your HDD just for the backup - backups are not a single file, so create a folder that you easily and quickly find in a TUI to store the SSD image.


3. Power off your machine and boot into Clonezilla.

4. Save a disk image of your NVMe SSD by following the steps at [Save disk image](https://clonezilla.org/show-live-doc-content.php?topic=clonezilla-live/doc/01_Save_disk_image) (they even have nice pictures).
    > Note: your SSD will most likely be listed as /dev/nvme#p# and your HDD as /dev/sd(a,b,c)#, so they will be easy to distinguish from each other.

5. Shut down and swap the SSDs.

6. Boot into Clonezilla and restore the disk image by following the steps at [Restore Disk Image](https://clonezilla.org/show-live-doc-content.php?topic=clonezilla-live/doc/02_Restore_disk_image).

 > Note: Clonezillla will ask you if you want to resize the partitions proportionally. While this might lead to a bit larger Windows system partitions than actually needed, it avoids the need to mess with devmgmt.msc later, so I suggest you go with that.

 7. Power off Clonezilla, remove the bootable USB, and boot into Windows. Your system will be up and running as if nothing happened (just that your Windows drive is magically larger).

 8. That's it! Don't forget to turn Windows hibernation back on, as it can significantly improve boot times.

 ## Video Guides

 
[Home Tech Adventure](https://www.youtube.com/@hometechadventure4462) has created a two-part YouTube series going over just this topic, such that if you feel lost at some point, 
you can refer to his videos, since he goes through all the steps in exactly the right order.

    > Note: He starts with a blank unformatted HDD which he uses as a repository for the disk image. If your HDD is already formatted, you can just skip to the "Boot from USB" part of his video.


{{< youtube Q0A9SqBvy1s >}}

{{< youtube eVOihefKE7w >}}


## Conclusion

All in all, Clonezilla's TUI is a bit rough and can be scary for new users, but the overall process of creating and flashing an image is _very_ straightforward if you have prepared a bit upfront. 
Given that this tool is FOSS, why pay for a commercial piece of software whose only advantage is a pretty GUI 🙂.

