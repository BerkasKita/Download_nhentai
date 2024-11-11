# Download_nhentai
+ Pastikan Node.js Terinstal di Komputer:
  + Cek apakah Node.js sudah terinstal dengan menjalankan perintah ini di terminal atau command prompt:
    
    ```
    node -v
    ```
  + Jika belum terinstal, kamu bisa mengunduh dan menginstal Node.js dari https://nodejs.org/
+ Buat File JavaScript:
  + Buat sebuah file baru dengan nama, misalnya, `download_Images_nhentai.js.`
  + Copy-paste kode yang sudah saya berikan ke dalam file tersebut.
    
    ```javascript
    const axios = require('axios');
    const cheerio = require('cheerio');
    const fs = require('fs');
    const path = require('path');
    const https = require('https'); // Digunakan untuk mengunduh gambar
    
    // Mengambil ID dari argument command line
    const galleryId = process.argv[2]; // Mengambil argumen kedua dari command line
    if (!galleryId) {
      console.error('ID galeri tidak disediakan! Gunakan perintah: node kode.js <galleryId>');
      process.exit(1); // Keluar dari program jika ID tidak disediakan
    }
    
    // URL blog/web yang ingin diambil
    const url = `https://nhentai.net/g/${galleryId}/`; // URL yang dibuat menggunakan ID dari command line
    const targetSelector = '.gallerythumb img'; // Selector CSS yang ditargetkan (misalnya kelas atau ID)
    
    // Fungsi untuk mengunduh gambar
    async function downloadImage(imageUrl, index, folderName) {
      // Pastikan folder dibuat jika belum ada
      if (!fs.existsSync(folderName)) {
        fs.mkdirSync(folderName, { recursive: true });
      }
    
      // Buat nama file di dalam folder
      const fileName = path.join(folderName, `image${index + 1}${path.extname(imageUrl)}`);
      const file = fs.createWriteStream(fileName);
      
      https.get(imageUrl, (response) => {
        response.pipe(file);
      }).on('error', (err) => {
        console.error(`Gagal mengunduh gambar ${imageUrl}: ${err.message}`);
      });
    
      return new Promise((resolve, reject) => {
        file.on('finish', resolve);
        file.on('error', reject);
      });
    }
    
    // Fungsi utama untuk mengunduh gambar dari URL blog/web
    async function fetchImagesFromUrl() {
      try {
        // Mengambil konten HTML dari URL
        const { data } = await axios.get(url);
        const $ = cheerio.load(data);
    
        // Mengambil teks dari elemen <h1 class="title">
        const baseFileName = $('h1.title').text().trim().replace(/[<>:"/\\|?*]+/g, '_'); // Membersihkan karakter ilegal di nama file
    
        // Menemukan semua elemen gambar berdasarkan selector yang ditentukan
        const images = [];
        $(targetSelector).each((index, element) => {
          const imgUrl = $(element).attr('data-src');
          if (imgUrl) {
            images.push(imgUrl.startsWith('http') ? imgUrl : `${url}${imgUrl}`);
          }
        });
    
        // Memodifikasi URL gambar sesuai kebutuhan
        const modifiedImages = images.map((imgUrl) => {
          let modifiedUrl = imgUrl.replace(/(?<=https:\/\/)[^/]+(?=\.nhentai)/, 'i5');
          modifiedUrl = modifiedUrl.replace(/(\d+)t\.(jpg|png|webm|webp|jpg.jpg|png\.png|webm\.webm|webp\.webp)$/, '$1.$2');
    
          // Mengatasi format yang ganda seperti .jpg.jpg atau .webp.webp
          modifiedUrl = modifiedUrl.replace(/(\.jpg|\.png|\.webm|\.webp|\.jpg\.jpg|\.png\.png|\.webm\.webm|\.webp\.webp)\1$/, '$1');
    
          return modifiedUrl;
        });
    
        console.log(modifiedImages);
    
        // Mengunduh semua gambar yang telah dimodifikasi
        for (let i = 0; i < modifiedImages.length; i++) {
          await downloadImage(modifiedImages[i], i, baseFileName);
        }
    
        console.log('Semua gambar telah diunduh ke folder:', baseFileName);
      } catch (error) {
        console.error(`Terjadi kesalahan: ${error.message}`);
      }
    }
    
    fetchImagesFromUrl();
    ```
+ Buka Terminal atau Command Prompt:
  + Navigasikan ke direktori di mana file `download_Images_nhentai.js` berada menggunakan perintah `cd`. Contohnya:
    
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
  + Setelah axios terpasang, jalankan file `download_Images_nhentai.js` dengan perintah:<br><br>
  ```
  node download_Images_nhentai.js ID_nhentai
  ```
  
  `ID_nhentai` ganti dengan id dari nhentai.net
  
  + Program akan mulai mengunduh gambar dan menyimpannya ke direktori tempat file tersebut dijalankan.
 
## Catatan Penting:

Hasil unduhan akan disimpan dengan nama image1.jpg, image2.jpg, dan seterusnya di folder yang sama dengan file skrip.
Pastikan URL gambar dapat diakses secara publik dan tidak diblokir atau memerlukan autentikasi khusus.
