# Project-Data-Analysis-for-B2B-Retail-Customer-Analytics-Report
## DQLab Project SQL
<hr>

### Tugas dan Langkah
xyz.com adalah perusahan rintisan B2B yang menjual berbagai produk tidak langsung kepada end user tetapi ke bisnis/perusahaan lainnya. Sebagai data-driven company, maka setiap pengambilan keputusan di xyz.com selalu berdasarkan data. Setiap quarter xyz.com akan mengadakan townhall dimana seluruh atau perwakilan divisi akan berkumpul untuk me-review performance perusahaan selama quarter terakhir.

Sebagai seorang data analyst, kamu dimintai untuk menyediakan data dan analisa mengenai kondisi perusahaan bulan terakhir untuk dipresentasikan di townhall tersebut. (Asumsikan tahun yang sedang berjalan adalah tahun 2004).

Adapun hal yang akan direview adalah :

1. Bagaimana pertumbuhan penjualan saat ini?
2. Apakah jumlah customers xyz.com semakin bertambah ?
3. Dan seberapa banyak customers tersebut yang sudah melakukan transaksi?
4. Category produk apa saja yang paling banyak dibeli oleh customers?
5. Seberapa banyak customers yang tetap aktif bertransaksi?
<br>

### Tabel yang Digunakan

<img src="https://miro.medium.com/max/1400/1*Y6kFpexXaMvW-_e61nX6vg.png">
Tabel yang akan digunakan pada project kali ini adalah sebagai berikut.

1. Tabel orders_1 : Berisi data terkait transaksi penjualan periode quarter 1 (Jan – Mar 2004)
2. Tabel Orders_2 : Berisi data terkait transaksi penjualan periode quarter 2 (Apr – Jun 2004)
3. Tabel Customer : Berisi data profil customer yang mendaftar menjadi customer xyz.com
<br>

### <b>Total Penjualan dan Revenue pada Quarter-1 (Jan, Feb, Mar) dan Quarter-2 (Apr,Mei,Jun)</b>
1. Dari tabel orders_1 lakukan penjumlahan pada kolom quantity dengan fungsi aggregate sum() dan beri nama “total_penjualan”, kalikan kolom quantity dengan kolom priceEach kemudian jumlahkan hasil perkalian kedua kolom tersebut dan beri nama “revenue”
2. Perusahaan hanya ingin menghitung penjualan dari produk yang terkirim saja, jadi kita perlu mem-filter kolom ‘status’ sehingga hanya menampilkan order dengan status “Shipped”.
3. Lakukan Langkah 1 & 2, untuk tabel orders_2.
Notes: Jangan lupa untuk mengakhiri setiap statement dengan titik koma sehingga kedua statement dapat dijalankan bersamaan.(;)

```
SELECT sum(quantity) AS total_penjualan, sum(quantity * priceeach) AS revenue
FROM orders_1
WHERE status = 'Shipped';

SELECT sum(quantity) AS total_penjualan, sum(quantity * priceeach) AS revenue
FROM orders_2
WHERE status = 'Shipped';
```

### <b>Menghitung persentasi keseluruhan penjualan</b>
Kedua tabel orders_1 dan orders_2 masih terpisah, untuk menghitung persentasi keseluruhan penjualan dari kedua tabel tersebut perlu digabungkan :

1. Pilihlah kolom “orderNumber”, “status”, “quantity”, “priceEach” pada tabel orders_1, dan tambahkan kolom baru dengan nama “quarter” dan isi dengan value “1”. Lakukan yang sama dengan tabel orders_2, dan isi dengan value “2”, kemudian gabungkan kedua tabel tersebut.
2. Gunakan statement dari Langkah 1 sebagai subquery dan beri alias “tabel_a”.
3. Dari “tabel_a”, lakukan penjumlahan pada kolom “quantity” dengan fungsi aggregate sum() dan beri nama “total_penjualan”, dan kalikan kolom quantity dengan kolom priceEach kemudian jumlahkan hasil perkalian kedua kolom tersebut dan beri nama “revenue”
4. Filter kolom ‘status’ sehingga hanya menampilkan order dengan status “Shipped”.
5. Kelompokkan total_penjualan berdasarkan kolom “quarter”, dan jangan lupa menambahkan kolom ini pada bagian select.
```
SELECT quarter, SUM(quantity) as total_penjualan, SUM(quantity*priceEach) as revenue
FROM
(SELECT orderNumber, status, quantity, priceEach, '1' as quarter
FROM orders_1
UNION
SELECT orderNumber, status, quantity, priceEach, '2' as quarter
FROM orders_2) AS tabel_a
WHERE status = 'Shipped'
GROUP BY quarter
```

### <b>Apakah jumlah customers xyz.com semakin bertambah?</b>
Penambahan jumlah customers dapat diukur dengan membandingkan total jumlah customers yang registrasi di periode saat ini dengan total jumlah customers yang registrasi diakhir periode sebelumnya.

1. Dari tabel customer, pilihlah kolom customerID, createDate dan tambahkan kolom baru dengan menggunakan fungsi QUARTER(…) untuk mengekstrak nilai quarter dari CreateDate dan beri nama “quarter”
2. Filter kolom “createDate” sehingga hanya menampilkan baris dengan createDate antara 1 Januari 2004 dan 30Juni 2004
3. Gunakan statement Langkah 1 & 2 sebagai subquery dengan alias tabel_b
4. Hitunglah jumlah unik customers à tidak ada duplikasi customers dan beri nama “total_customers”
5. Kelompokkan total_customer berdasarkan kolom “quarter”, dan jangan lupa menambahkan kolom ini pada bagian select.
<img src="https://miro.medium.com/max/424/1*ZFkymnuHixtT9IyykuWxnQ.png">

```
SELECT quarter,COUNT(DISTINCT customerID) AS total_customers
FROM
(SELECT customerID, createDate, QUARTER(createDate) as quarter
FROM customer
WHERE createDate BETWEEN '2004-01-01' AND '2004-06-30') AS tabel_b
GROUP BY quarter
```

### <b>Seberapa banyak customers tersebut yang sudah melakukan transaksi?</b>
Problem ini merupakan kelanjutan dari problem sebelumnya yaitu dari sejumlah customer yang registrasi di periode quarter-1 dan quarter-2, berapa banyak yang sudah melakukan transaksi

1. Dari tabel customer, pilihlah kolom customerID, createDate dan tambahkan kolom baru dengan menggunakan fungsi QUARTER(…) untuk mengekstrak nilai quarter dari CreateDate dan beri nama “quarter”
2. Filter kolom “createDate” sehingga hanya menampilkan baris dengan createDate antara 1 Januari 2004 dan 30 Juni 2004
3. Gunakan statement Langkah A&B sebagai subquery dengan alias tabel_b
4. Dari tabel orders_1 dan orders_2, pilihlah kolom customerID, gunakan DISTINCT untuk menghilangkan duplikasi, kemudian gabungkan dengan kedua tabel tersebut dengan UNION.
5. Filter tabel_b menggunakan operator IN() dan ‘Select statement Langkah d’, sehingga hanya customerID yang pernah bertransaksi (customerID tercatat di tabel orders) yang diperhitungkan.
6. Hitunglah jumlah unik customers (tidak ada duplikasi customers) di statement SELECT dan beri nama “total_customers”
7. Kelompokkan total_customer berdasarkan kolom “quarter”, dan jangan lupa menambahkan kolom ini pada bagian select.
<img src="https://miro.medium.com/max/414/1*pBM5g4zWA3AGzbaRxkp1vg.png">

```
SELECT quarter, COUNT(DISTINCT customerID) AS total_customers
FROM
(SELECT customerID,createDate, QUARTER(createDate) as quarter
FROM customer
WHERE createDate BETWEEN '2004-01-01' AND '2004-06-30') AS tabel_b
WHERE customerID IN (
  SELECT DISTINCT customerID
  FROM orders_1
  UNION
  SELECT DISTINCT customerID
  FROM orders_2
 )
GROUP BY quarter
```

### <b>Category produk apa saja yang paling banyak di-order oleh customers di Quarter-2?</b>
Untuk mengetahui kategori produk yang paling banyak dibeli, maka dapat dilakukan dengan menghitung total order dan jumlah penjualan dari setiap kategori produk.

1. Dari kolom orders_2, pilih productCode, orderNumber, quantity, status
2. Tambahkan kolom baru dengan mengekstrak 3 karakter awal dari productCode yang merupakan ID untuk kategori produk; dan beri nama categoryID
3. Filter kolom “status” sehingga hanya produk dengan status “Shipped” yang diperhitungkan
4. Gunakan statement Langkah 1, 2, dan 3 sebagai subquery dengan alias tabel_c
5. Hitunglah total order dari kolom “orderNumber” dan beri nama “total_order”, dan jumlah penjualan dari kolom “quantity” dan beri nama “total_penjualan”
6. Kelompokkan berdasarkan categoryID, dan jangan lupa menambahkan kolom ini pada bagian select.
7. Urutkan berdasarkan “total_order” dari terbesar ke terkecil
8. Hasilnya sebagai berikut :
<img src="https://miro.medium.com/max/520/1*3u5Tz5qk5IdqFGgTAVr3CQ.png">

```
SELECT *
FROM
(SELECT categoryID, COUNT(DISTINCT orderNumber) as total_order, SUM(quantity) as total_penjualan
FROM
(SELECT productCode, OrderNumber, quantity, status, LEFT(productCode, 3) AS categoryID
FROM orders_2
WHERE status = 'Shipped') AS tabel_c
GROUP BY categoryID) AS tabel_z
ORDER BY total_order DESC
```

### <b>Seberapa banyak customers yang tetap aktif bertransaksi setelah transaksi pertamanya?</b>
Mengetahui seberapa banyak customers yang tetap aktif menunjukkan apakah xyz.com tetap digemari oleh customers untuk memesan kebutuhan bisnis mereka. Hal ini juga dapat menjadi dasar bagi tim product dan business untuk pengembangan product dan business kedepannya. Adapun metrik yang digunakan disebut retention cohort. Untuk project ini, kita akan menghitung retention dengan query SQL sederhana, sedangkan cara lain yaitu JOIN dan SELF JOIN akan dibahas dimateri selanjutnya :

 

Oleh karena baru terdapat 2 periode yang Quarter 1 dan Quarter 2, maka retention yang dapat dihitung adalah retention dari customers yang berbelanja di Quarter 1 dan kembali berbelanja di Quarter 2, sedangkan untuk customers yang berbelanja di Quarter 2 baru bisa dihitung retentionnya di Quarter 3.

1. Dari tabel orders_1, tambahkan kolom baru dengan value “1” dan beri nama “quarter”
2. Dari tabel orders_2, pilihlah kolom customerID, gunakan distinct untuk menghilangkan duplikasi
3. Filter orders_1 menggunakan operator IN() dan ‘Select statement Langkah 2)’, sehingga hanya customerID yang pernah bertransaksi di quarter 2 (customerID tercatat di tabel orders_2) yang diperhitungkan.
4. Hitunglah jumlah unik customers (tidak ada duplikasi customers) dibagi dengan total_ customers dalam percentage, pada Select statement dan beri nama “Q2”
<img src="https://miro.medium.com/max/414/1*CWWJfgpS6dBfB6CCLcj14Q.png">

```
#Menghitung total unik customers yang transaksi di quarter_1
SELECT COUNT(DISTINCT customerID) as total_customers FROM orders_1;
#output = 25

SELECT '1' as quarter, (COUNT(DISTINCT customerID) * 100) / 25 AS q2
FROM orders_1
WHERE customerID in(
SELECT DISTINCT customerID
FROM orders_2
)

```
### Kesimpulan
Berdasarkan data yang telah kita peroleh melalui query SQL, Kita dapat menarik kesimpulan bahwa :

1. Performance xyz.com menurun signifikan di quarter ke-2, terlihat dari nilai penjualan dan revenue yang drop hingga 20% dan 24%,
2. erolehan customer baru juga tidak terlalu baik, dan sedikit menurun dibandingkan quarter sebelumnya.
3. Ketertarikan customer baru untuk berbelanja di xyz.com masih kurang, hanya sekitar 56% saja yang sudah bertransaksi. Disarankan tim Produk untuk perlu mempelajari behaviour customer dan melakukan product improvement, sehingga conversion rate (register to transaction) dapat meningkat.
4. Produk kategori S18 dan S24 berkontribusi sekitar 50% dari total order dan 60% dari total penjualan, sehingga xyz.com sebaiknya fokus untuk pengembangan category S18 dan S24.
5. Retention rate customer xyz.com juga sangat rendah yaitu hanya 24%, artinya banyak customer yang sudah bertransaksi di quarter-1 tidak kembali melakukan order di quarter ke-2 (no repeat order).
6. xyz.com mengalami pertumbuhan negatif di quarter ke-2 dan perlu melakukan banyak improvement baik itu di sisi produk dan bisnis marketing, jika ingin mencapai target dan positif growth di quarter ke-3. Rendahnya retention rate dan conversion rate bisa menjadi diagnosa awal bahwa customer tidak tertarik/kurang puas/kecewa berbelanja di xyz.com.
