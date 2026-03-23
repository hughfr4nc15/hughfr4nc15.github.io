---
title: Enable Intel NUC Internal Bluetooth for Home Assistant in Proxmox
date: 2026-03-23 02:00:00
categories: [Homelab, Home Assistant]
tags: [homelab, linux, home assistant, bluetooth]     # TAG names should always be lowercase
---

This guide shows how to enable and pass through the internal Intel Bluetooth on an Intel NUC running Proxmox to a Home Assistant VM, making it usable for BLE devices.

---

## 1️⃣ Check Bluetooth Hardware in Proxmox

- SSH into your Proxmox host.
- Verify USB devices:

```bash
lsusb
````

* Look for something like:

```text
Bus 001 Device 003: ID 8087:0aaa Intel Corp. Bluetooth 9460/9560 Jefferson Peak
```

* Check PCI wireless controller:

```bash
lspci | grep -i wireless
```

* Check kernel logs:

```bash
dmesg | grep -i bluetooth
```

---

## 2️⃣ Install Bluetooth Support on Proxmox

* Install packages:

```bash
apt update
apt install bluetooth bluez
```

* Enable and start the Bluetooth service:

```bash
systemctl enable bluetooth
systemctl start bluetooth
```

* Confirm the controller is detected:

```bash
bluetoothctl list
```

Expected output:

```text
Controller D8:F2:CA:C3:C8:59 MAINFRAME [default]
```

---

## 3️⃣ Pass Bluetooth to Home Assistant VM

* Go to **VM → Hardware → Add → USB Device**.
* Select `Intel Corp. Bluetooth 9460/9560 Jefferson Peak`.
* Click **Add** and reboot the VM.

---

## 4️⃣ Verify Bluetooth Inside Home Assistant

* SSH into Home Assistant OS / VM

* Check Bluetooth controller:

```bash
bluetoothctl list
```

Expected output:

```text
Controller D8:F2:CA:C3:C8:59 homeassistant [default]
```

* Optional: Test scanning nearby devices:

```bash
bluetoothctl
scan on
```

---

## 5️⃣ Add Bluetooth Integration in Home Assistant

* Go to **Settings → Devices & Services → Add Integration → Bluetooth**.
* Confirm `hci0` adapter appears.
* Pair BLE devices as needed.

---

## ✅ Completion Note

* Intel NUC internal Bluetooth detected and firmware loaded.
* Bluetooth service installed and active in Proxmox.
* Bluetooth device passed through to Home Assistant VM.
* `hci0` adapter verified inside Home Assistant.
* BLE devices can now be paired and used.
