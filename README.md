# LAB-25-SRC-NAT
tanggal 17 Agustus 2025

![m]()

# Langkah Konfigurasi Firewall Chain Forward SRC-NAT di Mikrotik (tanpa MASQUERADE)

1. Konfigurasi IP Address di Mikrotik.   
   
   ```
   /ip address add address=202.1.1.2/30 interface=eth1
   /ip address add address=192.168.10.1/24 interface=eth2
   ```

2.. Set Default Gateway ke Internet.  

   ```
   /ip route add gateway=202.1.1.1
   ```

3. Atur DNS agar client bisa resolve domain.   

   ```
   /ip dns set servers=8.8.8.8,8.8.4.4 allow-remote-requests=yes
   ```

4. Buat aturan SRC-NAT (tanpa MASQUERADE, tapi menggunakan action=src-nat).  

   ```
   /ip firewall nat add chain=srcnat out-interface=eth1 action=src-nat to-addresses=202.1.1.2
   ```

   **Penjelasan**:    

     - `chain=srcnat`-> untuk mengubah source address dari LAN.   
     - `out-interface=eth1` -> lalu lintas keluar lewat internet.     
     - `to-addresses=202.1.1.2` -> IP publik Mikrotik di eth1.    
       
5. Setting Otomatis (DHCP Server di Mikrotik)        

```
/ip dhcp-server add name=dhcp1 interface=eth2 lease-time=1h address-pool=dhcp_pool
/ip pool add name=dhcp_pool ranges=192.168.10.10-192.168.10.100
/ip dhcp-server network add address=192.168.10.0/24 gateway=192.168.10.1 dns-server=8.8.8.8
```

* Setelah itu, PC/Laptop tinggal diatur ke **Obtain IP automatically**, dan otomatis    
  dapat IP, gateway, serta DNS dari Mikrotik.
  
# pengujian   

   * Ping dari PC/Laptop ke internet (`ping 8.8.8.8`)

![m]()

# Kesimpulan     

SRC-NAT berfungsi untuk mengganti alamat IP private dari client menjadi alamat    
IP publik router agar bisa terhubung ke internet.  
