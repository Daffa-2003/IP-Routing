# Cisco Routing

Implementasi dan konfigurasi **routing statis (static routing)** menggunakan Cisco Packet Tracer.

| | |
|---|---|
| **Mata Kuliah** | Komunikasi dan Jaringan Komputer |
| **Dosen** | Dr. Ferry Astika Saputra, ST, M.Sc |
| **Oleh** | Daffa Muhammad Irhamni |
| **NRP** | 1226800010 |
| **Program Studi** | Pascasarjana Teknik Informatika dan Komputer |
| **Institusi** | Politeknik Elektronika Negeri Surabaya |
| **Tahun** | 2026 |

---

## Daftar Isi

- [Dasar Teori](#dasar-teori)
- [Tools](#tools)
- [Topologi 1: 1 Router + 2 Switch + 6 PC](#topologi-1-1-router--2-switch--6-pc)
- [Topologi 2: 2 Router + 2 Switch + 6 PC](#topologi-2-2-router--2-switch--6-pc)
- [Konsep Routing](#konsep-routing)
- [Kesimpulan](#kesimpulan)

---

## Dasar Teori

**Routing** adalah proses meneruskan paket data dari satu jaringan ke jaringan lain oleh perangkat *router*. Router bekerja pada Layer 3 (Network Layer) dan menentukan jalur terbaik berdasarkan **routing table** yang dimilikinya.

Secara umum ada dua jenis routing:

- **Static Routing** — jalur ditentukan secara manual oleh administrator jaringan. Sederhana, aman, dan cocok untuk topologi kecil atau tetap.
- **Dynamic Routing** — jalur dipelajari otomatis oleh protokol routing (RIP, OSPF, EIGRP, dll). Cocok untuk jaringan besar dan dinamis.

Pada praktikum ini digunakan **static routing**.

---

## Tools

- **Cisco Packet Tracer** — media simulasi jaringan (`routing-cisco.pkt`)
- **Cisco Router** dan **Switch**
- **PC / End Device** sebagai host

---

## Topologi 1: 1 Router + 2 Switch + 6 PC

Membuat topologi menggunakan **1 router** dan **2 switch**, dengan 6 PC yang dibagi rata: setiap switch menangani 3 PC.

### Skema Alamat

| Perangkat | Interface | Alamat IP | Keterangan |
|---|---|---|---|
| Router R1 | `G0/0` | `192.168.1.1/24` | Gateway Divisi A |
| Router R1 | `G0/1` | `192.168.2.1/24` | Gateway Divisi B |
| PC1–PC3 (SW1) | — | `192.168.1.2` – `192.168.1.4` | Divisi A, GW `192.168.1.1` |
| PC4–PC6 (SW2) | — | `192.168.2.2` – `192.168.2.4` | Divisi B, GW `192.168.2.1` |

### Konfigurasi Router

```cisco
Router> enable
Router# configure terminal
Router(config)# hostname R1
Router(config)# interface g0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
Router(config)# interface g0/1
Router(config-if)# ip address 192.168.2.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# end
```

Karena kedua jaringan terhubung langsung (*directly connected*), router sudah otomatis mengetahui kedua segmen tanpa perlu menambahkan rute statis.

### Hasil

Pada topologi ini, terdapat 1 router yang menghubungkan dua segmen jaringan yang berbeda.

- **Divisi A** berada pada segmen network `192.168.1.0` dan menggunakan gateway `192.168.1.1`.
- **Divisi B** berada pada segmen network `192.168.2.0` dan menggunakan gateway `192.168.2.1`.

Saat dilakukan pengujian, koneksi **ping** antar komputer dalam satu divisi maupun lintas divisi berstatus **success**. Hal ini membuktikan bahwa router telah berhasil menghubungkan dan meneruskan paket data di antara kedua jaringan tersebut.

> **Catatan:** Setiap interface router (gateway) harus dikonfigurasi sebagai default gateway pada masing-masing PC agar dapat berkomunikasi lintas jaringan.

---

## Topologi 2: 2 Router + 2 Switch + 6 PC

Membuat topologi menggunakan **2 router** dan **2 switch**, dengan 6 PC yang dibagi rata: setiap switch menangani 3 PC.

### Skema Alamat

| Perangkat | Interface | Alamat IP | Keterangan |
|---|---|---|---|
| Router A | `G0/0` | `192.168.1.1/24` | Gateway Divisi A |
| Router A | `G0/1` | `192.168.3.1/24` | Link ke Router B |
| Router B | `G0/0` | `192.168.2.1/24` | Gateway Divisi B |
| Router B | `G0/1` | `192.168.3.2/24` | Link ke Router A |
| PC1–PC3 (SW1) | — | `192.168.1.2` – `192.168.1.4` | Divisi A, GW `192.168.1.1` |
| PC4–PC6 (SW2) | — | `192.168.2.2` – `192.168.2.4` | Divisi B, GW `192.168.2.1` |

### Konfigurasi Router

```cisco
! ---------- Router A ----------
RouterA> enable
RouterA# configure terminal
RouterA(config)# interface g0/0
RouterA(config-if)# ip address 192.168.1.1 255.255.255.0
RouterA(config-if)# no shutdown
RouterA(config-if)# exit
RouterA(config)# interface g0/1
RouterA(config-if)# ip address 192.168.3.1 255.255.255.0
RouterA(config-if)# no shutdown
RouterA(config-if)# exit

! ---------- Router B ----------
RouterB> enable
RouterB# configure terminal
RouterB(config)# interface g0/0
RouterB(config-if)# ip address 192.168.2.1 255.255.255.0
RouterB(config-if)# no shutdown
RouterB(config-if)# exit
RouterB(config)# interface g0/1
RouterB(config-if)# ip address 192.168.3.2 255.255.255.0
RouterB(config-if)# no shutdown
RouterB(config-if)# exit
```

### Static Routing

Agar kedua router saling mengenal jaringan satu sama lain, ditambahkan rute statis di masing-masing router:

```cisco
! Router A - menuju jaringan lokal Router B
RouterA(config)# ip route 192.168.2.0 255.255.255.0 192.168.3.2

! Router B - menuju jaringan lokal Router A
RouterB(config)# ip route 192.168.1.0 255.255.255.0 192.168.3.1
```

### Hasil

Pada topologi ini terdapat **2 router**. Router A mengelola jaringan lokal dengan segmen `192.168.1.0`, sedangkan Router B mengelola jaringan lokal dengan segmen `192.168.2.0`. Kedua router tersebut saling terhubung melalui segmen jaringan perantara, yaitu `192.168.3.0`.

Saat dilakukan pengujian koneksi:

- Ping dari **PC-1 ke Router B** berstatus **success**.
- Ping lintas jaringan dari **PC-1 ke PC-4**, ping pertama mengalami **failed** karena perangkat masih melakukan proses pencarian jalur (**ARP**).
- Pada percobaan ping berikutnya koneksi berstatus **success**, menandakan konfigurasi routing statis telah berhasil menghubungkan kedua jaringan tersebut.

---

## Konsep Routing

### Default Gateway

Default gateway adalah alamat IP interface router yang menjadi pintu keluar paket ketika host ingin mengirim data ke jaringan di luar subnet-nya. Tanpa default gateway, host hanya dapat berkomunikasi dalam satu jaringan lokal.

### Routing Table

Router menyimpan informasi jalur pada **routing table**, yang berisi kolom:

| Kolom | Keterangan |
|---|---|
| **Network Destination** | Tujuan jaringan |
| **Netmask** | Subnet mask tujuan |
| **Gateway** | Alamat next-hop (on-link jika langsung terhubung) |
| **Interface** | Interface keluar paket |
| **Metric** | Biaya/jarak jalur (semakin kecil semakin diprioritaskan) |

Perintah untuk menampilkan tabel routing:

```cisco
Router# show ip route
```

Contoh isi routing table pada **Router A**:

```
Codes: C - connected, S - static, ...
S    192.168.2.0/24   [1/0] via 192.168.3.2
C    192.168.1.0/24   is directly connected, GigabitEthernet0/0
C    192.168.3.0/24   is directly connected, GigabitEthernet0/1
```

Baris `S` menandakan rute statis yang kita tambahkan secara manual, sedangkan `C` adalah jaringan yang terhubung langsung ke interface router.

### ARP (Address Resolution Protocol)

ARP menerjemahkan alamat IP menjadi **MAC address** pada jaringan lokal, sehingga frame bisa dikirim ke perangkat tujuan. Pada ping pertama yang gagal di Topologi 2, perangkat sedang membangun **ARP cache** untuk memetakan IP tujuan ke MAC address. Setelah cache terisi, ping berikutnya berhasil.

Perintah untuk melihat ARP cache:

```cisco
Router# show arp
```

### Subnetting

Subnetting membagi satu jaringan besar menjadi sub-jaringan yang lebih kecil menggunakan subnet mask. Jumlah host yang tersedia dapat dihitung dengan rumus:

```
Jumlah host = 2^n - 2
```

dengan `n` = jumlah bit host. Angka `-2` dikurangi untuk **network address** (IP pertama) dan **broadcast address** (IP terakhir) yang tidak dapat dipakai host.

Contoh untuk `/24` (n = 8 bit host):

```
Jumlah host = 2^8 - 2 = 256 - 2 = 254 host
```

| Keterangan | Nilai |
|---|---|
| Network address | `192.168.1.0` |
| Host pertama | `192.168.1.1` |
| Host terakhir | `192.168.1.254` |
| Broadcast address | `192.168.1.255` |

---

## Kesimpulan

1. Router berperan penting dalam menghubungkan dua atau lebih jaringan yang berbeda segmen.
2. **Default gateway** wajib dikonfigurasi pada setiap host agar dapat berkomunikasi lintas jaringan.
3. **Static routing** berhasil menghubungkan jaringan pada Topologi 2 melalui segmen perantara `192.168.3.0`.
4. Kegagalan ping pertama pada komunikasi lintas jaringan bersifat normal karena proses **ARP** untuk membangun pemetaan IP ke MAC address.
5. Pemahaman **subnetting** dan **routing table** menjadi dasar utama dalam perancangan jaringan yang benar.

---

## Referensi File

- `soal.jpeg` — soal / instruksi praktikum
- `routing-cisco.pkt` — file simulasi Cisco Packet Tracer
- `cisco-routing_1226800010_Daffa M.docx` — laporan praktikum
