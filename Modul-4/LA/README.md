# Laporan Tugas Modul

## Nama Kelompok
**Kelompok : Network Interface**

## Anggota Kelompok
1. Christopher Joenathan Hindarto (5024241028)
2. Catherine Patricia Sindunata (5024241040)
3. Rayyan Fathanza (5024241056)

---

# 1. Topologi Jaringan

**Topologi Jaringan** <img width="979" height="723" alt="image" src="https://github.com/user-attachments/assets/d1de1557-371b-47bf-ac21-b5f4746ee6e8" />


---

# 2. Penjelasan Perangkat

| No | Perangkat | Fungsi |
|----|-----------|---------|
| 1 | Cloud / Jaringan Lab | Menghubungkan simulasi PNETLab ke jaringan lab atau internet |
| 2 | MikroTik ISP | Berperan sebagai router ISP atau simulasi jaringan luar |
| 3 | FortiGate | Berperan sebagai firewall utama yang mengatur akses antara WAN, LAN, dan DMZ |
| 4 | Cisco Router | Berperan sebagai router internal menuju jaringan LAN |
| 5 | Client LAN Tiny Core Linux | Client internal yang berada di belakang Cisco Router |
| 6 | Client WAN Tiny Core Linux | Client dari sisi luar/internet untuk menguji akses ke server DMZ |
| 7 | Ubuntu Server DMZ | Server web yang diletakkan pada zona DMZ |

---

# 3. Segmentasi Jaringan

| Segment | Network | Keterangan |
|----------|----------|------------|
| Jaringan Lab / Internet | DHCP dari jaringan lab | Sumber koneksi luar |
| ISP ke FortiGate | 10.10.10.0/30 | Link antara MikroTik ISP dan FortiGate |
| Client WAN | 172.16.100.0/24 | Jaringan client dari sisi luar |
| FortiGate ke Cisco | 10.20.20.0/30 | Link antara FortiGate dan Cisco Router |
| LAN | 192.168.10.0/24 | Jaringan internal client |
| DMZ | 192.168.20.0/24 | Jaringan server DMZ |

---

# 4. Tabel IP Address

<img width="1011" height="610" alt="image" src="https://github.com/user-attachments/assets/f63f5f9c-a470-4a10-8cd2-46d306770d9d" />

---

# 5. Konfigurasi Perangkat

## 5.1 MikroTik ISP

### Screenshot Konfigurasi MikroTik

**MikroTik Configuration** <img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/3bd7d207-4754-44ad-a4c0-0720d3a63d9e" />


---

## 5.2 FortiGate

### Konfigurasi Interface

| Interface | Role | IP Address |
|------------|------|------------|
| port1 | WAN | 10.10.10.2/30 |
| port2 | INSIDE | 10.20.20.1/30 |
| port3 | DMZ | 192.168.20.1/24 |

### Konfigurasi Routing

- Default Route → 10.10.10.1
- Static Route LAN → 192.168.10.0/24 via 10.20.20.2

### Address Object

- LAN
- Server_DMZ
- Client_WAN

### Firewall Policy

#### LAN_to_WAN

- Source : LAN
- Destination : all
- Service : ALL
- NAT : Enable

#### LAN_to_DMZ

- Source : LAN
- Destination : Server_DMZ
- Service : ALL
- NAT : Disable

#### WAN_to_DMZ_HTTP

- Source : Client_WAN
- Destination : VIP_DMZ
- Service : HTTP
- NAT : Disable

### Screenshot Konfigurasi FortiGate

<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/98d4d060-4782-4be3-b314-0ca0cab5dfd1" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/2845d0b5-ce76-42c7-a58a-4a76f7ea9903" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/88f02a8c-3da9-4423-a7b7-2412a79d4d36" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/d06a7aad-77e0-46bb-8f17-76939263681a" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/27f1e7bf-31a2-49d5-bbe1-816c1a69002f" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/b24e53ea-02a2-451b-9aee-80ff4e920b6f" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/72d3ef2a-db0c-4d3d-8277-cefd8dbc9b2c" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/675a30aa-e155-46b8-8859-54cb2bac0a2e" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/aac51a1e-a879-41f5-93d4-87bf36f09f5e" />


---

## 5.3 Cisco Router
### Screenshot Konfigurasi Cisco Router

<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/ba340959-2a1d-4b9a-8c9f-1dfeecafbd7f" />

---

## 5.4 Client LAN (Tiny Core Linux)
### Screenshot Konfigurasi Client LAN

<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/2d1cf174-99c2-4ad1-90cd-358153f53d5a" />

---

## 5.5 Client WAN (Tiny Core Linux)
### Screenshot Konfigurasi Client WAN

<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/df608dab-fbad-44cb-ad0e-142292af3f68" />

---

## 5.6 Ubuntu Server DMZ
### Screenshot Konfigurasi Ubuntu Server

<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/d2a25fa7-03ce-41de-a25a-62c3a1ad267a" />
<img width="1920" height="1008" alt="image" src="https://github.com/user-attachments/assets/97d27126-7147-404a-b074-c9275ac9f730" />

---

# 6. Hasil Pengujian

## 6.1 Pengujian Client LAN ke Gateway Cisco
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/dadf5562-7840-4e53-abaf-0db53a51944c" />

---

## 6.2 Pengujian Client LAN ke Fortigate
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/35c62bae-ad41-4ada-9704-e34b9af3e669" />

---

## 6.3 Pengujian Client LAN ke DMZ
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/fa5cfefe-07e0-4d5c-9616-cde8d4370fb2" />

---

## 6.4 Pengujian Client LAN akses IP DMZ
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/ff56caca-b1f3-4b51-95eb-be387dd4a5fc" />

---

## 6.5 Pengujian Client WAN ping ke isp mikrotik
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/d5b81eb4-a29b-471a-8268-29dd49ed2f53" />

---

## 6.6 Pengujian Client WAN ping ke fortigate
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/5e710351-524f-43f5-a95f-0cff275f168a" />

---

## 6.7 Pengujian Client WAN akses http://10.10.10.2/
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/5eac293c-af90-42de-8420-0883aee8f701" />

---

## 6.8 Pengujian Client WAN ping client lain
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/e9dccdca-41dc-4438-8ab2-8beb90dca7dc" />

---

## 6.9 Pengujian Client WAN ping IP asli DMZ
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/d0ce6873-a459-4532-97ad-bde643559fbc" />

---

## 6.10 Pengujian Server DMZ ping lain
### Hasil

<img width="1130" height="1008" alt="image" src="https://github.com/user-attachments/assets/6791f8dc-a828-45c3-9337-a7c9b0b12ea4" />

---

# 7. Analisis

Pada tugas modul ini dilakukan implementasi arsitektur jaringan yang terdiri dari WAN, LAN, dan DMZ menggunakan MikroTik ISP, FortiGate Firewall, Cisco Router, serta Ubuntu Server sebagai web server.

FortiGate berfungsi sebagai perangkat keamanan utama yang mengatur lalu lintas jaringan menggunakan static route, firewall policy, dan Virtual IP (VIP). Fitur NAT digunakan untuk mengizinkan akses internet dari jaringan LAN, sedangkan akses dari jaringan WAN ke server DMZ dibatasi hanya melalui layanan HTTP menggunakan mekanisme port forwarding.

Hasil pengujian menunjukkan bahwa client LAN dapat mengakses server DMZ dan internet dengan baik. Client WAN juga dapat mengakses web server melalui alamat publik FortiGate, tetapi tidak dapat mengakses jaringan LAN maupun alamat asli server DMZ secara langsung. Hal ini menunjukkan bahwa konfigurasi firewall dan segmentasi jaringan telah berjalan sesuai tujuan keamanan yang diinginkan.

---

# 8. Kesimpulan

Berdasarkan hasil konfigurasi dan pengujian yang telah dilakukan, dapat disimpulkan bahwa implementasi DMZ menggunakan FortiGate berhasil dilakukan dengan baik. Server yang berada pada zona DMZ dapat diakses dari jaringan luar melalui mekanisme port forwarding tanpa membuka akses langsung ke jaringan internal. Selain itu, firewall policy yang diterapkan mampu membatasi akses antar jaringan sehingga meningkatkan keamanan sistem secara keseluruhan.

Dengan demikian, tujuan praktikum mengenai konfigurasi routing, firewall policy, NAT, DMZ, dan port forwarding telah berhasil dicapai.

---
