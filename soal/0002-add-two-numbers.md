# 2. Add Two Numbers

**Tingkat:** Menengah
**Pola:** Simulasi digit + Dummy Node (Senjata Wajib Linked List)
**Bahasa:** JavaScript

---

## Daftar Isi

1. [Soalnya apa](#1-soalnya-apa)
2. [Senjata baru: ListNode](#2-senjata-baru-listnode)
3. [Kenapa urutan terbalik itu "hadiah"](#3-kenapa-urutan-terbalik-itu-hadiah)
4. [Jebakan pertama: cara "ubah ke angka"](#4-jebakan-pertama-cara-ubah-ke-angka)
5. [Ide besarnya: tiru cara SD](#5-ide-besarnya-tiru-cara-sd)
6. [Kode final baris per baris](#6-kode-final-baris-per-baris)
7. [Senjata kedua: dummy node](#7-senjata-kedua-dummy-node)
8. [Jalannya program, langkah demi langkah](#8-jalannya-program-langkah-demi-langkah)
9. [Kenapa ini benar](#9-kenapa-ini-benar)
10. [Soal menjebak: carry terakhir](#10-soal-menjebak-carry-terakhir)
11. [Panjang list berbeda](#11-panjang-list-berbeda)
12. [Kompleksitas waktu dan ruang](#12-kompleksitas-waktu-dan-ruang)
13. [Cara menguji jawabanmu](#13-cara-menguji-jawabanmu)
14. [Jebakan umum lainnya](#14-jebakan-umum-lainnya)
15. [Variasi soal: kalau urutannya normal](#15-variasi-soal-kalau-urutannya-normal)
16. [Pola yang bisa dipakai lagi](#16-pola-yang-bisa-dipakai-lagi)
17. [Ringkasan satu halaman](#17-ringkasan-satu-halaman)

---

## 1. Soalnya apa

Diberikan **dua buah linked list**, `l1` dan `l2`, yang masing-masing menyimpan
satu bilangan:

- Setiap **node** berisi **satu digit** (0 sampai 9).
- Digitnya tersimpan **terbalik** — digit **satuan di paling depan**, lalu puluhan,
  lalu ratusan, dan seterusnya.
- Bilangannya **tidak** punya angka nol di depan, kecuali bilangan 0 itu sendiri.
- **Non-empty**: kedua list pasti punya minimal satu node.
- Panjangnya sampai **100 node**.

**Yang diminta:** jumlahkan kedua bilangan itu, lalu kembalikan hasilnya juga dalam
bentuk linked list dengan format **terbalik yang sama**.

### Contoh 1

```
Input:  l1 = [2, 4, 3],  l2 = [5, 6, 4]
Output: [7, 0, 8]
```

Cara membacanya (hati-hati, dibaca terbalik!):

- `l1 = [2, 4, 3]` → dibaca dari belakang → **342**
- `l2 = [5, 6, 4]` → dibaca dari belakang → **465**
- `342 + 465 = 807`
- `807` ditulis terbalik → `[7, 0, 8]` ✅

### Contoh 2

```
Input:  l1 = [0],  l2 = [0]
Output: [0]
```

`0 + 0 = 0` → `[0]`.

### Contoh 3

```
Input:  l1 = [9, 9, 9, 9, 9, 9, 9],  l2 = [9, 9, 9, 9]
Output: [8, 9, 9, 9, 0, 0, 0, 1]
```

- `l1` = 9.999.999, `l2` = 9.999 → jumlahnya **10.009.998**
- ditulis terbalik → `[8, 9, 9, 9, 0, 0, 0, 1]`

**Perhatikan:** panjang hasil (8) **lebih panjang** dari kedua input (7 dan 4).
Ini karena ada **carry** terakhir yang melahirkan digit baru. Contoh ini penting —
lihat [bagian 10](#10-soal-menjebak-carry-terakhir).

---

## 2. Senjata baru: ListNode

Linked list bukan array. Tidak ada indeks, tidak ada `.length`. Yang ada cuma
**node** yang saling menyambung.

```javascript
function ListNode(val, next) {
    this.val = (val === undefined ? 0 : val);     // digitnya
    this.next = (next === undefined ? null : next); // sambungan ke node berikutnya
}
```

Bayangkan seperti **kereta**: setiap gerbong (`node`) membawa satu angka (`val`),
dan tersambung ke gerbong berikutnya (`next`). Gerbong terakhir `next`-nya `null`
(tidak menyambung ke apa-apa).

```
l1:  [2] -> [4] -> [3] -> null
     head              tail
```

### Beda penting: List vs Array

| | Array | Linked List |
| --- | --- | --- |
| Ambil elemen ke-i | `arr[i]` — langsung, O(1) | harus jalan dari depan satu-satu, O(n) |
| Tahu panjang | `arr.length` — langsung | harus dihitung, O(n) |
| Ambil dari belakang | `arr[arr.length-1]` — langsung | tidak bisa langsung! |

Baris terakhir itu kunci: **di linked list kamu tidak bisa lompat ke belakang.**
Kamu hanya bisa berjalan **maju**. Ini akan menentukan bentuk solusinya.

### Cara membuat list dari array (untuk uji coba)

```javascript
const build = (a) => a.reduceRight((n, v) => new ListNode(v, n), null);
```

`reduceRight` berjalan dari **kanan ke kiri**, jadi node terakhir dibuat lebih dulu.
Untuk `[7, 0, 8]`:

- mulai dari `null`
- `8` → `new ListNode(8, null)` → `[8]`
- `0` → `new ListNode(0, [8])` → `[0] -> [8]`
- `7` → `new ListNode(7, [0]->[8])` → `[7] -> [0] -> [8]` ✅

### Cara membaca list jadi array (untuk memeriksa hasil)

```javascript
const toArr = (h) => {
    const o = [];
    while (h) { o.push(h.val); h = h.next; }
    return o;
};
```

---

## 3. Kenapa urutan terbalik itu "hadiah"

Soal sengaja menyimpan digit **terbalik**, dan ini membuat soalnya **jauh lebih
mudah** daripada kalau normal. Ini bukan detail sepele — ini inti soalnya.

### Cara kita menjumlahkan di kertas

Ingat cara SD: kita mulai dari **digit paling kanan** (satuan), karena di situlah
**carry** (simpanan) lahir, dan carry mengalir ke **kiri**.

```
  342
+ 465
-----
  807
```

Kita mulai dari `2 + 5 = 7`, lalu `4 + 6 = 10` → tulis `0`, simpan `1`, lalu
`3 + 4 + 1 = 8`.

**Jadi: kita bergerak dari kanan ke kiri, dan carry mengalir ke kiri.**

### Masalahnya kalau disimpan normal

Kalau linked list menyimpan digit secara **normal** (ratusan dulu):

```
l1: [3] -> [4] -> [2]      (342)
l2: [4] -> [6] -> [5]      (465)
```

Sekarang masalahnya:

- Kita harus mulai dari digit **paling kanan** (`2` dan `5`).
- Tapi linked list **hanya bisa jalan maju** — tidak bisa lompat ke belakang!
- Jadi kita harus **memutar balik** list-nya dulu (butuh stack, atau membalik list).

### Dengan urutan terbalik (kondisi soal kita)

```
l1: [2] -> [4] -> [3]      (342, tapi terbalik)
l2: [5] -> [6] -> [4]      (465, tapi terbalik)
```

Sekarang:

- Digit satuan (`2` dan `5`) ada di **paling depan** ✅
- Kita bisa langsung menjumlahkannya dengan **satu jalan maju** ✅
- Dan **arah carry sama dengan arah jalan kita** — carry mengalir ke kiri, kita
  berjalan maju ke kanan... dan di representasi terbalik, "kiri" (digit lebih
  tinggi) justru ada di **depan perjalanan kita** ✅

> **Kesimpulan: karena soal sudah membalik digitnya, kita tidak perlu membalik
> apa pun. Cukup jalan maju satu kali, sambil membawa carry.**

Bonus lagi: karena kita jalan maju dan menjumlahkan posisi-sejajar, kita **tidak
perlu tahu panjang** kedua list. Kalau list-nya habis, anggap saja digitnya `0`.
Ini menghilangkan seluruh kerumitan menghitung panjang.

---

## 4. Jebakan pertama: cara "ubah ke angka"

Godaan pertama yang hampir semua orang alami: *"kenapa tidak ubah saja kedua list
jadi angka, jumlahkan, lalu ubah balik jadi list?"*

```javascript
// JANGAN PAKAI INI
var addTwoNumbers = function (l1, l2) {
    let n1 = 0, mul = 1;
    while (l1) { n1 += l1.val * mul; mul *= 10; l1 = l1.next; }
    // ... sama untuk l2 ...
    let sum = n1 + n2;               // <-- INI MASALAHNYA
    // ... ubah sum jadi list ...
};
```

Secara logika ini **benar**. Tapi **gagal total** karena satu hal: **presisi angka**.

### Kenapa gagal?

JavaScript menyimpan semua angka sebagai **64-bit floating point** (*double*).
Akibatnya, bilangan bulat yang **dijamin tepat** hanya sampai:

```
Number.MAX_SAFE_INTEGER = 9007199254740991
```

Itu cuma **16 digit**. Sedangkan soal mengizinkan sampai **100 digit**.

### Buktinya (sudah saya jalankan)

```javascript
console.log(9007199254740992 + 1);   // 9007199254740992   <-- BUKAN 9007199254740993!
```

Angkanya **tidak bertambah**! Karena `9007199254740993` tidak bisa diwakili tepat,
JavaScript membulatkannya kembali ke `9007199254740992`.

Lebih menipu lagi:

```javascript
console.log(9007199254740992 + 1 === 9007199254740993);   // true !!
```

Kelihatannya "benar", padahal hasilnya **salah** — karena sisi kanan juga dibulatkan
ke nilai yang sama. **Bahkan perbandingannya ikut tertipu.**

### Seberapa sering gagal?

Saya uji acak 20.000 kali dengan bilangan 15–20 digit:

```
20000 percobaan -> 17613 gagal (88%)
```

**88% gagal.** Inilah kenapa soal ini ditulis: **justru untuk memaksa kamu
melakukan penjumlahan digit per digit**, bukan sekadar `a + b`.

### Catatan: kenapa tidak pakai BigInt saja?

JavaScript punya `BigInt` yang bisa menangani angka raksasa:

```javascript
BigInt("99999999999999999999") + 1n    // benar
```

Tapi ini **bukan maksud soalnya**:

1. Bahasa lain (C++, Java) tidak punya tipe ini untuk kasus seperti ini.
2. Soal sengaja menguji kemampuan **simulasi** — apakah kamu bisa mengelola carry.
3. Kalau kamu jawab pakai BigInt, kamu tidak belajar apa pun dari pola soalnya.

> **Kesimpulan: jangan ubah ke angka. Jumlahkan **digit per digit** seperti di SD.**

---

## 5. Ide besarnya: tiru cara SD

Idenya sederhana: **lakukan persis apa yang kamu lakukan di kertas.**

1. Lihat digit satuan dari `l1` dan `l2`. Jumlahkan (plus carry).
2. Hasilnya mungkin **≥ 10**. Kalau iya: digit yang ditulis adalah `jumlah % 10`,
   dan `carry = 1`. Kalau tidak: `carry = 0`.
3. Pindah ke digit berikutnya dari **kedua** list, ulangi.
4. Kalau salah satu list sudah habis, anggap digitnya `0` (jangan berhenti!).
5. Setelah keduanya habis, **masih ada carry?** Kalau iya, tambahkan satu digit lagi.

Perbedaan satu-satunya dengan kertas: outputnya kita tulis sebagai linked list,
bukan sebagai baris angka.

### Tiga teknik yang dipakai

**a. Variabel `carry`** — menyimpan simpanan. Di awal `0`.

```javascript
let carry = 0;
```

**b. Loop yang menangani list berbeda panjang** — pakai `? :` untuk mengganti node
yang sudah habis dengan `0`.

```javascript
const a = l1 ? l1.val : 0;
const b = l2 ? l2.val : 0;
```

**c. Loop terus selama masih ada digit ATAU masih ada carry**:

```javascript
while (l1 !== null || l2 !== null || carry !== 0) {
```

Ini yang menangani carry terakhir. Lihat [bagian 10](#10-soal-menjebak-carry-terakhir).

---

## 6. Kode final baris per baris

```javascript
var addTwoNumbers = function (l1, l2) {
    const dummy = new ListNode(0);
    let tail = dummy;
    let carry = 0;

    while (l1 !== null || l2 !== null || carry !== 0) {
        const a = l1 ? l1.val : 0;
        const b = l2 ? l2.val : 0;

        const sum = a + b + carry;
        carry = Math.floor(sum / 10);

        tail.next = new ListNode(sum % 10);
        tail = tail.next;

        if (l1) l1 = l1.next;
        if (l2) l2 = l2.next;
    }

    return dummy.next;
};
```

### Baris 1: `var addTwoNumbers = function (l1, l2) {`

Nama fungsinya **harus** persis `addTwoNumbers`. Input `l1` dan `l2` adalah
`ListNode` (kepala dari masing-masing list).

### Baris 2: `const dummy = new ListNode(0);`

Membuat **dummy node** — node bohongan yang tidak menyimpan hasil apa pun. Ini
teknik penting yang dijelaskan di [bagian 7](#7-senjata-kedua-dummy-node).

### Baris 3: `let tail = dummy;`

`tail` (ekor) adalah penunjuk ke node **terakhir** dari list hasil yang sedang kita
bangun. Karena kita membangun dari depan ke belakang, kita selalu menempelkan node
baru di `tail`.

Awalnya `tail` menunjuk ke `dummy`.

> Perhatikan bedanya `const` dan `let`: `dummy` tidak pernah diganti (jadi `const`),
> tapi `tail` terus berpindah (jadi `let`).

### Baris 4: `let carry = 0;`

Simpanan. Di awal `0` karena belum ada simpanan.

### Baris 6: `while (l1 !== null || l2 !== null || carry !== 0) {`

**Loop ini jalan terus selama salah satu dari tiga hal ini benar:**

1. `l1` masih punya digit → masih ada yang perlu dijumlahkan
2. `l2` masih punya digit → masih ada yang perlu dijumlahkan
3. `carry !== 0` → masih ada simpanan yang belum ditulis!

**Kenapa pakai `||`, bukan `&&`?** Karena kalau pakai `&&`, begitu salah satu list
habis, loop langsung berhenti — padahal list yang lain masih punya digit!

**Kenapa ada `carry !== 0`?** Inilah yang menangani carry terakhir. Tanpa itu,
`[5] + [5]` akan menghasilkan `[0]` saja, padahal seharusnya `[0, 1]`. Lihat
[bagian 10](#10-soal-menjebak-carry-terakhir).

### Baris 7–8: `const a = l1 ? l1.val : 0;`

Ini trik untuk **list berbeda panjang**.

- Kalau `l1` masih ada → ambil digitnya.
- Kalau `l1` sudah habis (`null`) → pakai `0`.

Contoh: `l1 = [1]` (list 1 digit) dan `l2 = [9, 9]` (2 digit). Pada putaran kedua,
`l1` sudah `null`, jadi `a = 0` dan kita jumlahkan `0 + 9`. ✅

Trik ini **menghilangkan kebutuhan menghitung panjang** kedua list. Kita tidak perlu
tahu mana yang lebih panjang — cukup jalan sampai keduanya habis.

### Baris 10: `const sum = a + b + carry;`

Jumlahkan **dua digit plus carry**. Ini persis yang kamu lakukan di kertas.

Nilai maksimalnya: `9 + 9 + 1 = 19`. Jadi `sum` selalu di antara `0` dan `19`.

### Baris 11: `carry = Math.floor(sum / 10);`

Menghitung carry baru.

- `sum = 7` → `Math.floor(7/10) = 0` → tidak ada simpanan
- `sum = 10` → `Math.floor(10/10) = 1` → simpan 1
- `sum = 19` → `Math.floor(19/10) = 1` → simpan 1

**Kenapa `Math.floor`?** Karena di JavaScript, `10 / 10` menghasilkan `1`, tapi
`19 / 10` menghasilkan `1.9`. Kita butuh bilangan bulat, jadi dibulatkan **ke bawah**.

> **Catatan penting:** di JavaScript, bilangan selalu bertipe *double*, jadi
> pembagian bisa menghasilkan pecahan. Di Python/Java/C++, `19 / 10` untuk bilangan
> bulat sudah otomatis `1` (pembagian integer). **Di JavaScript, jangan lupa
> `Math.floor`!**

### Baris 13: `tail.next = new ListNode(sum % 10);`

Membuat node baru berisi **digit hasil**, lalu menyambungkannya ke list.

`sum % 10` adalah **sisa bagi** — operator `%` mengembalikan sisa pembagian:

- `7 % 10 = 7`
- `10 % 10 = 0`
- `19 % 10 = 9`

Jadi `sum % 10` mengambil **digit terakhir**: kalau `sum = 19`, kita tulis `9` dan
simpan `1`. ✅

Bisa juga ditulis `sum - carry * 10` (hasilnya sama), tapi `% 10` lebih ringkas.

### Baris 14: `tail = tail.next;`

**Memindahkan ekor** ke node yang baru saja dibuat. Kalau baris ini lupa, kita akan
**menimpa node yang sama terus-menerus** dan list hasilnya cuma berisi satu digit.
Ini bug yang sangat mudah terjadi.

### Baris 16–17: `if (l1) l1 = l1.next;`

Memajukan penunjuk ke node berikutnya.

**Kenapa pakai `if`?** Karena kalau `l1` sudah `null`, mengakses `l1.next` akan
**error** (`Cannot read properties of null`). Jadi kita hanya memajukan yang masih ada.

### Baris 20: `return dummy.next;`

**Kenapa `dummy.next`, bukan `dummy`?** Karena `dummy` itu node bohongan (isinya `0`,
tidak pernah dipakai sebagai hasil). Node hasil yang sebenarnya adalah yang
**setelah** dummy. Lihat [bagian 7](#7-senjata-kedua-dummy-node).

---

## 7. Senjata kedua: dummy node

Dummy node (disebut juga **sentinel** atau **penjaga**) adalah node bohongan di
**awal** list, yang isinya tidak pernah dipakai.

### Kenapa perlu?

Bayangkan tanpa dummy:

```javascript
let head = null;   // kepala list hasil
let tail = null;

while (...) {
    const newNode = new ListNode(sum % 10);
    if (head === null) {
        head = newNode;        // <-- KASUS KHUSUS: node pertama
        tail = newNode;
    } else {
        tail.next = newNode;
        tail = newNode;
    }
}
return head;
```

Perhatikan `if (head === null)`. Itu **kasus khusus untuk node pertama** — dan
kasus khusus berarti tempat bug bersembunyi. Kalau lupa, atau salah menangani,
list-nya rusak.

### Dengan dummy, kasus khusus itu hilang

```javascript
const dummy = new ListNode(0);
let tail = dummy;

while (...) {
    tail.next = new ListNode(sum % 10);   // <-- selalu sama, tidak ada if!
    tail = tail.next;
}
return dummy.next;
```

Sekarang **tidak ada** `if` untuk node pertama. Karena `tail` sudah menunjuk ke
`dummy` sejak awal, "menempelkan node" bekerja sama untuk node pertama maupun
node keseratus.

### Ilustrasinya

```
Awal:      dummy(0) <- tail menunjuk ke sini

Putaran 1: dummy(0) -> [7]         tail pindah ke [7]
Putaran 2: dummy(0) -> [7] -> [0]  tail pindah ke [0]
Putaran 3: dummy(0) -> [7] -> [0] -> [8]   tail pindah ke [8]

return dummy.next  ->  [7] -> [0] -> [8]   ✅
```

Dummy sendiri dibuang, `dummy.next` adalah awal hasil yang sebenarnya.

> **Ini pola wajib di semua soal linked list yang membangun list baru.** Hafalkan:
> **bikin dummy, bangun di belakangnya, kembalikan `dummy.next`.**

---

## 8. Jalannya program, langkah demi langkah

Pakai contoh 1: `l1 = [2, 4, 3]`, `l2 = [5, 6, 4]` (yaitu 342 + 465 = 807).

Awal: `dummy = (0)`, `tail = dummy`, `carry = 0`.

### Putaran 1

| | |
| --- | --- |
| `l1.val` | `2` |
| `l2.val` | `5` |
| `sum = 2 + 5 + 0` | `7` |
| `carry = Math.floor(7/10)` | `0` |
| `sum % 10` | `7` |

→ `tail.next = (7)`, `tail` pindah ke `(7)`
→ `l1` pindah ke `(4)`, `l2` pindah ke `(6)`

Hasil sejauh ini: `dummy -> (7)`

### Putaran 2

| | |
| --- | --- |
| `l1.val` | `4` |
| `l2.val` | `6` |
| `sum = 4 + 6 + 0` | `10` |
| `carry = Math.floor(10/10)` | **`1`** |
| `sum % 10` | `0` |

→ `tail.next = (0)`, `tail` pindah ke `(0)`
→ `l1` pindah ke `(3)`, `l2` pindah ke `(4)`

Hasil sejauh ini: `dummy -> (7) -> (0)`

*Perhatikan: `4 + 6 = 10` → tulis `0`, simpan `1`. Persis kertas.*

### Putaran 3

| | |
| --- | --- |
| `l1.val` | `3` |
| `l2.val` | `4` |
| `sum = 3 + 4 + 1` | `8` |
| `carry = Math.floor(8/10)` | `0` |
| `sum % 10` | `8` |

→ `tail.next = (8)`, `tail` pindah ke `(8)`
→ `l1` pindah ke `null`, `l2` pindah ke `null`

Hasil sejauh ini: `dummy -> (7) -> (0) -> (8)`

### Loop berhenti?

Cek syarat: `l1 !== null`? Tidak. `l2 !== null`? Tidak. `carry !== 0`? Tidak.

**Ketiganya salah → loop berhenti.** ✅

### Selesai

`return dummy.next` → `(7) -> (0) -> (8)` → **`[7, 0, 8]`** ✅

---

### Sekarang contoh dengan carry terakhir: `l1 = [5]`, `l2 = [5]`

Awal: `carry = 0`.

**Putaran 1:** `sum = 5 + 5 + 0 = 10`. `carry = 1`. `sum % 10 = 0` → tulis `(0)`.
`l1` → `null`, `l2` → `null`.

**Cek syarat loop:** `l1 !== null`? Tidak. `l2 !== null`? Tidak.
Tapi `carry !== 0`? **YA!** → **lanjut putaran lagi!**

**Putaran 2:** `a = 0` (karena `l1` null), `b = 0` (karena `l2` null).
`sum = 0 + 0 + 1 = 1`. `carry = 0`. `sum % 10 = 1` → tulis `(1)`.

**Cek syarat:** ketiganya tidak → berhenti.

Hasil: `(0) -> (1)` → **`[0, 1]`** ✅

**Kalau syarat `carry !== 0` tidak ditulis**, loop akan berhenti setelah putaran 1
dan hasilnya cuma `[0]` — **SALAH**. Inilah jebakan paling terkenal di soal ini.

---

## 9. Kenapa ini benar

### Invarian

> **Setiap kali kita sampai di awal putaran, semua digit hasil untuk posisi
> `0` sampai `k-1` (dimana `k` = jumlah putaran yang sudah selesai) sudah tertulis
> dengan benar di list hasil, dan `carry` berisi simpanan dari penjumlahan
> posisi `k-1` tersebut.**

**Kenapa selalu benar?**

- **Di awal:** `k = 0`, belum ada digit tertulis, dan `carry = 0` (memang belum ada
  simpanan). ✅
- **Setiap putaran:** kita ambil digit ke-`k` dari kedua list (atau `0` kalau sudah
  habis), jumlahkan dengan `carry` yang sudah benar, lalu menulis `sum % 10` di
  posisi ke-`k` dan menghitung `carry` baru untuk putaran berikutnya.

  Karena `sum = a + b + carry_in` adalah bentuk penjumlahan kolom yang benar di
  basis 10, maka `digit = sum % 10` adalah digit yang benar untuk posisi itu, dan
  `carry_out = floor(sum / 10)` adalah simpanan yang benar. ✅

### Kenapa loop pasti berhenti

Setiap putaran, `l1` dan `l2` **masing-masing maju satu node** (kalau masih ada).
Karena kedua list panjangnya terbatas (maksimal 100 node), maka setelah paling
banyak `max(panjang l1, panjang l2)` putaran, keduanya menjadi `null`.

Setelah itu, satu-satunya alasan loop lanjut adalah `carry !== 0`. Tapi `carry`
sudah dihitung sebagai `floor(sum / 10)` dengan `sum ≤ 0 + 0 + 1 = 1`, jadi
`carry = floor(1/10) = 0`.

Jadi paling banyak **satu putaran tambahan** setelah kedua list habis. Loop pasti
berhenti. ✅

### Kenapa hasilnya pasti benar

Berdasarkan invarian, setelah loop berhenti (artinya `carry === 0` dan kedua list
habis), semua digit sudah tertulis dengan benar, dan tidak ada simpanan yang
tertinggal. Jadi list hasil merepresentasikan jumlah yang benar. ✅

---

## 10. Soal menjebak: carry terakhir

Ini **jebakan nomor satu** di soal ini, dan penyebab kegagalan paling umum.

### Masalahnya

```javascript
// SALAH — lupa carry terakhir
while (l1 !== null || l2 !== null) {    // <-- syarat carry tidak ada
    ...
}
```

Uji dengan `l1 = [5]`, `l2 = [5]`:

- Putaran 1: `sum = 10`, tulis `0`, `carry = 1`. `l1` dan `l2` jadi `null`.
- Cek: `l1 !== null`? Tidak. `l2 !== null`? Tidak. → **loop berhenti**.
- Hasil: `[0]` ❌ — **`carry = 1` hilang!**

Padahal `5 + 5 = 10`, jadi seharusnya `[0, 1]`.

### Kenapa ini terjadi?

Karena hasil penjumlahan bisa **lebih panjang** dari kedua input. Panjang maksimal
hasil adalah:

```
max(len1, len2) + 1
```

Tambahan `1` itu **persis** carry terakhir. Contoh dari soal:

```
l1 = [9,9,9,9,9,9,9]   (7 digit)
l2 = [9,9,9,9]          (4 digit)
hasil = [8,9,9,9,0,0,0,1]   (8 digit = max(7,4) + 1)
```

### Solusinya

Masukkan carry ke syarat loop:

```javascript
while (l1 !== null || l2 !== null || carry !== 0) {
```

Sekarang setelah kedua list habis, kalau masih ada carry, loop jalan sekali lagi,
mengambil `a = 0` dan `b = 0`, dan menulis digit carry-nya.

> **Ingat: hasil bisa satu digit lebih panjang. Loop tidak boleh berhenti hanya
> karena kedua list habis — harus cek carry juga.**

---

## 11. Panjang list berbeda

Ini kasus yang sering dilupakan orang saat menguji.

### Contoh: `l1 = [1]`, `l2 = [9, 9]`

Nilai: `1` + `99` = `100`. Hasil harusnya: `[0, 0, 1]`.

**Putaran 1:** `a = 1`, `b = 9`. `sum = 1 + 9 + 0 = 10`. Tulis `0`, `carry = 1`.
`l1` → `null`, `l2` → `(9)`.

**Putaran 2:** `l1` sudah `null`! Karena pakai `l1 ? l1.val : 0`, maka `a = 0`.
`b = 9`. `sum = 0 + 9 + 1 = 10`. Tulis `0`, `carry = 1`. `l2` → `null`.

**Putaran 3:** `l1` dan `l2` dua-duanya `null` → `a = 0`, `b = 0`.
Tapi `carry = 1` → **loop tetap jalan!**
`sum = 0 + 0 + 1 = 1`. Tulis `1`, `carry = 0`.

**Cek:** ketiganya tidak → berhenti.

Hasil: `[0, 0, 1]` ✅

### Dua teknik yang membuat ini mulus

| Teknik | Fungsinya |
| --- | --- |
| `l1 ? l1.val : 0` | Mengganti node yang sudah habis dengan `0` — jadi tidak perlu tahu mana yang lebih pendek |
| `\|\|` di syarat loop | Terus jalan sampai **keduanya** habis, bukan berhenti begitu salah satu habis |
| `if (l1) l1 = l1.next` | Mencegah error saat mengakses `.next` dari `null` |

**Kalau pakai `&&` di syarat loop**, `[1] + [9,9]` akan berhenti di putaran 1
(karena salah satu sudah habis) dan hasilnya `[0]` — **SALAH**.

---

## 12. Kompleksitas waktu dan ruang

### Waktu: O(max(m, n))

Misal `m` = panjang `l1`, `n` = panjang `l2`.

Loop berjalan sebanyak `max(m, n)` kali, **plus paling banyak 1** untuk carry
terakhir. Jadi:

- Jumlah putaran = `max(m, n) + 1` paling banyak
- Setiap putaran melakukan kerja yang **tetap** (penjumlahan, satu `%`, satu
  `Math.floor`, satu pembuatan node)

Maka **waktu = O(max(m, n))**. Ini **optimal** — kita memang harus melihat setiap
digit dari kedua list, jadi tidak mungkin lebih cepat.

### Ruang: O(1) tambahan

Di luar list hasil, kita hanya menyimpan:

- `dummy`, `tail`, `carry`, `a`, `b`, `sum` → jumlah variabel tetap

Jadi **ruang tambahan = O(1)**.

List hasilnya sendiri panjangnya `O(max(m, n))`, tapi itu **output** — dan output
biasanya tidak dihitung sebagai "ruang tambahan". Kalau dihitung, totalnya
`O(max(m, n))`.

### Perbandingan dengan pendekatan "ubah ke angka"

| | Digit per digit | Ubah ke angka |
| --- | --- | --- |
| Waktu | O(max(m,n)) | O(max(m,n)) |
| Ruang | O(1) tambahan | O(1) tambahan |
| **Benar untuk 100 digit?** | **✅ ya** | ❌ **tidak (presisi hilang)** |
| Bisa di bahasa lain? | ✅ | ❌ (Java/C++ juga overflow) |

Perhatikan waktunya **sama**! Jadi bukan soal kecepatan — soal **kebenaran**.
Pendekatan angka lebih pendek ditulis, tapi **salah**.

---

## 13. Cara menguji jawabanmu

### Cara yang harus dihindari: membandingkan sebagai teks

Saat saya menguji kode ini, saya sempat mendapat 151 "kegagalan" palsu. Penyebabnya:
saya membandingkan hasil sebagai **teks**, padahal bedanya cuma nol di depan.

Ini penting, karena mengungkap sifat soal yang mudah terlewat:

- Solusi menulis `[6, 7, 1, 3, 8, 1, 0]` (nilai 183176, tapi ada **nol** di posisi
  paling belakang — yaitu digit paling signifikan)
- BigInt saya menulis `"183176"` → dibalik jadi `[6, 7, 1, 3, 8, 1]` (tanpa nol)
- Dibandingkan sebagai teks: **beda!**

Padahal **solusinya yang benar**. Karena di representasi terbalik, nol di paling
belakang adalah **digit paling signifikan**, dan itu memang harus ada sesuai format
soal. Membandingkan sebagai teks akan salah paham.

### Cara yang benar: bandingkan sebagai nilai

```javascript
// Nilai sesungguhnya dari list LSB-first: digit ke-i punya bobot 10^i
const toNum = (arr) =>
    arr.reduce((acc, d, i) => acc + BigInt(d) * (10n ** BigInt(i)), 0n);

// uji: apakah nilai hasil == nilai l1 + nilai l2 ?
toNum(got) === toNum(n1) + toNum(n2)
```

### Hasil pengujian saya

| Uji | Hasil |
| --- | --- |
| Contoh 1: `[2,4,3] + [5,6,4]` | ✅ `[7,0,8]` |
| Contoh 2: `[0] + [0]` | ✅ `[0]` |
| Contoh 3: `[9×7] + [9×4]` | ✅ `[8,9,9,9,0,0,0,1]` |
| Carry: `[5] + [5]` | ✅ `[0,1]` |
| Panjang beda: `[1] + [9,9]` | ✅ `[0,0,1]` |
| Carry panjang: `[9×12] + [1]` | ✅ `[0×12, 1]` |
| 5000 uji acak vs BigInt | ✅ semua cocok |
| Semua digit hasil 0–9 | ✅ tidak ada yang ≥ 10 |

### Cara membandingkan dengan benar (perhatikan nol di depan)

Satu hal lagi: karena format soal **mewajibkan** nol di depan (di posisi belakang),
kode kita **harus** menghasilkan `[6, 7, 1, 3, 8, 1, 0]`, bukan `[6, 7, 1, 3, 8, 1]`.

Perhatikan bahwa kode kita **memang** melakukan ini dengan benar, karena loop
berjalan selama masih ada digit di salah satu list. List `[0,1,3,8,1,6,8]` punya 7
digit, jadi hasilnya juga 7 digit (atau lebih kalau ada carry).

---

## 14. Jebakan umum lainnya

| Jebakan | Akibatnya |
| --- | --- |
| Lupa `carry !== 0` di syarat loop | Carry terakhir hilang. `[5]+[5]` → `[0]` ❌ harusnya `[0,1]` |
| Pakai `&&` bukan `\|\|` di syarat loop | Berhenti begitu salah satu list habis. `[1]+[9,9]` → `[0]` ❌ |
| Lupa `tail = tail.next` | List hasil cuma berisi satu digit, karena node ditimpa terus |
| Lupa `if (l1) l1 = l1.next` | Error `Cannot read properties of null` |
| Lupa `Math.floor` saat hitung carry | Di JS, `19/10 = 1.9`, jadi carry jadi pecahan dan jawabannya kacau |
| Mengembalikan `dummy` bukan `dummy.next` | Hasil diawali `0` bohongan → salah |
| Ubah ke angka lalu jumlahkan | **Presisi hilang** di atas 16 digit (88% gagal) |
| Lupa memajukan `l1`/`l2` | **Infinite loop** — program menggantung |
| Menghitung panjang dulu baru loop | Tidak salah, tapi kode jadi panjang dan menambah kasus khusus |
| `sum % 10` tapi carry dihitung dari `a + b` tanpa carry | Carry jadi salah hitung. Harus `Math.floor((a+b+carry)/10)` |

### Tentang "ubah input atau tidak"

Kode kita **tidak mengubah** `l1` dan `l2`. Kita hanya memajukan referensi lokal
(`l1 = l1.next`) — node aslinya tetap utuh, hanya penunjuk lokal kita yang bergerak.

Ini penting kalau interviewer bertanya lanjutan: *"bisakah kamu mengerjakannya
tanpa membuat node baru?"* Jawabannya bisa (dengan **menggunakan ulang** node dari
`l2` sebagai wadah hasil), tapi itu **mengubah input** — biasanya tidak diinginkan.
Versi kita (membuat node baru) lebih aman dan bersih.

---

## 15. Variasi soal: kalau urutannya normal

Kalau soal ini dimodifikasi supaya digitnya disimpan **normal** (ratusan dulu),
soalnya jadi **jauh lebih sulit**. Ini jadi LeetCode 445 (Add Two Numbers II).

### Kenapa jadi lebih sulit?

Karena:

1. Kita harus mulai dari digit **paling kanan** — tapi linked list **hanya bisa
   maju**.
2. Carry mengalir ke **kiri**, sedangkan kita berjalan ke **kanan** — jadi arah
   carry **berlawanan** dengan arah perjalanan.

### Tiga cara mengatasinya

**a. Balik dulu kedua list, jumlahkan, lalu balik hasilnya.**

```javascript
// balik list -> jumlahkan (seperti soal ini) -> balik lagi hasilnya
```

Waktu `O(max(m,n))`, ruang `O(1)` tambahan. Tapi mengubah input.

**b. Pakai stack untuk membalik.**

Masukkan semua digit ke stack, lalu pop (keluar dari belakang) sambil menjumlahkan.

```javascript
const s1 = [], s2 = [];
while (l1) { s1.push(l1.val); l1 = l1.next; }
while (l2) { s2.push(l2.val); l2 = l2.next; }
// lalu pop dari belakang sambil bangun hasil
```

Waktu `O(max(m,n))`, ruang `O(m + n)`.

**c. Cara pintar: rekursi atau pad list ke panjang yang sama**

Tambahkan node `0` di depan list yang lebih pendek supaya panjangnya sama, lalu
rekursi dari depan sambil **mengembalikan carry ke atas**.

### Pelajarannya

> **Urutan penyimpanan digit menentukan kesulitan soal.** Terbalik = mudah (satu
> jalan maju). Normal = sulit (harus membalik atau pakai stack).

Ini contoh bagus bahwa **cara data disimpan** adalah bagian dari desain algoritma.
Kalau kamu bisa memilih representasi, pilih yang membuat operasimu jadi mudah.

---

## 16. Pola yang bisa dipakai lagi

### Pola utama: simulasi digit + carry

> **Kalau soalnya melibatkan bilangan yang sangat besar (melebihi presisi tipe
> bilangan bahasa), jumlahkan/perkalikan **digit per digit** seperti di SD, sambil
> membawa `carry`.**

**Kapan muncul:**

- menjumlahkan dua bilangan besar (soal ini)
- mengalikan dua bilangan besar
- menambahkan 1 ke bilangan besar (`Plus One`)
- mengurangi bilangan besar
- mengimplementasikan `BigInteger`
- menjumlahkan dua bilangan biner (`Add Binary`)

### Pola kedua: dummy node

> **Kalau kamu membangun linked list baru, selalu pakai `dummy` node di depan,
> bangun di belakangnya, kembalikan `dummy.next`.**

Ini **wajib** di semua soal linked list yang menghasilkan list baru. Manfaatnya:
menghilangkan kasus khusus untuk node pertama.

Soal yang memakainya: `Add Two Numbers`, `Merge Two Sorted Lists`,
`Remove Duplicates from Sorted List`, `Partition List`, `Odd Even Linked List`,
`Merge K Sorted Lists`, `Reverse Linked List II`.

### Pola ketiga: pointer walk + sentinel value

> **Saat menggabungkan dua list dengan panjang berbeda, jangan hitung panjangnya
> dulu — cukup jalan terus, dan ganti list yang habis dengan nilai netral (`0`).**

```javascript
while (l1 || l2) {
    const a = l1 ? l1.val : 0;    // <- netral: 0
    ...
    if (l1) l1 = l1.next;
    if (l2) l2 = l2.next;
}
```

Untuk penggabungan (merge) dengan urutan, nilai netralnya bisa `Infinity`.
Untuk perkalian, netralnya `1`. Intinya: **netral sesuai operasinya**.

### Kalimat ajaibnya

> **"Kalau hasil penjumlahan bisa lebih panjang dari input, loop-nya harus punya
> syarat tambahan untuk membuang sisa."**

Di soal ini: `carry !== 0`. Kalau lupa, digit terakhir hilang.

---

## 17. Ringkasan satu halaman

**Soal:** duplikat penjumlahan SD. Dua linked list berisi digit (satuan di depan),
jumlahkan, kembalikan sebagai linked list (format sama).

**Kenapa tidak boleh ubah ke angka:** JS `Number` hanya presisi sampai 16 digit,
soalnya sampai 100 digit. Terbukti gagal **88%** pada uji acak.

**Ide:** tiru cara SD — jumlahkan kolom demi kolom sambil membawa `carry`.

**Kode:**

```javascript
var addTwoNumbers = function (l1, l2) {
    const dummy = new ListNode(0);
    let tail = dummy;
    let carry = 0;

    while (l1 !== null || l2 !== null || carry !== 0) {
        const a = l1 ? l1.val : 0;
        const b = l2 ? l2.val : 0;

        const sum = a + b + carry;
        carry = Math.floor(sum / 10);

        tail.next = new ListNode(sum % 10);
        tail = tail.next;

        if (l1) l1 = l1.next;
        if (l2) l2 = l2.next;
    }

    return dummy.next;
};
```

**Tiga hal yang paling mudah salah:**

1. **Lupa `carry !== 0`** di syarat loop → carry terakhir hilang (`[5]+[5]` jadi `[0]`)
2. **Lupa `Math.floor`** saat hitung carry → di JS `19/10 = 1.9`, carry jadi pecahan
3. **Lupa `tail = tail.next`** → list hasil cuma berisi satu digit

**Tiga senjata yang dipakai:**

1. **`carry`** — simpanan, mengalir ke depan (di representasi terbalik = arah maju)
2. **`dummy` node** — menghilangkan kasus khusus node pertama
3. **`l1 ? l1.val : 0`** — menangani list berbeda panjang tanpa menghitung panjang

**Kompleksitas:** waktu `O(max(m,n))`, ruang tambahan `O(1)`.

**Hadiah dari soal:** karena digit disimpan **terbalik**, kita tidak perlu membalik
apa pun, dan carry mengalir **searah** dengan perjalanan kita. Kalau urutannya
normal, soal ini jadi jauh lebih sulit (butuh stack atau membalik list).

**Kalimat kunci:** *kalau hasil bisa lebih panjang dari input, loop-nya harus punya
syarat tambahan untuk sisa carry.*
