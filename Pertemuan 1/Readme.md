1.5.4 
Pertanyaan Praktikum: 
1. Pada kondisi apa program masuk ke blok if?
JAWAB: Program akan mengeksekusi perintah di dalam blok if ketika kondisi yang ditentukan bernilai benar (true). Pada program ini, salah satu contohnya adalah ketika nilai variabel timeDelay telah mencapai kurang dari atau sama dengan 100, yang menandakan bahwa LED sudah berada pada kecepatan maksimum.
2. Pada kondisi apa program masuk ke blok else?
JAWAB: Program akan masuk ke blok else apabila kondisi pada pernyataan if tidak terpenuhi atau bernilai salah (false). Dalam konteks program ini, kondisi tersebut terjadi ketika nilai timeDelay masih lebih besar dari 100, sehingga proses percepatan LED masih dapat dilanjutkan.
3. Apa fungsi dari perintah delay(timeDelay)?
JAWAB: Perintah delay(timeDelay) berfungsi untuk memberikan jeda atau menghentikan sementara eksekusi program selama waktu tertentu dalam satuan milidetik (ms), sesuai dengan nilai yang tersimpan pada variabel timeDelay. Dalam percobaan ini, fungsi tersebut digunakan untuk mengatur durasi LED saat menyala (HIGH) dan mati (LOW), sehingga menghasilkan efek kedipan dengan kecepatan yang dapat berubah-ubah.
4. Jika program yang dibuat memiliki alur mati → lambat → cepat → reset (mati), 
ubah menjadi LED tidak langsung reset → tetapi berubah dari cepat → sedang → 
mati dan berikan penjelasan disetiap baris kode nya.

JAWAB:
## Source Code
```cpp
const int ledPin = 6;          // Menetapkan pin digital 6 sebagai output LED [cite: 123]
int timeDelay = 1000;          // Inisialisasi awal durasi delay 1000ms (Lambat) [cite: 124]
bool percepat = true;          // Flag untuk menentukan arah perubahan kecepatan

void setup() {
  pinMode(ledPin, OUTPUT);     // Mengonfigurasi pin 6 sebagai output [cite: 127]
}

void loop() {
  // Fase Eksekusi Fisik (Kedip LED)
  digitalWrite(ledPin, HIGH);  // Menyalakan LED [cite: 130]
  delay(timeDelay);            // Menahan status nyala sesuai nilai variabel timeDelay [cite: 131]
  digitalWrite(ledPin, LOW);   // Mematikan LED [cite: 132]
  delay(timeDelay);            // Menahan status mati sesuai nilai variabel timeDelay [cite: 134]

  // Fase Logika Kontrol Dinamis
  if (percepat) {              
    if (timeDelay <= 100) {    // Jika sudah mencapai batas tercepat (100ms) 
      percepat = false;        // Ubah arah logika menjadi melambat (tidak langsung reset)
    } else {
      timeDelay -= 100;        // Jika belum, terus percepat dengan mengurangi delay [cite: 140]
    }
  } else {                     
    if (timeDelay >= 1000) {   // Jika sudah kembali ke kondisi terlambat (1000ms)
      percepat = true;         // Siapkan fase untuk kembali mempercepat pada siklus berikutnya
      delay(3000);             // Jeda reset selama 3 detik sebelum mulai lagi 
    } else {
      timeDelay += 450;        // Tambah delay secara signifikan untuk efek transisi ke "Sedang"
    }
  }
}
```
1.6.4 
Pertanyaan Praktikum 
1. Gambarkan rangkaian schematic 5 LED running yang digunakan pada percobaan!
JAWAB:
https://www.tinkercad.com/things/8aPGxsdifsX-rangkaian-perulangan
3. Jelaskan bagaimana program membuat efek LED berjalan dari kiri ke kanan!
JAWAB: Efek LED berjalan dari kiri ke kanan dihasilkan menggunakan struktur perulangan for dengan proses inkrementasi (penambahan nilai). Program dimulai dari pin terendah, kemudian secara bertahap berpindah ke pin yang lebih tinggi.
Pada setiap iterasi:
-LED dinyalakan dengan digitalWrite(HIGH)
-Diberikan jeda menggunakan delay()
-LED dimatikan kembali dengan digitalWrite(LOW)
Proses ini menciptakan efek visual seolah-olah LED bergerak dari kiri ke kanan.
4. Jelaskan bagaimana program membuat LED kembali dari kanan ke kiri!
JAWAB: Efek ini merupakan kebalikan dari sebelumnya, yaitu menggunakan perulangan for dengan dekrementasi (pengurangan nilai).
Program dimulai dari pin tertinggi, kemudian bergerak ke pin yang lebih rendah. Langkahnya tetap sama:
-LED menyala
-Delay
-LED mati
Sehingga menghasilkan efek LED berjalan dari kanan ke kiri.
5. Buatkan program agar LED menyala tiga LED kanan dan tiga LED kiri secara bergantian 
dan berikan penjelasan disetiap baris kode nya
JAWAB:
## Source Code
```cpp
void setup() {
  // Menginisialisasi pin 2 sampai 7 sebagai OUTPUT
  for (int ledPin = 2; ledPin < 8; ledPin++) {
    pinMode(ledPin, OUTPUT); 
  }
}

void loop() {
  // --- MENYALAKAN 3 LED KIRI ---
  for (int i = 2; i <= 4; i++) {
    digitalWrite(i, HIGH); // LED kiri menyala
  }
  for (int i = 5; i <= 7; i++) {
    digitalWrite(i, LOW);  // LED kanan mati
  }
  delay(1000); // Jeda 1 detik

  // --- MENYALAKAN 3 LED KANAN ---
  for (int i = 2; i <= 4; i++) {
    digitalWrite(i, LOW);  // LED kiri mati
  }
  for (int i = 5; i <= 7; i++) {
    digitalWrite(i, HIGH); // LED kanan menyala
  }
  delay(1000); // Jeda 1 detik
}
```
