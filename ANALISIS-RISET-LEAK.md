# Analisis Riset: Source Code Claude Code v2.1.88 (Hasil Insiden Source Map Leak)

- **Tanggal insiden:** 31 Maret 2026
- **Sumber bocor:** npm registry — `@anthropic-ai/claude-code` v2.1.88 (source map 59,8 MB)
- **Codebase ini:** Dikemas ulang dari hasil leak untuk keperluan riset/edukasi (versi `1.0.0-research`)
- **Jumlah file TypeScript:** ~1.884 file, 512.000+ baris kode

> **Catatan:** Dokumen ini adalah hasil analisis akademik terhadap source code yang bocor secara tidak sengaja. Tujuannya semata-mata untuk memahami arsitektur AI modern, bukan untuk keperluan yang merugikan.

---

## Apa yang Bocor?

Anthropic tidak sengaja menyertakan file `.map` (source map) berukuran besar ke dalam paket npm resmi mereka. File source map ini, yang seharusnya hanya digunakan untuk debugging internal, ternyata berisi referensi langsung ke arsip TypeScript source code tidak terobfuskasi yang tersimpan di Cloudflare R2 milik Anthropic.

Dalam waktu kurang dari 3 jam, source code sudah tersebar luas dan di-fork lebih dari **41.500 kali** di GitHub.

---

## Apa yang Ada di Dalam Codebase Ini?

### Ringkasan Singkat

Codebase ini bukan sekedar aplikasi CLI biasa. Di dalamnya tersembunyi banyak fitur eksperimental yang belum pernah diumumkan ke publik — dari asisten proaktif mandiri, mode suara, sistem multi-agen, hingga "hewan peliharaan virtual" yang menyertai pengguna.

---

## 1. Fitur-Fitur Tersembunyi yang Ditemukan

### A. KAIROS — Mode Asisten Proaktif
**File terkait:** `src/commands/brief.ts`, `src/bootstrap/state.ts`

Fitur ini mengubah Claude Code dari asisten yang hanya merespons perintah menjadi **agen yang beroperasi secara mandiri**. Dengan KAIROS aktif, Claude bisa:

- Menjalankan tugas tanpa menunggu input pengguna
- Mengelola banyak sub-agen secara paralel
- Berlangganan ke aktivitas GitHub (PR, webhook)
- Mengirim push notification
- Mengkonsolidasi memori secara otomatis

Sub-flag yang ditemukan di dalam kode:
| Flag | Fungsi |
|------|--------|
| `KAIROS` | Fitur utama proactive mode |
| `KAIROS_BRIEF` | Output terkompresi (ringkas) |
| `KAIROS_CHANNELS` | Integrasi Slack |
| `KAIROS_DREAM` | Auto-konsolidasi memori |
| `KAIROS_GITHUB_WEBHOOKS` | Subscribe aktivitas PR GitHub |
| `KAIROS_PUSH_NOTIFICATION` | Push notification ke pengguna |

---

### B. VOICE MODE — Input/Output Suara
**File terkait:** `src/voice/voiceModeEnabled.ts`, `src/commands/voice/voice.ts`

Nama internal: **Tengu Amber Quartz**

Claude Code ternyata sudah disiapkan untuk menerima perintah suara dan merespons secara audio. Fitur ini membutuhkan:
- **Autentikasi OAuth dari claude.ai** (bukan API Key biasa)
- Tombol push-to-talk: spasi (default)
- Deteksi bahasa otomatis sebelum transkripsi

Terdapat kill-switch via GrowthBook (`tengu_amber_quartz_disabled`) yang bisa menonaktifkan fitur ini dari jarak jauh secara seketika.

---

### C. COORDINATOR MODE — Orkestrasi Multi-Agen
**File terkait:** `src/coordinator/coordinatorMode.ts`

Sistem ini memungkinkan satu instance Claude bertindak sebagai **koordinator** yang mengelola banyak *worker* (Claude lain). Cara kerjanya mirip seperti project manager yang mendistribusikan pekerjaan ke anggota tim.

Alur kerja yang didefinisikan dalam kode:
```
Riset → Sintesis → Implementasi → Verifikasi
```

Cara mengaktifkan (tersembunyi): `CLAUDE_CODE_COORDINATOR_MODE=1` sebagai environment variable.

---

### D. BUDDY — Hewan Peliharaan Virtual
**File terkait:** `src/buddy/companion.ts`, `src/buddy/CompanionSprite.tsx`

Ini mungkin temuan paling mengejutkan: Claude Code memiliki sistem **hewan peliharaan virtual** (companion sprite) yang belum pernah diumumkan sama sekali.

**18 jenis hewan:** bebek, angsa, blob, kucing, naga, gurita, burung hantu, penguin, kura-kura, bekicot, hantu, axolotl, capybara, kaktus, robot, kelinci, jamur, dan *chonk*.

**Sistem kelangkaan:**
| Tingkat | Peluang |
|---------|---------|
| Common | 60% |
| Uncommon | 25% |
| Rare | 10% |
| Epic | 4% |
| Legendary | 1% |

Setiap pengguna mendapat companion yang **deterministik berdasarkan user ID** — jadi tidak bisa "dimanipulasi" untuk mendapat yang langka. Companion ini punya nama, kepribadian, dan statistik unik (`DEBUGGING`, `PATIENCE`, `CHAOS`, `WISDOM`, `SNARK`), serta animasi idle, berkedip, dan "dipet" dengan efek hati.

---

### E. BRIDGE MODE & REMOTE SESSION — Kontrol Jarak Jauh
**File terkait:** `src/bridge/bridgeEnabled.ts`, `src/remote/RemoteSessionManager.ts`

Nama internal: **Tengu CCR** (Cloud Control Room)

Sistem ini memungkinkan Claude Code dioperasikan dari jarak jauh melalui WebSocket, termasuk:
- Mode "viewer-only" untuk mengamati sesi tanpa ikut campur
- Otomatis tersambung kembali jika koneksi terputus
- Integrasi dengan dashboard web claude.ai

---

### F. UNDERCOVER MODE — Keamanan untuk Kontribusi Open Source
**File terkait:** `src/utils/undercover.ts`

Fitur keamanan yang aktif **secara otomatis** saat Claude Code mendeteksi bahwa repository yang sedang dikerjakan adalah repositori publik/open source.

Yang dilakukan Undercover Mode:
- Menghapus nama kode model internal Anthropic dari output (Capybara, Tengu, dll.)
- Memblokir nomor versi yang belum dirilis
- Menghapus referensi ke repositori dan tooling internal
- Menghapus atribusi `Co-Authored-By` internal
- Memvalidasi pesan commit agar terlihat seperti ditulis manusia

Desainnya **fail-safe**: tidak ada cara untuk mematikan paksa fitur ini dari luar. Bisa diaktifkan manual via `CLAUDE_CODE_UNDERCOVER=1`.

---

### G. DAEMON WORKER — Proses Latar Belakang
**File terkait:** `src/entrypoints/cli.tsx`

Sistem worker yang berjalan di background untuk mendukung orkestrasi. Diaktifkan via flag CLI tersembunyi `--daemon-worker=<jenis>`. Didesain "lean" (ringan) tanpa memuat seluruh konfigurasi, khusus untuk tugas performa tinggi.

---

## 2. Arsitektur Sistem

### Gambaran Alur Data

```
Pengguna (input)
    ↓
cli.tsx (entry point) — fast-path untuk: --version, --help, daemon
    ↓
main.tsx (REPL loop)
    ↓
QueryEngine.ts (pemrosesan pesan & tool invocation)
    ↓
Permission System (izin multi-layer)
    ↓
Tools (43 tool) / Commands (50+ slash command)
    ↓
Anthropic API / MCP servers
```

### 43 Tool yang Tersedia

Beberapa yang menarik:
| Tool | Fungsi |
|------|--------|
| `AgentTool` | Menjalankan sub-agen |
| `SendMessageTool` | Komunikasi antar-agen |
| `TeamCreateTool` | Membuat tim worker |
| `RemoteTriggerTool` | Trigger remote task |
| `ScheduleCronTool` | Penjadwalan tugas |
| `EnterWorktreeTool` | Masuk ke git worktree terisolasi |
| `EnterPlanModeTool` | Mode perencanaan khusus |
| `WebBrowserTool` | Otomasi browser |
| `NotebookEditTool` | Edit Jupyter notebook |

---

## 3. Sistem Izin

Claude Code menggunakan sistem izin berlapis yang cukup canggih:

### Mode Izin
| Mode | Perilaku |
|------|----------|
| `AUTO` | Keputusan otomatis via ML classifier |
| `INTERACTIVE` | Tanya pengguna setiap kali |
| `YOLO` | Berbasis threshold (agresif) |
| `ALLOW` | Semua diizinkan (berbahaya) |
| `DENY` | Semua ditolak |
| `DONT_ASK` | Hanya berbasis aturan |

### Transcript Classifier
Sistem ML yang berjalan di background untuk mengklasifikasikan apakah sebuah tindakan perlu izin atau tidak. Jika classifier tidak merespons dalam **30 menit**, sistem otomatis beralih ke mode interaktif (fail-closed).

---

## 4. Feature Flag — 103 Total

Total ditemukan 103 feature flag. Ini menunjukkan betapa banyaknya fitur yang sudah dibangun tapi belum dirilis. Beberapa yang menarik:

| Flag | Deskripsi |
|------|-----------|
| `ULTRATHINK` | Mode berpikir diperpanjang |
| `ULTRAPLAN` | Mode perencanaan ekstensif |
| `WEB_BROWSER_TOOL` | Otomasi browser penuh |
| `COMPUTER_USE_MCP` | Kontrol layar komputer |
| `SSH_REMOTE` | Sesi SSH remote |
| `SELF_HOSTED_RUNNER` | Eksekusi di infrastruktur sendiri |
| `EXTRACT_MEMORIES` | Ekstraksi memori otomatis |
| `TEMPLATES` | Sistem template |
| `WORKFLOW_SCRIPTS` | Otomasi workflow |
| `PERFETTO_TRACING` | Tracing performa |

---

## 5. Temuan Teknis Menarik Lainnya

### Enkripsi Nama Kode Model
Di dalam `src/buddy/types.ts`, nama-nama model internal Anthropic di-encode secara karakter demi karakter agar tidak terdeteksi oleh `grep`:

```typescript
const capybara = c(0x63, 0x61, 0x70, 0x79, 0x62, 0x61, 0x72, 0x61)
// Ini sama dengan string 'capybara', tapi tidak bisa di-grep langsung
```

Ini menunjukkan Anthropic sadar bahwa source code mereka bisa bocor, dan sudah mencoba melindungi nama kode model internal.

### Nama Kode Internal yang Bocor
Dari analisis kode, ditemukan nama-nama kode internal yang selama ini tidak pernah diungkap:
- **Capybara** — nama model (mungkin versi Claude tertentu)
- **Tengu** — nama kode untuk sistem cloud/remote (CCR = Cloud Control Room)
- **Amber Quartz** — nama kode untuk Voice Mode

### TODO yang Belum Diselesaikan
Ditemukan beberapa kode yang masih "dalam pengerjaan":
- Migrasi token OAuth ke SecureStorage yang lebih aman (masih belum selesai)
- Pembacaan JWT dari stdin (bukan argv) untuk keamanan
- Timeout otomatis 5 menit saat tidak aktif di diff viewer

---

## 6. Perbedaan Codebase Ini vs Source Asli

| Aspek | Source Asli (v2.1.88) | Codebase Ini (1.0.0-research) |
|-------|----------------------|-------------------------------|
| Versi | 2.1.88 | 1.0.0-research |
| `src/` | Identik (dari leak) | Identik (dari leak) |
| `package.json` | Dari npm (tidak bocor) | Dibuat ulang oleh penulis Gitee |
| `tsconfig.json` | Tidak bocor | Dibuat ulang |
| `scripts/build.ts` | Tidak bocor | Dibuat ulang |
| Build config | Tidak bocor | Ditambahkan agar bisa dikompilasi |
| Feature flags | Aktif sesuai env prod | Semua dinonaktifkan (`false`) |
| Paket internal `@ant/*` | Ada di prod Anthropic | Di-stub (placeholder kosong) |

---

## 7. Apa yang Bisa Dipelajari dari Leak Ini

### Untuk Memahami Pengembangan AI
- Claude Code jauh lebih kompleks dari yang kelihatan — ini bukan sekadar wrapper API
- Anthropic menggunakan Claude sendiri sebagai infrastruktur internal (multi-agent, coordinator)
- Sistem feature flag memungkinkan rollout bertahap yang sangat terkontrol

### Untuk Memahami Keamanan Software
- Source map yang disertakan ke production adalah **kesalahan packaging yang umum** tapi berbahaya
- Obfuskasi nama kode via character encoding menunjukkan kesadaran akan risiko leak
- Desain fail-safe (Undercover Mode tidak bisa dimatikan paksa) adalah praktik yang baik

### Untuk Memahami Arsitektur CLI Modern
- Penggunaan React + Ink untuk terminal UI yang kompleks
- Commander.js untuk CLI parsing
- Bun sebagai runtime sekaligus bundler

---

## Referensi & Sumber

- [VentureBeat — Claude Code's source code appears to have leaked](https://venturebeat.com/technology/claude-codes-source-code-appears-to-have-leaked-heres-what-we-know)
- [The Register — Anthropic accidentally exposes Claude Code source code](https://www.theregister.com/2026/03/31/anthropic_claude_code_source_code/)
- [The Hacker News — Claude Code Source Leaked via npm Packaging Error](https://thehackernews.com/2026/04/claude-code-tleaked-via-npm-packaging.html)
- [Alex Kim's Blog — The Claude Code Source Leak: fake tools, frustration regexes, undercover mode, and more](https://alex000kim.com/posts/2026-03-31-claude-code-source-leak/)
- [Penligent — Claude Code Source Map Leak, What Was Exposed and What It Means](https://www.penligent.ai/hackinglabs/claude-code-source-map-leak-what-was-exposed-and-what-it-means/)

---

*Analisis ini dibuat untuk keperluan edukasi dan pemahaman arsitektur AI. Semua fitur yang disebutkan adalah milik Anthropic dan mungkin sudah berubah sejak tanggal bocornya source code.*
