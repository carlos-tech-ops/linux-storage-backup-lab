# Linux Storage & Backup Lab 💾
_A real-world lab to master Linux storage management, LVM, `rsync`, and reliable backup/restore operations._

---

## 📌 Objective

This lab simulates a full backup-and-recovery workflow using:

- **LVM** (Logical Volume Manager)
- **`rsync`** for incremental backups
- Mount control for restore points
- Real-world file deletion and recovery

Great practice for anyone preparing for **CompTIA Linux+ (XK0-005)** or day-to-day sysadmin roles.

---

## 🧠 Skills Demonstrated

- Create and manage loopback storage with `dd` and `losetup`
- Provision LVM: PV ➜ VG ➜ LV
- Format and mount volumes (`mkfs`, `mount`)
- Perform safe backups and restores with `rsync`
- Troubleshoot access issues and restore deleted data

---

## 🖥️ Environment

| Component | Version / Tool              |
|-----------|-----------------------------|
| OS        | Fedora 41 (workstation)     |
| Shell     | Bash                        |
| Key CLI   | `losetup`, `lvm`, `rsync`, `mount`, `dd`, `df`, `lsblk` |

---

## 🛠️ Tasks Performed

### 🧱 Disk Provisioning & LVM Setup

```bash
# 1️⃣  Create a 1 GB “disk” file
sudo dd if=/dev/zero of=/opt/backup-disk.img bs=1M count=1024

# 2️⃣  Attach it as /dev/loopX
sudo losetup -fP /opt/backup-disk.img

# 3️⃣  Create the Physical Volume (replace X with actual loop number)
sudo pvcreate /dev/loopX

# 4️⃣  Create VG & LV
sudo vgcreate backup_vg /dev/loopX
sudo lvcreate -L 900M -n backup_lv backup_vg

# 5️⃣  Format & mount
sudo mkfs.ext4 /dev/backup_vg/backup_lv
sudo mkdir -p /mnt/backup
sudo mount /dev/backup_vg/backup_lv /mnt/backup

sudo mkdir -p /opt/projects
echo "Alpha Project" | sudo tee /opt/projects/alpha.txt
echo "Beta  Project" | sudo tee /opt/projects/beta.txt

sudo rsync -avh /opt/projects/ /mnt/backup/

sudo find /opt/projects -type f -exec rm -f {} +

sudo rsync -avh /mnt/backup/ /opt/projects/ | sudo tee outputs/restore-log.txt
```

---

---

## 📄 Output Logs

| File                            | Description                                    |
|---------------------------------|------------------------------------------------|
| `outputs/restore-log.txt`       | Log captured from the `rsync` restore process  |

---

## 📸 Screenshots

### 🧾 Data Restore Confirmation
Restored files (`alpha.txt`, `beta.txt`) successfully appear under `/opt/projects` after the `rsync` restore operation.

![Data Restore Confirmation](images/data-restore-success.png)

---

### 📋 Rsync Restore Log Output
Output of the `rsync` command during the restore process, captured using `tee`.

![Rsync Restore Log Output](images/restore-log-output.png)
