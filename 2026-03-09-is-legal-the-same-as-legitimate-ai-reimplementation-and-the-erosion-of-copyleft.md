# Apakah Legal Sama dengan Legitim: Reimplementasi AI dan Pengikisan Copyleft

- **Tanggal:** 9 Maret 2026
- **Penulis:** Hong Minhee
- **Konteks sumber:** _Hong Minhee on Things_ (English / 日本語 / 朝鮮語 (國漢文) / 한국어 (한글))
- **Catatan arsip:** Salinan ini dinormalisasi dari teks yang diberikan pengguna untuk konteks riset/arsip repositori ini. Tautan navigasi situs/bahasa footer diubah menjadi metadata.

Minggu lalu, Dan Blanchard, pemelihara chardet — sebuah library Python untuk mendeteksi encoding teks yang digunakan oleh sekitar 130 juta proyek per bulan — merilis versi baru. Versi 7.0 ini 48 kali lebih cepat dari pendahulunya, mendukung multi-core, dan dirancang ulang dari awal. Claude dari Anthropic tercantum sebagai kontributor. Lisensinya berubah dari LGPL menjadi MIT.

Blanchard menyatakan bahwa ia tidak pernah melihat langsung source code yang sudah ada. Ia hanya memberikan API dan test suite kepada Claude, lalu memintanya untuk mengimplementasikan ulang library tersebut dari nol. Kode yang dihasilkan memiliki kemiripan kurang dari 1,3% dengan versi sebelumnya, diukur menggunakan JPlag. Kesimpulannya: ini adalah karya baru yang independen, dan ia tidak berkewajiban untuk meneruskan LGPL. Mark Pilgrim, penulis asli library ini, membuka GitHub issue untuk mengajukan keberatan. LGPL mengharuskan modifikasi didistribusikan di bawah lisensi yang sama, dan reimplementasi yang dibuat dengan keterpaparan besar pada codebase asli tidak bisa, menurut Pilgrim, dianggap sebagai upaya clean-room.

Perselisihan ini mendapat respons dari dua tokoh terkemuka di dunia open source. Armin Ronacher, pencipta Flask, menyambut baik pergantian lisensi tersebut. Salvatore Sanfilippo (antirez), pencipta Redis, menerbitkan pembelaan yang lebih luas terhadap reimplementasi berbasis AI, berlandaskan hukum hak cipta dan sejarah proyek GNU. Keduanya menyimpulkan, melalui jalur yang berbeda, bahwa apa yang dilakukan Blanchard adalah sah. Saya menghormati kedua penulis ini, dan saya kira keduanya salah — atau lebih tepatnya, keduanya menghindari pertanyaan yang sebenarnya penting.

Pertanyaan itu adalah: apakah legal sama dengan legitim? Tidak ada satu pun tulisan yang menjawabnya. Keduanya bergerak dari "ini diizinkan secara hukum" ke "ini tidak masalah," tanpa berhenti di celah antara dua klaim tersebut. Hukum menetapkan batas minimum; melewatinya tidak berarti tindakan tersebut sudah benar. Celah itulah tempat esai ini dimulai.

## Analogi yang Menunjuk Arah yang Salah

Antirez membangun argumennya berdasarkan sejarah. Ketika proyek GNU mengimplementasikan ulang userspace UNIX, itu sah secara hukum. Begitu pula Linux. Hukum hak cipta melarang penyalinan "ekspresi yang dilindungi" — kode aktual, strukturnya, mekanisme spesifiknya — tetapi tidak melindungi ide atau perilaku. Reimplementasi berbantuan AI menempati posisi hukum yang sama. Maka, itu sah secara hukum.

Analisis hukumnya sebagian besar benar, dan saya tidak memperdebatkannya. Masalahnya terletak pada langkah berikutnya: antirez menyajikan kesimpulan hukum seolah-olah itu juga merupakan kesimpulan sosial, dan menggunakan analogi historis yang, jika dikaji lebih teliti, justru menentang posisinya sendiri.

Ketika GNU mengimplementasikan ulang userspace UNIX, arahnya adalah dari perangkat lunak proprietary ke bebas. Stallman menggunakan batas-batas hukum hak cipta untuk mengubah perangkat lunak proprietary menjadi perangkat lunak bebas. Kekuatan etis dari proyek itu tidak berasal dari keabsahan hukumnya — melainkan dari arah gerakannya, dari fakta bahwa ia memperluas ruang bersama (commons). Itulah mengapa orang-orang mendukungnya.

Arah dalam kasus chardet justru sebaliknya. Perangkat lunak yang dilindungi oleh lisensi copyleft — yang menjamin hak pengguna untuk mempelajari, memodifikasi, dan mendistribusikan ulang karya turunan di bawah ketentuan yang sama — telah diimplementasikan ulang di bawah lisensi permissive yang tidak memberikan jaminan semacam itu. Ini bukan reimplementasi yang memperluas ruang bersama. Ini adalah reimplementasi yang menghapus pagar yang melindungi ruang bersama. Karya turunan yang dibangun di atas chardet 7.0 tidak berkewajiban untuk berbagi source code-nya. Kewajiban itu, yang berlaku untuk library yang diunduh 130 juta kali per bulan, kini telah hilang.

Antirez tidak membahas perbedaan arah ini. Ia menggunakan preseden GNU, tetapi preseden itu justru merupakan contoh yang menentang kesimpulannya, bukan yang mendukungnya.

## Apakah GPL Bertentangan dengan Semangat Berbagi?

Argumen Ronacher berbeda. Ia mengungkapkan sejak awal bahwa ia memiliki kepentingan dalam hasil ini: "Saya pribadi punya kepentingan di sini karena saya juga sudah lama ingin agar chardet berada di bawah lisensi non-GPL. Jadi anggap saya sangat bias dalam hal ini." Ia kemudian menulis bahwa ia menganggap "GPL bertentangan dengan semangat itu dengan membatasi apa yang bisa dilakukan dengannya" — semangat yang dimaksud adalah bahwa masyarakat lebih baik ketika kita berbagi.

Klaim ini didasarkan pada kesalahpahaman mendasar tentang apa yang dilakukan GPL.

Mulai dari apa yang sebenarnya dilarang GPL. GPL tidak melarang menyimpan source code secara privat. GPL tidak membatasi modifikasi perangkat lunak GPL secara privat untuk digunakan sendiri. Ketentuan GPL hanya berlaku saat distribusi. Jika Anda mendistribusikan kode yang dimodifikasi, atau menawarkannya sebagai layanan jaringan, Anda harus membuat source code tersedia di bawah ketentuan yang sama. Ini bukan pembatasan berbagi. Ini adalah syarat yang ditempatkan pada berbagi: jika Anda berbagi, Anda harus berbagi secara setimpal.

Persyaratan bahwa perbaikan dikembalikan ke ruang bersama bukanlah mekanisme yang menekan berbagi. Ini adalah mekanisme yang membuat berbagi bersifat rekursif dan saling memperkuat. Klaim bahwa mewajibkan kontribusi dari pengguna ruang bersama melemahkan budaya berbagi tidak bisa diterima secara logis.

Kontras dengan lisensi MIT memperjelas poin ini. Di bawah MIT, siapa pun dapat mengambil kode, memperbaikinya, dan menutupnya menjadi produk proprietary. Anda bisa menerima dari ruang bersama tanpa memberikan kembali. Jika Ronacher menyebut struktur ini "lebih ramah berbagi," ia menggunakan konsep berbagi dengan arah tertentu yang sudah tertanam: berbagi mengalir ke arah siapa pun yang memiliki lebih banyak modal dan lebih banyak insinyur untuk memanfaatkannya.

Rekam sejarah mendukung hal ini. Pada tahun 1990-an, perusahaan-perusahaan secara rutin menyerap kode GPL ke dalam produk proprietary — bukan karena mereka telah memilih lisensi permissive, melainkan karena penegakan copyleft lemah. Penguatan mekanisme copyleft menutup celah itu. Bagi pengembang individu dan proyek kecil yang tidak memiliki sumber daya untuk bersaing kecuali melalui timbal balik, copyleft adalah yang membuat pertukaran menjadi kira-kira adil.

Pencipta Flask mengetahui perbedaan ini. Jika ia tetap mengabaikannya, argumennya bukan naif — melainkan memang menguntungkan dirinya.

## Contoh yang Membantah Diri Sendiri

Momen paling menarik dalam tulisan Ronacher bukan pada argumennya, melainkan pada sebuah detail yang ia sebutkan sambil lalu: Vercel mengimplementasikan ulang GNU Bash menggunakan AI dan mempublikasikannya, lalu bereaksi marah ketika Cloudflare mengimplementasikan ulang Next.js dengan cara yang sama.

Ronacher mencatat ini sebagai sebuah ironi lalu melanjutkan. Namun ironi itu lebih dalam dari yang ia akui. Next.js berlisensi MIT. vinext dari Cloudflare tidak melanggar lisensi apa pun — itu persis seperti yang disebut Ronacher sebagai kontribusi untuk budaya keterbukaan, diterapkan pada codebase berlisensi permissive. Reaksi Vercel tidak ada hubungannya dengan pelanggaran lisensi; itu murni bersifat kompetitif dan teritorial. Posisi implisitnya adalah: mengimplementasikan ulang perangkat lunak GPL menjadi MIT adalah kemenangan untuk berbagi, tetapi ketika perangkat lunak MIT kita sendiri diimplementasikan ulang oleh pesaing, itu layak untuk marah. Inilah wujud nyata dari klaim bahwa lisensi permissive "lebih ramah berbagi" daripada copyleft. Semangat berbagi, ternyata, hanya mengalir dalam satu arah: keluar dari diri sendiri.

Ronacher mencatat kontradiksi ini dan tidak berhenti. "Perkembangan ini sejalan dengan pandangan dunia saya," tulisnya. Ketika Anda menyajikan bukti yang memotong posisi Anda sendiri, mengakuinya, lalu tetap melanjutkan ke kesimpulan semula tanpa perubahan — itu adalah sinyal bahwa kesimpulan mendahului argumen.

## Legalitas dan Legitimasi Sosial adalah Ranah yang Berbeda

Kembali ke pertanyaan di awal. Apakah legal sama dengan legitim?

Antirez menutup analisis hukumnya yang cermat seolah-olah itu sudah menyelesaikan masalah. Ronacher mengakui bahwa "ada pertanyaan moral yang jelas di sini, tetapi bukan itu yang saya minati." Kedua tulisan memperlakukan keabsahan hukum sebagai pengganti legitimasi sosial. Padahal hukum hanya mengatakan tindakan mana yang tidak akan dicegahnya — ia tidak mensertifikasi tindakan tersebut sebagai benar. Minimisasi pajak agresif yang tidak pernah menyentuh batas ilegal mungkin tetap dianggap antisosial secara luas. Perusahaan farmasi yang secara legal memperoleh paten atas obat generik lama lalu menaikkan harganya seratus kali lipat telah melakukan sesuatu yang legal, tetapi itu tidak menjadikannya benar. Legalitas adalah syarat yang diperlukan; bukan syarat yang cukup.

Dalam kasus chardet, perbedaannya bahkan lebih tajam. Yang dilindungi LGPL bukan hanya jerih payah Blanchard seorang. Itu adalah kesepakatan sosial yang disetujui oleh semua orang yang berkontribusi pada library tersebut selama dua belas tahun. Ketentuan kesepakatan itu adalah: jika Anda mengambil ini dan membangun di atasnya, Anda berbagi kembali di bawah ketentuan yang sama. Kesepakatan ini berfungsi sebagai instrumen hukum, ya, tetapi juga merupakan fondasi kepercayaan yang membuat kontribusi menjadi rasional. Fakta bahwa reimplementasi mungkin memenuhi syarat secara hukum sebagai karya baru, dan fakta bahwa itu mengkhianati kepercayaan para kontributor asli, adalah pertanyaan yang terpisah. Jika suatu pengadilan akhirnya memutuskan mendukung Blanchard, keputusan itu akan memberi tahu kita apa yang diizinkan hukum. Itu tidak akan memberi tahu kita bahwa tindakan itu sudah benar.

Zoë Kooyman, direktur eksekutif FSF, mengatakannya dengan lugas: "Menolak memberikan hak yang Anda sendiri terima sebagai pengguna kepada orang lain adalah sangat antisosial, tidak peduli metode apa yang Anda gunakan."

## Perspektif Siapa yang Menjadi Acuan?

Membaca debat ini, saya terus kembali pada pertanyaan tentang posisi. Dari mana kedua penulis ini melihat situasi ini?

Antirez menciptakan Redis. Ronacher menciptakan Flask. Keduanya adalah tokoh di pusat ekosistem open source, dengan audiens besar dan reputasi yang sudah mapan. Bagi mereka, biaya reimplementasi AI yang semakin rendah berarti sesuatu yang spesifik: lebih mudah untuk mengimplementasikan ulang hal-hal yang mereka inginkan dalam bentuk berbeda. Ronacher secara eksplisit menyebut bahwa ia telah mulai mengimplementasikan ulang GNU Readline justru karena ketentuan copyleft-nya.

Bagi orang-orang yang telah bertahun-tahun berkontribusi pada library seperti chardet, pergeseran biaya yang sama berarti sesuatu yang sangat berbeda: perlindungan copyleft di sekitar kontribusi mereka dapat dihapus. Kedua penulis berbicara dari posisi pertama kepada orang-orang di posisi kedua, memberitahu mereka bahwa ini selalu sah secara hukum, bahwa preseden historis mendukungnya, dan bahwa respons yang tepat adalah adaptasi.

Ketika asimetri posisional semacam ini diabaikan, dan argumen disajikan sebagai analisis universal, yang Anda dapatkan bukan analisis melainkan rasionalisasi. Kedua penulis tiba pada kesimpulan yang sesuai persis dengan kepentingan mereka sendiri. Pembaca sebaiknya mengingat fakta itu.

## Ke Mana Pertarungan Ini Mengarah

Bruce Perens, yang menulis Definisi Open Source asli, mengatakan kepada The Register: "Seluruh ekonomi pengembangan perangkat lunak sudah mati, pergi, selesai, tamat!" Ia mengatakannya sebagai peringatan. Antirez, dari penilaian situasi yang serupa, menarik kesimpulan: beradaptasi. Ronacher mengatakan ia menemukan arahnya menarik.

Tidak ada satu pun dari ketiga respons itu yang menjawab pertanyaan sentral. Ketika copyleft menjadi lebih mudah secara teknis untuk disiasati, apakah itu membuatnya kurang perlu, atau lebih?

Saya pikir lebih. Yang dilindungi GPL bukan kelangkaan kode melainkan kebebasan pengguna. Fakta bahwa memproduksi kode menjadi lebih murah tidak membuatnya dapat diterima untuk menggunakan kode itu sebagai kendaraan untuk mengikis kebebasan. Justru seiring hilangnya gesekan reimplementasi, hilang pula gesekan untuk menggunduli copyleft dari apa pun yang terekspos. Pengikisan kapasitas penegakan adalah masalah hukum. Itu tidak menyentuh penilaian normatif yang mendasarinya.

Penilaian itu adalah: mereka yang mengambil dari ruang bersama berhutang sesuatu kepada ruang bersama. Prinsip ini tidak berubah tergantung pada apakah reimplementasi membutuhkan lima tahun atau lima hari. Tidak ada putusan pengadilan tentang kode yang dihasilkan AI yang akan mengubah bobot sosialnya.

Di sinilah hukum dan norma komunitas berbeda. Hukum dibuat secara perlahan, setelah kenyataan, mencerminkan tatanan kekuasaan yang sudah ada. Norma-norma yang dibangun komunitas open source selama beberapa dekade tidak menunggu persetujuan pengadilan. Orang-orang memilih GPL ketika hukum tidak menawarkan jaminan penegakannya, karena itu mengekspresikan nilai-nilai komunitas yang ingin mereka miliki. Nilai-nilai itu tidak kedaluwarsa ketika hukum berubah.

Dalam tulisan sebelumnya, saya mengajukan copyleft pelatihan (TGPL) sebagai langkah berikutnya dalam jalur pengembangan ini. Situasi chardet menunjukkan bahwa argumennya harus lebih jauh: menuju copyleft spesifikasi yang mencakup lapisan di bawah source code. Jika source code kini dapat dihasilkan dari sebuah spesifikasi, maka spesifikasilah tempat konten intelektual esensial dari proyek GPL berada. Klaim Blanchard sendiri — bahwa ia hanya bekerja dari test suite dan API tanpa membaca source — adalah, secara paradoks, argumen untuk melindungi test suite dan spesifikasi API tersebut di bawah ketentuan copyleft.

Sejarah GPL adalah sejarah alat lisensi yang berkembang sebagai respons terhadap bentuk-bentuk eksploitasi baru: GPLv2 ke GPLv3, lalu AGPL. Yang mendorong setiap evolusi bukan keputusan pengadilan melainkan komunitas yang mencapai penilaian nilai terlebih dahulu, kemudian mencari instrumen hukum untuk mengekspresikannya. Urutan yang sama tersedia sekarang. Apa pun yang akhirnya diputuskan pengadilan tentang reimplementasi AI, pertanyaan yang perlu kita jawab terlebih dahulu bukan pertanyaan hukum. Itu adalah pertanyaan sosial. Apakah mereka yang mengambil dari ruang bersama berhutang sesuatu kembali? Saya pikir mereka berhutang. Penilaian itu tidak membutuhkan vonis.

Yang membuat tulisan antirez dan Ronacher layak dibaca bukan karena keduanya benar. Melainkan karena keduanya membuat terlihat, dengan kejelasan yang tidak biasa, apa yang mereka pilih untuk tidak lihat. Ketika legalitas digunakan sebagai pengganti penilaian nilai, pertanyaan yang sebenarnya penting terkubur dalam catatan kaki dari sebuah hukum yang sudah dilampaui jauh sebelumnya.
