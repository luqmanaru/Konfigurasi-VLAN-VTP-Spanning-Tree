# Konfigurasi-VLAN-VTP-Spanning-Tree

Repository ini berisi tugas individu mata kuliah Jaringan Komputer Terapan 1 tentang implementasi VLAN dengan VTP, trunking, dan spanning tree.

## Deskripsi

Tugas ini bertujuan untuk mengimplementasikan jaringan VLAN dengan VTP (VLAN Trunking Protocol), trunking antar switch, dan spanning tree protocol untuk mencegah loop. Implementasi dilakukan menggunakan switch Layer 3 sebagai switch utama untuk routing antar VLAN dan beberapa switch Layer 2 yang terhubung secara trunking untuk mendukung domain VTP.

## Topologi Jaringan

```
        +------------------+
        | Switch Layer 3   |
        | (Multilayer)     |
        +------------------+
              /    |    \
             /     |     \
            /      |      \
+-----------+ +-----------+ +-----------+
| Switch L2 | | Switch L2 | | Switch L2 |
| (Server)  | | (Client)  | | (Transparent)|
+-----------+ +-----------+ +-----------+
```
<img width="827" height="457" alt="image" src="https://github.com/user-attachments/assets/43601c64-f829-417b-8dfe-80e657d088a6" />

## Pembagian VLAN

| VLAN ID | Nama VLAN | IP Network       | Switch yang Mengandung VLAN |
|---------|-----------|------------------|----------------------------|
| 10      | SALES     | 192.168.10.0/24 | Semua switch               |
| 20      | HR        | 192.168.20.0/24 | Semua switch               |
| 30      | IT        | 192.168.30.0/24 | Semua switch               |

## Konfigurasi VLAN

```
Switch# show vlan brief

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------
1    default                          active    Fa0/1, Fa0/2, Fa0/3, Fa0/4
                                                Fa0/5, Fa0/6, Fa0/7, Fa0/8
                                                Fa0/9, Fa0/10, Fa0/11, Fa0/12
                                                Fa0/13, Fa0/14, Fa0/15, Fa0/16
                                                Fa0/17, Fa0/18, Fa0/19, Fa0/20
                                                Fa0/21, Fa0/22, Fa0/23, Fa0/24
10   SALES                            active    
20   HR                               active    
30   IT                               active    
1002 fddi-default                     active    
1003 token-ring-default               active    
1004 fddinet-default                  active    
1005 trnet-default                    active    
```

## Konfigurasi VTP

### VTP Server

```
Switch# show vtp status

VTP Version                     : 2
Configuration Revision          : 1
Maximum VLANs supported locally : 1005
Number of existing VLANs        : 8
VTP Operating Mode              : Server
VTP Domain Name                 : mynetwork
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0x4A 0x1C 0x43 0x79 0x4E 0x45 0x5D 0x26
```

### VTP Client

```
Switch# show vtp status

VTP Version                     : 2
Configuration Revision          : 1
Maximum VLANs supported locally : 1005
Number of existing VLANs        : 8
VTP Operating Mode              : Client
VTP Domain Name                 : mynetwork
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0x4A 0x1C 0x43 0x79 0x4E 0x45 0x5D 0x26
```

### VTP Transparent

```
Switch# show vtp status

VTP Version                     : 2
Configuration Revision          : 0
Maximum VLANs supported locally : 1005
Number of existing VLANs        : 8
VTP Operating Mode              : Transparent
VTP Domain Name                 : mynetwork
VTP Pruning Mode                : Disabled
VTP V2 Mode                     : Disabled
VTP Traps Generation            : Disabled
MD5 digest                      : 0x4A 0x1C 0x43 0x79 0x4E 0x45 0x5D 0x26
```

## Konfigurasi Trunking

```
Switch# show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/1       on           802.1q         trunking      1

Port        Vlans allowed on trunk
Fa0/1       1-1005

Port        Vlans allowed and active in management domain
Fa0/1       1,10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/1       1,10,20,30
```

## Konfigurasi Spanning Tree

```
Switch# show spanning-tree vlan 10

VLAN0010
  Spanning tree enabled protocol rstp
  Root ID    Priority    4096
             Address     0001.42EA.A681
             Cost        19
             Port        1 (FastEthernet0/1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    4096  (priority 4096 sys-id-ext 0)
             Address     0001.42EA.A681
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/1               Root FWD 19        128.1    P2p
Fa0/2               Altn BLK 19        128.2    P2p
```

```
Switch# show spanning-tree vlan 20

VLAN0020
  Spanning tree enabled protocol rstp
  Root ID    Priority    8192
             Address     0001.42EA.A681
             Cost        19
             Port        1 (FastEthernet0/1)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    8192  (priority 8192 sys-id-ext 0)
             Address     0001.42EA.A681
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20 sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Fa0/1               Root FWD 19        128.1    P2p
Fa0/2               Altn BLK 19        128.2    P2p
```

## Konfigurasi Interface VLAN pada Switch Layer 3

```
Switch# show ip interface brief

Interface              IP-Address      OK? Method Status                Protocol
Vlan1                  unassigned      YES unset  administratively down down
Vlan10                 192.168.10.1    YES manual up                    up
Vlan20                 192.168.20.1    YES manual up                    up
Vlan30                 192.168.30.1    YES manual up                    up
FastEthernet0/0         unassigned      YES unset  down                  down
FastEthernet0/1         unassigned      YES unset  up                    up
FastEthernet0/2         unassigned      YES unset  down                  down
```

```
Switch# show ip route

Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

C    192.168.10.0/24 is directly connected, Vlan10
C    192.168.20.0/24 is directly connected, Vlan20
C    192.168.30.0/24 is directly connected, Vlan30
```

## Hasil Testing

### Testing Antar VLAN yang Sama

| Dari VLAN | Ke VLAN | Dari Device       | Ke Device         | Hasil |
|-----------|---------|-------------------|-------------------|-------|
| 10        | 10      | PC Sales SW1      | PC Sales SW2      | Success |
| 20        | 20      | PC HR SW1         | PC HR SW2         | Success |
| 30        | 30      | PC IT SW1         | PC IT SW2         | Success |

### Testing Antar VLAN yang Berbeda

| Dari VLAN | Ke VLAN | Dari Device       | Ke Device         | Hasil |
|-----------|---------|-------------------|-------------------|-------|
| 10        | 20      | PC Sales SW1      | PC HR SW1         | Success |
| 10        | 30      | PC Sales SW1      | PC IT SW1         | Success |
| 20        | 30      | PC HR SW1         | PC IT SW1         | Success |
| 10        | 20      | PC Sales SW2      | PC HR SW2         | Success |
| 10        | 30      | PC Sales SW2      | PC IT SW2         | Success |
| 20        | 30      | PC HR SW2         | PC IT SW2         | Success |

### Testing ke Gateway

| VLAN     | Device       | Gateway          | Hasil |
|----------|--------------|------------------|-------|
| VLAN 10  | PC Sales     | 192.168.10.1     | Success |
| VLAN 20  | PC HR        | 192.168.20.1     | Success |
| VLAN 30  | PC IT        | 192.168.30.1     | Success |

## Kesimpulan

Dari hasil pengujian, dapat disimpulkan bahwa:
1. Konfigurasi VLAN telah berhasil diimplementasikan pada semua switch.
2. Konfigurasi VTP berfungsi dengan baik, VLAN yang dibuat di server dapat diterima oleh client.
3. Konfigurasi trunking antar switch berfungsi dengan benar untuk menghubungkan VLAN yang sama di berbagai switch.
4. Konfigurasi spanning tree berfungsi dengan baik untuk mencegah loop pada jaringan.
5. Konfigurasi interface VLAN pada switch Layer 3 berfungsi dengan baik sebagai gateway untuk setiap VLAN.
6. Perangkat dalam VLAN yang berbeda dapat saling berkomunikasi melalui switch Layer 3.

---
**luqmanaru**
