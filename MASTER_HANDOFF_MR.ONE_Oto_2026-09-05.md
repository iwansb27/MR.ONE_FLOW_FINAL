# MASTER BASELINE — MR.ONE — OTO & AFFILIATE

Tanggal: 5 September 2026
Status: BASELINE FINAL — dasar implementasi

## 1. TUJUAN
MR.ONE adalah satu tool sederhana dengan hanya dua mode: OTO dan AFFILIATE.
Semua konten berupa video final yang dibuat di luar MR.ONE dan maksimal 20 detik.
MR.ONE tidak membuat video, tidak membuat slideshow, tidak membaca screenshot marketplace, dan tidak mempunyai IMAGE mode aktif.

## 2. MODE OTO
OTO khusus kendaraan.
Input utama:
- Merk
- Tipe/Model
- Tahun
- No. HP
- Upload 1 video final
- Judul
- Deskripsi
- Kategori

Alur:
Input → Upload Video → bantu isi metadata → Review → Approval → Schedule → Publish → History.

OTO tidak mempunyai affiliate_link. Affiliate link harus tetap NULL/tidak digunakan untuk OTO.
Tidak ada perubahan konsep OTO dari rencana yang telah disepakati.

## 3. MODE AFFILIATE
AFFILIATE adalah mode promosi produk dengan 1 video final per produk, maksimal 20 detik.
Input utama:
- Produk
- Affiliate link
- Upload 1 video final
- Judul
- Deskripsi
- Kategori

ChatGPT membantu user mengisi Judul, Deskripsi, dan Kategori berdasarkan informasi yang diberikan user. AI media generation/extraction bukan bagian dari MR.ONE.

Affiliate link boleh berasal dari Shopee, Lazada, Tokopedia, atau TikTok Shop.
Platform tersebut adalah sumber/tujuan affiliate link, BUKAN publishing channel MR.ONE.

## 4. PRODUKSI MEDIA
User membuat video sendiri di luar MR.ONE.
Video dibuat netral agar tidak terlihat sebagai materi marketplace tertentu.
MR.ONE hanya menerima FINAL VIDEO.

Tidak ada di dalam MR.ONE:
- screenshot marketplace sebagai input produksi
- image recognition untuk produk
- image cleaning
- slideshow generation
- video generation
- voice generation
- music generation
- affiliate-link analysis untuk membuat media

## 5. MEDIA STANDARD
Satu produk = satu video final.
Master yang direkomendasikan:
- MP4
- 9:16
- 1080 × 1920
- maksimal 20 detik

Batas upload internal MR.ONE: maksimal 20 MB per video.
Maksimal 1 video per produk/post.

## 6. CHANNEL PUBLISHING
Publishing channel hanya:
- TikTok
- Facebook
- YouTube

Shopee, Lazada, Tokopedia, dan TikTok Shop bukan publishing channel.
TikTok dan TikTok Shop harus diperlakukan sebagai dua entitas berbeda.

## 7. ATURAN AFFILIATE PER CHANNEL
Untuk mode AFFILIATE:

### TikTok
- Publish video.
- Affiliate link TIDAK dimasukkan ke caption/deskripsi.
- TikTok dipakai sebagai channel penyebaran informasi/viewers.
- MR.ONE tidak menjadikan TikTok sebagai channel monetisasi affiliate-link.
- Tidak adanya link tidak berarti konten otomatis non-komersial; jika konten memenuhi kondisi disclosure platform, disclosure komersial tetap harus dilakukan.

### Facebook
- Publish video.
- Affiliate link dimasukkan ke deskripsi/caption sesuai format yang digunakan MR.ONE.
- Jika disclosure komersial diperlukan oleh kebijakan Meta, jangan dihilangkan.

### YouTube
- Publish video.
- Affiliate link dimasukkan ke deskripsi.
- Jika disclosure komersial/affiliate diperlukan, ikuti mekanisme disclosure YouTube yang berlaku.

Aturan utama: TikTok = video tanpa affiliate link; Facebook dan YouTube = video dengan affiliate link.

## 8. OTO PER CHANNEL
Untuk OTO:
- TikTok: video.
- Facebook: video.
- YouTube: video.

Tidak ada affiliate link di OTO.

## 9. PEMBAGIAN TUGAS
### User
- Menyiapkan video final.
- Mengisi data produk/kendaraan dan affiliate link bila mode AFFILIATE.
- Melakukan review dan approval.

### ChatGPT
- Membantu mengisi Judul.
- Membantu mengisi Deskripsi.
- Membantu mengisi Kategori.
- Tidak mengarang fakta yang tidak diberikan user.

### MR.ONE
- Form input.
- Upload dan penyimpanan video.
- Penyimpanan metadata.
- Review/Approval.
- Scheduling.
- Publishing orchestration.
- History.
- Reset Product.
- Reset Content.

### Supabase
- Database.
- Storage.
- Source of Truth.

Supabase tidak perlu dipasangi AI.

### Supabase Edge Functions
Digunakan sebagai server-side integration layer bila diperlukan untuk komunikasi aman dengan Buffer/API.
Secret/API key tidak boleh ditaruh di browser/frontend atau ditempelkan user ke chat.

### Buffer
Jalur publishing untuk channel sosial yang terhubung, termasuk TikTok, Facebook, dan YouTube sesuai koneksi/kemampuan akun yang tersedia.
Jangan menganggap marketplace sebagai channel Buffer.

## 10. AI SCOPE
AI tidak membuat media.
AI hanya membantu metadata percakapan: Judul, Deskripsi, Kategori.
AI wajib hanya memakai fakta yang diberikan/ditetapkan user.
AI tidak boleh mengarang:
- spesifikasi
- fitur
- harga
- tahun
- mileage
- mesin
- transmisi
- kondisi
- benefit faktual yang tidak diberikan

Jika fakta tidak tersedia, gunakan bahasa netral atau minta data yang diperlukan; jangan menebak.

## 11. DATABASE BASELINE
Gunakan Supabase yang sudah ada sebagai fondasi, tanpa membuat proyek database baru.
Pisahkan domain dengan field/aturan yang tegas:
- mode = OTO | AFFILIATE

Aturan data:
- OTO → affiliate_link NULL/tidak digunakan.
- AFFILIATE → affiliate_link boleh diisi.

Affiliate source/destination dan publishing channel adalah konsep berbeda.
Jika diperlukan untuk implementasi, simpan sumber affiliate secara eksplisit, misalnya Shopee/Lazada/Tokopedia/TikTok Shop, tanpa memasukkannya ke daftar publishing platform.

Schema lama boleh tetap ada untuk kompatibilitas/migrasi, tetapi IMAGE/AI media-generation fields tidak boleh menjadi workflow aktif.
Jangan menghapus data lama secara membabi buta.

## 12. DATA YANG AKTIF
Workflow aktif berfokus pada:
- posts
- products/items
- post_products bila masih diperlukan untuk relasi
- media_assets untuk final video
- publishing
- platform/channel configuration

Field lama seperti master_image, clean_image, content_builds, music_library, dan workflow IMAGE dapat dipertahankan sementara untuk backward compatibility, tetapi tidak digunakan oleh workflow baru.

Untuk workflow baru, 1 post/product = 1 final video.

## 13. STORAGE
Bucket Supabase yang sudah digunakan:
`mr-one-oto-originals`

Video aktif:
- MP4
- maksimal 20 MB pada validasi aplikasi
- 1 video per post/product

Storage policy harus tetap least-privilege. Jangan membuat broad anonymous DELETE hanya untuk cleanup.

Jika upload database insert gagal setelah storage upload, implementasikan cleanup server-side/transactional compensation tanpa membuka DELETE publik.

## 14. PERSISTENCE
Semua state penting harus persistent di Supabase:
- product/item
- mode
- title
- description
- category
- affiliate link jika AFFILIATE
- video path/public URL
- WhatsApp untuk OTO
- review/approval state
- schedule
- publishing state
- error/retry information
- history

Jangan menjadikan browser/local state sebagai source of truth.

## 15. RESET
### Reset Product
Memungkinkan user mengganti produk/data yang salah tanpa menghapus keseluruhan posting secara tidak perlu.

### Reset Content
Memungkinkan pengulangan content metadata/state tanpa harus mengulang data dasar yang masih benar.

Reset harus menjaga data yang memang ditetapkan tetap dan tidak merusak schedule/publishing yang tidak terkait.

## 16. PUBLISHING STATE
Per channel harus independen.
Minimal:
- queued
- publishing
- published
- failed
- retry

Kegagalan TikTok tidak boleh membuat Facebook/YouTube dianggap gagal.
Publishing harus idempotent agar retry tidak membuat duplikasi.
Simpan external post ID/Buffer post ID bila tersedia.

## 17. SCHEDULING
Scheduling harus persistent.
Default jadwal yang pernah dipakai tetap dapat menjadi preset, tetapi waktu/platform tidak boleh dianggap sebagai fakta bisnis yang tidak bisa diubah.
User dapat memilih schedule.
Per channel mempunyai status sendiri.

## 18. CAPTION GENERATION LOGIC
MR.ONE tidak perlu mempunyai AI service internal untuk membuat caption.
ChatGPT membantu user secara percakapan.
Hasil yang sudah disetujui user kemudian disimpan ke Supabase.

Caption final harus dapat berbeda per channel:
- TikTok AFFILIATE: tanpa affiliate link.
- Facebook AFFILIATE: dengan affiliate link.
- YouTube AFFILIATE: dengan affiliate link.
- OTO: tanpa affiliate link di semua channel.

Affiliate link tidak boleh dipakai sebagai media URL.

## 19. DISCLOSURE
Sistem tidak boleh menyimpulkan bahwa tidak adanya affiliate link berarti tidak ada kewajiban disclosure.
Jika konten merupakan konten komersial/affiliate dan platform meminta disclosure, user harus dapat menandai/menjalankan disclosure sesuai platform.

## 20. UI BASELINE
Home hanya mempunyai dua pilihan utama:
1. OTO
2. AFFILIATE

Tidak ada IMAGE mode.
Tidak ada Video Studio yang membuat video.
Tidak ada AI Processing pipeline untuk membaca gambar marketplace.

UI inti:
- Dashboard
- OTO
- Affiliate
- Review/Approval
- Schedule
- Publishing History

## 21. SUCCESS CRITERIA
Baseline dianggap berjalan jika:
1. User dapat membuat OTO dengan data kendaraan + 1 video.
2. OTO tidak memiliki affiliate link.
3. User dapat membuat AFFILIATE dengan 1 produk + 1 video + affiliate link.
4. Satu video maksimal 20 detik dan maksimal 20 MB.
5. Data persistent setelah refresh/reopen.
6. User dapat Review dan Approval.
7. User dapat Schedule.
8. TikTok menerima video tanpa affiliate link.
9. Facebook menerima video dengan affiliate link pada deskripsi/caption.
10. YouTube menerima video dengan affiliate link pada deskripsi.
11. Channel gagal secara independen dan dapat retry tanpa duplikasi.
12. Reset Product dan Reset Content bekerja sesuai aturan.

## 22. IMPLEMENTATION ORDER
Bangun langsung berdasarkan baseline ini. Urutan:

PHASE 1 — Foundation
- Rapikan domain OTO/AFFILIATE.
- Pastikan persistence Supabase.
- Video-only media validation.
- Product/Post relation.
- Reset Product/Content.

PHASE 2 — Metadata
- Title.
- Description.
- Category.
- Affiliate source/link untuk AFFILIATE.
- Channel-specific caption variants.

PHASE 3 — Review & Schedule
- Review.
- Approval.
- Persistent scheduling.
- Per-channel state.

PHASE 4 — Publishing
- Supabase Edge Function server-side integration.
- Buffer publishing.
- TikTok no-link rule.
- Facebook/YouTube affiliate-link rule.
- Idempotency/retry/logging.

PHASE 5 — Verification
- End-to-end OTO test.
- End-to-end AFFILIATE test.
- Refresh persistence test.
- Reset test.
- Per-channel publishing test.
- Failure/retry test.

## 23. HARD RULES
- Jangan mengembalikan AppDeploy ke arsitektur.
- Jangan mengembalikan IMAGE mode.
- Jangan membangun AI video/image generation.
- Jangan menjadikan Shopee/Lazada/Tokopedia/TikTok Shop sebagai publishing channel.
- Jangan memasukkan affiliate link ke TikTok AFFILIATE.
- Jangan menghapus affiliate link untuk Facebook/YouTube AFFILIATE.
- Jangan memberi affiliate_link pada OTO.
- Jangan meminta user menempelkan secret/API key ke chat.
- Jangan mengarang fakta produk/kendaraan.
- Jangan mengubah blueprint ini tanpa technical blocker nyata.

## 24. BASELINE LOCK
Dokumen ini adalah dasar implementasi MR.ONE — OTO & AFFILIATE.
Semua pekerjaan pembangunan setelah ini harus mengikuti baseline ini.
Jika ditemukan technical blocker, selesaikan dengan perubahan minimal yang menjaga tujuan dan aturan bisnis baseline.
