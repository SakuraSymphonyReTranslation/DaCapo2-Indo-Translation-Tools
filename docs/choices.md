# Pilihan DC2 pada BijikTools

Paket `BijikTools-DC2-ChoiceFix.rar` berisi BijikTools v0.6-beta2 yang diperbaiki untuk mengenali pilihan DC2 saat ekspor maupun impor. Jalankan `BijikTools.exe` dari hasil ekstraksi paket ini dan pertahankan `DC2ChoiceSupport.dll` di folder yang sama.

## Penyebab dan perbaikan

Pilihan DC2 disimpan dalam array string 8 atau 16 sebelum instruksi pemilihan `0x170`. Parser lama melewati instruksi debug `0x7F` dan mengategorikan referensi tersebut sebagai string internal sehingga teks pilihan tidak masuk JSON.

Perbaikan menambahkan satu pemanggilan setelah `BgiScript.DisassembleV1`. Helper mengenali pola referensi pilihan dan mengategorikannya sebagai `Message`. Ekspor dan impor menggunakan parser yang sama. DLL sebelum perbaikan disimpan sebagai `BijikTools.original.dll` dalam paket.

## Hasil pada sumber yang disediakan

- 799 skrip diekspor menjadi 799 JSON.
- 217 entri pilihan ditambahkan dalam 63 JSON.
- 155 teks pilihan unik; jumlah entri lebih besar karena ada pemakaian ulang pada alur berbeda.
- Dialog lama tetap identik setelah entri pilihan tambahan disisihkan.

Contoh `1_1216_b1_cmn.json`: `Prefer a puppet show` dan `Prefer a haunted house` masing-masing memiliki dua kemunculan. Terjemahkan kedua kemunculannya dan pertahankan urutan entri.

## Melanjutkan terjemahan lama

Ekspor lama yang belum memuat pilihan memiliki jumlah entri berbeda. Gunakan ekspor baru dari parser yang diperbaiki sebagai struktur lengkap, kemudian cocokkan dialog terjemahan lama ke dialog sumber yang sama. Masukkan pilihan pada posisi hasil ekspor baru. Jangan hanya menambahkan seluruh pilihan di akhir JSON karena impor mengikuti urutan referensi skrip.

Pasangkan JSON dengan skrip hasil unpack dari arsip sumber yang sama. JSON lengkap dari parser yang diperbaiki harus diimpor memakai parser yang diperbaiki pula.

## Validasi sebelumnya

Seluruh 63 skrip pilihan diuji ekspor → impor tanpa terjemahan: hasil identik byte demi byte, lalu ekspor kembali cocok. Terjemahan pilihan pada `1_1216_b1_cmn` dan `1_1218_d1_mnt` juga diuji; referensi pilihan dan hasil ekspor kembali cocok. Ukuran skrip bertambah 30 dan 31 byte. Byte dialog serta instruksi lain tetap sama pada kedua uji tersebut.

Uji tersebut memvalidasi parser dan skrip. Tampilan pilihan di dalam game setelah repack masih perlu diperiksa.
