<div align="center">
    <h1>MODULE 6</h1>
</div>

<div align="center">
    <img src="assets/images/burhan_pixel.png" alt="burhan" width="200"/>
</div>

<div align="center">
    <h2>Alwie Attar Elfandra</h2>
    <h2>2306241726</h2>
</div>

## Commit 1

Fungsi `handle_connection` menerima parameter `stream` bertipe `TcpStream`, yang merepresentasikan koneksi jaringan dengan klien. Berikut adalah penjelasan langkah demi langkah dari fungsi tersebut:  

### **1. Membungkus `TcpStream` dalam `BufReader`**
```rust
let buf_reader = BufReader::new(&mut stream);
```
- `BufReader` digunakan untuk membaca data dari `stream` secara buffered, yang meningkatkan efisiensi I/O dibandingkan membaca byte per byte langsung dari `TcpStream`.  

### **2. Membaca Baris demi Baris Permintaan HTTP**
```rust
let http_request: Vec<_> = buf_reader
    .lines()
    .map(|result| result.unwrap())
    .take_while(|line| !line.is_empty())
    .collect();
```
- `.lines()`: Menghasilkan iterator yang membaca koneksi sebagai baris-baris teks.  
- `.map(|result| result.unwrap())`: Mengambil setiap hasil dari iterator, yang bertipe `Result<String, Error>`, dan menggunakan `.unwrap()` untuk mendapatkan nilai `String`. (Perlu diperhatikan bahwa `.unwrap()` dapat menyebabkan panic jika terjadi error.)  
- `.take_while(|line| !line.is_empty())`: Mengambil baris-baris selama baris tersebut tidak kosong. Dalam protokol HTTP, bagian header dan request dipisahkan oleh baris kosong, jadi ini memastikan hanya header request yang dikumpulkan.  
- `.collect()`: Mengubah iterator menjadi `Vec<String>` untuk menyimpan semua baris request dalam sebuah vektor.  

### **3. Mencetak Request ke Konsol**
```rust
println!("Request: {:#?}", http_request);
```
- Menampilkan daftar baris permintaan HTTP yang diterima dari klien dengan format yang lebih mudah dibaca (`{:#?}` adalah pretty-print untuk debugging).  

TLDR : Fungsi `handle_connection` membaca permintaan HTTP dari klien, mengambil header request hingga menemukan baris kosong, lalu mencetaknya ke konsol.

Adapun pada device saya, request yang diterima kurang lebih :
1. Jenis Permintaan: GET / HTTP/1.1 (permintaan halaman utama)
2. User-Agent: Browser yang digunakan adalah Chrome versi 134 di Windows.
3. Cookie: Terdapat informasi sesi pengguna dengan user_role=Pelanggan dan user_name=Andi, yang menunjukkan bahwa klien sudah login. (Ini merupakan cookies dari matkul Basdat)
4. Keamanan: Header seperti Sec-Fetch-Site, Sec-Fetch-Mode, dan Sec-Fetch-User membantu mencegah serangan keamanan seperti CSRF.

## Commit 2