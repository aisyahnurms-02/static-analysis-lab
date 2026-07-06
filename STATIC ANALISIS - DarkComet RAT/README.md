# Sample 02 — Static Analysis of malware.exe.malz

**Binary:** malware.exe.malz  
**Jenis Analisis:** Static Analysis  
**Platform:** Microsoft Windows  
**Format Binary:** Portable Executable (PE)  
**Status:** Completed

---

# Pendahuluan

Dokumentasi ini berisi hasil **Static Analysis** terhadap binary **malware.exe.malz** sebagai bagian dari praktikum Reverse Engineering. Analisis dilakukan tanpa menjalankan executable sehingga seluruh informasi diperoleh melalui observasi terhadap struktur internal file **Portable Executable (PE)** menggunakan PEStudio.

Melalui proses ini diperoleh informasi mengenai struktur executable, hash file, section, import function, strings, metadata file, serta indikator awal yang dapat digunakan untuk mengidentifikasi karakteristik binary.

---

# Tujuan Analisis

Analisis ini bertujuan untuk:

- Mengidentifikasi karakteristik dasar executable.
- Memahami struktur Portable Executable (PE).
- Mengidentifikasi library Windows yang digunakan.
- Mengidentifikasi section dan entry point executable.
- Mendokumentasikan hasil analisis secara sistematis.

---

# Tools

Tools yang digunakan selama proses analisis:

- PEStudio
- Detect It Easy (DIE)
- Ghidra
- IDA Free
- HxD
- CertUtil

---

# 1. Triage

Tahap triage dilakukan untuk memperoleh informasi awal mengenai executable sebelum memasuki proses analisis lebih lanjut.

| Atribut | Nilai |
|---------|-------|
| Nama File | malware.exe.malz |
| Format | PE 32-bit |
| Arsitektur | x86 |
| Operating System | Microsoft Windows |
| Subsystem | GUI |
| Compiler | Microsoft Linker 2.25 / Borland Delphi |
| File Description | Remote Service Application |
| Original File Name | MSRSAAP.EXE |

Hasil identifikasi menunjukkan bahwa binary menggunakan format **Portable Executable (PE)** 32-bit yang berjalan pada sistem operasi Windows dengan subsystem GUI.

---

# 2. Hash Analysis

Hash digunakan sebagai identitas unik executable sehingga memudahkan proses identifikasi maupun pencarian informasi pada database malware.

| Algoritma | Nilai |
|-----------|-------|
| SHA-256 | `01E521B7DEA93A8EB80883D7BC535DEDC2A09EEB981729FD46F4EE677900C64E` |

Berdasarkan hasil analisis, ukuran file adalah sekitar **775.168 bytes** dengan nilai **entropy 6.534**, yang menunjukkan tingkat kompleksitas data cukup tinggi.

---

# 3. PE Header Analysis

PE Header menyimpan informasi dasar mengenai executable.

| Parameter | Hasil |
|-----------|-------|
| File Format | Portable Executable (PE) |
| Architecture | 32-bit |
| Subsystem | GUI |
| Entry Point | 0x0008F888 (.data) |
| Compiler | Microsoft Linker 2.25 / Borland Delphi |
| File Description | Remote Service Application |
| Original Filename | MSRSAAP.EXE |

### Hasil Analisis

Entry point executable berada pada alamat **0x0008F888** yang mengarah ke section **.data**. Kondisi ini cukup tidak umum karena executable Windows biasanya memulai eksekusi dari section **.text**, sehingga dapat menjadi indikasi adanya teknik packing atau modifikasi terhadap struktur executable.

---

# 4. Section Analysis

Executable terdiri dari empat section utama.

| Section | Virtual Address | Virtual Size | Karakteristik |
|---------|-----------------|--------------|---------------|
| .text | 0x00001000 | 579824 bytes | Execute, Read |
| .itext | 0x0008F800 | 6484 bytes | Execute, Read |
| .data | 0x00091000 | 15676 bytes | Read, Write |
| .bss | 0x00095000 | 29700 bytes | Virtual, Read/Write |

Informasi tambahan:

- Total section: **4**
- Entropy `.text` : **6.553**
- Entropy `.itext` : **5.954**
- Entropy `.data` : **4.835**

### Hasil Analisis

Struktur executable terdiri atas section standar dengan tambahan **.itext**. Entry point yang berada pada **.data** merupakan karakteristik yang tidak lazim sehingga layak dianalisis lebih lanjut menggunakan disassembler atau debugger.

---

# 5. Strings Analysis

Hasil PEStudio menunjukkan executable memiliki sekitar:

- **15.366 Strings**

Jumlah string yang cukup besar menunjukkan executable menyimpan banyak informasi tekstual yang dapat digunakan untuk analisis lanjutan, seperti:

- API Windows
- Pesan internal program
- Nama file
- Registry
- URL atau alamat jaringan (jika ada)
- Konfigurasi internal executable

Analisis lebih lanjut terhadap strings diperlukan untuk mengidentifikasi perilaku malware secara lebih rinci.

---

# 6. Import Function Analysis

PEStudio mendeteksi:

| Parameter | Nilai |
|-----------|-------|
| Import Functions | 153 |
| Libraries | > 8 DLL |

Hal ini menunjukkan executable memanfaatkan berbagai Windows API melalui lebih dari delapan library sistem.

Import Function dapat digunakan untuk mengetahui kemampuan executable, misalnya:

- Operasi file
- Registry
- Proses
- Thread
- Networking
- Memory management

Analisis lebih lanjut terhadap daftar import diperlukan untuk mengetahui fungsi yang digunakan secara spesifik.

---

# 7. Indicators of Compromise (IOC)

| Jenis | Nilai |
|------|-------|
| File Name | malware.exe.malz |
| Original File Name | MSRSAAP.EXE |
| File Description | Remote Service Application |
| SHA-256 | 01E521B7DEA93A8EB80883D7BC535DEDC2A09EEB981729FD46F4EE677900C64E |
| Architecture | PE 32-bit |
| Compiler | Microsoft Linker 2.25 / Borland Delphi |
| VirusTotal Detection | 67 / 71 |
| Strings | 15.366 |
| Import Functions | 153 |

Berdasarkan hasil PEStudio, executable memiliki **VirusTotal Detection Score 67/71**, yang mengindikasikan bahwa sebagian besar mesin antivirus mengenali file ini sebagai malware.

---

# 8. Ringkasan Analisis

Hasil Static Analysis menunjukkan bahwa:

- Binary menggunakan format Portable Executable (PE) 32-bit.
- Memiliki empat section utama (.text, .itext, .data, .bss).
- Entry point berada pada section `.data`, yang merupakan karakteristik tidak umum.
- Menggunakan lebih dari delapan library Windows dengan 153 import function.
- Memiliki sekitar 15.366 strings.
- Terdeteksi oleh **67 dari 71** antivirus pada VirusTotal.
- Dibangun menggunakan **Microsoft Linker 2.25 / Borland Delphi**.

---

# 9. Kesimpulan

Static Analysis memberikan gambaran awal mengenai karakteristik internal executable tanpa perlu menjalankan file tersebut. Berdasarkan hasil analisis menggunakan PEStudio, binary **malware.exe.malz** merupakan executable Windows 32-bit dengan struktur PE yang relatif lengkap.

Beberapa indikator seperti **VirusTotal Detection Score (67/71)**, **entry point pada section `.data`**, jumlah **import function** yang cukup banyak, serta ribuan **strings** menunjukkan bahwa executable ini memiliki karakteristik yang patut dicurigai dan layak dianalisis lebih lanjut menggunakan teknik **Dynamic Analysis**, **Debugging**, maupun **Reverse Engineering** untuk memahami perilaku sebenarnya ketika dijalankan.
