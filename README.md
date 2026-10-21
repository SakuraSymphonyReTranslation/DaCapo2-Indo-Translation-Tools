# DaCapo2 Indo Translation Tools

Kumpulan tools dan berkas kerja untuk penerjemahan **Da Capo II (DC2)** pada engine **BGI / Ethornell**, dikelola oleh **Sakura Symphony Re;Translation**.

Alur pengerjaan mengikuti [Da Capo Patch Tools](https://github.com/SakuraSymphonyReTranslation/Da-Capo-Patch-Tools): **unpack arsip → extract JSON → cadangkan sumber → terjemahkan → insert script → pack ARC V1 → pasang hasil ke game**. Paket Bijik di sini sudah diperbaiki agar teks pilihan DC2 ikut diekspor dan dapat diimpor kembali.

## Isi folder

| Berkas | Keterangan |
| --- | --- |
| `BijikTools-DC2-ChoiceFix.rar` | BijikTools v0.6-beta2 dengan perbaikan ekspor dan impor pilihan DC2. Ekstrak seluruh isinya sebelum menjalankan tools. |
| `data01100.arc` | Arsip sumber skenario: 799 entri `PackFile`, seluruhnya memakai kompresi DSC. Hasil unpack cocok dengan sumber yang digunakan untuk memverifikasi perbaikan pilihan. |
| `data01000.arc` | Arsip kerja/patch yang tersedia saat ini: 799 entri `PackFile` tanpa kompresi DSC. Kelengkapan terjemahannya belum diaudit. |
| `system.arc` | Arsip sistem yang disediakan untuk disalin langsung saat pemasangan patch. |
| `BGI.gdb` | Database BGI dari folder kerja DC2, disimpan sebagai bahan pendukung. |
| `docs/choices.md` | Penjelasan perbaikan pilihan dan pemakaian JSON hasil parser yang diperbaiki. |
| `docs/verification.md` | Hasil pemeriksaan paket dan berkas. |
| `docs/file-manifest.json` | Ukuran, SHA-256, dan informasi header berkas yang disediakan. |

`data01100.arc` digunakan sebagai sumber untuk membuat terjemahan baru. `data01000.arc` di root folder adalah arsip patch/kerja yang sudah tersedia. Keduanya memiliki isi biner dan hash berbeda; jangan memakai hasil JSON dari satu sumber untuk mengimpor ke sumber lain tanpa mencocokkannya.

## Struktur dan alur kerja

Folder bertanda **lokal** dibuat setelah mengunduh atau mengekstrak tools.

```text
DaCapo2 Indo Translation Tools/
├── .gitattributes
├── .gitignore
├── README.md
├── BijikTools-DC2-ChoiceFix.rar
├── BGI.gdb
├── data01000.arc                     # arsip patch/kerja yang tersedia
├── data01100.arc                     # arsip sumber yang tersedia
├── system.arc
├── docs/
├── BijikTools-DC2-ChoiceFix/          # lokal: hasil ekstraksi paket
│   ├── BijikTools.exe
│   ├── BijikTools.dll
│   ├── BijikTools.original.dll
│   ├── DC2ChoiceSupport.dll
│   └── ...
└── work/                            # lokal: sumber dan hasil pengerjaan
    ├── data01100.arc                 # cadangan sumber setelah langkah 3
    ├── data01000/                    # skrip sumber hasil unpack
    ├── data01000_json/               # JSON untuk diterjemahkan
    ├── data01000res/                 # skrip hasil insert
    └── data01000.arc                 # arsip patch hasil pack
```

Nama folder contoh menggunakan `E:\Games\DaCapo2\DaCapo2 Indo Translation Tools`. Sesuaikan dengan lokasi folder Anda.

```text
work\data01000.arc (salinan sumber pada persiapan)
  → Unpack Archive → work\data01000
  → Extract Script → work\data01000_json
  → rename arsip input menjadi work\data01100.arc
  → edit terjemahan
  → Insert Script  → work\data01000res
  → Pack ARC V1    → work\data01000.arc (hasil patch)
```

## Persyaratan

- Windows.
- **.NET 8 Desktop Runtime x86** untuk paket Bijik ini. Paket menargetkan .NET 8 dan Windows Desktop.
- 7-Zip atau WinRAR untuk mengekstrak `.rar`.
- Editor JSON, misalnya Visual Studio Code.
- Instalasi DC2 yang sesuai dengan sumber ini untuk menguji hasil patch.

## Persiapan

1. Unduh repository melalui **Code > Download ZIP**, atau clone ke folder pilihan Anda:

   ```powershell
   git clone https://github.com/SakuraSymphonyReTranslation/DaCapo2-Indo-Translation-Tools.git 'DaCapo2 Indo Translation Tools'
   Set-Location -LiteralPath '.\DaCapo2 Indo Translation Tools'
   ```
2. Ekstrak `BijikTools-DC2-ChoiceFix.rar`. Jalankan `BijikTools.exe` dari folder hasil ekstraksi. Pertahankan semua DLL, konfigurasi runtime, folder `lib`, `app`, dan `external` bersama tools.
3. Pasang .NET 8 Desktop Runtime x86 bila belum tersedia.
4. Buat folder `work`. Untuk mulai dari sumber yang disediakan, **salin `data01100.arc` dari root folder sebagai `work\data01000.arc`**. Penamaan salinan ini mengikuti langkah di bawah; isinya tetap sama dengan sumber.

   ```powershell
   Set-Location -LiteralPath 'E:\Games\DaCapo2\DaCapo2 Indo Translation Tools'
   New-Item -ItemType Directory -Path '.\work' -Force | Out-Null
   Copy-Item -LiteralPath '.\data01100.arc' -Destination '.\work\data01000.arc'
   ```

   Jalankan persiapan pada folder `work` baru sebelum ada hasil patch bernama sama. Jika memakai arsip sumber dari instalasi sendiri, salin arsip tersebut sebagai `work\data01000.arc` dan gunakan pasangan skrip serta JSON hasil ekstraksinya selama seluruh proses.

## Panduan BijikTools

Pilih **Engine Target: `BGI / Ethornell`** selama unpack, extract, insert, dan pack. Gunakan Bijik yang diperbaiki dari paket repository ini.

### Langkah 1: Unpack `data01000.arc` ke `data01000`

1. Buka **Archive Tool**.
2. Pada **Archive File (Unpack) / Folder (Pack)**, klik **File...** dan pilih arsip input.
3. Isi **Output Path** dengan folder tujuan tanpa ekstensi `.arc`:

   ```text
   Input : E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000.arc
   Output: E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000
   ```

4. Klik **Unpack Archive** dan tunggu sampai selesai. Periksa log serta folder hasil. Untuk `data01100.arc` yang disediakan, hasil unpack berisi 799 skrip.

### Langkah 2: Extract script menjadi JSON

1. Buka **Extract Script**.
2. Pilih folder skrip hasil langkah 1 dan folder JSON yang terpisah:

   ```text
   Folder Script (Input): E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000
   Folder Output (json) : E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000_json
   ```

3. Klik **Gas Extract!**, lalu tunggu sampai proses selesai.
4. Periksa JSON hasil ekspor. Parser yang diperbaiki menyertakan dialog serta pilihan DC2. Pada sumber yang disediakan, 217 entri pilihan terdapat dalam 63 dari 799 JSON.

### Langkah 3: Rename arsip sumber menjadi `data01100.arc`

Setelah unpack dan ekstraksi JSON selesai, ubah nama arsip input **`work\data01000.arc` menjadi `work\data01100.arc`**. Simpan berkas tersebut sebagai cadangan sumber saat membuat arsip hasil patch. Cadangan ini berasal dari arsip yang Anda pilih pada persiapan; mengganti nama berkas tidak mengubah isinya.

```text
Sebelum: E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000.arc
Sesudah: E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01100.arc
```

```powershell
Rename-Item -LiteralPath '.\work\data01000.arc' -NewName 'data01100.arc'
```

Lakukan rename pada salinan di `work`. Folder skrip tetap `data01000`, dan folder JSON tetap `data01000_json`. Nama `work\data01000.arc` sekarang tersedia untuk hasil patch. Jika cadangan bernama `work\data01100.arc` sudah ada, simpan cadangan tersebut sebelum menyiapkan pengerjaan baru.

### Langkah 4: Terjemahkan JSON

1. Buka `work\data01000_json` di editor teks. Terjemahkan nilai `message` untuk dialog, narasi, dan pilihan; nilai `name` digunakan untuk nama pembicara bila ada.
2. Pertahankan key, urutan, jumlah entri, dan struktur JSON. Jaga tanda kutip, koma, serta escape seperti `\n` dan `\"` agar JSON tetap valid.
3. Terjemahkan semua kemunculan pilihan yang berulang. Contohnya, `Prefer a puppet show` dan `Prefer a haunted house` masing-masing muncul dua kali dalam `1_1216_b1_cmn.json` pada sumber yang disediakan.
4. Simpan berkas sebagai UTF-8. Bijik memasukkan teks ke skrip dengan CP932; gunakan karakter yang dapat dikodekan dan periksa hasilnya di game.

JSON untuk parser yang diperbaiki harus berasal dari ekspor parser tersebut. Saat melanjutkan ekspor lama yang belum memuat pilihan, cocokkan ulang terjemahan dialog ke ekspor lengkap sebelum impor. Lihat [catatan pilihan DC2](docs/choices.md).

### Langkah 5: Insert JSON menjadi script game

1. Buka **Insert Script** dan pilih engine **BGI / Ethornell**.
2. Isi folder berikut:

   ```text
   Folder Script (Awal)       : E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000
   Folder JSON               : E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000_json
   Folder Output (Script Baru): E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000res
   ```

3. Biarkan **Panjang WordWrap (0 = Mati)** bernilai `0` untuk mempertahankan pengaturan baris dalam JSON.
4. Klik **Gaskan!**, lalu periksa log sampai proses selesai.
5. Periksa folder `data01000res`. GUI melewati skrip yang tidak memiliki pasangan JSON; salin skrip tersebut dari `data01000` ke hasil akhir bila diperlukan. Periksa kegagalan insert secara terpisah dan perbaiki sebelum pack. Semua 799 nama skrip dari sumber harus tetap tersedia sebelum pack.

### Langkah 6: Pack menjadi `data01000.arc`

1. Buka **Archive Tool** dengan engine **BGI / Ethornell**.
2. Klik **Folder...** untuk memilih `work\data01000res`.
3. Isi **Output Path** dengan nama berkas hasil, termasuk `.arc`:

   ```text
   Input : E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000res
   Output: E:\Games\DaCapo2\DaCapo2 Indo Translation Tools\work\data01000.arc
   ```

4. Centang **Kompres?** bila ingin memakai kompresi DSC seperti arsip sumber. Opsi ini tersedia dalam paket Bijik v0.6-beta2 yang disediakan.
5. Klik **Pack ARC V1** untuk menghasilkan arsip dengan signature `PackFile`.
6. Tunggu sampai selesai, periksa log, lalu pastikan `work\data01000.arc` terbentuk. `work\data01100.arc` tetap menjadi cadangan sumber.

Arsip sumber yang disediakan memakai kompresi DSC pada semua skrip. Hasil pack tanpa kompresi dapat lebih besar walaupun jumlah entri sama. `data01000.arc` yang tersedia saat ini berukuran 16.200.826 byte dan tidak memakai kompresi DSC; `data01100.arc` berukuran 4.002.120 byte. Perbedaan ukuran saja tidak membuktikan kerusakan arsip.

## Pemasangan ke game

1. Tutup game. Cadangkan `data01000.arc`, `data01100.arc`, dan `system.arc` dari instalasi ke folder cadangan terpisah.
2. Salin **`work\data01000.arc` hasil langkah 6** ke folder game yang memuat `dacapo2.exe`.
3. Pertahankan arsip sumber `data01100.arc` di instalasi game.
4. Salin **`system.arc` dari root repository** ke folder game bila menggunakan berkas sistem yang disediakan bersama patch ini. Berkas tersebut langsung digunakan dan tidak perlu dipack ulang dalam alur skenario ini.
5. Jalankan game dan periksa pemuatan skenario, nama pembicara, dialog, pilihan, pergantian baris, serta save/load.

```text
work\data01000.arc → <folder game>\data01000.arc
system.arc         → <folder game>\system.arc
```

Untuk memakai arsip patch/kerja yang sudah tersedia, salin `data01000.arc` dari root folder sebagai sumber pemasangan pada langkah 2. Kelengkapan terjemahan dan tampilan pilihan pada arsip tersebut belum diaudit.

`BGI.gdb` disimpan sebagai bahan pendukung dan tidak digunakan dalam enam langkah konversi skenario. Folder hasil ekstraksi Bijik dan `work` bersifat lokal; `.gitignore` mengecualikannya dari Git.

## Status verifikasi

- Integritas paket `.rar` diperiksa dengan 7-Zip dan berhasil.
- Header, jumlah entri, batas offset, dan kompresi DSC arsip diperiksa.
- `data01100.arc` di folder ini menghasilkan 799 skrip identik byte demi byte dengan sumber perbaikan pilihan.
- Parser yang diekstrak dari paket menghasilkan 799 JSON identik dengan ekspor pilihan yang telah diverifikasi.
- Uji Pack ARC V1 dengan kompresi DSC pada dua skrip, lalu unpack kembali, mempertahankan isi kedua skrip byte demi byte.
- Uji sebelumnya untuk 63 skrip pilihan: ekspor → impor tanpa perubahan menghasilkan skrip identik; uji terjemahan pilihan pada dua skrip juga berhasil.
- Pembuatan arsip terjemahan baru dan pengujian di dalam game belum dilakukan dalam persiapan folder ini. Edisi/build game yang tepat belum diidentifikasi.

Lihat [rincian verifikasi](docs/verification.md) dan [manifest berkas](docs/file-manifest.json).

## Atribusi

Pemeliharaan kumpulan berkas: [SakuraSymphonyReTranslation](https://github.com/SakuraSymphonyReTranslation).

Alur panduan mengacu pada [Da Capo Patch Tools](https://github.com/SakuraSymphonyReTranslation/Da-Capo-Patch-Tools), dengan penamaan cadangan `data01100.arc` dan dukungan pilihan yang disesuaikan untuk DC2.

BijikTools, komponen eksternal, dan aset game merupakan karya pemilik masing-masing. Repository ini tidak menetapkan lisensi baru untuk komponen pihak ketiga atau aset game; informasi lisensi distribusi asal tetap berlaku.
