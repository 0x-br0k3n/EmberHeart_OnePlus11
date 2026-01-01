# Fix Slow Boot & Install NetHunter on Android 16+

On Android 16+, storing thousands of NetHunter files directly on `/data` causes massive boot delays (5-6+ minutes) due to stricter system integrity checks.

### Step 1: Download the Rootfs

Download the official Kali NetHunter rootfs (choose one):

* **Full (Recommended):** [Download Link](https://kali.download/nethunter-images/current/rootfs/kali-nethunter-rootfs-full-arm64.tar.xz)
* **Minimal:** [Download Link](https://kali.download/nethunter-images/current/rootfs/kali-nethunter-rootfs-minimal-arm64.tar.xz)

### Step 2: Create the Image Container

Open a terminal (Termux/ADB) as root (`su`) and run the following.
*Note: The full rootfs requires ~10GB. We will create a 20GB image to allow space for tools.*

```bash
# 1. Create a 20GB image file (count=20000 is ~20GB)
dd if=/dev/zero of=/sdcard/nethunter.img bs=1M count=20000 status=progress

# 2. Format it as ext4
mke2fs -t ext4 /sdcard/nethunter.img

```

### Step 3: Install Kali into the Image

Mount the image temporarily to install or migrate your system.

```bash
mkdir -p /mnt/tmp_nh
mount -t ext4 -o rw /sdcard/nethunter.img /mnt/tmp_nh

```

#### **Option A: Migrating an Existing Install**

*Use this if you already have NetHunter installed and want to keep your data.*

1. **Stop NetHunter** completely.
2. Run this command to copy your system:
```bash
# cp -ax prevents copying external mounts like /sdcard
cp -ax /data/local/nhsystem/kali-arm64/. /mnt/tmp_nh/

```


3. Once finished, you can delete the old files from the NetHunter app or manually (`rm -rf /data/local/nhsystem/kali-arm64/*`).

#### **Option B: Fresh Install**

*Use this if you are starting from scratch.*
Run one of the commands below depending on which file you downloaded in Step 1:

**For Full Rootfs:**

```bash
busybox xz -d -c /sdcard/Download/kali-nethunter-rootfs-full-arm64.tar.xz | tar -xvf - -C /mnt/tmp_nh

```

**For Minimal Rootfs:**

```bash
busybox xz -d -c /sdcard/Download/kali-nethunter-rootfs-minimal-arm64.tar.xz | tar -xvf - -C /mnt/tmp_nh

```

**Fix Folder Structure (Fresh Install Only):**
The tarball extracts into a subfolder. Move files to the root of the image:

```bash
mv /mnt/tmp_nh/kali-arm64/* /mnt/tmp_nh/
rmdir /mnt/tmp_nh/kali-arm64

```

#### **Finalize Step 3**

Unmount the temporary image:

```bash
umount /mnt/tmp_nh

```

### Step 4: Auto-Mount on Boot

We need a script to mount the image automatically so the NetHunter app can see it.

1. **Prepare the mount point** (Crucial step!):
```bash
mkdir -p /data/local/nhsystem/kali-arm64

```


2. **Create the Magisk Service Script:**
File: `/data/adb/service.d/mount_nh.sh`
```bash
#!/system/bin/sh

IMG_PATH="/sdcard/nethunter.img"
MNT_POINT="/data/local/nhsystem/kali-arm64"

# Wait for SD card to be ready
while [ ! -f "$IMG_PATH" ]; do
  sleep 2
done

sleep 3
mount -t ext4 -o rw "$IMG_PATH" "$MNT_POINT"

```


3. **Make it executable:**
```bash
chmod +x /data/adb/service.d/mount_nh.sh

```



### Step 5: Safe "Stop" Script

The NetHunter app's "Stop" button may crash your phone because it tries to unmount the image while system bindings are active. Use this script instead.

**Save this as `/data/local/tmp/stop_nh`:**

```bash
#!/system/bin/sh
NH_PATH="/data/local/nhsystem/kali-arm64"

echo "Killing processes..."
pgrep -f "kali" | grep -v $$ | xargs kill -9 2>/dev/null

echo "Unmounting bindings..."
umount -l "$NH_PATH/dev/pts" 2>/dev/null
umount -l "$NH_PATH/dev" 2>/dev/null
umount -l "$NH_PATH/proc" 2>/dev/null
umount -l "$NH_PATH/sys" 2>/dev/null
umount -l "$NH_PATH/sdcard" 2>/dev/null

echo "Done. (Image remains mounted for next time)"

```

**Usage:**
To stop NetHunter, run: `su -c /data/local/tmp/stop_nh`

---

### Extras

**How to check available space:**

```bash
df -h /data/local/nhsystem/kali-arm64

```

**How to resize the image (Add more space):**
If you run out of space, follow these steps to add 5GB (for example) without losing data.

1. **Stop NetHunter** (use the stop script).
2. **Unmount the image:**
```bash
umount -l /data/local/nhsystem/kali-arm64

```


3. **Append space to the file:**
```bash
# Adds 5GB (count=5000) to the existing file
dd if=/dev/zero bs=1M count=5000 >> /sdcard/nethunter.img

```


4. **Check and Resize the filesystem:**
```bash
# Force check the file
e2fsck -f /sdcard/nethunter.img

# Resize to fill the new space
resize2fs /sdcard/nethunter.img

```


5. **Reboot** or re-mount.