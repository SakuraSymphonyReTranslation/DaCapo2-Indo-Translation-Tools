# Verifikasi berkas DC2

Pemeriksaan persiapan folder dilakukan pada **22 Oktober 2026**. Ukuran dan SHA-256 tersimpan di [file-manifest.json](file-manifest.json).

## Paket tools

`BijikTools-DC2-ChoiceFix.rar` diuji dengan `7z t`: **Everything is Ok**, 85 berkas dan 21 folder. Paket kemudian diekstrak ke folder verifikasi terpisah.

Hash `BijikTools.dll` dan `DC2ChoiceSupport.dll` hasil ekstraksi cocok dengan salinan perbaikan yang sebelumnya diuji. Parser dalam paket juga digunakan langsung untuk mengekspor ulang sumber.

## Arsip skenario dan sistem

| Berkas | Ukuran (byte) | Signature | Entri | Entri DSC |
| --- | ---: | --- | ---: | ---: |
| `data01100.arc` | 4.002.120 | `PackFile` | 799 | 799 |
| `data01000.arc` | 16.200.826 | `PackFile` | 799 | 0 |
| `system.arc` | 60.406 | `PackFile` | 4 | 0 |

Semua nama entri unik, tabel entri berada dalam batas berkas, dan seluruh offset serta ukuran payload berada dalam batas arsip. `system.arc` memuat `ipl._bp`, `ipl._bp.bak`, `launcher._bp`, dan `VeraMoBd._ft`.

Salinan ketiga arsip di folder ini memiliki hash yang sama dengan berkas bernama sama di instalasi lokal saat pemeriksaan. Hal ini membuktikan salinannya cocok; status sebagai distribusi game yang belum dimodifikasi belum ditetapkan.

## Pemeriksaan sumber dan JSON

1. `data01100.arc` dibuka dengan `BgiArchive.Unpack` dari paket yang diekstrak.
2. Semua 799 skrip hasil unpack cocok byte demi byte dengan folder sumber `E:\Games\DaCapo2\data01100` yang dipakai pada perbaikan pilihan.
3. Semua skrip tersebut diekspor dengan `BgiScript.Extract` dari paket yang sama.
4. Semua 799 JSON cocok persis dengan ekspor sumber lengkap yang telah diverifikasi sebelumnya di `raw\data01000_json_with_choices_original`.

Uji sebelumnya untuk 63 skrip pilihan dan dua terjemahan contoh dijelaskan di [choices.md](choices.md).

## Pemeriksaan pack dengan kompresi

Skrip `1_1216_b1_cmn` dan `1_1218_d1_mnt` dipack dengan `BgiArchive.Pack` memakai ARC V1 dan kompresi DSC, lalu di-unpack kembali. Kedua skrip hasil unpack identik byte demi byte dengan input. Pemeriksaan ini memvalidasi opsi **Kompres?** pada dua contoh, bukan seluruh arsip patch terjemahan.

## Pekerjaan yang masih memerlukan pengujian

- Isi terjemahan pada `data01000.arc` yang tersedia belum diaudit secara menyeluruh.
- Arsip patch terjemahan lengkap baru belum dibuat dalam persiapan dokumentasi ini.
- GUI Bijik dan tampilan patch di dalam game belum diuji pada tahap ini. Pemeriksaan paket menggunakan pemanggilan parser langsung.
- Kompatibilitas dengan edisi/build DC2 lain belum ditetapkan.
