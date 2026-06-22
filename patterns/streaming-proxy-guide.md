# Dokumentasi: Media Streaming Proxy & Metadata Integration di Nuxt 3

Dokumentasi ini membahas cara mengimplementasikan sistem streaming proxy mandiri di Nuxt 3 (Nitro) untuk mem-bypass batasan multi-IP dari layanan Debrid (seperti Real-Debrid) dan mengintegrasikan katalog film/series secara dinamis menggunakan API eksternal (TMDb / TVMaze) untuk antarmuka web stream internal (seperti FikVault).

---

## 1. Pemahaman Teori & Arsitektur

### Masalah: Pembatasan Multi-IP Debrid
Layanan debrid premium seperti Real-Debrid menyediakan link unduhan/streaming langsung berkecepatan tinggi dari file torrent. Namun, mereka menerapkan aturan ketat: **Satu akun hanya boleh streaming dari satu IP Address publik dalam satu waktu**. Jika akun digunakan di dua lokasi fisik (IP berbeda) secara bersamaan, akun akan diblokir otomatis.

### Solusi: Streaming Proxy (Server-Side Piping)
Daripada memberikan direct link Real-Debrid langsung ke browser client, kita melewatkan request streaming melalui server backend Nuxt 3 (Nitro). 
1. Client meminta segmen video ke endpoint server lokal: `/api/stream?url=<RD_URL>`.
2. Engine Nitro backend meminta data ke Real-Debrid menggunakan IP Server Nuxt (IP tunggal terdaftar).
3. Server mem-pipe stream data secara real-time kembali ke client.
4. Upstream (Real-Debrid) hanya melihat satu IP pengakses, yaitu IP server Anda.

```
+------------------+                   +-----------------------+                   +---------------+
|  Browser Client  | --(Req Video)-->  | Nuxt 3 Server (Nitro) | --(Req Video)-->  |  Real-Debrid  |
|  (Banyak User)   | <-- (Pipe Stream)- |     (IP Tetap Server) | <-- (Stream Data)- |  (IP Teraman) |
+------------------+                   +-----------------------+                   +---------------+
```

---

## 2. Implementasi Backend Nuxt 3 (Nitro Server Route)

Langkah krusial pada server stream proxy adalah mengimplementasikan **Range Requests (HTTP 206)**. Tanpa penanganan HTTP Range headers, pemutar video di browser tidak dapat melakukan *seeking* (maju-mundur detik video) dan browser terpaksa mendownload seluruh file dari nol.

Buat file di `/server/api/stream.ts`:

```typescript
import { defineEventHandler, getQuery, getHeader, setHeaders, sendStream } from 'h3'

export default defineEventHandler(async (event) => {
  const query = getQuery(event)
  const streamUrl = query.url as string // URL download instan dari Real-Debrid

  if (!streamUrl) {
    throw createError({ statusCode: 400, statusMessage: 'URL stream kosong.' })
  }

  // Ambil header 'Range' yang dikirim oleh video player browser (misal: bytes=0-1048576)
  const rangeHeader = getHeader(event, 'range')

  const headers: Record<string, string> = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36',
  }

  // Jika browser meminta byte range spesifik, teruskan ke upstream
  if (rangeHeader) {
    headers['Range'] = rangeHeader
  }

  try {
    const response = await fetch(streamUrl, { headers })

    const responseHeaders: Record<string, string> = {}
    const copyHeaders = [
      'content-type',
      'content-length',
      'content-range',
      'accept-ranges',
    ]

    // Salin header respons penting dari Real-Debrid ke client
    copyHeaders.forEach((h) => {
      const val = response.headers.get(h)
      if (val) responseHeaders[h] = val
    })

    setHeaders(event, responseHeaders)
    event.node.res.statusCode = response.status

    // Alirkan body response tanpa memuatnya ke memori RAM (Piping)
    if (response.body) {
      return sendStream(event, response.body)
    }
  } catch (error) {
    console.error('Streaming proxy error:', error)
    throw createError({ statusCode: 500, statusMessage: 'Gagal melakukan proxy stream.' })
  }
})
```

---

## 3. Integrasi Metadata (Daftar & Episode Film)

Untuk menampilkan katalog film layaknya Netflix, kita tidak boleh melakukan scraping langsung ke web Netflix karena proteksi mereka kuat. Sebagai gantinya, kita gunakan API Metadata seperti **TMDb** atau **TVMaze**.

### A. Mendapatkan Daftar Episode (Backend/Frontend Nuxt)
Endpoint untuk memuat detail episode dari TVMaze secara dinamis berdasarkan `show_id`:

```typescript
// Contoh fetch daftar episode menggunakan TVMaze API
const fetchEpisodes = async (showId: number) => {
  const data = await $fetch(`https://api.tvmaze.com/shows/${showId}/episodes`)
  return data // Mengembalikan array episode berisi: season, number, name, summary, image
}
```

### B. Struktur Resolusi Stream ke Pemutar Video
Format penamaan file torrent orisinal biasanya menggunakan standar `S{Season}E{Episode}` (misal: `S01E02`). 
Skema alur pemutarannya:

1. User memilih Episode (misal: Season 1, Episode 1).
2. Sistem mengambil objek episode rujukan dan menyusun string kueri: `Stranger Things S01E01`.
3. Kueri dikirim ke API Torrent Scraper untuk menemukan hash/magnet link torrent yang sesuai.
4. Kirim magnet link tersebut ke API Real-Debrid `/torrents/addMagnet`.
5. Ambil link hasil bypass dari Real-Debrid, lalu arahkan source video player ke proxy Nuxt:
   ```html
   <video controls>
     <source :src="`/api/stream?url=${encodeURIComponent(realDebridLink)}`" type="video/mp4" />
   </video>
   ```

---

## 4. Analisis Keamanan & Performa Server

*   **Piping Stream vs Loading File:** Server menggunakan `sendStream(event, response.body)` sehingga data dialirkan per segmen. Memori RAM server tidak akan membengkak meskipun file video berukuran 50GB.
*   **Security Token Protection:** Token API Real-Debrid dan API Key pihak ketiga (TMDb) tidak pernah dikirim ke browser client. Semua proses enkripsi/dekripsi tautan diselesaikan secara aman di server side.
*   **IP Address Masking:** Dari perspektif Real-Debrid, semua request download diklaim oleh IP VPS Anda. Metode ini efektif mencegah hukuman pemblokiran akun dari pembagian kredensial Real-Debrid bersama teman-teman Anda.
