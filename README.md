Nama = Go Nadine Audelia
NPM = 2406348774

# COMMIT 1 REFLECTION
Saya mempelajari bagaimana server memproses HTTP request dari browser menggunakan TcpStream. Fungsi handle_connection memanfaatkan BufReader untuk membaca aliran data secara lebih efisien karena data dibuffer terlebih dahulu. Aliran data tersebut kemudian dipecah menjadi baris-baris teks menggunakan .lines(). Karena proses pembacaan dari jaringan berpotensi mengalami error, .map(|result| result.unwrap()) digunakan untuk mengekstrak nilai teks aslinya. Sebuah HTTP request dari browser selalu diakhiri dengan baris kosong berturut-turut sehingga metode .take_while(|line| !line.is_empty()) diterapkan untuk menghentikan pembacaan tepat saat mencapai akhir dari header request. Terakhir, seluruh baris yang telah dibaca dikumpulkan menjadi sebuah struktur data bernama http_request menggunakan .collect() yang berfungsi untuk mencetak detail request tersebut ke terminal.

# COMMIT 2 REFLECTION
![Commit 2 screen capture](/assets/images/commit2.png)
Saya mempelajari cara mengembalikan respons berupa file HTML dari server ke browser. Kode main.rs terbaru menggunakan fs::read_to_string untuk membaca seluruh isi file hello.html dan mengubahnya menjadi tipe data String. Lalu, membuat variabel response yang diawali dengan status line (HTTP/1.1 200 OK), serta diikuti oleh header Content Length untuk memberi tahu browser ukuran data yang dikirim. Setelah itu, disisipkan carriage return dan line feed ganda yaitu \r\n\r\n sebagai pemisah wajib antara bagian header dan body HTML. Data response tersebut diubah menjadi bytes dan dikirimkan kembali ke klien melalui metode stream.write_all().
