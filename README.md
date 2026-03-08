# GesaAI

Aplikasi AI chat open-source yang mendukung berbagai provider seperti Groq, OpenAI, Anthropic, Google, Mistral, dan lainnya.

<img src="./public/readme/screenshot.png" alt="GesaAI" width="600">

## Fitur Utama

- 🤖 Dukungan multi-provider: Groq, OpenAI, Anthropic, Google Gemini, Mistral, Perplexity, OpenRouter, Azure OpenAI, Ollama
- 🔑 API key bisa diset per-user atau via environment variable
- 💾 Penyimpanan percakapan menggunakan Supabase (PostgreSQL)
- 🌐 Antarmuka berbahasa internasional (i18n)
- 📱 PWA-ready

---

## Tutorial Cara Run (Lokal)

### Prasyarat

Pastikan kamu sudah menginstall:
- [Node.js v18+](https://nodejs.org/)
- [Docker Desktop](https://docs.docker.com/get-docker/) (untuk Supabase lokal)
- [Supabase CLI](https://supabase.com/docs/guides/cli)
- npm (sudah termasuk bersama Node.js)

### 1. Clone Repository

```bash
git clone https://github.com/gopaltrebung/GesaAIV2.git
cd GesaAIV2
```

### 2. Install Dependencies

```bash
npm install
```

### 3. Jalankan Supabase Lokal

Pastikan Docker Desktop sudah berjalan, lalu:

```bash
supabase start
```

Setelah berhasil, akan muncul output seperti berikut:

```
API URL: http://localhost:54321
DB URL: postgresql://postgres:postgres@localhost:54322/postgres
Studio URL: http://localhost:54323
anon key: eyJ...
service_role key: eyJ...
```

Catat nilai `API URL`, `anon key`, dan `service_role key` — kamu akan membutuhkannya di langkah berikutnya.

### 4. Setup Environment Variables

Salin file contoh `.env.local`:

```bash
cp .env.local.example .env.local
```

Buka `.env.local` dan isi nilai berikut (dari output `supabase start` di atas):

```env
# Wajib diisi
NEXT_PUBLIC_SUPABASE_URL=http://localhost:54321
NEXT_PUBLIC_SUPABASE_ANON_KEY=<anon key dari supabase start>
SUPABASE_SERVICE_ROLE_KEY=<service_role key dari supabase start>

# API Key provider (opsional — isi salah satu atau lebih)
GROQ_API_KEY=             # Dapatkan di https://console.groq.com
OPENAI_API_KEY=           # Dapatkan di https://platform.openai.com
ANTHROPIC_API_KEY=
GOOGLE_GEMINI_API_KEY=
MISTRAL_API_KEY=
PERPLEXITY_API_KEY=
OPENROUTER_API_KEY=

# Ollama (opsional, untuk model lokal)
NEXT_PUBLIC_OLLAMA_URL=http://localhost:11434
```

> **Catatan:** Jika environment variable API key diset, input di halaman settings pengguna akan dinonaktifkan dan semua pengguna akan menggunakan key tersebut.

### 5. Setup SQL (Sekali Saja)

Buka file migrasi pertama: `supabase/migrations/20240108234540_setup.sql`

Di sekitar baris 53–54, ganti nilai berikut:
- `project_url`: Ganti dengan `http://localhost:54321` (atau biarkan default jika `project_id` di `config.toml` tidak diubah)
- `service_role_key`: Ganti dengan nilai `service_role key` dari output `supabase start`

Ini diperlukan agar penghapusan file storage berfungsi dengan benar.

### 6. (Opsional) Setup Groq API Key

[Groq](https://groq.com) menyediakan inference LLaMA3, Mixtral, dan Gemma yang sangat cepat secara gratis.

1. Buat akun di [https://console.groq.com](https://console.groq.com)
2. Buat API key baru
3. Tambahkan ke `.env.local`:
   ```env
   GROQ_API_KEY=gsk_...
   ```

Model Groq yang tersedia:
- `llama3-8b-8192` — LLaMA3 8B (cepat, ringan)
- `llama3-70b-8192` — LLaMA3 70B (lebih powerful)
- `mixtral-8x7b-32768` — Mixtral 8x7B (context window besar: 32k)
- `gemma-7b-it` — Gemma 7B

### 7. Jalankan Aplikasi

```bash
npm run chat
```

Perintah ini akan otomatis:
1. Menjalankan Supabase (jika belum berjalan)
2. Menggenerate TypeScript types dari database
3. Menjalankan server Next.js di mode development

Setelah berhasil, buka browser dan akses:
- **Aplikasi:** [http://localhost:3000](http://localhost:3000)
- **Supabase Studio (backend):** [http://localhost:54323](http://localhost:54323)

---

## Update Aplikasi

Untuk mengupdate ke versi terbaru:

```bash
npm run update
```

Jika kamu menggunakan instance hosted, jalankan juga:

```bash
npm run db-push
```

---

## Hosted Quickstart (Deploy ke Cloud)

### 1. Setup Backend dengan Supabase

1. Buat project baru di [supabase.com](https://supabase.com/)
2. Di dashboard project, pergi ke **Project Settings → API**
3. Catat nilai:
   - `Project URL` → `NEXT_PUBLIC_SUPABASE_URL`
   - `anon public` key → `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `service_role` key → `SUPABASE_SERVICE_ROLE_KEY`
4. Di **Authentication → Providers**, pastikan "Email" diaktifkan

### 2. Push Database

```bash
supabase login
supabase link --project-ref <project-id>
supabase db push
```

### 3. Deploy Frontend ke Vercel

1. Buka [vercel.com](https://vercel.com/) dan buat project baru
2. Import repository ini dari GitHub
3. Set Framework Preset ke **Next.js**
4. Tambahkan environment variables berikut:
   - `NEXT_PUBLIC_SUPABASE_URL`
   - `NEXT_PUBLIC_SUPABASE_ANON_KEY`
   - `SUPABASE_SERVICE_ROLE_KEY`
   - `GROQ_API_KEY` *(opsional)*
   - API key provider lain yang ingin digunakan

Untuk daftar lengkap environment variables, lihat file `.env.local.example`.

---

## Kontribusi

Pull request dan issue sangat disambut! Silakan fork repository ini dan buat PR.

## Repository

[https://github.com/gopaltrebung/GesaAIV2](https://github.com/gopaltrebung/GesaAIV2)
