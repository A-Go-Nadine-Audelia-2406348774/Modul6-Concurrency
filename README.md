Nama = Go Nadine Audelia
NPM = 2406348774

## COMMIT 1 REFLECTION
Saya mempelajari bagaimana server memproses HTTP request dari browser menggunakan TcpStream. Fungsi handle_connection memanfaatkan BufReader untuk membaca aliran data secara lebih efisien karena data dibuffer terlebih dahulu. Aliran data tersebut kemudian dipecah menjadi baris-baris teks menggunakan .lines(). Karena proses pembacaan dari jaringan berpotensi mengalami error, .map(|result| result.unwrap()) digunakan untuk mengekstrak nilai teks aslinya. Sebuah HTTP request dari browser selalu diakhiri dengan baris kosong berturut-turut sehingga metode .take_while(|line| !line.is_empty()) diterapkan untuk menghentikan pembacaan tepat saat mencapai akhir dari header request. Terakhir, seluruh baris yang telah dibaca dikumpulkan menjadi sebuah struktur data bernama http_request menggunakan .collect() yang berfungsi untuk mencetak detail request tersebut ke terminal.

## COMMIT 2 REFLECTION
![Commit 2 screen capture](/assets/images/commit2.png)
Saya mempelajari cara mengembalikan respons berupa file HTML dari server ke browser. Kode main.rs terbaru menggunakan fs::read_to_string untuk membaca seluruh isi file hello.html dan mengubahnya menjadi tipe data String. Lalu, membuat variabel response yang diawali dengan status line (HTTP/1.1 200 OK), serta diikuti oleh header Content Length untuk memberi tahu browser ukuran data yang dikirim. Setelah itu, disisipkan carriage return dan line feed ganda yaitu \r\n\r\n sebagai pemisah wajib antara bagian header dan body HTML. Data response tersebut diubah menjadi bytes dan dikirimkan kembali ke klien melalui metode stream.write_all().

## COMMIT 3 REFLECTION
![Commit 3 screen capture](/assets/images/commit3.png)
Saya memodifikasi server agar dapat memvalidasi request dan memberikan respons yang spesifik. Cara kerjanya adalah dengan memeriksa request line dan jika sesuai dengan rute utama, maka server menetapkan status 200 OK dan memuat hello.html. Namun jika rute tidak dikenali, maka server menetapkan status 404 Not Found dan memuat 404.html. Dalam proses ini saya melakukan refactoring dengan mengekstrak variabel status_line dan filename di bagian if-else. Refactoring ini dilakukan untuk menghindari duplikasi kode.

## COMMIT 4 REFLECTION
Saya mencoba slow response dan mengobservasi kelemahan utama dari arsitektur single-threaded. Dengan menambahkan rute /sleep yang menahan eksekusi selama 10 detik, saya melihat bahwa request lain di tab berbeda harus ikut menunggu hingga proses di tab pertama selesai. Hal ini terjadi karena server hanya memiliki satu jalur thread. Ketika hanya ada satu jalur thread maka akan menyebabkan satu request yang memakan waktu lama akan memblokir seluruh koneksi lain yang masuk. Hal ini membuktikan bahwa single-threaded server sangat tidak efisien dan rentan mengalami bottleneck.

## COMMIT 5 REFLECTION
Saya mengimplementasikan ThreadPool untuk mengubah server dari singlethreaded menjadi multithreaded. Arsitektur ini menggunakan sejumlah worker threads yang dibuat untuk menangani permintaan secara paralel. Saya menggunakan mpsc::channel untuk mengirimkan tugas dari thread utama ke para worker. Agar receiver dapat digunakan oleh banyak thread secara aman maka saya membungkusnya dengan Arc Mutex yang menjamin hanya satu worker yang mengambil tugas pada satu waktu. Dengan sistem ini, server kini dapat memproses permintaan yang memakan waktu lama atau /sleep tanpa menghentikan permintaan lainnya yang masuk.

## COMMIT BONUS REFLECTION
Saya mempelajari cara mengimplementasikan fungsi build sebagai alternatif yang lebih aman dibandingkan fungsi new untuk menginisialisasi ThreadPool. Perbedaan utamanya terletak pada strategi penanganan error. Fungsi new biasanya menggunakan assert! yang akan menghentikan program secara mendadak jika input tidak valid. Sedangkan, fungsi build dirancang untuk mengembalikan tipe data result. Pendekatan ini memungkinkan sistem menangkap kegagalan inisialisasi dan mengambil tindakan pencegahan tanpa menyebabkan seluruh server mati. 