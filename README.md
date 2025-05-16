
### a. Apa itu AMQP?

**AMQP (Advanced Message Queuing Protocol)** adalah protokol komunikasi open standard yang dirancang untuk message-oriented middleware. AMQP memungkinkan sistem untuk berkomunikasi satu sama lain melalui pertukaran pesan (message exchange) dengan cara yang andal dan terstruktur. Protokol ini biasa digunakan dalam sistem arsitektur event-driven untuk mengirim dan menerima pesan secara asynchronous.

AMQP banyak digunakan bersama **RabbitMQ**, yang merupakan salah satu implementasi paling populer dari message broker berbasis AMQP.


### b. Apa arti dari `guest:guest@localhost:5672`?

Format `guest:guest@localhost:5672` adalah sebuah **URI (Uniform Resource Identifier)** yang digunakan untuk menghubungkan aplikasi dengan server RabbitMQ menggunakan protokol AMQP.

Penjelasan elemen-elemennya:

* **guest** (yang pertama): adalah **username** yang digunakan untuk login ke RabbitMQ.
* **guest** (yang kedua): adalah **password** dari username tersebut.
* **localhost**: menunjukkan bahwa koneksi dilakukan ke server RabbitMQ yang berjalan di komputer lokal (127.0.0.1).
* **5672**: adalah **port default** yang digunakan oleh RabbitMQ untuk komunikasi AMQP.

Jadi, `amqp://guest:guest@localhost:5672` berarti aplikasi akan mencoba terhubung ke RabbitMQ yang berjalan di komputer lokal menggunakan username dan password `guest`, melalui port AMQP standar yaitu `5672`.

![RabbitMQ slow simulation](https://media.discordapp.net/attachments/916932753897967666/1372904644568678431/image.png?ex=68287896&is=68272716&hm=967261737d40e9dcebb2d70c3ecf64b51e50ac11b85f0866b9d8b8fda858d3a0&=&format=webp&quality=lossless&width=1744&height=856)


![running 3 subscribers](https://media.discordapp.net/attachments/916932753897967666/1372906362165530705/image.png?ex=68287a30&is=682728b0&hm=fac3049aeef06289c6d92aae69362ced568af5ae2b163bdbbc65278fe09f0c71&=&format=webp&quality=lossless&width=1734&height=856)

![running 3 subscribers console](https://media.discordapp.net/attachments/916932753897967666/1372906482596712478/image.png?ex=68287a4c&is=682728cc&hm=87271226f218426a51da35575e9d6e18d2531ddb62dba2c6dad5758beab71fb7&=&format=webp&quality=lossless&width=1594&height=856)


## Refleksi: Mengapa Pesan Terproses Seperti Itu?

### Penjelasan

Dalam arsitektur *event-driven*, subscriber yang terkoneksi ke queue RabbitMQ akan menerima pesan secara bergiliran (*round-robin*), dengan syarat mereka menggunakan **queue yang sama** dan **subscription yang aktif** pada waktu bersamaan. Ketika kita menjalankan beberapa subscriber secara paralel (misalnya melalui beberapa terminal dengan `cargo run`), RabbitMQ akan membagi beban pesan di antara mereka secara otomatis.

Hal ini menjawab mengapa ketika ada dua subscriber, terlihat bahwa pesan-pesan diproses secara terpisah: satu subscriber memproses pesan untuk “Budi” dan “Dira”, sedangkan yang lain memproses “Cica”, “Amir”, dan “Emir”. RabbitMQ menggunakan mekanisme *load balancing* sederhana berdasarkan antrian, sehingga distribusi menjadi efisien dan menghindari penumpukan beban di satu subscriber.

Jika hanya ada satu subscriber, maka semua pesan harus diproses oleh proses tunggal tersebut, yang menyebabkan antrean pesan (*queue*) menumpuk dan diproses lebih lambat. Namun ketika kita menambahkan subscriber lain, beban tersebut dibagi, sehingga pemrosesan pesan menjadi lebih cepat dan spike pada message queue di RabbitMQ menjadi lebih rendah atau cepat menurun.

### Hasil Pengamatan

Berdasarkan gambar monitor dari RabbitMQ browser, terlihat bahwa:

* Lonjakan (spike) pesan pada queue cepat menurun saat subscriber dijalankan lebih dari satu.
* Tidak ada pesan yang tertunda (*Ready = 0*), menunjukkan bahwa subscriber berhasil mengkonsumsi semua pesan secara merata.
* Hal ini menandakan sistem berjalan efisien dan sesuai dengan karakteristik *event-driven system*.

### Kesimpulan

Distribusi pesan yang terbagi antara beberapa subscriber adalah hal yang **diharapkan** dan **normal** dalam arsitektur ini. Semakin banyak subscriber aktif, semakin cepat pesan dalam queue dapat diproses, selama pesan tersebut cukup banyak dan subscriber menggunakan konfigurasi yang tepat terhadap queue yang sama.

