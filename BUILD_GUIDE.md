# Panduan Kompilasi Source Code Claude Code

> Dokumen ini mencatat proses lengkap untuk melengkapi file konfigurasi yang hilang dari snapshot source code Claude Code dan berhasil mengompilasi serta menjalankannya.

## Gambaran Umum

Snapshot source code Claude Code hanya berisi direktori `src/` dan `README.md`, tanpa file konfigurasi build. Panduan ini mencakup seluruh langkah untuk memulihkan lingkungan build dari awal.

---

## File Konfigurasi yang Ditambahkan

### 1. `package.json`

File konfigurasi inti proyek, mendefinisikan:

- **Nama paket**: `@anthropic-ai/claude-code`
- **Entry point**: `src/entrypoints/cli.tsx`
- **Output build**: `dist/cli.js`
- **Perintah script**:
  - `bun run build` — menjalankan script build
  - `bun run dev` — menjalankan source code langsung (mode pengembangan)
  - `bun run typecheck` — pengecekan tipe TypeScript

**Dependensi dibagi menjadi tiga kategori:**

| Kategori | Jumlah | Contoh |
|----------|--------|--------|
| Paket npm publik | ~75 | `react`, `chalk`, `zod`, `@anthropic-ai/sdk` |
| Paket internal Anthropic (perlu stub) | ~10 | `@ant/*`, `@anthropic-ai/sandbox-runtime` |
| Dependensi pengembangan | ~13 | `typescript`, `@types/react`, `@types/bun` |

### 2. `tsconfig.json`

Konfigurasi kompilasi TypeScript, pengaturan penting:

- **Sistem modul**: ESNext + bundler resolution
- **JSX**: `react-jsx` (transformasi JSX baru React 19)
- **Alias path**: `src/*` → `./src/*` (proyek banyak menggunakan format import `import from 'src/...'`)
- **Target**: ESNext (didukung native oleh Bun)
- **Tipe**: mencakup definisi tipe `bun-types` dan `node` sekaligus

### 3. `globals.d.ts`

File deklarasi tipe global, mendefinisikan:

- **Konstanta `MACRO`**: definisi macro yang diinjeksi saat build
  - `MACRO.VERSION` — nomor versi
  - `MACRO.BUILD_TIME` — timestamp build
  - `MACRO.PACKAGE_URL` — alamat paket npm
  - `MACRO.NATIVE_PACKAGE_URL` — alamat paket native
  - `MACRO.FEEDBACK_CHANNEL` — saluran umpan balik
  - `MACRO.ISSUES_EXPLAINER` — penjelasan pelaporan masalah
  - `MACRO.VERSION_CHANGELOG` — catatan perubahan versi

- **Modul `bun:bundle`**: deklarasi tipe untuk modul feature flags saat build dengan Bun
- **Stub tipe untuk paket internal**: menyediakan definisi tipe untuk paket internal Anthropic yang tidak dipublikasikan

### 4. `scripts/build.ts`

Script build inti, menggunakan API `Bun.build()`, menangani masalah-masalah penting berikut:

#### a) Penanganan Feature Flags `bun:bundle`

Kode asli menggunakan `import { feature } from 'bun:bundle'` untuk eliminasi kode mati saat kompilasi. Script build menyediakan polyfill untuk fungsi `feature()` melalui plugin Bun kustom — semua feature flag mengembalikan `false` secara default pada build eksternal:

```
PROACTIVE, KAIROS, BRIDGE_MODE, DAEMON, VOICE_MODE,
AGENT_TRIGGERS, MONITOR_TOOL, COORDINATOR_MODE,
ABLATION_BASELINE, DUMP_SYSTEM_PROMPT, CHICAGO_MCP
```

#### b) Injeksi Konstanta Build-time `MACRO.*`

Semua referensi `MACRO.*` diganti dengan nilai aktual saat kompilasi melalui opsi `define` pada `Bun.build()`.

#### c) Plugin Stub untuk Paket Internal

Menyediakan stub inline untuk paket internal Anthropic yang tidak dipublikasikan (`@ant/*`, `@anthropic-ai/sandbox-runtime`, dll.), memastikan semua named export dapat terselesaikan dengan benar saat kompilasi tanpa menimbulkan dependensi runtime.

#### d) Plugin Stub Otomatis untuk File Source yang Hilang

Beberapa file tidak ada dalam snapshot source code (dilindungi oleh feature flag `feature()` atau termasuk modul internal). Script build secara **otomatis mendeteksi dan men-stub file-file yang hilang ini saat build** menggunakan plugin `missingSourceStubPlugin`, tanpa perlu membuat file stub secara manual di pohon source:

- Mencegat semua import file source melalui hook `onResolve`, mendeteksi apakah file target ada di disk
- File yang tidak ada secara otomatis diarahkan ke namespace virtual `missing-source`
- Menyediakan stub presisi untuk modul yang membutuhkan named export tertentu (seperti `connectorText`, `protectedNamespace`, dll.)
- Modul yang hilang lainnya mengembalikan `export default {}` generik
- File `.md` dan `.txt` mengembalikan string kosong, file `.d.ts` mengembalikan `export {}`
- Modul yang di-stub otomatis ditandai dengan `⚠️ Auto-stubbing missing module:` di log build

#### e) Penanganan Dependensi Eksternal

Semua paket npm publik ditandai sebagai `external`, tidak dikemas dalam output, dan diselesaikan dari `node_modules` saat runtime.

### 5. Perbaikan Source Code

Format flag pendek `-d2e` di `src/main.tsx` tidak kompatibel dengan Commander.js v13 (v13 mengharuskan flag pendek berupa satu karakter), sudah diubah agar hanya menggunakan flag panjang `--debug-to-stderr`.

---

## Langkah-langkah Kompilasi

### Prasyarat

- [Bun](https://bun.sh) ≥ 1.1 (disarankan 1.3+)
- Windows / macOS / Linux

### Langkah 1: Instal Dependensi

```bash
bun install
```

Ini akan menginstal sekitar 600+ paket (termasuk dependensi transitif), membutuhkan waktu sekitar 1-2 menit.

### Langkah 2: Jalankan Build

```bash
bun run build
```

Script build akan:
1. Menginisialisasi polyfill feature flag `bun:bundle`
2. Menginjeksi konstanta build-time `MACRO.*`
3. Menghasilkan stub inline untuk paket internal Anthropic
4. Secara otomatis mendeteksi dan men-stub file source yang hilang dari snapshot
5. Mengemas entry point `src/entrypoints/cli.tsx`
6. Menghasilkan output ke `dist/cli.js` (sekitar 11,7 MB) dan `dist/cli.js.map`

### Langkah 3: Verifikasi Eksekusi

```bash
# Cek versi
bun dist/cli.js --version
# Output: 1.0.0-research (Claude Code)

# Lihat bantuan
bun dist/cli.js --help

# Jalankan antarmuka interaktif (membutuhkan API Key)
bun dist/cli.js
```

### Kustomisasi Nomor Versi

```bash
CLAUDE_CODE_VERSION=2.0.0 bun run build
```

---

## Output Build

| File | Ukuran | Keterangan |
|------|--------|------------|
| `dist/cli.js` | ~11,7 MB | Paket program utama (format ESM) |
| `dist/cli.js.map` | ~38,6 MB | Source Map |

---

## Poin Teknis Penting

### Gambaran Arsitektur

```
Entry point: src/entrypoints/cli.tsx
    ↓ (dynamic import)
Program utama: src/main.tsx (Commander.js CLI)
    ↓
UI Terminal: src/ink/ (implementasi Ink kustom + React 19)
    ↓
Sistem inti:
├── src/tools/      (~40 implementasi tool)
├── src/commands/   (~50 slash command)
├── src/services/   (API, MCP, OAuth, analitik)
├── src/hooks/      (sistem izin)
└── src/coordinator/ (koordinasi multi-agen)
```

### Stack Teknologi Utama

| Komponen | Teknologi |
|----------|-----------|
| Runtime | Bun |
| Bahasa | TypeScript (strict) |
| UI Terminal | React 19 + fork Ink kustom |
| Framework CLI | Commander.js 13 |
| Validasi Schema | Zod v3 |
| Protokol | MCP SDK, LSP |
| API | Anthropic SDK |
| Telemetri | OpenTelemetry 2.x |
| Layout Engine | Implementasi yoga-layout murni TypeScript |

### Catatan Penting

1. **Paket internal tidak tersedia**: Paket seri `@ant/*` dan sebagian paket `@anthropic-ai/*` tidak dipublikasikan di npm publik. Fitur terkait (integrasi Chrome, computer use, sandbox runtime, dll.) tidak tersedia dalam build ini.

2. **Semua Feature Flag dinonaktifkan**: Semua feature flag `bun:bundle` mengembalikan `false` secara default, artinya jalur kode untuk fitur eksperimental (suara, daemon, mode koordinator, dll.) telah dieliminasi.

3. **Membutuhkan API Key untuk penggunaan nyata**: Meskipun CLI dapat dikompilasi dan dijalankan (UI terminal lengkap, pemilihan tema, dll. berfungsi normal), penggunaan sebenarnya membutuhkan Anthropic API Key atau login OAuth.

4. **Versi React Reconciler**: Proyek menggunakan Hook `useEffectEvent`, yang membutuhkan `react-reconciler@0.33.0` (bukan 0.31.0) — versi inilah yang mengimplementasikan scheduler `useEffectEvent`.

5. **Stub paket internal perlu signature metode lengkap**: Stub untuk beberapa paket internal (seperti `SandboxManager` dari `@anthropic-ai/sandbox-runtime`) harus menyertakan metode statis lengkap (seperti `isSupportedPlatform`, `checkDependencies`, `wrapWithSandbox`, dll.), jika tidak runtime akan error saat mengakses properti yang tidak terdefinisi. Script build sudah menyediakan signature metode lengkap untuk semua kelas internal yang diketahui.

6. **Build tidak resmi**: Build ini hanya untuk keperluan edukasi dan riset keamanan, bukan rilis resmi dari Anthropic.
