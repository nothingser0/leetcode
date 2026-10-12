# 1. Two Sum — Penjelasan Dari Nol (JavaScript)

**Tingkat:** Mudah
**Pola:** Hash Map (mencari pasangan / komplemen)
**Bahasa:** JavaScript

> Dokumen ini ditulis dengan asumsi **kamu belum pernah belajar hash map**.
> Setiap istilah dijelaskan saat pertama kali muncul. Tidak ada langkah yang
> dilewati.

---

## Daftar Isi

1. [Soalnya apa](#1-soalnya-apa)
2. [Contoh dan cara membacanya](#2-contoh-dan-cara-membacanya)
3. [Cara paling polos (brute force)](#3-cara-paling-polos-brute-force)
4. [Kenapa cara polos itu lambat](#4-kenapa-cara-polos-itu-lambat)
5. [Konsep baru: Map](#5-konsep-baru-map)
6. [Ide besarnya](#6-ide-besarnya)
7. [Kode final baris per baris](#7-kode-final-baris-per-baris)
8. [Jalannya program, langkah demi langkah](#8-jalannya-program-langkah-demi-langkah)
9. [Kenapa ini benar](#9-kenapa-ini-benar)
10. [Soal menjebak: `[3, 3]`](#10-soal-menjebak-3-3)
11. [Kesalahan simpan-dulu-baru-cek](#11-kesalahan-simpan-dulu-baru-cek)
12. [Kenapa harus Map, bukan object `{}`](#12-kenapa-harus-map-bukan-object-)
13. [Kompleksitas waktu dan ruang](#13-kompleksitas-waktu-dan-ruang)
14. [Jebakan khas JavaScript](#14-jebakan-khas-javascript)
15. [Error yang muncul di LeetCode](#15-error-yang-muncul-di-leetcode)
16. [Jebakan umum lainnya](#16-jebakan-umum-lainnya)
17. [Tiga pendekatan resmi (dan kapan masing-masing dipakai)](#17-tiga-pendekatan-resmi-dan-kapan-masing-masing-dipakai)
18. [Pola yang bisa dipakai lagi](#18-pola-yang-bisa-dipakai-lagi)
19. [Ringkasan satu halaman](#19-ringkasan-satu-halaman)

---

## 1. Soalnya apa

Diberikan:

- `nums` — sebuah array berisi bilangan bulat, misal `[2, 7, 11, 15]`
- `target` — sebuah bilangan bulat, misal `9`

**Yang diminta:** kembalikan **indeks** dari dua angka di dalam `nums` yang kalau
dijumlahkan hasilnya sama dengan `target`.

### Apa itu "indeks"?

Indeks itu **nomor urut posisi**. Dihitung mulai dari **0**, bukan 1.

```
Angka:   [ 2,   7,   11,  15 ]
Indeks:    0    1     2    3
```

Jadi:

- `nums[0]` artinya "angka di posisi 0", nilainya `2`
- `nums[1]` artinya "angka di posisi 1", nilainya `7`

**Ini poin paling penting di soal ini:** yang diminta jawabannya adalah **nomor
posisinya** (indeks), **bukan angkanya**. Jadi untuk contoh di atas, jawaban yang
benar adalah `[0, 1]`, **bukan** `[2, 7]`.

### Aturan yang dijamin oleh soal

1. **Selalu ada tepat satu jawaban.** Kamu tidak perlu memikirkan kasus "tidak ada
   jawaban" atau "ada dua jawaban".
2. **Satu elemen tidak boleh dipakai dua kali.** Kalau `nums[0]` sudah dipakai,
   tidak boleh dipakai lagi. Tapi hati-hati — ini soal **posisi**, bukan soal
   **nilai**. Dua posisi berbeda yang kebetulan isinya sama **boleh** dipakai
   dua-duanya. (Contohnya di [bagian 10](#10-soal-menjebak-3-3).)
3. **Urutan jawaban bebas.** `[0, 1]` dan `[1, 0]` sama-sama diterima.

---

## 2. Contoh dan cara membacanya

```
Input:  nums = [2, 7, 11, 15], target = 9
Output: [0, 1]
```

**Cara membacanya, pelan-pelan:**

- Kita butuh dua angka yang jumlahnya `9`.
- Coba `nums[0] = 2`. Berapa yang dibutuhkan lagi supaya jadi 9? Jawabnya `9 - 2 = 7`.
- Apakah ada angka `7` di dalam array? Ada, di indeks `1`.
- Berarti pasangannya adalah indeks `0` dan `1`.
- Jawaban: `[0, 1]`. ✅ Cek: `nums[0] + nums[1] = 2 + 7 = 9`. Benar.

```
Input:  nums = [3, 2, 4], target = 6
Output: [1, 2]
```

- `nums[1] = 2`. Yang dibutuhkan: `6 - 2 = 4`. Ada angka `4`? Ada, di indeks `2`.
- Jawaban: `[1, 2]`. Cek: `2 + 4 = 6`. ✅

```
Input:  nums = [3, 3], target = 6
Output: [0, 1]
```

- `nums[0] = 3`. Yang dibutuhkan: `6 - 3 = 3`. Ada angka `3` lagi? Ada, di indeks `1`.
- Jawaban: `[0, 1]`. Cek: `3 + 3 = 6`. ✅

### Istilah baru: "komplemen"

Di tiga penjelasan di atas, saya selalu menghitung `target - angka`. Hasil
pengurangan itu disebut **komplemen** — yaitu "angka pasangan yang saya butuhkan".

- Kalau aku sedang memegang `2` dan targetnya `9`, komplemennya `9 - 2 = 7`.
- Kalau aku sedang memegang `7` dan targetnya `9`, komplemennya `9 - 7 = 2`.

Ingat kata **komplemen** ini, karena akan terus dipakai.

---

## 3. Cara paling polos (brute force)

Sebelum mikir yang pintar-pintar, coba dulu cara paling bodoh yang pasti jalan.

Idenya: **coba semua pasangan yang mungkin**, satu per satu, sampai ketemu yang
jumlahnya `target`.

```javascript
var twoSum = function (nums, target) {
    const n = nums.length;

    for (let i = 0; i < n; i++) {              // angka pertama, di posisi i
        for (let j = i + 1; j < n; j++) {      // angka kedua, setelah i
            if (nums[i] + nums[j] === target) {
                return [i, j];
            }
        }
    }

    return [];
};
```

### Kenapa `j` dimulai dari `i + 1`, bukan `0`?

Kalau `j` dimulai dari 0, kita akan memeriksa:

- pasangan `(0, 1)` dan `(1, 0)` → ini pasangan yang **sama**, cuma dibalik. Buang waktu.
- pasangan `(0, 0)` → ini angka yang **sama dengan dirinya sendiri**, artinya satu
  posisi dipakai dua kali. Ini **melanggar aturan** soal.

Dengan memulai `j` dari `i + 1`, kita memastikan:

- angka kedua selalu **setelah** angka pertama → tidak ada pemeriksaan kembar
- `i` dan `j` pasti **berbeda** → tidak melanggar aturan "tidak boleh dipakai dua kali"

### Apakah ini benar?

Ya, pasti benar. Kalau ada pasangan yang jumlahnya `target` di suatu tempat, kita
akan pasti menemukannya, karena kita memeriksa **semuanya**.

Tapi ada masalah: **terlalu lambat.** Lanjut ke bagian berikutnya.

### Tiga variasi brute force (dan mana yang benar)

Ada tiga cara menulis brute force, dan bedanya halus tapi penting. Semuanya
dijalankan dengan `n = 4` (indeks 0, 1, 2, 3).

#### Variasi A — `i` dan `j` sama-sama dari 0, tanpa syarat

```javascript
for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
        if (nums[i] + nums[j] === target) return [i, j];
    }
}
```

Yang diperiksa — **16 pemeriksaan**:

```
00 01 02 03
10 11 12 13
20 21 22 23
30 31 32 33
```

Ini **SALAH**. Perhatikan `00`, `11`, `22`, `33` — itu posisi yang sama dipakai
dua kali. Contoh: `nums = [3]`, `target = 6` akan mengembalikan `[0, 0]`.
Melanggar aturan soal.

#### Variasi B — `i` dan `j` dari 0, tapi ada syarat `i !== j`

```javascript
for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
        if (i !== j && nums[i] + nums[j] === target) return [i, j];
    }
}
```

Yang diperiksa — **12 pemeriksaan**:

```
01 02 03
10 12 13
20 21 23
30 31 32
```

Ini **BENAR**, karena `00`, `11`, `22`, `33` sudah dibuang. Aturan "tidak boleh
dipakai dua kali" terpenuhi, dan jawabannya valid.

Tapi perhatikan: pasangan yang sama diperiksa **dua kali**.

```
01  dan  10   <- pasangan yang SAMA (indeks 0 dan 1), cuma urutannya dibalik
02  dan  20
03  dan  30
12  dan  21
13  dan  31
23  dan  32
```

Setiap pasangan muncul dua kali, jadi kerjanya 2x lebih banyak dari yang perlu.

#### Variasi C — `j` selalu mulai dari `i + 1`

```javascript
for (let i = 0; i < n; i++) {
    for (let j = i + 1; j < n; j++) {
        if (nums[i] + nums[j] === target) return [i, j];
    }
}
```

Yang diperiksa — **6 pemeriksaan**:

```
01 02 03
12 13
23
```

Ini yang kita pakai. Setiap pasangan diperiksa **tepat sekali**, dan karena `j`
selalu lebih besar dari `i`, dua hal otomatis terpenuhi sekaligus:

- tidak ada `00`, `11`, ... → aturan "tidak boleh dipakai dua kali" aman
- tidak ada `10`, `20`, ... → tidak ada kerja kembar

#### Ringkasan ketiga variasi

| Variasi | Yang diperiksa (n = 4) | Jumlah | Benar? |
| --- | --- | --- | --- |
| A | `00 01 02 03 10 11 12 13 20 21 22 23 30 31 32 33` | 16 | ❌ ada `00`, `11`, ... (posisi dipakai 2x) |
| B | `01 02 03 10 12 13 20 21 23 30 31 32` | 12 | ✅ benar, tapi tiap pasangan diperiksa 2x |
| C | `01 02 03 12 13 23` | 6 | ✅ benar, dan paling hemat |

| n | A = n² | B = n(n-1) | C = n(n-1)/2 |
| --- | --- | --- | --- |
| 4 | 16 | 12 | 6 |
| 10 | 100 | 90 | 45 |
| 100 | 10.000 | 9.900 | 4.950 |
| 1.000 | 1.000.000 | 999.000 | 499.500 |
| 10.000 | 100.000.000 | 99.990.000 | 49.995.000 |

**Kesimpulan penting:** ketiganya sama-sama **O(n²)**. Bedanya cuma "angka
pengali" (konstanta) — B mengerjakan 2x lebih banyak dari C. Jadi ketiganya
sama-sama kena TLE di soal ini.

Artinya: memperbaiki brute force dari B ke C itu hanya menghaluskan, bukan
menyelamatkan. **Peningkatan besarnya baru datang dari hash map** (solusi `O(n)`
di bagian 6 dan seterusnya), bukan dari merapikan brute force.

Satu catatan: variasi B bisa mengembalikan `[1, 0]` (urutan terbalik), karena dia
juga memeriksa `10`. LeetCode menerima urutan apa pun, jadi tidak masalah. Tapi
kalau suatu saat soal mewajibkan `i < j`, urutannya perlu dirapikan dulu.

---

## 4. Kenapa cara polos itu lambat

Mari hitung berapa kali kita memeriksa. Misal `n` adalah banyaknya angka.

- Untuk `i` di posisi 0, kita periksa `n - 1` pasangan
- Untuk `i` di posisi 1, kita periksa `n - 2` pasangan
- ...
- Totalnya kira-kira `n × n / 2` pemeriksaan

Jadi kalau `n = 10_000` (batas yang biasa dipakai LeetCode), itu sekitar
**50 juta pemeriksaan**. Di JavaScript, ini bisa makan beberapa detik — melebihi
batas waktu (biasanya 1–2 detik). Hasilnya: **Time Limit Exceeded (TLE)**.

Jadi kita butuh cara yang lebih pintar.

---

## 5. Konsep baru: Map

Di JavaScript, ada dua cara menyimpan pasangan **kunci → nilai**:

1. **Object biasa** — ditulis `{}` (kurung kurawal)
2. **`Map`** — cara yang lebih modern dan lebih aman

Untuk soal ini kita pakai **`Map`**, dan di [bagian 12](#12-kenapa-harus-map-bukan-object-)
akan dijelaskan kenapa object biasa berbahaya.

### Analogi

Bayangkan kamu punya rak loker. Setiap loker punya **label** (kunci) dan **isinya**
(nilai).

```javascript
const terlihat = new Map();

terlihat.set("nama", "Andi");     // loker berlabel "nama" diisi "Andi"
terlihat.set("umur", 20);         // loker berlabel "umur" diisi 20
```

- `"nama"`, `"umur"` disebut **kunci** (key)
- `"Andi"`, `20` disebut **nilai** (value)

### Tiga operasi dasar yang akan kita pakai

```javascript
const terlihat = new Map();          // bikin Map kosong

terlihat.set("nama", "Andi");        // MENYIMPAN: kunci "nama" -> nilai "Andi"
terlihat.set("umur", 20);            // menyimpan lagi

console.log(terlihat.has("nama"));   // MENGECEK ada/tidak -> true
console.log(terlihat.has("kota"));   // -> false (belum pernah disimpan)

console.log(terlihat.get("nama"));   // MENGAMBIL nilainya -> "Andi"
console.log(terlihat.get("umur"));   // -> 20
```

Ingat tiga kata ini:

| Perintah | Artinya |
| --- | --- |
| `map.set(kunci, nilai)` | Simpan |
| `map.has(kunci)` | Cek ada atau tidak (hasilnya `true`/`false`) |
| `map.get(kunci)` | Ambil nilainya |

### Kenapa Map cepat?

Karena operasi **menyimpan**, **mengecek ada/tidak**, dan **mengambil** semuanya
butuh waktu yang **kira-kira tetap**, tidak peduli isinya sudah sebanyak apa.

Bandingkan dengan **array**. Kalau kamu punya array berisi 10.000 angka dan ingin
tahu "apakah angka 7 ada di sini?" dengan `nums.includes(7)`, JavaScript harus
memeriksa satu per satu dari depan — itu kerja 10.000 langkah. Kalau pakai Map,
jumlah langkahnya tetap sedikit.

Inilah kuncinya. Ingat-ingat: **Map = pengecekan super cepat.**

---

## 6. Ide besarnya

Sekarang kita gabungkan semua yang sudah dipelajari.

### Melihat lagi cara polos

Perhatikan loop dalam (`for j ...`) di cara polos. Apa yang sebenarnya dia lakukan?

Dia menjawab **satu pertanyaan**: *"apakah `target - nums[i]` ada di dalam array?"*

Pertanyaan itu dijawab dengan cara **memeriksa satu per satu** — dan itu lambat.

### Pertanyaan kunci

> Loop dalam itu sebenarnya cuma butuh **tahu ada atau tidak**.
> Apakah ada cara menjawab "ada atau tidak" tanpa memeriksa satu per satu?

Tentu ada — pakai **Map**, yang kita pelajari di bagian 5!

### Perubahan besar

Daripada memeriksa ulang ke depan (yang lambat), kita **catat** saja semua angka
yang sudah kita lewati, lalu tanya ke catatan itu.

Rencananya:

1. Siapkan catatan kosong (`Map`) bernama `terlihat`.
2. Telusuri `nums` dari kiri ke kanan. Di setiap angka `x`:
   - Hitung **komplemennya**: `target - x`.
   - **Tanya ke catatan:** apakah komplemen ini sudah pernah aku lewati?
   - **Kalau sudah** → ketemu! Kembalikan posisinya.
   - **Kalau belum** → catat `x` beserta posisinya, lalu lanjut.

Analogi: seperti mencatat barang di buku saat kamu menyusuri gudang, biar kalau
butuh sesuatu tinggal cek buku, tidak perlu balik-balik ke belakang.

Satu kali jalan saja. Tidak ada pemeriksaan ulang. **Inilah peningkatan besar itu.**

---

## 7. Kode final baris per baris

```javascript
var twoSum = function (nums, target) {
    const terlihat = new Map();

    for (let i = 0; i < nums.length; i++) {
        const x = nums[i];
        const komplemen = target - x;

        if (terlihat.has(komplemen)) {
            return [terlihat.get(komplemen), i];
        }

        terlihat.set(x, i);
    }

    return [];
};
```

Sekarang setiap baris, satu per satu.

### Baris 1: `var twoSum = function (nums, target) {`

Di LeetCode, kamu **tidak perlu** menulis `class Solution` seperti di Python atau
Java. Cukup tulis fungsi bernama `twoSum` seperti ini.

- `twoSum` = nama fungsinya. **Harus** sama dengan yang diminta soal, dan perhatikan
  huruf besar-kecilnya: `twoSum`, bukan `two_sum` atau `twosum`.
- `nums, target` = dua input dari soal.
- `var ... = function ...` adalah cara "menyimpan fungsi ke dalam variabel".
  Bisa juga ditulis `function twoSum(nums, target) { ... }` — dua-duanya jalan di
  LeetCode.

### Baris 2: `const terlihat = new Map();`

Membuat catatan baru berupa `Map` yang masih kosong.

- `new Map()` = perintah membuat Map baru
- `const` = "nilai ini tidak akan diganti dengan Map lain". Kita tetap boleh
  **mengubah isinya** (pakai `.set()`), hanya tidak boleh mengganti Map-nya sendiri.

Nanti isinya akan seperti:

```javascript
Map(2) { 2 => 0, 7 => 1 }
```

Bacanya: "angka 2 di posisi 0, angka 7 di posisi 1".

- **Kunci** (`key`) = **angkanya** (misal `2`)
- **Nilai** (`value`) = **posisinya** (misal `0`)

> **Perhatian:** penamaannya agak mudah tertukar. Di Map ini, yang jadi *kunci*
> adalah **angka dari array**, dan yang jadi *nilai* adalah **indeksnya**.
> Karena itu baris `terlihat.set(x, i)` — `x` (angkanya) jadi kunci,
> `i` (indeksnya) jadi nilainya.

### Baris 4: `for (let i = 0; i < nums.length; i++) {`

Loop yang menelusuri array dari posisi `0` sampai habis.

- `let i = 0` → mulai dari posisi 0
- `i < nums.length` → lanjut selama `i` belum melewati panjang array
- `i++` → setiap putaran, `i` ditambah 1

Jadi `i` akan bernilai `0, 1, 2, 3, ...` sampai habis.

Bisa juga ditulis lebih singkat seperti ini (hasilnya sama):

```javascript
for (const [i, x] of nums.entries()) {
```

`nums.entries()` memberikan **posisinya** (`i`) dan **angkanya** (`x`) sekaligus.
Tapi untuk pemula, versi `for (let i = ...)` lebih jelas karena kita lihat sendiri
bagaimana `i` naik. Di dokumen ini saya pakai versi panjang.

### Baris 5: `const x = nums[i];`

Mengambil angka di posisi `i`, supaya tidak perlu menulis `nums[i]` berulang kali.

### Baris 6: `const komplemen = target - x;`

Menghitung angka pasangan yang kita butuhkan.

Contoh, kalau `target = 9` dan `x = 2`, maka `komplemen = 7`. Artinya: "aku
memegang 2, dan aku butuh angka 7 supaya jumlahnya jadi 9".

### Baris 8: `if (terlihat.has(komplemen)) {`

**Ini baris paling penting.** Di sini kita bertanya ke catatan:

> "Apakah `komplemen` ini sudah pernah aku lewati sebelumnya?"

`.has()` mengembalikan `true` atau `false`. Kalau `true`, berarti angka pelengkapnya
sudah kita lihat sebelumnya.

Inilah pengganti loop dalam yang lambat tadi. Satu baris, kerja sedikit.

### Baris 9: `return [terlihat.get(komplemen), i];`

Artinya: "berhenti, dan kembalikan jawabannya."

- `terlihat.get(komplemen)` = **posisi di mana komplemen itu tadi ditemukan**
- `i` = **posisi angka yang sekarang aku pegang**

Diurutkan `[posisi_lama, posisi_sekarang]`, jadi yang depan pasti lebih kecil.

Karena `return` langsung menghentikan fungsi, sisa array **tidak perlu diperiksa
lagi**. Kalau jawabannya sudah ketemu di depan, kita tidak buang waktu.

### Baris 12: `terlihat.set(x, i);`

Kalau komplemennya **tidak** ketemu, berarti angka ini belum punya pasangan.
Catat dulu: "angka `x` sudah aku lihat, di posisi `i`", lalu lanjut ke angka berikutnya.

Contohnya kalau `x = 2` dan `i = 0`, jadi catatannya berisi `2 => 0`.

### Baris 15: `return [];`

Cadangan kalau loop selesai tanpa ketemu. Karena soal menjamin selalu ada
jawaban, baris ini **tidak akan pernah** dijalankan. Ditulis hanya supaya fungsi
selalu mengembalikan sesuatu.

---

## 8. Jalannya program, langkah demi langkah

Sekarang kita ikuti betul-betul, baris per baris. Pakai contoh pertama.

```javascript
nums = [2, 7, 11, 15]
target = 9
```

Awalnya: `terlihat = new Map()` (kosong).

### Langkah 1

| | |
| --- | --- |
| `i` | `0` |
| `x` | `2` |
| komplemen = `9 - 2` | `7` |

Pertanyaan: apakah `7` ada di `terlihat`? Isinya masih kosong, jadi **tidak ada**.

Maka lanjut ke baris `terlihat.set(x, i)` → catat `2 => 0`

*Artinya: "angka 2 sudah aku lihat, di posisi 0."*

### Langkah 2

| | |
| --- | --- |
| `i` | `1` |
| `x` | `7` |
| komplemen = `9 - 7` | `2` |

Pertanyaan: apakah `2` ada di `terlihat`? Isinya `{ 2 => 0 }` → **ADA!**

Maka kita `return [terlihat.get(2), 1]` → `terlihat.get(2)` adalah `0` → hasilnya **`[0, 1]`**

### Selesai ✅

Sudah ketemu sebelum array habis. Jadi `11` dan `15` sama sekali tidak diperiksa.
Hasil akhir: `[0, 1]`.

---

### Sekarang contoh `[3, 3]`, target `6`

Contoh ini penting karena berisi angka yang sama dua kali.

Awalnya: `terlihat` kosong.

**Langkah 1:** `i = 0`, `x = 3`. Komplemen = `6 - 3 = 3`.
Apakah `3` ada di catatan? Tidak. Simpan → catatan berisi `3 => 0`.

**Langkah 2:** `i = 1`, `x = 3`. Komplemen = `6 - 3 = 3`.
Apakah `3` ada di catatan? **ADA!** (dari langkah 1).
Kembalikan `[terlihat.get(3), 1]` = `[0, 1]`. ✅

Perhatikan: angka `3` muncul dua kali di array, dan **itu tidak masalah**. Yang kita
simpan di catatan adalah angka `3` sebagai *kunci*, dan **posisinya ditimpa**
dari `0` menjadi `1` — tapi karena jawabannya sudah ketemu di langkah 2, kita
`return` lebih dulu dan penimpaan itu tidak pernah terjadi.

---

## 9. Kenapa ini benar

Bagian ini namanya **pembuktian**. Kedengarannya serius, tapi idenya sederhana:
kita yakinkan diri bahwa program ini **tidak mungkin** salah.

### Invarian

> **Invarian** artinya "hal yang selalu benar". Kita akan pastikan satu hal selalu
> benar di setiap titik program.

Invarian kita:

> **Setiap kali kita sampai di awal iterasi dengan posisi `i`, isi `terlihat`
> berisi persis semua angka dari posisi `0` sampai `i-1` (semua yang sudah
> dilewati), beserta posisinya masing-masing.**

**Kenapa ini selalu benar?**

- **Di awal:** `i = 0`. Belum ada yang dilewati. Isi `terlihat` kosong.
  Benar. ✅
- **Setiap langkah:** di akhir iterasi `i`, kita menambahkan `set(x, i)`.
  Jadi sekarang catatannya mencakup posisi `i` juga. Saat masuk iterasi
  berikutnya (`i+1`), catatannya berisi semua dari `0` sampai `i` — persis yang
  seharusnya. ✅

Jadi invariannya terbukti selalu benar.

### Kenapa jawabannya pasti ketemu

Misal jawaban yang benar adalah pasangan posisi `(j, i)` dengan `j < i`.

Saat loop sampai di posisi `i`:

- Menurut invarian, `terlihat` sudah berisi semua posisi `0` sampai `i-1`.
- Karena `j < i`, berarti posisi `j` **sudah ada** di dalam `terlihat`.
- Angka di posisi `j` adalah `nums[j]`.
- Komplemen yang kita hitung adalah `target - nums[i]`.
- Karena `(j, i)` adalah jawaban valid, maka `nums[j] + nums[i] = target`,
  artinya `nums[j] = target - nums[i]` = komplemen kita.
- Jadi komplemen itu **pasti ditemukan**, dan yang kita kembalikan
  adalah `[j, i]` — jawaban yang benar. ✅

### Kenapa `j` selalu lebih kecil dari `i`

Karena kita hanya mengecek **yang sudah lewat** (bukan yang belum). Angka yang
belum dilewati belum masuk catatan. Jadi kalau ketemu, posisi yang di catatan
pasti lebih dulu. Ini otomatis memastikan `j < i`, artinya **tidak pernah** kita
mengembalikan `[i, i]` (satu posisi dipakai dua kali). ✅

### Kenapa loop pasti berhenti

Karena loop `for` menelusuri array yang panjangnya terbatas (`n` angka), jadi
paling banyak `n` kali putaran. Tidak mungkin berputar selamanya. ✅

---

## 10. Soal menjebak: `[3, 3]`

Ini bagian yang paling sering menipu.

Soal bilang: *"satu elemen tidak boleh dipakai dua kali."* Banyak orang membacanya
sebagai: *"angkanya harus berbeda."* **Itu keliru.**

Yang dilarang adalah **posisi** yang sama dipakai dua kali. Dua posisi berbeda yang
kebetulan isinya sama itu **boleh**.

Mari bandingkan:

| Kasus | Boleh? | Hasil |
| --- | --- | --- |
| `nums = [3, 3]`, `target = 6` → pakai posisi `0` dan `1` | ✅ Boleh | `[0, 1]` |
| Pakai posisi `0` dan posisi `0` (angka sama, posisi sama) | ❌ Dilarang | ini yang dilarang soal |

Kalau kamu keliru paham dan menganggap "angkanya harus beda", kamu akan menolak
kasus `[3, 3]` dan menjawab salah.

**Kenapa kode kita sudah aman untuk kasus ini?**

Karena kita mengecek catatan **sebelum** mencatat angka sekarang. Jadi saat kita
memeriksa `x` di posisi `1`, yang ada di catatan hanya posisi `0` — tidak ada
kemungkinan ketemu dirinya sendiri.

---

## 11. Kesalahan simpan-dulu-baru-cek

Sekarang lihat bagaimana **urutan** baris menentukan benar-salah. Ini contoh versi
yang **SALAH**:

```javascript
for (let i = 0; i < nums.length; i++) {
    const x = nums[i];
    terlihat.set(x, i);                       // <-- SIMPAN DULU
    const komplemen = target - x;
    if (terlihat.has(komplemen)) {            // <-- baru CEK
        return [terlihat.get(komplemen), i];
    }
}
```

Bandingkan dengan versi **BENAR**:

```javascript
for (let i = 0; i < nums.length; i++) {
    const x = nums[i];
    const komplemen = target - x;
    if (terlihat.has(komplemen)) {            // <-- CEK DULU
        return [terlihat.get(komplemen), i];
    }
    terlihat.set(x, i);                       // <-- baru SIMPAN
}
```

### Apa yang rusak di versi salah?

Uji dengan `nums = [3]`, `target = 6`. (Ini contoh buatan supaya kelihatan jelas;
di soal asli panjang minimalnya 2, tapi prinsipnya sama.)

**Versi salah:**
- `i = 0`, `x = 3`. Simpan dulu → catatan `3 => 0`.
- Hitung komplemen: `6 - 3 = 3`.
- Cek: apakah `3` ada di catatan? **ADA** — tapi itu dari angka yang baru saja
  kita simpan **sendiri**!
- Kembalikan `[0, 0]`. ❌ **SALAH** — posisi 0 dipakai dua kali!

**Versi benar:**
- `i = 0`, `x = 3`. Hitung komplemen: `6 - 3 = 3`.
- Cek: apakah `3` ada di catatan? Masih kosong → belum ada.
- Baru simpan → catatan `3 => 0`.
- Loop habis (cuma 1 angka), kembali `[]`. ✅ Tidak ada jawaban palsu.

### Pelajarannya

> **Selalu cek dulu, baru simpan.**

Penyebabnya jelas: kalau menyimpan dulu, komplemen yang "ditemukan" bisa jadi
adalah angka itu sendiri, bukan pasangan yang sesungguhnya.

---

## 12. Kenapa harus Map, bukan object `{}`

Ini bagian penting khusus JavaScript.

### Godaan memakai object biasa

Di JavaScript, object biasa juga bisa dipakai seperti catatan:

```javascript
const terlihat = {};        // object biasa
terlihat[2] = 0;            // simpan
if (2 in terlihat) { }      // cek
```

Kelihatannya lebih pendek. **Tapi ada dua masalah serius.**

### Masalah 1: kuncinya diubah jadi teks

Di object biasa, semua kunci dipaksa menjadi **string** (teks). Jadi angka `2`
sebenarnya disimpan sebagai teks `"2"`.

```javascript
const o = {};
o[2] = "dua";
console.log(Object.keys(o));   // ["2"]  <-- string, bukan angka!
```

Untuk soal ini kebetulan tidak jadi masalah, tapi ini perilaku yang perlu kamu
ketahui.

### Masalah 2: `in` ikut memeriksa "warisan" (ini yang berbahaya)

```javascript
const terlihat = {};
console.log("toString" in terlihat);        // true  <-- !!
console.log(terlihat.hasOwnProperty("toString"));  // false
```

Kenapa `true`? Karena di JavaScript, **semua object biasa otomatis mewarisi**
beberapa properti bawaan dari `Object.prototype`, seperti `toString`,
`constructor`, `valueOf`, dan lainnya.

Akibatnya, kalau kamu pakai `if (komplemen in terlihat)`, ada kemungkinan itu
mengembalikan `true` padahal kamu **belum pernah menyimpannya** — hanya karena
namanya kebetulan sama dengan properti bawaan.

Untuk soal Two Sum dengan angka, ini tidak terjadi. Tapi ini jebakan yang nyata di
soal lain, dan kebiasaan buruk.

### Solusinya: pakai `Map`

```javascript
const terlihat = new Map();
terlihat.set(2, 0);
console.log(terlihat.has(2));              // true
console.log(terlihat.has("toString"));     // false  <-- aman!
```

`Map` menyimpan kunci **apa adanya** (angka tetap angka, bukan diubah jadi teks),
dan `Map` **tidak mewarisi** apa pun. Jadi tidak ada kejutan.

### Perbandingan singkat

| | Object `{}` | `Map` |
| --- | --- | --- |
| Simpan | `o[k] = v` | `m.set(k, v)` |
| Cek | `k in o` (bahaya) | `m.has(k)` ✅ |
| Ambil | `o[k]` | `m.get(k)` |
| Tipe kunci | dipaksa jadi teks | apa adanya ✅ |
| Warisan bawaan | ada (bikin `in` bohong) | tidak ada ✅ |

> **Kesimpulan: kalau butuh hash map di JavaScript, biasakan langsung pakai `Map`.**

---

## 13. Kompleksitas waktu dan ruang

Kedua istilah ini penting untuk interview, jadi kita pelajari.

### Notasi O besar

`O(n)` dibaca "orde n". Artinya: **berapa banyak kerja yang dibutuhkan
dibandingkan jumlah input `n`.**

- `O(n)` = kalau input jadi 2 kali lebih banyak, kerja jadi 2 kali lebih banyak.
  Ini bagus. Namanya **linear**.
- `O(n²)` = kalau input jadi 2 kali lebih banyak, kerja jadi **4 kali** lebih
  banyak. Ini jelek. Namanya **kuadratik**.

### Waktu

Kode kita melakukan **satu kali penelusuran** (`for` sebanyak `n` kali putaran).
Di setiap putaran:

- ambil `nums[i]` → 1 langkah
- hitung komplemen → 1 langkah
- `terlihat.has(komplemen)` → kira-kira 1 langkah (Map itu cepat)
- `terlihat.set(x, i)` → kira-kira 1 langkah

Jadi totalnya kira-kira `4 × n` langkah. Karena angkanya konstan tidak penting
dalam notasi O besar, hasilnya:

> **Waktu: O(n)** — sangat cepat.

Bandingkan dengan cara polos yang `O(n × n) = O(n²)`.

| n | O(n) langkah | O(n²) langkah |
| --- | --- | --- |
| 100 | 100 | 10.000 |
| 1.000 | 1.000 | 1.000.000 |
| 10.000 | 10.000 | 100.000.000 |

Lihat betapa jauh bedanya di `n = 10.000`. Inilah kenapa cara polos kena TLE.

### Ruang

`Map` `terlihat` bisa menampung sampai `n` angka (kalau jawabannya ada di paling
akhir, hampir semua angka tersimpan).

> **Ruang: O(n)** — sebanding dengan jumlah input.

Ini harga yang kita bayar: kita menukar **kecepatan** dengan **memori**. Cara polos
tidak makan memori tambahan (`O(1)`), tapi lambat. Biasanya pertukaran ini
menguntungkan — memori biasanya murah, waktu tidak.

---

## 14. Jebakan khas JavaScript

| Jebakan | Penjelasan |
| --- | --- |
| Pakai `{}` alih-alih `new Map()` | `in` bisa bohong karena warisan bawaan. Lihat [bagian 12](#12-kenapa-harus-map-bukan-object-) |
| Tulis `two_sum` atau `twosum` | LeetCode memanggil nama fungsi yang persis: harus `twoSum` |
| Lupa `return` | Fungsi mengembalikan `undefined`, bukan array |
| `=` vs `===` | `=` untuk mengisi nilai, `===` untuk membandingkan. Di `if`, selalu pakai `===` |
| Pakai `var i = 0` di dalam loop | `var` bocor keluar blok. Biasakan `let` (nilainya bisa berubah) dan `const` (tidak berubah) |
| `nums.includes(komplemen)` di dalam loop | Benar secara hasil, tapi `includes` memindai dari awal satu-satu → jadi `O(n²)`, sama lambatnya dengan brute force |
| Lupa tanda `;` di akhir baris | Di JavaScript, `;` sebenarnya **opsional** (otomatis ditambahkan), tapi biasakan menulisnya supaya tidak bingung |
| Kurang tanda `}` | Pesan error JavaScript sering menunjuk baris yang salah. Kalau error aneh, **hitung kurung kurawalnya** |

### Khusus `nums.includes` — kenapa berbahaya?

```javascript
if (nums.includes(komplemen)) {
    return [nums.indexOf(komplemen), i];      // <-- JANGAN
}
```

Hasilnya **sering kebetulan benar**, jadi kelihatannya aman. Tapi `includes` dan
`indexOf` itu **memindai array dari awal satu per satu** sampai ketemu — itu `O(n)`.
Karena dilakukan di dalam loop yang juga `O(n)`, totalnya jadi `O(n²)` lagi. Sama
lambatnya dengan cara polos. Karena itu jangan dipakai.

### Beda Python vs JavaScript untuk soal ini

| | Python | JavaScript |
| --- | --- | --- |
| Membungkus | wajib `class Solution` | tidak perlu, langsung `var twoSum = ...` |
| Membandingkan | `==` | `===` |
| Kode blok | ditentukan **indentasi** | ditentukan `{ }` |
| Simpan ke map | `d[k] = v` | `m.set(k, v)` |
| Cek ada/tidak | `k in d` | `m.has(k)` |
| Ambil nilai | `d[k]` | `m.get(k)` |

Karena JavaScript menentukan blok kode dengan `{ }` (bukan spasi), **kamu tidak akan
kena masalah indentasi** seperti di Python. Ini keuntungan nyata JavaScript untuk pemula.

---

## 15. Error yang muncul di LeetCode

### `SyntaxError: Invalid or unexpected token`

Biasanya karena:

1. **Kutip melengkung dari copy-paste.** Kadang dari PDF/Word masuk tanda `"` `"` `'` `'`
   yang tidak dikenali. Ganti dengan `"` atau `'` biasa.
2. **Karakter tak terlihat** ikut ter-paste. Solusinya: **hapus seluruh editor**,
   lalu tempel lagi. Jangan menempel di atas kode lama.

### `SyntaxError: Unexpected end of input`

Artinya ada `{` yang **tidak ditutup** dengan `}`. Hitung ulang kurung kurawalnya.

### `nums[i] + nums[j] === target` tapi hasilnya aneh

Kalau `nums` berisi **teks** (misal `["2", "7"]`) bukan angka, `+` akan
menyambung teks, bukan menjumlahkan. Tapi di soal ini LeetCode selalu memberi angka,
jadi tidak perlu khawatir.

### `Maximum call stack size exceeded`

Ini terjadi kalau kamu pakai fungsi rekursif (memanggil diri sendiri) tanpa henti.
Solusi kita tidak pakai rekursi, jadi tidak akan kena.

---

## 16. Jebakan umum lainnya

| Jebakan | Kenapa salah |
| --- | --- |
| Mengembalikan **nilai** bukan **indeks** | Soal minta indeks. `[2, 7]` salah, seharusnya `[0, 1]` |
| Simpan dulu, baru cek | Bisa mengembalikan `[i, i]` — posisi dipakai dua kali |
| Menganggap "tidak boleh dua kali" soal nilai | Padahal soal **posisi**. `[3, 3] → [0, 1]` itu valid |
| Pakai `{}` alih-alih `Map` | Bisa kena masalah warisan bawaan |
| Pakai `nums.includes` / `nums.indexOf` di dalam loop | Jadi `O(n²)`, tidak ada gunanya |
| Lupa `return []` di akhir | Fungsi mengembalikan `undefined` kalau tidak ketemu |

---

## 17. Tiga pendekatan resmi (dan kapan masing-masing dipakai)

Di halaman LeetCode biasanya ada tiga pendekatan yang disediakan. Dua di antaranya
sama-sama pakai hash map, dan bedanya cuma di **kapan** hash map-nya diisi.

### Approach 1: Brute Force

```javascript
var twoSum = function (nums, target) {
    for (let i = 0; i < nums.length; i++) {
        for (let j = i + 1; j < nums.length; j++) {
            if (nums[i] + nums[j] === target) return [i, j];
        }
    }
    return [];
};
```

- **Waktu O(n²), ruang O(1)**
- Cara berpikir: coba semua pasangan.
- **Keunggulan sebenarnya: hemat memori ekstrem dan tidak mengubah apa pun.**

### Approach 2: Two-pass Hash Table

**Dua kali jalan.** Jalan pertama mencatat **semua** angka dulu, jalan kedua baru
bertanya.

```javascript
var twoSum = function (nums, target) {
    const map = new Map();

    // Jalan 1: catat semua angka (kalau ada duplikat, indeks terakhir yang menang)
    for (let i = 0; i < nums.length; i++) {
        map.set(nums[i], i);
    }

    // Jalan 2: baru bertanya
    for (let i = 0; i < nums.length; i++) {
        const komplemen = target - nums[i];
        // map.get(komplemen) !== i  <- pengaman, lihat penjelasan di bawah
        if (map.has(komplemen) && map.get(komplemen) !== i) {
            return [i, map.get(komplemen)];
        }
    }

    return [];
};
```

- **Waktu O(n), ruang O(n)**
- Karena map sudah berisi **semua** elemen, saat kita memeriksa indeks `i`, elemen
  `nums[i]` **juga ada di dalam map**. Jadi sebuah angka bisa "mencocokkan dirinya
  sendiri"! Contoh: `nums = [1, 2, 3]`, `target = 2`. Komplemen dari `1` adalah `1`,
  dan `1` ada di map (di indeks 0) — padahal itu angka yang sama.
- Karena itu **wajib** ada pengaman `map.get(komplemen) !== i`.
- **Inilah alasan Two-pass lebih rapuh:** kalau pengaman itu lupa ditulis, jawabannya
  `[0, 0]` yang melanggar aturan.

### Approach 3: One-pass Hash Table

**Satu kali jalan.** Cek dulu, baru simpan.

```javascript
var twoSum = function (nums, target) {
    const terlihat = new Map();

    for (let i = 0; i < nums.length; i++) {
        const x = nums[i];
        const komplemen = target - x;

        if (terlihat.has(komplemen)) {
            return [terlihat.get(komplemen), i];
        }

        terlihat.set(x, i);
    }

    return [];
};
```

- **Waktu O(n), ruang O(n)**
- Map hanya berisi elemen yang **sudah dilewati**, jadi elemen tidak mungkin
  mencocokkan dirinya sendiri. Pengaman seperti di Two-pass **tidak perlu**.
- Bisa berhenti lebih awal (begitu ketemu, langsung `return`), sedangkan Two-pass
  sudah "membayar" satu jalan penuh di depan.

### Perbandingan langsung

| | Approach 1: Brute Force | Approach 2: Two-pass | Approach 3: One-pass |
| --- | --- | --- | --- |
| Waktu | O(n²) | O(n) | O(n) |
| Ruang | **O(1)** | O(n) | O(n) |
| Jumlah jalan | 1 | **2** | 1 |
| Perlu pengaman `!== i`? | tidak | **ya** | tidak |
| Bisa berhenti lebih awal? | ya | tidak jelas | **ya** |
| Kerapuhan | aman | paling mudah salah | aman |

### Lalu Three-pass / dst?

Tidak ada. Menambah jalan ketiga tidak memberi apa pun — kerja tambahan tanpa
manfaat. Yang ada justru **arah sebaliknya**: kalau bisa menggabungkan jalan,
gabungkan.

### Kapan masing-masing dipakai (studi kasus)

#### Pakai Brute Force kalau:

1. **n sangat kecil** (misal `n < 100`). Rumusnya `n × (n−1) / 2`: untuk `n = 100`
   cuma 4.950 pemeriksaan — selesai dalam sekejap. Menambah hash map di sini
   justru **lebih lambat** di dunia nyata, karena membuat Map ada biayanya
   (alokasi memori, perhitungan hash tiap elemen).
2. **Memori sangat terbatas** — sistem tertanam (embedded), mikrokontroler,
   perangkat dengan RAM kecil. `O(1)` vs `O(n)` di sini bisa menentukan.
3. **Hanya untuk menyelesaikan masalah**, bukan untuk produksi. Kadang nulis 5 baris
   brute force lebih baik daripada 15 baris yang perlu di-debug.
4. **Sebagai penghitung pembanding** saat menguji solusi cepatmu (seperti yang
   saya lakukan di dokumen ini — brute force dipakai memverifikasi hash map).

**Intinya: brute force bukan "cara bodoh", tapi pertukaran yang masuk akal saat n kecil.**

#### Pakai Two-pass kalau:

1. **Butuh daftar lengkap lebih dulu, baru menjawab.** Misal kamu punya banyak
   pertanyaan (`target` berbeda-beda) atas **array yang sama**, dan array-nya tidak
   berubah. Bangun map **sekali**, lalu jawab semua pertanyaan.
2. **Soalnya minta "semua pasangan"**, bukan satu. Di sini "cek dulu baru simpan"
   milik One-pass malah menghalangi, karena kita justru perlu tahu isi array
   seluruhnya untuk menemukan semua kombinasi.
3. **Soalnya dua arah / bisa ditanyakan mundur.** Contoh: "untuk tiap angka A,
   adakah angka B di tempat lain yang memenuhi syarat?". Karena peta sudah lengkap,
   pertanyaan ke arah mana pun bisa dijawab.
4. **Soalnya tidak peduli urutan indeks**, jadi tidak masalah mengembalikan
   `[i, map.get(komplemen)]` (yang depan bisa lebih besar).

#### Pakai One-pass (yang kita pakai) kalau:

1. **Cukup satu jawaban** dan boleh berhenti begitu ketemu. ← **soal kita**
2. **Ingin bentuk paling ringkas dan paling sulit salah.** Tidak ada pengaman
   `!== i` yang bisa lupa ditulis.
3. **Ingin berhenti lebih awal** saat jawabannya ada di depan array.
4. **Soal berbentuk "cari pasangan" yang khas** — dua sum, dua selisih, duplikat.

#### Aturan praktis untuk memutuskan

> **Tanya tiga hal:**
>
> 1. **Berapa besar n?** Kecil (< ~1000) → brute force sering sudah cukup.
> 2. **Perlu daftar lengkap dulu, atau cukup sepotong?**
>    Cukup sepotong → one-pass. Perlu lengkap → two-pass.
> 3. **Satu pertanyaan atau banyak pertanyaan atas data yang sama?**
>    Banyak pertanyaan → two-pass (bangun sekali, pakai berkali-kali).

### Pendekatan lain di luar ketiga itu

| Pendekatan | Waktu | Ruang | Kenapa tidak dipakai |
| --- | --- | --- | --- |
| Sortir + dua pointer | O(n log n) | O(n) | Sortir merusak urutan asli, jadi posisi hilang. Harus bawa pasangan `(nilai, posisi)` — lebih rumit dan hasilnya tetap lebih lambat |
| Dua pointer tanpa sortir | — | — | Tidak berlaku. Teknik dua pointer hanya masuk akal kalau data terurut |
| `Set` nilai yang sudah dilihat | O(n) | O(n) | Cepat, tapi tidak menyimpan posisi — padahal itu yang diminta |
| `nums.indexOf` di dalam loop | O(n²) | O(n) | Sama lambatnya dengan brute force |
| Binary search pada array terurut | O(n log n) | O(1) | Kalah dari dua pointer pada array terurut, yang cuma O(n) |

**Dua pengecualian penting untuk sortir + dua pointer** (ini jadi pilihan
**terbaik**, bukan sekedar alternatif):

- inputnya **sudah terurut** → jadi `O(n)` waktu dan `O(1)` ruang, menang telak
  atas hash map
- soalnya hanya menanyakan **apakah ada** pasangan, bukan **di posisi mana**

Jadi pertanyaan penentunya: **apakah posisi asli harus dipertahankan?**
Kalau ya → hash map. Kalau tidak → pertimbangkan sortir + dua pointer.

---

## 18. Pola yang bisa dipakai lagi

Yang paling berharga dari soal ini bukan jawabannya, tapi **cara berpikirnya**,
karena bisa dipakai di banyak soal lain.

### Polanya

> **Kalau kamu perlu mencari "pasangan" untuk setiap elemen, simpan elemen yang
> sudah dilewati dalam hash map, lalu tanyakan komplemennya.**

### Kapan pola ini muncul?

Kalau soal berbentuk *"temukan dua hal yang jika digabungkan memenuhi sifat tertentu"*:

- jumlah dua angka sama dengan target ← **soal ini**
- selisih dua angka sama dengan target
- ada angka yang duplikat
- jumlah subarray sama dengan target (pakai prefix sum + hash map)
- mencocokkan anagram

### Kalimat ajaib untuk mengingatnya

> **"Ubah pencarian menjadi pengecekan keanggotaan."**

Artinya begini:

- **Pencarian** = memeriksa satu per satu sampai ketemu (lambat, `O(n)`)
- **Pengecekan keanggotaan** = tanya langsung "ada atau tidak?" (cepat, `O(1)`)

Map adalah alat yang mengubah yang pertama menjadi yang kedua, dengan
"membayar" `O(n)` memori di awal.

### Kebiasaan yang perlu dilatih

Setiap kali kamu menulis atau melihat **loop bersarang** (`for` di dalam `for`),
tanyakan:

> **"Sebenarnya loop yang di dalam ini sedang menjawab pertanyaan apa? Apakah ada
> struktur data yang bisa menjawabnya lebih cepat?"**

Ini satu pertanyaan yang mengubah sebagian besar solusi `O(n²)` menjadi `O(n)`.
Untuk soal ini, jawabannya: loop dalam sedang menjawab "apakah ada angka X?", dan
Map bisa menjawabnya lebih cepat.

---

## 19. Ringkasan satu halaman

**Soal:** cari dua posisi di `nums` yang jumlahnya `target`. Jaminan: tepat satu
jawaban, posisi tidak boleh dipakai dua kali.

**Ide:** telusuri sekali dari kiri ke kanan. Simpan angka yang sudah dilewati di
`Map` (`angka → posisi`). Sebelum menyimpan angka sekarang, cek dulu apakah
komplemennya (`target - x`) sudah ada di Map. Kalau ada → selesai.

**Kode:**

```javascript
var twoSum = function (nums, target) {
    const terlihat = new Map();

    for (let i = 0; i < nums.length; i++) {
        const x = nums[i];
        const komplemen = target - x;

        if (terlihat.has(komplemen)) {
            return [terlihat.get(komplemen), i];
        }

        terlihat.set(x, i);
    }

    return [];
};
```

**Tiga hal yang paling mudah salah:**

1. Kembalikan **indeks**, bukan nilai.
2. **Cek dulu, baru simpan** — kalau dibalik, hasilnya bisa `[i, i]`.
3. Pakai **`Map`**, bukan object `{}` — karena `in` pada object bisa bohong.

**Kompleksitas:** waktu `O(n)`, ruang `O(n)`.

**Kalimat kunci:** *ubah pencarian menjadi pengecekan keanggotaan.*

**Keuntungan JavaScript untuk pemula:** blok kode ditentukan `{ }`, bukan spasi.
Jadi kamu tidak akan kena error gara-gara indentasi seperti di Python.
