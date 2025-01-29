Laporan Desain dan Perancangan REST API - Sistem Manajemen Perpustakaan

B. Perancangan Database
Berikut adalah desain database yang digunakan dalam sistem ini, dengan minimal 4 tabel yang saling berelasi:

1. Tabel users

Field      Tipe Data	  Keterangan
id	         INT	     Primary Key, Auto Increment
username	   VARCHAR	 Nama pengguna (unik)
email	       VARCHAR	 Email pengguna (unik)
password	   VARCHAR	 Password pengguna
role	       ENUM      Peran pengguna (user atau admin)


2. Tabel books

Field	           Tipe Data	             Keterangan
id	                INT	       Primary Key, Auto Increment
title	              VARCHAR	   Judul buku
author	            VARCHAR	   Penulis buku
genre	              VARCHAR	   Genre buku
year_published	    INT	       Tahun penerbitan
quantity_available	INT	       Jumlah buku yang tersedia

3. Tabel borrowed_books

Field	      Tipe Data	         Keterangan
id	          INT	        Primary Key, Auto Increment
user_id	      INT	        Foreign Key (mengacu ke tabel users)
book_id	      INT 	      Foreign Key (mengacu ke tabel books)
borrow_date 	DATETIME	  Tanggal peminjaman
return_date 	DATETIME	  Tanggal pengembalian
status	      ENUM	      Status peminjaman (borrowed, returned)


4. Tabel reviews

Field	      Tipe Data	          Keterangan
id	          INT	        Primary Key, Auto Increment
user_id	      INT     	  Foreign Key (mengacu ke tabel users)
book_id	      INT	        Foreign Key (mengacu ke tabel books)
rating	      INT	        Rating buku antara 1 sampai 5
comment	      TEXT	      Komentar atau ulasan dari pengguna
review_date	  DATETIME   	Tanggal ulasan dibuat

Relasi antar tabel:

Tabel users berelasi dengan tabel borrowed_books dan reviews melalui user_id.
Tabel books berelasi dengan tabel borrowed_books dan reviews melalui book_id.


Perancangan Endpoint
Berikut adalah minimal 20 endpoint untuk REST API sistem manajemen perpustakaan:

POST /api/v1/auth/register - Registrasi pengguna baru
POST /api/v1/auth/login - Login pengguna dan mendapatkan JWT
GET /api/v1/books - Menampilkan daftar buku
POST /api/v1/books - Menambahkan buku baru
GET /api/v1/books/{book_id} - Menampilkan detail buku
PUT /api/v1/books/{book_id} - Mengupdate detail buku
DELETE /api/v1/books/{book_id} - Menghapus buku
POST /api/v1/borrow - Meminjam buku
PUT /api/v1/borrow/{borrow_id} - Mengembalikan buku
GET /api/v1/borrowed-books - Menampilkan daftar buku yang dipinjam
GET /api/v1/borrowed-books/{borrow_id} - Menampilkan detail peminjaman
POST /api/v1/reviews - Menambahkan ulasan untuk buku
GET /api/v1/reviews/{book_id} - Menampilkan ulasan buku
GET /api/v1/users/{user_id} - Menampilkan data pengguna
PUT /api/v1/users/{user_id} - Mengupdate data pengguna
DELETE /api/v1/users/{user_id} - Menghapus pengguna
GET /api/v1/users - Menampilkan daftar semua pengguna (admin only)
POST /api/v1/auth/logout - Logout dan menghapus token JWT
GET /api/v1/search-books - Mencari buku berdasarkan kata kunci
POST /api/v1/admin/ban-user - Admin membekukan akun pengguna


Dokumentasi Backend API

Endpoint: Registrasi Pengguna
Endpoint: /api/v1/auth/register
Method: POST
Body Parameter:
   username: (string) Nama pengguna
   email: (string) Email pengguna
   password: (string) Password pengguna
   role: (string) Peran pengguna (user atau admin)
   
Response:
  201 Created:

{
  "message": "User registered successfully"
}


Endpoint: Login Pengguna
Endpoint: /api/v1/auth/login
Method: POST
Body Parameter:
  email: (string) Email pengguna
  password: (string) Password pengguna
Response:
  200 OK:
  
{
  "token": "JWT_Token_Here"
}

Integrasi API Publik
API dapat mengintegrasikan beberapa layanan publik, seperti:
- Google Books API: Untuk mencari buku berdasarkan judul atau penulis.
    - Endpoint: https://www.googleapis.com/books/v1/volumes?q={search_query}
    - Menggunakan endpoint ini untuk mencari buku dari Google Books API dan menambahkannya ke dalam sistem.
- Open Library API: Untuk mengakses informasi buku yang lebih lengkap.
    - Endpoint:: https://openlibrary.org/search.json?q={search_query}
 
Middleware dan Validasi JWT
- Middleware digunakan untuk mengamankan endpoint yang membutuhkan autentikasi. Middleware akan memvalidasi JWT token di setiap request.
- JWT Middleware:

  const jwt = require('jsonwebtoken');

const authMiddleware = (req, res, next) => {
  const token = req.header('Authorization')?.replace('Bearer ', '');

  if (!token) {
    return res.status(401).json({ message: 'Access denied' });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(400).json({ message: 'Invalid token' });
  }
};

Pengujian Endpoint
- Pengujian dilakukan menggunakan Postman untuk menguji setiap endpoint API.

Test Cases:
Cek endpoint POST /api/v1/auth/register untuk registrasi pengguna baru.
Cek endpoint POST /api/v1/auth/login untuk login dan mendapatkan token.
Cek endpoint GET /api/v1/books untuk melihat daftar buku yang tersedia.
Cek endpoint POST /api/v1/books untuk menambah buku baru ke perpustakaan.
Cek endpoint POST /api/v1/borrow untuk meminjam buku.
