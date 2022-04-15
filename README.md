# RonR-RaspberryPi-image-utils

**NB** scruss is *not* the author or maintainer of thse files. No — and I mean **no** — issues will be accepted on this repo. Take it up in the [Image File Utilities](https://www.raspberrypi.org/forums/viewtopic.php?t=247568#p1511694) thread.

Tools to create a backup of a running Raspbian system to an SD card image file.

This is a copy of the files that [Raspberry Pi Forums](https://www.raspberrypi.org/forums/index.php) user [RonR](https://www.raspberrypi.org/forums/memberlist.php?mode=viewprofile&u=186692) posted to the topic [Image File Utilities](https://www.raspberrypi.org/forums/viewtopic.php?t=247568#p1511694) on 2019-08-01. File attachments to this forum don't seem to be persistent, so this repo was created to ensure a working snapshot is always available.

Copies of the original post/attachment are also available on the [Internet Archive](https://archive.org/):

* article: https://web.archive.org/web/20190824163430/https://www.raspberrypi.org/forums/viewtopic.php?t=247568

* attachment: https://web.archive.org/web/20190824162104/https://www.raspberrypi.org/forums/download/file.php?id=31366&sid=107ba04af18e19ad587c5bcf8ebacd38

**NB from repo creator**: This is not my code. I don't use it, but I saw that others do and were being frustrated by the forum download links expiring. I won't be responding to issues posted here.

Original README converted to markdown follows:

---------------------------------------------------------------------

## image-backup:

`image-backup` creates a backup of a running Raspbian system to a standard 'raw' image file that can be written to an SD card or a USB device card with Etcher, imageUSB, etc. It will also perform incremental updates to an existing backup image file.

Running image-backup with no parameters will create a full backup. To create the smallest possible image, specify an Image ROOT filesystem size of 0 to determine the minimum allowed size. If you plan to incrementally update the image file, specify a considerably larger size to allow for additional growth.

Running image-backup with a parameter of an existing image filename will incrementally update that image file.


## image-check:

`image-check` will check the integrity of a standard 'raw' image file.  Usage is:

    image-check imagefile [W95|Linux]

where *W95* checks the BOOT partition and *Linux* checks the ROOT partition.  If neither is specified, *Linux* is assumed.


## image-compare:

`image-compare` compares a running Raspbian system to an existing standard 'raw' image file and displays the incremental changes that image-backup would perform if run.  Usage is:

    image-compare [imagefile]


## image-mount:

`image-mount` mounts a standard 'raw' image file to allow it to be read or written as if it were a device.  Usage is:

    image-mount imagefile mountpoint [W95|Linux]

where *W95* mounts the BOOT partition and *Linux* mounts the ROOT partition.  If neither is specified, *Linux* is assumed.


## image-set-ptuuid:

`image-set-ptuuid` sets the Partition Table UUID value of a standard 'raw' image file.  Usage is:

    image-set-ptuuid imagefile ptuuid

where *ptuuid* is 8 hex digits


## image-shrink:

`image-shrink` shrinks a standard 'raw' image file to its smallest possible size (plus an optional additional amount of free space).  Usage is:

    image-shrink imagefile [Additional MB]

where *Additional MB* is an additional amount of free space to be added.

## Usage:

(https://raspberrypi.stackexchange.com/questions/120124/how-to-copy-sd-card-whithout-copying-the-unallocated-space/120154#120154)

## Question:

>I have a 32Gb SD card and I want to make a light copy of my os to make it work on a 16Gb SDcard.

## Answer - use `image-backup` 

Perhaps the easiest way to do this is with the `image-backup` utility - part of `image-utils`. If you are running RPi OS, `image-backup` has these advantages: 

* `image-backup` may be done on a "live" system: no need to shut down the system, or unmount the device. 

* `image-backup` is fast: **3min:55sec elapsed time** to create a bootable image on RPi 4B from a 32GB SD card; activities included in elapsed time: 
   * Includes *interactive* dialog w/ `image-backup` & USB HDD I/O (USB-3 port).  
   * Includes 3x `resize2fs` and `e2fsck` on the finished `ext4` partition - part of `image-backup`
   * On a RPi 3B: **13min:42sec** from a 16GB SD card to USB "thumb" drive (USB-2 port). 
   * Elapsed times measured as follows: 
   ```
   $ echo $(date) && sudo ./image-backup && echo $(date)  
   ```

* `image-backup` produces a small raw image file: 
   * image file contains *only* actual data on SD card - not the entire partition (à la `dd`) 
   * 32GB GB SD card `-->` 4.05GB  
   For comparison: 
   ```
   $ df -h
   Filesystem             Size  Used Avail Use% Mounted on
   /dev/root               29G  5.0G   23G  18% /
   /dev/mmcblk0p1         256M   47M  209M  19% /boot
   ``` 
* `image-backup` produces an **.img** file that can be *flashed* to another SD card, USB drive, etc using common tools such as `etcher` and `rufus`.  

* `image-backup` is free (no cost) and open sourced `bash` script.

As its name implies, the primary purpose of `image-backup` is making a **live backup** of an RPi. *Coincidentally*, it's also a good solution to **shrinking** the OS to fit on a smaller SD card. 

You can [download a copy of `image-utils` from this location](https://forums.raspberrypi.com/download/file.php?id=53282&sid=79adb1b7a65e23de9520a5b13d75f038). The instructions (in the `README` file) may be a bit fuzzy at first, but it's very simple to use. If you have problems, you can post questions to the author in the forum, or ask a specific question here. Alternatively, you can [install on your RPi using `git` from here.](https://github.com/seamusdemora/RonR-RPi-image-utils)

## How will `image-backup` address my question? 

Once you've *"installed"* it, you can make a complete backup of your entire system to an **image file**. By "**image file**", I mean a file that can be flashed to your (smaller) SD card using `etcher` or `rufus` (for example), and then booted in your RPi. 

`image-backup` compresses the image file, and installing to a smaller (32GB->16GB) SD card will not be a problem based the info in your question. 

## Show me how...

There is no *package* for `image-utils`, so `apt` cannot be used to install it. However, `image-utils` is just a collection of scripts (text files), and very easy to install; you may install manually, or via `git`:

### Manual installation of `image-utils` on your RPi:

Open a terminal window (or via SSH) in your RPi, and from your home directory (`/home/pi`): 

```bash
$ mkdir image-utilities
$ cd image-utilities
$ wget -O image.zip https://www.raspberrypi.org/forums/download/file.php?id=39167
$ unzip ./image.zip 
$ chmod a+x image-*
```  

### run `image-backup`
At this point, `image-backup` is ready to use, but you will need to have a USB drive mounted (or any drive under `/mnt`) as a destination for the image file it will create. Once your USB drive is mounted, you may create the image file as follows: 

```bash
$ sudo ./image-backup
```

* **`image-backup` will prompt you for the options needed:**

   ```
   Image file to create? /mnt/Passport2TB/img_backups/20210113_Pi4B_imagebackup.img
   ```
> NOTE: A full path specification to the image file you want to create. The drive must be mounted under `/mnt` or `/media`. I have embedded a date & machine id in my filename. Then, ↵ 
   ```
   Initial image file ROOT filesystem size (MB) [5933]?
   ```
> NOTE: Accept default ( ↵ )
   ```
   Added space for incremental updates after shrinking (MB) [0]?
   ```
> NOTE: Accept default ( ↵ )
   ```
   Create /mnt/Passport2TB/img_backups/20210113_Pi4B_imagebackup.img (y/n)? y
   ```
> NOTE: Enter `y`, then ↵ 

   ```
   Starting full backup (for incremental backups, run: ./image-backup /mnt/Passport2TB/img_backups/20210113_Pi4B_imagebackup.img)

   ``` 
> NOTE: The instructions for making an *incremental* backup are N/A here because you are running `image_backup` on a one-time basis.  
> Additional output will show 3 iterations of `resize2fs` to reduce the image file to its smallest possible size, and upon completion, `e2fsck` will be run to verify the integrity of the filesystem. 

* **You should now have an image file that is ready to be flashed to your spare SD card.**

### Verify & inspect the image file

I have used an external drive mounted at `/mnt/Passport2TB` to store my backup image - in a folder named `img_backups`. Your external drive will likely be different than mine. You can verify your external drive is mounted by using the `lsblk --fs` command. 

List the contents of `/mnt/Passport2TB/img_backups` to verify the image file is there, and check its size: 

```bash
$ ls -l /mnt/Passport2TB/img_backups 
total 28533608

...

-rw-r--r-- 1 root root  3921674240 Jan 13 18:48 20210113_Pi4B_imagebackup.img
```

Note that the size of my image file is 3.92GB. Yours will likely be a different size based on which version of the OS you've installed (I use the ***Lite*** version of RPi OS). To provide some context for comparison, let's look at how much space is being used on my RPi 4B:

```bash
$ df -h -t ext4
Filesystem      Size  Used Avail Use% Mounted on
/dev/root        29G  4.9G   23G  18% /
```

And so, in my case, `image-backup` reduced 4.9GB to a 3.92GB `.img` file - or about a 20% reduction. 
