# Token Radar — desain entry (target peringkat 1)

Kompetisi: token-radar-01, "Build the best token launch radar".
Deadline: 2026-12-14T10:00:00Z (17:00 WIB). Pot $1.325, juara 1 $1.000.
Akun: @verlianmr. Entry fee: $0. Maks 5 submit/hari, 10 total.
Snapshot: 2026-11-14, 758 protokol (TVL >= $5M), watchlist 40, 342 tanpa token.

## Acuan: starter (2-plain anchor, strength 0.593)

Model starter (radar-1): implied = TVL x 0.25 x growthMult x maturityMult.
growthMult = 1 + clamp(change_7d,-60,120)/100 x 0.5.
maturityMult = 0.6 + min(age,1095)/1095 x 0.4 (umur tak diketahui: 365 hari).
Board 200 baris (40 watchlist + 160 early by TVL). Farm plan 6 langkah generik.
Dashboard plain: tabel + filter + tab live + panel model + dialog detail.
Untuk menang, entry ini harus mengalahkan starter di kriteria 2 (value model),
3 (farm plan) dan 4-5 (dashboard), sambil menjaga kriteria 1 (coverage penuh).

## Model radar-2 (market-calibrated)

Semua input dari snapshot; tidak ada nama protokol yang di-special-case.

1. Category multiple (pembeda utama): untuk tiap kategori, median rasio
   mcap_usd/tvl_usd dari protokol live-token dalam kategori itu (dari snapshot
   sendiri, min 3 comps, non-CEX, mcap tidak null). Kategori tanpa comps cukup
   memakai median global semua protokol live. Clamp ke [0.05, 3.0].
   Rasional: pasar sendiri yang memberi harga kategori itu; model memakai
   harga pasar, bukan angka flat 0.25.
2. base = tvl_usd x category_multiple
3. growthMult = 1 + clamp(change_7d, -60, 120)/100 x 0.5 (dipertahankan)
4. maturityMult = 0.6 + min(age_days, 1095)/1095 x 0.4 (dipertahankan)
5. breadthMult = 1 + min(max(chains-1,0), 9) x 0.02 (max 1.18)
   Rasional: makin banyak chain, makin luas distribusi & basis pengguna.
6. implied = max(1, round(base x growthMult x maturityMult x breadthMult))

Sortir by implied desc, tiebreak slug asc (deterministik). Rank 1..N.
Board: 200 baris (40 watchlist + 160 early pre-token di bawah floor by TVL).
Live view: 24 protokol live-token by mcap (dipisah, tidak masuk ranking).

## Farm plan (7 langkah)

Kerangka 6 langkah starter dipertahankan (fresh wallet, aksi utama, aksi
mingguan, hold, klaim aman, log), ditambah langkah ke-7 "why now" yang
mengutip angka protokol itu sendiri (TVL, jumlah chain, umur, growth 7d).
Cabang aksi diperluas ke kategori yang ada di board: Risk Curators, Onchain
Capital Allocator, CDP, RWA, Collateral Markets, Dexs, Lending, Perps,
Yield, Liquid Staking/Restaking, Bridge, Stable, Insurance, Prediction.
Setiap langkah: aksi + Cost + Time. Panjang 20-600 karakter, 3-12 langkah.

## Dashboard (dark radar/sonar)

Palet gelap: navy pekat, aksen cyan/phosphor-green, angka monospace.
Struktur: header + stats, lede, kontrol (search, filter kategori, filter
chain, toggle watchlist-only, tab pre-token/live), tabel sortable
(rank, nama, TVL, 7d, umur, implied), panel model (formula + tabel category
multiples + metodologi), dialog detail per protokol (breakdown tiap
multiplier + farm plan), tab live dengan kolom rasio mcap/TVL (comps).
Total source dashboard <= 55 KB (batas dossier 64 KB). Tanpa network:
tidak ada http(s) di atribut/import/CSS.
data.js di /output root (di luar dossier text).

## Screenshot (Playwright Chromium, viewport 1440x900)

1-board.png: board penuh. 2-detail.png: dialog detail rank #1 terbuka.
3-filtered.png: tampilan terfilter (kategori dipilih). Target 300-350 KB
per file (PNG; kalau lewat, turunkan ke JPEG q80). Long side <= 1440,
short side >= 800, max 512 KiB.

## Repo & submit

Repo baru (Besmart buat di github.com): verlianmr/token-launch-radar,
public, dibuat setelah 14 Nov 2026. Isi: main.ts, lib/model.ts,
vendor/makefun-board.ts (dari kit, byte-for-byte), dashboard/,
screenshots/, LICENSE (MIT), README.md (badge make.fun per template).
About repo: deskripsi + homepage make.fun + topik makefun,ai-agents,
bounties,trading (Besmart set di github.com atau via gh bila auth ada).
Verifikasi: grade-local dulu sampai 7 gate lolos, baru submit via CLI
(node cli/makefun.mjs submit --competition token-radar-01 ...).
Wallet: akun sudah punya wallet utama Besmart (linked, primary) + wallet
agen antiplenger. Entry ditandatangani keypair agen (connected agent's
wallet sah untuk entry_signature). Wallet add via CLI ditolak 403
(scope profile belum diizinkan) — bukan blocker submit; bila submit
gagal soal wallet, Besmart izinkan scope profile di
make.fun/profile?tab=keys#paired-agents.
Transkrip: build dikerjakan langsung di sesi Muse ini (recorded, usage
dari provider). Bundle di akhir via kit CLI. Jangan bangun lewat
subagent tanpa memastikan sesinya ikut terbundle.

## Validasi fleet (18 Nov, T-069/T-070/T-072)

- **T-069 (STF-06)** memvalidasi angka model persis: median global mcap/TVL
  0,1857 (n=133), Dexs 0,3424, RWA 0,2748, CDP 0,1687, Onchain Capital
  Allocator 0,0059 (3 comps: 0,0191 / 0,0059 / 0,0000), Risk Curators 1 comp
  (api3, 2,9747). Laporan mereka memakai median mentah (veda jatuh #3 -> #36);
  radar-2 memakai shrinkage prior 3 (veda #4) — pilihan yang dipertahankan
  karena laporan sendiri mencatat comps tipis itu rapuh, dan dashboard
  menyatakan jumlah comps di samping tiap multiple.
- **T-070 (STF-07)** menulis playbook farm per kategori (53 kategori, gas per
  kelas chain, risiko + larangan per kategori). Diimplementasikan ke model.ts:
  5 cabang baru (Liquidity Manager, Farm, Anchor BTC, Decentralized BTC,
  Crypto Card Issuer) + 2 cabang spesifik (Options Vault, Interest Rate
  Derivatives) — **fallback generik kini 0 baris** (dari 10; starter 23).
  Gas kini per kelas chain pertama protokol (Ethereum L1 $2-$30, L2/app
  $0,05-$2, Solana dkk di bawah $0,10, Bitcoin bervariasi). Tiap langkah aksi
  membawa satu kalimat risiko kategorinya. Step tetap 142-262 karakter.
- **T-072 pembanding ORC-01** (laptop Windows, Node v24.20.0): determinisme
  lolos di mesin ketiga (board.json 336.351 byte, identik antar run),
  7/7 gate efektif lolos; satu bug tooling ditemukan (gate-check.py tidak
  portabel ke Windows — import ESM path mentah) dan diperbaiki (file:// URL
  + tempfile + json.dumps untuk path JS).
