## 🗺️ Topologi Jaringan & Addressing

### 🖧 Topologi Tugas Modul
Berikut adalah arsitektur topologi jaringan Enterprise HQ–Branch yang digunakan dalam tugas ini:

<img width="1228" height="738" alt="image" src="https://github.com/user-attachments/assets/48448903-2ac0-4f07-9da3-482395678069" />


## 🛠️ Panduan Tugas Modul (1 - 10)

### Tugas Modul 1: Konfigurasi Cisco Switch Jakarta

#### Perintah Utama:
```ios
Switch# configure terminal
Switch(config)# vlan 10
Switch(config-vlan)# name Finance
Switch(config-vlan)# vlan 20
Switch(config-vlan)# name IT
Switch(config-vlan)# vlan 60
Switch(config-vlan)# name SERVER-HQ
Switch(config-vlan)# exit

! Atur Access Port
Switch(config)# interface gi0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

! Atur Trunk Link Ke Router
Switch(config)# interface range gi0/2 - 3
Switch(config-if-range)# switchport trunk encapsulation dot1q
Switch(config-if-range)# switchport mode trunk
Switch(config-if-range)# switchport trunk allowed vlan 10,20,60
```

#### 📁 Bukti Verifikasi:
1. **Show VLAN Brief & Interfaces Trunk:**
<img width="1112" height="622" alt="Switch JKT VLAN Brief, Interface Trunk" src="https://github.com/user-attachments/assets/114d41fa-d4d9-4953-8060-48401616ca71" />

---

### Tugas Modul 2: Konfigurasi Cisco Router Jakarta

#### Perintah Utama:
```ios
! Subinterface & IP Physical
interface Gi0/1.10
 encapsulation dot1Q 10
 ip address 192.168.10.2 255.255.255.0
 vrrp 10 ip 192.168.10.1
 vrrp 10 priority 110
 ip helper-address 192.168.60.10
!
interface Gi0/1.20
 encapsulation dot1Q 20
 ip address 192.168.20.2 255.255.255.0
 vrrp 20 ip 192.168.20.1
 vrrp 20 priority 90
 ip helper-address 192.168.60.10
!
interface Gi0/1.60
 encapsulation dot1Q 60
 ip address 192.168.60.2 255.255.255.0
 vrrp 60 ip 192.168.60.1
 vrrp 60 priority 110
!
! Link Transit ke FortiGate
interface Gi0/0
 ip address 10.10.100.2 255.255.255.252
 no shutdown
!
ip route 0.0.0.0 0.0.0.0 10.10.100.1
```

#### 📁 Bukti Verifikasi:
1. **Show IP Interface Brief, VRRP Brief, dan Ping:**
  <img width="1118" height="622" alt="Router JKT Interface trunk, vrrp, ping" src="https://github.com/user-attachments/assets/5842f8e5-8c5a-4579-88c7-210230b4b504" />

---

### Tugas Modul 3: Konfigurasi MikroTik Router Jakarta

#### Perintah Utama:
```forticonfig
# Membuat Interface VLAN
/interface vlan add name=vlan10-finance vlan-id=10 interface=ether2
/interface vlan add name=vlan20-it vlan-id=20 interface=ether2
/interface vlan add name=vlan60-ubuntu-server vlan-id=60 interface=ether2

# Konfigurasi IP Alamat Fisik
/ip address add address=192.168.10.3/24 interface=vlan10-finance
/ip address add address=192.168.20.3/24 interface=vlan20-it
/ip address add address=192.168.60.3/24 interface=vlan60-ubuntu-server
/ip address add address=10.10.101.2/30 interface=ether1 comment="TO-FORTINET"

# Konfigurasi High Availability VRRP (Master di VLAN 20, Backup di 10 & 60)
/interface vrrp add name=vrrp10 vrid=10 interface=vlan10-finance priority=90 version=3
/interface vrrp add name=vrrp20 vrid=20 interface=vlan20-it priority=120 version=3
/interface vrrp add name=vrrp60 vrid=60 interface=vlan60-ubuntu-server priority=90 version=3

# Binding Virtual IP ke Interface VRRP
/ip address add address=192.168.10.1/32 interface=vrrp10
/ip address add address=192.168.20.1/32 interface=vrrp20
/ip address add address=192.168.60.1/32 interface=vrrp60

# DHCP Relay Terpusat menuju Ubuntu Server
/ip dhcp-relay add name=relay-vlan10 interface=vlan10-finance dhcp-server=192.168.60.10 local-address=192.168.10.3
/ip dhcp-relay add name=relay-vlan20 interface=vlan20-it dhcp-server=192.168.60.10 local-address=192.168.20.3

# Default Route ke Firewall
/ip route add dst-address=0.0.0.0/0 gateway=10.10.101.1
```

#### 📁 Bukti Verifikasi:
1. **IP Address Print & VRRP Status:**
<img width="930" height="792" alt="Mikrotik JKT Address vrrp" src="https://github.com/user-attachments/assets/9a8c51d1-3ef9-4c72-8781-e819f7524c4f" />


2. **DHCP Relay Configuration & Ping Test:**
<img width="926" height="367" alt="Mikrotik JKT DHCP relay, ping" src="https://github.com/user-attachments/assets/360dd3ea-3181-40e7-b9a9-3b683629393f" />

   ```
3. **IP Route Firewall Gateway:**
<img width="752" height="522" alt="Mikrotik JKT ip route" src="https://github.com/user-attachments/assets/3df18c55-fe43-4df8-b2e7-84ade8add502" />


---

### Tugas Modul 4: Konfigurasi Ubuntu Server Jakarta

💡 **Tips Penting Troubleshooting**: Hubungkan internet via Cloud Network Management di awal pengerjaan untuk mengunduh packages server, lakukan instalasi, barulah pindah ke topologi internal (VLAN 60) dengan IP Static.

#### Konfigurasi File `/etc/dhcp/dhcpd.conf`:
```text
authoritative;
default-lease-time 600;
max-lease-time 7200;

option domain-name-servers 8.8.8.8, 1.1.1.1;

# Network Pool VLAN 10 - Finance
subnet 192.168.10.0 netmask 255.255.255.0 {
  range 192.168.10.100 192.168.10.200;
  option routers 192.168.10.1; # Mengarah ke Virtual IP VRRP
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.10.255;
}

# Network Pool VLAN 20 - IT
subnet 192.168.20.0 netmask 255.255.255.0 {
  range 192.168.20.100 192.168.20.200;
  option routers 192.168.20.1; # Mengarah ke Virtual IP VRRP
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.20.255;
}

# Network Deklarasi VLAN 60 - Server Network
subnet 192.168.60.0 netmask 255.255.255.0 {
  option routers 192.168.60.1;
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.60.255;
}
```

#### 📁 Bukti Verifikasi:
1. **Output Perintah `ip a` & `ip route` & Ping :**
 <img width="930" height="790" alt="UBUNTU JKT IP A, IP route, ping" src="https://github.com/user-attachments/assets/5a674d6e-138b-4fec-8569-a3d2e8c0bbd1" />


2. **Pengecekan Layanan Web & Konektivitas Eksternal:**
<img width="748" height="406" alt="UBUNTU JKT file" src="https://github.com/user-attachments/assets/7372336d-fe9d-4135-a93e-63ddbb29d38a" />


---

### Tugas Modul 5: Konfigurasi FortiGate Jakarta

#### Cuplikan Perintah CLI Utama:
```forticonfig
config system interface
    edit "port1"
        set ip 10.10.100.1 255.255.255.252
    next
    edit "port2"
        set ip 10.10.101.1 255.255.255.252
    next
    edit "port3"
        set ip 10.0.12.2 255.255.255.252
    next
end

config router static
    edit 1
        set gateway 10.0.12.1
        set device "port3"
    next
    edit 2
        set dst 192.168.10.0 255.255.255.0
        set gateway 10.10.100.2
        set device "port1"
    next
end
```

#### 📁 Bukti Verifikasi:
1. **Physical Interfaces Verification:**
<img width="1042" height="1008" alt="Forti JKT system interface physical" src="https://github.com/user-attachments/assets/a540f300-6ad3-48dd-880e-d973a0f1f14a" />

2. **OSPF Status:** Dapat dikonfirmasi melalui perintah `get router info ospf neighbor` & Mengetes Ping.
<img width="1042" height="1008" alt="Forti JKT ping 8 8 8 8, 172, routing table ospf" src="https://github.com/user-attachments/assets/30c4b0f9-0eb2-4e88-a239-ff2130af6c97" />

   3. **Routing Table:** Routing table & Firewall Policy
      <img width="1042" height="1008" alt="Forti JKT routing trable   firewall policy pt1" src="https://github.com/user-attachments/assets/4e566026-0c98-4490-8993-cc454aec52dc" />
 <img width="1042" height="1008" alt="Forti JKT firewall policy pt2" src="https://github.com/user-attachments/assets/484acc2b-87c6-4df7-8e75-46f75d19dc71" />
<img width="1042" height="1008" alt="Forti JKT firewall policy pt3" src="https://github.com/user-attachments/assets/800fcfdf-5a9b-4078-921a-d3934d63b1ef" />


---

### Tugas Modul 6: Konfigurasi MikroTik ISP

#### Perintah Utama:
```forticonfig
/ip address add address=10.0.12.1/30 interface=ether2 comment="LINK-TO-JKT"
/ip address add address=10.0.13.1/30 interface=ether3 comment="LINK-TO-SBY"

# NAT Masquerade Akses Eksternal Cloud NAT
/ip firewall nat add chain=srcnat action=masquerade out-interface=ether1
```

#### 📁 Bukti Verifikasi:
1. **IP Table Print:**
   <img width="1042" height="1008" alt="Mikrotik ISP print addr, route firewall nat, ping 8 8 8 8, 10 0 12 2" src="https://github.com/user-attachments/assets/65c198d5-8a93-4f06-8a25-726682d4ff93" />

2. **Ping 8.8.8.8 10.0.12.2, & 10.0.13.2**
<img width="1042" height="1008" alt="Mikrotik ISP ping all" src="https://github.com/user-attachments/assets/e9455093-3fd5-4f2d-9642-2212b405e9c3" />


---

### Tugas Modul 7: Konfigurasi Switch dan MikroTik Surabaya

#### Perintah Utama Switch SBY:
```ios
SWITCH-SURABAYA# conf t
SWITCH-SURABAYA(config)# vlan 30
SWITCH-SURABAYA(config-vlan)# name sales
SWITCH-SURABAYA(config-vlan)# vlan 40
SWITCH-SURABAYA(config-vlan)# name operations
```

#### Perintah Utama MikroTik SBY:
```forticonfig
/interface vlan add name=vlan30-sales vlan-id=30 interface=ether2
/interface vlan add name=vlan40-operations vlan-id=40 interface=ether2

/ip address add address=192.168.30.1/24 interface=vlan30-sales
/ip address add address=192.168.40.1/24 interface=vlan40-operations
/ip address add address=10.10.200.2/30 interface=ether1

# Alokasi DHCP Lokal SBY
/ip pool add name=dhcp_pool0 ranges=192.168.30.2-192.168.30.254
/ip dhcp-server add name=dhcp1 interface=vlan30-sales address-pool=dhcp_pool0 disabled=no
/ip dhcp-server network add address=192.168.30.0/24 gateway=192.168.30.1 dns-server=8.8.8.8

/ip route add dst-address=0.0.0.0/0 gateway=10.10.200.1
```

#### 📁 Bukti Verifikasi:
1. **VLAN Table SBY:**
 <img width="1042" height="1008" alt="switch SBY vlan brief" src="https://github.com/user-attachments/assets/c0b96c53-6b33-48be-9182-0a6b085a7c6b" />


3. **IP Address & DHCP Server SBY:**
   ```text
<img width="1042" height="1008" alt="Mikrotik SBY Print addr, dhcp-server, pool, route, ping" src="https://github.com/user-attachments/assets/56aa82ee-46bd-414f-94ae-510046c5824b" />

4. **Client IP Request Test (VPCS) & Ping Test:**
<img width="1042" height="1008" alt="VLAN30 ping 192 168 10 10" src="https://github.com/user-attachments/assets/50422883-2696-412d-ab83-d0a41ff5c4f0" />


---

### Tugas Modul 8: Konfigurasi FortiGate Surabaya

#### 📁 Bukti Verifikasi Jaringan & Routing Table:
1. **Routing Table Lengkap (`get router info routing-table all`):**
<img width="1042" height="1008" alt="Forti SBY routing table,  firewall policy pt 1" src="https://github.com/user-attachments/assets/d8b837e4-5521-4217-bedb-30240dbaf17b" />
<img width="1042" height="1008" alt="Forti SBY firewall policy pt 2" src="https://github.com/user-attachments/assets/9141803b-7b1e-4f6e-8a40-5c8f7967de54" />
<img width="1042" height="1008" alt="Forti SBY firewall policy pt3" src="https://github.com/user-attachments/assets/1c1ddd4f-0e55-4d61-9d85-ff73409654f2" />

2. **Ping Test ke Virtual Endpoint JKT Tunnel, OSPF Adjacency Status & Spesifik OSPF Route Database:**
<img width="1042" height="1008" alt="Forti SBY ping 8 8 8 8, 172 16 0 2, routing table ospf" src="https://github.com/user-attachments/assets/04c8f228-a619-49bb-84c7-4dc3b6c1d416" />


---

### Tugas Modul 9: Konfigurasi GRE Tunnel dan OSPF over GRE

Implementasi ini menjembatani FortiGate Jakarta dan Surabaya menggunakan terowongan virtual point-to-point. Setelah interkoneksi WAN terbentuk, OSPF diaktifkan di atas interface terowongan (`GRE-JKT-SBY` dan `GRE-SBY-JKT`). Kebijakan **redistribute static** diaktifkan agar rute network internal yang dicatat secara static oleh firewall dapat dipropagasikan secara otomatis ke lokasi seberang.

#### Verifikasi Ping Tunnel Eksekusi:
<img width="990" height="188" alt="image" src="https://github.com/user-attachments/assets/b7ceb5fc-49ce-4bc0-be4e-73b7805b4877" />
---

### Tugas Modul 10: Pengujian Akhir

#### Ringkasan Hasil Uji Validitas Jaringan:
- **DHCP Client JKT Validasi**: Client JKT berhasil mendapatkan alokasi dynamic IP pool.
<img width="1042" height="1008" alt="VLAN10 ping 192 168 30 1" src="https://github.com/user-attachments/assets/cd85e9b8-354d-4ea6-a95e-69669b2b671a" />
<img width="1042" height="1008" alt="VLAN20 ping 192 168 30 1" src="https://github.com/user-attachments/assets/b027ce60-e2ad-4468-beea-52533a4e875f" />

- **Ping Lintas Kantor (Intersite Link)**: PC JKT (VLAN 10) berhasil interkoneksi penuh menuju PC SBY (VLAN 40).
  ```text
<img width="1042" height="1008" alt="VLAN20 ping 192 168 30 1" src="https://github.com/user-attachments/assets/86c3d5fb-bb8c-48fa-8c01-102a11801f21" />
<img width="1042" height="1008" alt="VLAN10 ping 192 168 30 1" src="https://github.com/user-attachments/assets/cd85e9b8-354d-4ea6-a95e-69669b2b671a" />
  ```

#### 📝 Analisis Singkat Jalur Trafik Surabaya ke Web Server Jakarta:
1. Paket diinisiasi oleh Client Surabaya (`192.168.30.X`/`192.168.40.X`) menuju default gateway MikroTik Surabaya (`10.10.200.2`).
2. MikroTik Surabaya melempar paket ke internal interface FortiGate Surabaya (`10.10.200.1`).
3. FortiGate Surabaya membaca tabel routing OSPF. Paket diarahkan masuk ke interface virtual tunnel **GRE-SBY-JKT** dengan enkapsulasi IP luar WAN menuju FortiGate Jakarta.
4. Paket melintasi infrastruktur ISP secara transparan dan tiba di FortiGate Jakarta.
5. FortiGate Jakarta mendekapsulasi paket, membaca IP tujuan (`192.168.60.10`), lalu melemparkannya ke internal gateway router (Cisco/MikroTik via VRRP virtual IP `192.168.60.1`).
6. Router internal meneruskan data ke segmen VLAN 60 hingga diterima oleh Ubuntu Server Jakarta.

