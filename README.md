## P2S VPN Gateway-in-Azure
Got it 👍 You want all the above steps (Point-to-Site VPN setup + VM private IP webpage access) written in a **GitHub README.md** style.
Here’s a clean **README.md** draft you can directly use in your repo:

---

````markdown
# 🚀 Access Azure VM Webpage via Private IP using Point-to-Site (P2S) VPN


This guide explains how to deploy a simple **Ubuntu VM webpage** in Azure and access it securely using **Point-to-Site (P2S) VPN** through an **Azure VPN Gateway**.

---

## 📌 Prerequisites
- Azure Subscription
- A **Virtual Network (VNet)** with address space (e.g., `10.0.0.0/16`)
- A subnet for VM (e.g., `10.0.1.0/24`)
- A deployed **Ubuntu VM** inside the VNet
- Azure **VPN Gateway** deployed in a `GatewaySubnet`

---

## 🖥️ Step 1: Install Web Server on Ubuntu VM
SSH into your VM and install Apache:

```bash
sudo apt update
sudo apt install apache2 -y
````

Deploy a simple HTML page:

```bash
echo '<!DOCTYPE html>
<html>
<head><title>Private Webpage</title></head>
<body>
  <h1>Welcome!</h1>
  <p>This page is only accessible via Azure VPN 🚀</p>
</body>
</html>' | sudo tee /var/www/html/index.html > /dev/null
```

Check Apache is running:

```bash
systemctl status apache2
```
![WhatsApp Image 2025-08-27 at 11 45 23_241d02c5](https://github.com/user-attachments/assets/f95eb5ae-5fea-433e-a889-7bca8c400f59)

![WhatsApp Image 2025-08-26 at 16 18 15_04e18221](https://github.com/user-attachments/assets/081bb9b8-06c9-42f0-98ce-3f08dd01b81b)

---

## 🌐 Step 2: Configure Point-to-Site VPN in Azure

1. Go to **Azure Portal → Virtual Network Gateway**.
2. Open **Point-to-site configuration** → click **Configure now**.
3. Fill in:

   * **Address pool**: `172.16.0.0/24`
   * **Tunnel type**: `OpenVPN (SSL)`
   * **Authentication type**: `Azure certificate`
![WhatsApp Image 2025-08-27 at 11 46 27_24b00a5d](https://github.com/user-attachments/assets/8f5bc581-55da-4006-b1f8-2e04276bf87d)

---

## 🔑 Step 3: Generate Root Certificate

### Windows (PowerShell)

```powershell
New-SelfSignedCertificate -Type Custom -KeySpec Signature -Subject "CN=AzureVPNRoot" `
  -KeyExportPolicy Exportable -HashAlgorithm sha256 -KeyLength 2048 `
  -CertStoreLocation "Cert:\CurrentUser\My"
```

Export the **public key (.cer)** file and upload it to Azure under **Root certificates**.

---

## 📥 Step 4: Download VPN Client

* In **Point-to-site configuration**, click **Download VPN client**.
* Extract the package.
* Use it in your OS VPN client.

---

## 🔗 Step 5: Connect from Your Machine

### Windows

* Install the downloaded VPN client or import into **Azure VPN Client**.

![WhatsApp Image 2025-08-26 at 16 19 14_3d111d78](https://github.com/user-attachments/assets/0eae57a8-fbd4-424d-ba8b-c96954d984bc)


### Linux

```bash
sudo openvpn --config azurevpnconfig.ovpn
```

### MacOS

* Import the `.ovpn` file into **Tunnelblick** or OpenVPN client.

---

## ✅ Step 6: Test Access

1. Check your VPN-assigned IP (from `172.16.0.0/24`).
2. Ping the VM’s private IP:

   ```bash
   ping 10.0.1.4
   ```
3. Open in browser:

   ```
   http://10.0.1.4
   ```

🎉 You should see your **private webpage**, accessible only through VPN.

![WhatsApp Image 2025-08-26 at 16 17 40_a50126e6](https://github.com/user-attachments/assets/b871dea2-6365-48bb-a54d-a3c77d4d5714)

---

## 📌 Summary

* VM private IP: `10.0.1.4`
* Apache web server hosts the page
* Azure VPN Gateway provides secure P2S access
* Access is only possible while VPN is connected

