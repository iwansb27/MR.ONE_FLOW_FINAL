# MASTER HANDOFF — MR.ONE_Oto

Tanggal: 5 September 2026  
Nama proyek: MR.ONE_Oto  
Jenis: Automotive Media Publishing / Content Studio  
Status: Rebuild di AppDeploy baru — belum dideploy pada sesi tujuan  
Source of Truth: Blueprint + implementasi/handoff MR.ONE_Oto yang sudah ada dalam percakapan ini

## 1. TUJUAN PROYEK

MR.ONE_Oto adalah sistem operasional untuk membuat konten promosi kendaraan dari data/media kendaraan.

Alur utama:

Input kendaraan → Media → Review → AI Processing → Content → Review/Approval → Schedule → Publishing → History

Sistem harus sederhana, modular, dan mengutamakan komponen gratis jika memungkinkan.

Jangan melakukan redesign blueprint tanpa technical blocker.

## 2. BATASAN PROYEK

MR.ONE_Oto hanya untuk:

- kendaraan / automotive
- pembuatan konten promosi kendaraan
- image workflow
- video workflow
- review konten
- scheduling
- publishing
- operational history

Bukan bagian proyek ini:

- marketplace affiliate workflow
- Tokopedia/Shopee/Lazada workflow
- BOT Trading AI
- Coinrule
- R2
- proyek MR.ONE Content Studio V1

Jangan mencampurkan proyek-proyek tersebut.

## 3. ARSITEKTUR FINAL

### Application

MR.ONE_Oto

### Backend / Operational Layer

AppDeploy

AppDeploy digunakan sebagai backend/operasional teknis aplikasi.

### Database + Storage

Supabase

Supabase adalah:

- Database
- Storage
- Source of Truth

Project Supabase yang sudah digunakan:

`dtvpjgprxpmkxfztyfrx`

URL:

`https://dtvpjgprxpmkxfztyfrx.supabase.co`

Credential Supabase yang sudah terhubung secara aman:

`SUPABASE_PUBLISHABLE_KEY`

Jangan meminta user menempelkan secret/key ke chat.

## 4. KONDISI APPDEPLOY

Ada AppDeploy baru.

Pada koneksi AppDeploy baru yang sudah dicek:

`apps: []`

Artinya belum ada aplikasi yang terlihat pada koneksi tersebut.

Interpretasi yang benar:

AppDeploy baru belum memiliki app MR.ONE_Oto.

Jangan mengklaim app lama sudah berpindah.

App lama:

`mr-one-content-studio-publisher-tgt77z`

adalah app lama dan deployment quota-nya sudah habis.

Jangan menggunakan app lama untuk rebuild ini.

Targetnya adalah:

membuat ulang MR.ONE_Oto sebagai app baru pada AppDeploy baru.

## 5. DATABASE SUPABASE

Required tables yang sudah diverifikasi:

- posts
- post_products
- products
- media_assets
- content_builds
- music_library
- platform_variants
- publishing

Semua table tersebut sudah tersedia.

RLS sudah aktif pada:

- products
- media_assets
- platform_variants
- publishing
- posts
- post_products
- content_builds
- music_library

Tidak ada critical security lint yang ditemukan.

Performance advisor hanya menunjukkan beberapa informasi mengenai:

- FK yang belum memiliki index
- beberapa unused indexes

Tidak ada alasan untuk redesign database sekarang.

## 6. STORAGE

Bucket khusus MR.ONE_Oto:

`mr-one-oto-originals`

Konfigurasi:

- public: true
- maximum file size: 100 MB
- MIME: image/png, image/jpeg, image/webp, video/mp4

Storage policy yang sudah diperkeras:

### Public read

Hanya object dengan:

`bucket_id = 'mr-one-oto-originals'`

dan:

`name like 'mr-one-oto/%'`

### Upload

Anon/authenticated boleh INSERT hanya ke:

`mr-one-oto-originals`

dan path:

`mr-one-oto/%`

### Delete

Tidak ada broad anonymous DELETE policy.

Jangan mengembalikan broad anon DELETE hanya untuk memperbaiki cleanup.

## 7. MEDIA RULE

Setiap posting:

maksimal 5 media

Media dapat berupa:

- IMAGE
- VIDEO

IMAGE dan VIDEO harus mempunyai workflow terpisah.

Media diikat ke product melalui `media_assets.product_id`.

## 8. PRODUCT

Product kendaraan memiliki data utama:

- product name
- price
- specifications
- features
- benefits
- workflow type
- status
- confidence

Status awal:

`Draft`

### Penting

Schema `products` yang sekarang tidak boleh diasumsikan memiliki field `category`.

Jangan menambahkan category hanya karena source AppDeploy lama pernah menggunakannya.

Jika membutuhkan field baru, cek schema terlebih dahulu.

## 9. POST

Post memiliki konsep:

- title
- workflow_type
- status
- whatsapp_number
- whatsapp_filled_at
- generated_caption
- generated_cta
- master_video_url
- master_video_storage_path
- master_image_url
- master_image_storage_path
- content_state
- scheduled_at
- music_id
- reset_version

Post juga terhubung ke product melalui:

`post_products`

## 10. WHATSAPP RULE

WhatsApp adalah metadata posting.

Aturan:

1 posting = 1 nomor WhatsApp

WhatsApp bukan vehicle fact.

AI tidak boleh menggunakan nomor WhatsApp sebagai fakta kendaraan.

UI harus tetap menyediakan:

WhatsApp

Dengan konsep:

Nomor WhatsApp diatur sekali untuk posting ini.

## 11. PRODUCT FORM

Form kendaraan minimal:

### Nama kendaraan

Contoh:

Toyota Avanza 1.5 G

### Harga

Harga kendaraan.

### Keterangan

Keterangan dari user mengenai kendaraan.

### Workflow

Pilih:

- IMAGE
- VIDEO

## 12. KETERANGAN

Field Keterangan memang diminta user.

Schema product saat ini tidak memiliki field khusus description.

Jangan sembarangan mengubah schema.

Untuk sementara, informasi Keterangan harus disimpan menggunakan struktur yang sudah tersedia dan sesuai blueprint, terutama:

`posts.content_state`

atau struktur existing yang memang ditujukan untuk content state.

Jangan mengubah makna specifications hanya untuk menampung Keterangan jika itu menyebabkan data menjadi semantically incorrect.

## 13. CURRENT UI FOUNDATION

UI terakhir yang sudah dibuat memiliki:

- Dashboard
- Image Studio
- Video Studio
- AI Processing
- Review
- Schedule Queue
- Calendar
- Posting History

Bagian yang belum selesai masih dapat berupa placeholder/disabled sesuai tahap implementasi.

UI sudah memiliki:

- Product form
- Product selection
- WhatsApp
- Image upload
- Video upload
- persistence checklist

Namun implementasi belum lengkap.

## 14. CURRENT MEDIA UPLOAD

Endpoint:

`mr-one-media-upload_POST.ts`

Fungsi:

- membutuhkan productId
- memastikan product exists
- maksimal 5 files
- menerima: name, type, base64
- upload ke Supabase Storage
- insert ke media_assets
- mengembalikan metadata upload

Jenis:

- png
- jpeg
- webp
- mp4

Bucket yang benar:

`mr-one-oto-originals`

## 15. MEDIA UPLOAD KNOWN LIMITATIONS

Masih ada robustness issue:

1. DB insert gagal setelah storage upload
   - Object storage dapat menjadi orphan.

2. Multi-file upload partial failure
   - Sebagian object dapat sudah terupload sebelum error.

3. Base64 video
   - Bisa membutuhkan memory besar.

4. Endpoint belum melakukan explicit file-size validation
   - Bucket sendiri memiliki limit 100 MB.

Jangan menyelesaikan masalah ini dengan broad anonymous DELETE policy.

## 16. PRODUCT SAVE CURRENT STATE

Endpoint:

`mr-one-products_POST.ts`

Saat ini insert:

- product_name
- price
- specifications
- features
- benefits
- workflow_type
- status
- confidence

UI saat ini menyimpan:

- product_name
- price
- specifications: [description]
- workflow_type

Ini diketahui sebagai mapping sementara yang belum ideal.

Perlu diperbaiki agar Keterangan tidak dipaksakan menjadi specification.

## 17. POST CREATION

Endpoint yang sudah tersedia:

`mr-one-posts_POST.ts`

Membuat post dengan:

- title
- workflow_type
- status
- whatsapp_number
- generated_caption = null
- generated_cta = null
- scheduled_at = null
- content_state = {}
- reset_version = 0

Namun UI terakhir belum benar-benar menjalankan alur lengkap:

save post → product → post_products → media

Ini adalah salah satu titik implementasi berikutnya.

## 18. RESET RULES

Ini WAJIB dipertahankan.

### Reset Product

Reset Product:

- menghapus/reset product-related state
- menghapus/reset hubungan product/content build yang terkait
- tidak menghapus posting secara keseluruhan
- tidak menghapus hal yang memang ditetapkan tetap oleh blueprint

Tujuannya:

jika data kendaraan salah dimasukkan, user dapat mengganti produk.

### Reset Content

Reset Content:

- mempertahankan product
- mempertahankan media
- mempertahankan WhatsApp
- mempertahankan schedule
- hanya reset content-generation/content state

Tujuannya:

mengulang pembuatan konten tanpa harus menginput kendaraan dan media dari awal.

## 19. AI RULE

AI harus:

hanya menggunakan fakta kendaraan yang terlihat / diberikan / sudah direview.

AI tidak boleh:

- mengarang spesifikasi
- mengarang fitur
- mengarang benefit
- mengarang tahun kendaraan
- mengarang mileage
- mengarang mesin
- mengarang transmisi
- mengarang kondisi kendaraan
- mengarang harga

Jika informasi tidak tersedia:

jangan dibuat-buat.

## 20. AI EXTRACTION

Workflow AI extraction:

Media kendaraan → AI membaca → structured vehicle facts → User Review/Edit → Approved Facts

AI extraction sebelumnya dirancang menghasilkan:

- product_name
- price
- specifications
- features
- benefits
- confidence

Jika ada data yang tidak terlihat:

jangan ditebak.

## 21. REVIEW

Review adalah gate penting.

Alur:

AI Extraction → Review/Edit → Approval → Copy Generation

User harus dapat melihat dan mengoreksi data AI sebelum data digunakan untuk membuat caption/content.

## 22. COPY GENERATION

AI copy menggunakan hanya:

- approved vehicle facts
- posting context

WhatsApp hanya sebagai contact metadata.

AI menghasilkan:

- caption
- CTA

Caption harus sederhana, natural, dan promotional.

Tidak boleh membuat vehicle facts baru.

## 23. MUSIC

Tersedia table:

`music_library`

Blueprint sebelumnya meminta pilihan background music instrumental gratis.

Target:

3 pilihan musik instrumental gratis.

Jangan menjadikan music system kompleks.

## 24. IMAGE WORKFLOW

IMAGE workflow terpisah dari VIDEO.

Konsep:

Product → Image Media → AI/Content Processing → Review → Master Image → Schedule → Publish

## 25. VIDEO WORKFLOW

VIDEO workflow terpisah.

Konsep:

Product → Video Media → AI/Content Processing → Review → Master Video → Schedule → Publish

Target video sederhana sekitar:

20 detik

Tidak perlu membuat video production system kompleks.

## 26. PUBLISHING

Blueprint publishing terakhir:

### Facebook

Direct publishing.

### YouTube

Menggunakan Buffer.

### TikTok

Menggunakan Buffer.

Jangan menghapus/mengganti publishing architecture ini tanpa technical blocker.

## 27. SCHEDULE

Posting harian konsisten:

09:00 WIB

Platform pertama

09:05 WIB

Platform kedua

09:10 WIB

Platform ketiga

Jadwal harus persistent.

Schedule → state machine → retry/idempotency → operational logs.

## 28. PUBLISHING STATE

Publishing perlu mempunyai state machine yang jelas.

Minimal konsep:

- queued
- publishing
- published
- failed
- retry

Platform harus independen.

Kegagalan satu platform tidak boleh secara otomatis membuat platform lain dianggap gagal.

## 29. OPERATIONAL PRINCIPLES

Gunakan:

- persistence
- state machine
- retry
- idempotency
- operational logging

Tetapi tetap sederhana.

Jangan membangun queue infrastructure yang berlebihan jika Supabase/AppDeploy sudah cukup.

## 30. EXISTING VERIFICATION ENDPOINTS

Health:

`/api/mr-one-health`

Expected:

`ok = true`  
`provider = supabase`  
`sourceOfTruth = true`  
`missingSecret = false`

Schema:

`/api/mr-one-schema-status`

Expected semua:

`posts = true`  
`post_products = true`  
`products = true`  
`media_assets = true`  
`content_builds = true`  
`music_library = true`  
`platform_variants = true`  
`publishing = true`

Products:

`/api/mr-one-products`

Posts:

`/api/mr-one-posts`

Saat terakhir diverifikasi:

`products = []`  
`posts = []`

## 31. OPENAI CREDENTIAL

OpenAI credential belum terhubung.

Secure connection request:

Job ID:

`1ebf01cc-60bd-4aa2-9cec-134471aea54f`

Secure connection:

`https://floot.com/mcp-connect?request=1ebf01cc-60bd-4aa2-9cec-134471aea54f`

Jangan meminta API key ditempelkan ke chat.

AI implementation dilakukan setelah foundation persistence siap.

## 32. FLOOT PROJECT

Floot project yang sebelumnya menjadi foundation:

MR.ONE_Oto

Project ID:

`4d7b0fc8-0c07-494b-aabd-a501b4f202ab`

Internal API:

`https://4d7b0fc8-0c07-494b-aabd-a501b4f202ab.sandbox.floot.app`

Foundation source sudah pernah dimigrasikan dari AppDeploy lama.

File penting:

- `endpoints/mr-one-posts_GET.ts`
- `endpoints/mr-one-health_GET.ts`
- `endpoints/mr-one-posts_POST.ts`
- `pages/_index.tsx`
- `pages/_index.module.css`
- `endpoints/mr-one-products_GET.ts`
- `endpoints/mr-one-schema-status_GET.ts`
- `endpoints/mr-one-media-upload_POST.ts`

Floot project ini adalah reference/foundation.

Tetapi target sekarang:

rebuild MR.ONE_Oto pada AppDeploy baru menggunakan source/handoff yang sudah ada.

## 33. CURRENT EXACT CONTINUATION POINT

Jangan mulai dari blueprint lagi.

Blueprint sudah final.

Titik lanjut:

### PHASE A — Persistence Foundation

Implementasikan:

- Product creation
- Post creation
- Post ↔ Product relation
- Keterangan persistence
- WhatsApp persistence
- Media persistence
- Reset Product
- Reset Content

Kemudian:

### PHASE B — Review / AI

- AI extraction
- Review/Edit extracted facts
- Approved facts
- Caption + CTA generation

Kemudian:

### PHASE C — Content

- Image workflow
- Video workflow
- Master media

Kemudian:

### PHASE D — Scheduling

- Persistent schedule
- Queue
- State machine
- Retry
- Idempotency
- Operational logs

Kemudian:

### PHASE E — Publishing

- Facebook direct
- YouTube via Buffer
- TikTok via Buffer
- Platform-independent status
- Posting history

## 34. FIRST IMPLEMENTATION TASK

Saat AppDeploy tersedia:

### Task 1

Buat app baru:

`MR.ONE_Oto`

Jangan menggunakan:

`mr-one-content-studio-publisher-tgt77z`

karena itu app lama.

### Task 2

Rebuild foundation berdasarkan source/handoff ini.

### Task 3

Pastikan Supabase menjadi source of truth.

### Task 4

Implementasikan Product + Post + post_products.

### Task 5

Implementasikan:

- Reset Product
- Reset Content

### Task 6

Implementasikan media 1–5.

### Task 7

Typecheck.

### Task 8

Run endpoint/database verification.

### Task 9

Buat checkpoint setelah unit persistence stabil.

## 35. ATURAN OPERASIONAL UNTUK ASSISTANT

Assistant yang menerima handoff ini harus:

- Jangan mengarang akses.
- Jangan mengklaim AppDeploy sudah terhubung jika tool belum tersedia.
- Jangan mengklaim deployment berhasil tanpa hasil deployment.
- Jangan mengklaim database berubah tanpa hasil Supabase.
- Jangan meminta secret/API key melalui chat.
- Jangan redesign blueprint.
- Jangan mencampurkan MR.ONE_Oto dengan proyek lain.
- Jangan menghapus data production tanpa instruksi eksplisit.
- Jangan membuat app baru di AppDeploy lama.
- Jangan menggunakan R2.
- Jangan menambahkan dependency yang tidak diperlukan.
- Gunakan Supabase sebagai source of truth.
- Setelah perubahan, lakukan typecheck/testing.
- Jika ada technical blocker, jelaskan blocker secara spesifik.
- Jika akses tool tidak tersedia, berhenti pada batas akses tersebut dan jelaskan apa yang harus dilakukan user.

## 36. HANDOFF VERIFICATION REQUIRED

Assistant di chat tujuan harus terlebih dahulu menyatakan:

### Pemahaman

Bahwa proyek yang dilanjutkan adalah:

`MR.ONE_Oto`

dan bukan:

- MR.ONE Content Studio V1
- BOT Trading AI
- Coinrule

### Akses aktual

Harus menyebutkan apakah secara teknis tersedia:

- AppDeploy
- Supabase
- Floot source project

### Limitasi

Jika tool tidak tersedia, harus mengatakan secara eksplisit.

### Continuation point

Harus melanjutkan dari:

Persistence Foundation → Product/Post/Post_Product → Reset → Media

bukan mengulang blueprint.

## 37. DEFINITION OF DONE UNTUK FOUNDATION

Foundation dianggap selesai jika:

- Product dapat disimpan
- Post dapat disimpan
- Product terhubung ke Post
- WhatsApp tersimpan pada Post
- Keterangan tersimpan dengan benar
- Media dapat disimpan
- Maksimal 5 media enforced
- IMAGE/VIDEO workflow terpisah
- Reset Product bekerja
- Reset Content bekerja
- Data tetap ada setelah refresh
- Supabase menjadi source of truth
- Typecheck bersih
- Endpoint verification berhasil

Baru setelah itu lanjut ke AI.

## 38. STATUS SAAT HANDOFF DIBUAT

- Blueprint: FINAL / LOCKED
- Supabase: FOUNDATION READY
- Storage: READY
- Media endpoint: EXISTING / NEED ROBUSTNESS LATER
- Product persistence: PARTIAL
- Post persistence: PARTIAL
- Post ↔ Product: BELUM SELESAI DI UI
- WhatsApp persistence: BELUM SELESAI DI UI
- Reset Product: BELUM SELESAI
- Reset Content: BELUM SELESAI
- AI: BELUM DILANJUTKAN
- Review/Edit AI facts: BELUM DILANJUTKAN
- Scheduling: BELUM DILANJUTKAN
- Publishing: BELUM DILANJUTKAN
- AppDeploy baru: BELUM MEMILIKI APP MR.ONE_Oto

## 39. INSTRUKSI MULAI DI CHAT BARU

Setelah handoff ini ditempel ke chat baru, gunakan prompt:

> “Verifikasi handoff ini. Jangan mengarang akses. Sebutkan akses AppDeploy/Supabase yang benar-benar tersedia di chat ini. Jika AppDeploy tersedia, langsung mulai rebuild MR.ONE_Oto dari Persistence Foundation. Jika tidak tersedia, berhenti dan beri tahu saya tepatnya apa yang harus saya lakukan.”

---

**END OF MASTER HANDOFF**
