
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