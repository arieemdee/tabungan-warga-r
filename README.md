# Roadmap Pemakaian Aplikasi Tabungan & Arisan Warga

Panduan bertahap supaya pengurus RT bisa mulai memakai aplikasi ini dari nol
sampai berjalan rutin tiap bulan. Ikuti urutan fase di bawah — jangan lompat
ke Fase 3 kalau Fase 1 (data warga) belum beres, karena hampir semua modul
lain bergantung pada data warga.

---

## Peran & Hak Akses

| Role | Bisa lihat (baca) | Bisa kelola (tulis) — default |
|---|---|---|
| **Tanpa login (publik)** | Beranda, Data Warga, Tabungan, Kas RT, Arisan (peserta/panggonan/periode/setoran/kocokan), Iuran Bulanan & Kematian, Gudang RT, Pengumuman | Kirim Pengaduan baru |
| **Sekretaris** | Semua yang publik lihat + Daftar Pengaduan, Surat-Menyurat | Pengumuman, tanggapan Pengaduan, Surat-Menyurat (buat & cetak) |
| **Bendahara** | Semua yang publik lihat + Daftar Pengaduan | Iuran Bulanan, Iuran Kematian, Kas RT, **Arisan** (kocok, peserta, periode, panggonan, setoran), **Gudang RT** (barang & peminjaman), **Koperasi Syariah** (pinjaman & angsuran) |
| **Admin** | Semua | Semua modul + Pengguna, Log Aktivitas, Backup, Pengaturan (menu **Utility**) |

> **Tabungan** dan **Data Warga** sengaja **admin-only secara default** (beda dari modul lain yang otomatis kebagian bendahara/sekretaris) — kalau mau bendahara/sekretaris tertentu bisa kelola itu, berikan lewat **Akses Tambahan** (lihat Fase 5), bukan berlaku otomatis ke semua orang dengan role itu.
>
> Surat-Menyurat sengaja jadi satu-satunya menu yang **tidak publik** (wajib login sekretaris/admin), karena isinya data pribadi warga (NIK, alasan mengurus SKTM, dll) — beda dari modul lain yang memang didesain transparan.

> Detail lengkap ada di `lib/roles.js`. Satu akun bisa dibuatkan lebih dari
> satu role kalau pengurusnya rangkap jabatan (mis. sekretaris merangkap
> bendahara) — buat lewat menu **Pengguna**.

---

## Fase 0 — Instalasi

- [ ] Jalankan `npm install`, lalu `npm start` (atau pakai `.exe` hasil build kalau tidak ada Node.js).
- [ ] Ikuti wizard instalasi pertama kali untuk isi koneksi database & identitas RT.
- [ ] Login pertama pakai akun admin default (`admin` / `admin123`) yang dibuat otomatis.
- [ ] **Segera ganti password admin** lewat menu **Pengguna**.
- [ ] Kalau baru upgrade dari versi sebelumnya (bukan instalasi baru), jalankan migration SQL tambahan yang belum pernah dijalankan, mis. `db_backup/07.migration_arisan.sql`.

## Fase 1 — Data Dasar

- [ ] Isi **Pengaturan Umum**: nama RT, alamat, nomor telepon, nama ketua RT, logo.
- [ ] Buat akun untuk tiap pengurus di menu **Pengguna**, kasih role yang sesuai (jangan semua dijadikan admin).
- [ ] Input seluruh **Data Warga** (menu Warga) — ini jadi rujukan untuk Tabungan, Iuran, Gudang, dan Arisan, jadi sebaiknya dituntaskan dulu sebelum lanjut ke fase berikutnya.

## Fase 2 — Modul Keuangan Rutin

Aktifkan sesuai kebutuhan RT — tidak semua RT pakai semua modul di fase ini.

- [ ] **Tabungan** — kalau RT punya program tabungan warga per keluarga (setoran/penarikan/saldo berjalan).
- [ ] **Iuran Bulanan** & **Iuran Kematian** — kalau ada iuran rutin/kondisional yang perlu direkap per warga per periode. Menunya ada di grup **Layanan Warga** (bukan di sini) karena statusnya sekarang publik — detail di Fase 4.
- [ ] **Kas RT Umum** — buku kas operasional RT (bukan saldo tabungan pribadi warga), dipakai untuk transparansi pemasukan/pengeluaran ke seluruh warga.

## Fase 3 — Arisan Warga (fitur baru)

- [ ] Buka menu **Arisan Warga → Kelola Peserta**, daftarkan warga yang ikut arisan (ambil dari data Warga yang sudah diinput di Fase 1). Kalau semua warga ikut arisan, tinggal pakai tombol **Tambahkan Semua Warga** sekali klik — tidak perlu pilih satu-satu.
- [ ] Buat **Periode** pertama (1 periode = 1 bulan pertemuan). Saat membuat periode, atur:
  - **Setoran per warga** — default Rp 10.000, bisa diubah per periode kalau nominalnya naik/turun.
  - **Dikocok berapa kali** — pilihan 1x / 2x / 3x, default 2x. Pengaturan ini per periode, jadi bulan depan bisa diubah tanpa memengaruhi periode yang sudah lewat.
- [ ] Sebelum atau saat pertemuan, bendahara buka halaman detail periode dan tandai warga yang sudah menyetor (nominal otomatis terisi sesuai default periode, bisa diubah kalau ada yang bayar beda jumlah). Total terkumpul & progres warga yang sudah setor langsung terlihat.
- [ ] Saat hari pertemuan, bendahara pencet **Kocok** — sistem otomatis mengundi sejumlah pemenang sesuai pengaturan periode (1x/2x/3x) dari peserta aktif yang belum pernah menang di putaran berjalan.
- [ ] Ulangi tiap bulan: buat periode baru → catat setoran → kocok. Peserta yang sudah menang ditandai badge hijau **"Sudah Dapat"** dan otomatis tidak diundi lagi sampai satu putaran penuh selesai.
- [ ] Kalau semua peserta aktif sudah kebagian, kocokan berikutnya **otomatis memulai putaran baru** (pool direset) — tidak perlu aksi manual.
- [ ] Kalau ada warga berhenti ikut arisan, gunakan **Nonaktifkan** di Kelola Peserta (bukan Hapus), supaya riwayat kemenangan & setorannya tetap tersimpan.
- [ ] Warga bisa cek status peserta, progres setoran, dan riwayat kocokan kapan saja lewat `/arisan` tanpa perlu login.
- [ ] **Panggonan (lokasi bergilir)** — di menu **Kelola Panggonan**, kandidat lokasi/tuan rumah diambil langsung dari daftar warga yang sudah jadi peserta arisan (pilih dari dropdown, bukan tulis manual), nama & alamat otomatis terisi dari data warga. Bisa juga pakai tombol **Tambahkan Semua Peserta** kalau semua peserta arisan mau ikut giliran panggonan. Berbeda dari model arisan lama yang otomatis pemenang jadi tuan rumah berikutnya, sekarang panggonan **dikocok sendiri** (terpisah dari kocok uang, selalu 1x per periode) lewat tombol **Kocok Panggonan** di tabel periode — hasilnya jadi lokasi pertemuan berikutnya. Sama seperti peserta uang, panggonan yang sudah kebagian giliran otomatis tidak diundi lagi sampai satu putaran penuh selesai, lalu direset otomatis.
- [ ] Kalau warga yang kena kocok panggonan ternyata **berhalangan** (ada acara, dll) saat itu juga, pencet tombol **Tidak Jadi** di sebelah hasil kocokan — sistem otomatis membatalkan hasilnya dan tombol **Kocok Panggonan** muncul lagi untuk diundi ulang. Warga yang sudah bilang tidak jadi tidak akan diundi ulang lagi untuk periode yang sama, tapi tetap ikut normal di periode berikutnya.
- [ ] Sama seperti panggonan, kalau salah satu **pemenang uang** menolak (mis. alasan belum siap kalau sekalian kebagian giliran panggonan), pencet ikon <i class="fa fa-times-circle"></i> **Tidak Jadi** di sebelah namanya. Hanya slot pemenang itu yang dibatalkan & bisa dikocok ulang — pemenang lain di periode yang sama tidak ikut terganggu. Pencet tombol **Kocok** lagi untuk mengisi slot yang kosong tadi.
- [ ] Semua tabel di modul Arisan (peserta, periode, panggonan, status setoran) sudah otomatis punya **pagination** (10 baris/halaman) begitu datanya banyak — tidak perlu setting tambahan.

## Fase 4 — Layanan & Aset Warga

Menu **Pengaduan**, **Pengumuman**, **Iuran**, **Koperasi Syariah**, **Gudang RT**, dan **Surat-Menyurat** digabung jadi satu grup sidebar **"Layanan Warga"**.

- [ ] **Pengaduan** — sosialisasikan ke warga bahwa mereka bisa lapor tanpa login lewat menu Pengaduan; pengurus (sekretaris/admin) menanggapi lewat **Daftar Pengaduan**.
- [ ] **Pengumuman** — buat lewat Layanan Warga → Pengumuman → Buat Pengumuman (judul, isi, tanggal mulai/selesai). Bisa dilihat semua warga tanpa login, otomatis dapat label **Akan Datang / Berlangsung / Berakhir** sesuai tanggalnya. Edit/hapus hanya sekretaris/admin.
- [ ] **Iuran Bulanan & Iuran Kematian** — dibuka publik supaya semua warga bisa lihat siapa yang belum bayar dan saling mengingatkan, sama seperti transparansi Kas RT/Arisan. Tombol catat/batalkan pembayaran tetap terkunci khusus bendahara/admin (`requireRole('keuangan')`) — pengunjung publik & role lain cuma bisa lihat status, tidak ada tombol aksi yang muncul.
- [ ] **Koperasi Syariah (Qardhul Hasan)** — pinjaman kebajikan tanpa bunga/riba. Bendahara catat pinjaman baru (warga, jumlah, keperluan, tanggal), lalu catat angsuran tiap kali warga bayar. Sistem **otomatis menolak** kalau angsuran yang diinput melebihi sisa pokok — jadi tidak mungkin ada tambahan di luar pokok yang dipinjam, sesuai akad Qardhul Hasan. Status otomatis jadi "Lunas" begitu total angsuran sama dengan pokok pinjaman. Dibuka publik juga supaya warga bisa saling mengingatkan siapa yang masih punya pinjaman berjalan.
- [ ] **Gudang RT** — kalau RT punya inventaris (tenda, kursi, sound system, dll) yang sering dipinjam warga, catat barang & riwayat peminjamannya di sini. Dibuka publik juga, jadi siapa saja bisa lihat barang apa yang sedang dipinjam siapa & belum dikembalikan — supaya warga bisa saling mengingatkan. Tombol tambah/edit/hapus barang & catat pinjam/kembali tetap khusus bendahara/admin.
- [ ] **Surat-Menyurat** — beda dari modul lain, halaman ini **wajib login** (sekretaris/admin) dan **tidak publik**, karena isinya data pribadi warga (NIK, alasan mengurus SKTM, dll). Alurnya: Layanan Warga → Surat-Menyurat → Buat Surat → pilih warga pemohon, jenis surat (Pengantar/Domisili/SKTM/Usaha/Lainnya), isi keperluan → sistem otomatis bikin nomor surat & render PDF siap cetak dengan kop surat sesuai data di Pengaturan Umum (nama RT, alamat, nama Ketua RT). Riwayat surat yang pernah diterbitkan bisa dicek & dicetak ulang kapan saja. Kalau butuh jenis surat lain, pilih **"Surat Keterangan Lainnya"** lalu tulis isinya bebas.

## Fase 5 — Kontrol & Keamanan (Admin)

- [ ] Cek **Log Aktivitas** secara berkala untuk memantau siapa mengubah apa (termasuk histori kocok arisan).
- [ ] Jadwalkan **Backup & Restore** database secara rutin (mingguan/bulanan), terutama sebelum ganti perangkat.
- [ ] Review ulang role tiap pengurus di menu **Pengguna** saat ada pergantian kepengurusan RT.
- [ ] **Akses Tambahan (delegasi & pembagian tugas)** — buka menu **Pengguna → Atur Akses** pada user yang mau diatur (tombol ini cuma muncul di baris bendahara/sekretaris, bukan admin — kalau di tabel Pengguna baru ada 1 akun admin, buat dulu akun bendahara/sekretarisnya lewat tombol **Tambah**). Centang = beri akses modul itu, hapus centang = **cabut** akses modul itu — berlaku dua arah, jadi bisa juga membatasi akses yang biasanya otomatis didapat dari role. Modulnya: Tabungan, **Iuran Bulanan (Swadaya)**, **Iuran Kematian** (sengaja dipisah), Kas RT, Arisan, Gudang RT, Data Warga, Pengumuman, Surat-Menyurat, Pengaduan. Cocok untuk kasus 2 bendahara yang tugasnya dibagi — misalnya bendahara A cuma pegang Iuran Kematian, bendahara B cuma pegang Iuran Bulanan, walau keduanya sama-sama role Bendahara yang defaultnya dapat dua-duanya. Isi **"Berlaku Sampai"** untuk perubahan sementara (otomatis balik ke default role setelah tanggal itu lewat, cocok untuk cuti/delegasi), atau kosongkan untuk permanen sampai diubah manual lagi.

---

## Siklus Bulanan yang Disarankan (setelah semua fase di atas selesai)

1. Awal bulan: bendahara catat transaksi Kas RT & setoran Iuran/Tabungan.
2. Buat periode Arisan baru untuk bulan berjalan.
3. Saat pertemuan warga: kocok arisan langsung di depan warga (layar bisa diarahkan ke proyektor karena halaman bisa dibuka publik).
4. Setelah pertemuan: sekretaris tindak lanjuti Pengaduan yang masuk, update Gudang RT kalau ada barang dipinjam/dikembalikan.
5. Akhir bulan: admin cek Log Aktivitas & lakukan Backup.

---

## Potensi Pengembangan Lanjutan

Belum diimplementasikan, bisa jadi roadmap teknis berikutnya kalau dibutuhkan:

- Notifikasi WhatsApp/Telegram otomatis ke grup warga saat kocokan arisan selesai.
- Export riwayat arisan & setoran ke Excel (project sudah punya `exceljs`, tinggal dipakaikan ke modul Arisan).
- Dashboard ringkasan gabungan (Kas + Iuran + Arisan) di halaman Beranda.
- Pengaturan nominal setoran default global (menu Pengaturan Umum) supaya tidak perlu isi ulang tiap bikin periode baru.
- Pengumuman yang sedang berlangsung tampil otomatis di halaman Beranda (saat ini harus buka menu Pengumuman sendiri).
- Jenis & template Surat masih tetap di kode (5 jenis bawaan); bisa dikembangkan jadi bisa diatur sendiri dari UI kalau RT butuh jenis surat lain di luar itu secara rutin.
