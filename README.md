# Download_nhentai
+ Pastikan Node.js Terinstal di Komputer:
  + Cek apakah Node.js sudah terinstal dengan menjalankan perintah ini di terminal atau command prompt:
    
    ```
    node -v
    ```
  + Jika belum terinstal, kamu bisa mengunduh dan menginstal Node.js dari https://nodejs.org/
+ Buat File JavaScript:
  + Buat sebuah file baru dengan nama, misalnya, `downloadImages.js.`
  + Copy-paste kode yang sudah saya berikan ke dalam file tersebut.
    
    ```javascript
    const axios = require('axios');
    const fs = require('fs');
    const path = require('path');
    
    // Mendapatkan ID dan max dari command line arguments
    const id = process.argv[2]; // Ambil ID dari argumen pertama
    const max = parseInt(process.argv[3], 10); // Ambil max dari argumen kedua dan ubah menjadi angka
    const formats = ['jpg', 'png', 'webp']; // Daftar format yang akan dicoba
    
    // Validasi ID dan max
    if (!id || isNaN(max)) {
      console.error('ID galeri dan jumlah maksimal gambar tidak disediakan atau tidak valid!');
      console.error('Gunakan perintah: node downloadImages.js <id> <max>');
      process.exit(1); // Keluar dari program jika ID atau max tidak disediakan atau tidak valid
    }
    
    // Membuat array URL gambar secara dinamis
    const imageUrls = [];
    for (let i = 1; i <= max; i++) {
      imageUrls.push(`https://i5.nhentai.net/galleries/${id}/${i}`);
    }
    
    // Fungsi untuk mengunduh gambar dengan mencoba beberapa format
    async function downloadImage(baseUrl, index) {
      for (let format of formats) {
        try {
          const url = `${baseUrl}.${format}`;
          const response = await axios({
            url,
            method: 'GET',
            responseType: 'stream'
          });
    
          const fileName = `image${index + 1}.${format}`; // Menentukan nama file
          const writer = fs.createWriteStream(path.resolve(__dirname, fileName));
    
          response.data.pipe(writer);
    
          return new Promise((resolve, reject) => {
            writer.on('finish', resolve);
            writer.on('error', reject);
          });
        } catch (error) {
          if (error.response && error.response.status === 404) {
            console.log(`Format ${format} tidak ditemukan untuk gambar pada indeks ${index}. Mencoba format berikutnya...`);
          } else {
            console.error(`Terjadi kesalahan pada gambar indeks ${index} dengan format ${format}: ${error.message}`);
            break; // Jika error bukan karena 404, hentikan percobaan format berikutnya
          }
        }
      }
    }
    
    // Fungsi untuk mengunduh semua gambar
    async function downloadAllImages() {
      for (let i = 0; i < imageUrls.length; i++) {
        await downloadImage(imageUrls[i], i);
      }
      console.log('Semua gambar telah diunduh');
    }
    
    downloadAllImages();
    ```
+ Buka Terminal atau Command Prompt:
  + Navigasikan ke direktori di mana file `downloadImages.js` berada menggunakan perintah `cd`. Contohnya:
    ```
    cd path/to/your/directory
    ```
    `path/to/your/directory` ganti dengan lokasi folder kalian
+ Pasang `axios`:
  + Jalankan perintah berikut di terminal untuk menginstal `axios`:
    ```
    npm install axios
    ```
+ Jalankan Program:
  + Setelah axios terpasang, jalankan file `downloadImages.js` dengan perintah:
  ```
  node downloadImages.js 3107705 56
  ```
  + Program akan mulai mengunduh gambar dan menyimpannya ke direktori tempat file tersebut dijalankan.
 
## Catatan Penting:

Hasil unduhan akan disimpan dengan nama image1.jpg, image2.jpg, dan seterusnya di folder yang sama dengan file skrip.
Pastikan URL gambar dapat diakses secara publik dan tidak diblokir atau memerlukan autentikasi khusus.
