# Jawaban Pertanyaan Praktikum: Modul 2 - Pemrograman GPIO
-----

## 2.5.4 Pertanyaan Praktikum (Percobaan 2A: Seven Segment)

**1. Gambarkan rangkaian schematic yang digunakan pada percobaan\!**

**Jawab:** 


-----

**2. Apa yang terjadi jika nilai num lebih dari 15?**

**Jawab:** Variabel digitPattern didefinisikan sebagai array dua dimensi yang memiliki 16 elemen (indeks 0–15). Apabila nilai num melebihi batas tersebut, misalnya 16, maka program akan mengakses indeks di luar kapasitas array (out-of-bounds). Akibatnya, mikrokontroler akan membaca data acak dari memori. Kondisi ini dapat menyebabkan tampilan seven segment menjadi tidak sesuai, menampilkan nilai tidak valid, atau bahkan berpotensi menyebabkan sistem mengalami gangguan seperti restart.

-----

**3. Apakah program ini menggunakan common cathode atau common anode? Jelaskan alasannya\!**

**Jawab:** Program ini pada dasarnya dirancang untuk **Common Cathode (CC)**.

  * **Alasannya:** pada array digitPattern, nilai logika 1 digunakan untuk mengaktifkan segmen. Pada konfigurasi common cathode, segmen LED akan menyala ketika diberikan logika HIGH (5V), karena terminal katoda terhubung langsung ke ground. Hal ini sesuai dengan logika yang diterapkan pada program.

-----

**4. Modifikasi program agar tampilan berjalan dari F ke 0 dan berikan penjelasan disetiap baris kode nya\!**

**Jawab:**
Berikut adalah kode yang telah dimodifikasi agar melakukan hitung mundur (*countdown*) dari F ke 0:

```cpp
// Pin mapping segment (a, b, c, d, e, f, g, dp)
const int segmentPins[8] = {7, 6, 5, 11, 10, 8, 9, 4};

// Pola digit 0-F (Urutan: a, b, c, d, e, f, g, dp)
byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, //0
  {0,1,1,0,0,0,0,0}, //1
  {1,1,0,1,1,0,1,0}, //2
  {1,1,1,1,0,0,1,0}, //3
  {0,1,1,0,0,1,1,0}, //4
  {1,0,1,1,0,1,1,0}, //5 
  {1,0,1,1,1,1,1,0}, //6
  {1,1,1,0,0,0,0,0}, //7
  {1,1,1,1,1,1,1,0}, //8
  {1,1,1,1,0,1,1,0}, //9
  {1,1,1,0,1,1,1,0}, //A
  {0,0,1,1,1,1,1,0}, //b
  {1,0,0,1,1,1,0,0}, //C
  {0,1,1,1,1,0,1,0}, //d
  {1,0,0,1,1,1,1,0}, //E
  {1,0,0,0,1,1,1,0}  //F
};

void displayDigit(int num) {
  for(int i=0; i<8; i++) {
    // Membalik logika 1 jadi 0 atau sebaliknya agar sesuai hardware
    digitalWrite(segmentPins[i], !digitPattern[num][i]);
  }
}

void setup() {
  for(int i=0 ;i<8; i++) {
    pinMode(segmentPins[i], OUTPUT); // Mengatur semua pin segmen sebagai OUTPUT
  }
}

void loop() {
  // MODIFIKASI: Perulangan menurun dari indeks 15 (F) ke 0
  for(int i=15; i>=0; i--) {
    displayDigit(i);  // Menampilkan karakter berdasarkan indeks i
    delay(1000);      // Jeda 1 detik sebelum berganti ke karakter berikutnya
  }
}
```

-----

## 2.6.4 Pertanyaan Praktikum (Percobaan 1B: Kontrol Counter Dengan Push Button)

**1. Gambarkan rangkaian schematic yang digunakan pada percobaan\!**

**Jawab:**


-----

**2. Mengapa pada push button digunakan mode INPUT\_PULLUP pada Arduino Uno? Apa keuntungannya dibandingkan rangkaian biasa?**

**Jawab:** Mode INPUT_PULLUP digunakan untuk mengaktifkan resistor pull-up internal pada mikrokontroler, sehingga pin akan terhubung ke tegangan HIGH secara default.

 **Keuntungan INPUT_PULLUP:**
- Tidak memerlukan resistor tambahan  
- Mencegah kondisi floating  
- Membuat pembacaan sinyal lebih stabil
 
-----

**3. Jika salah satu LED segmen tidak menyala, apa saja kemungkinan penyebabnya dari sisi hardware maupun software?**

**Jawab:**

  * **Hardware:**
      * LED pada segmen rusak atau tidak berfungsi.
      * Kabel jumper tidak terhubung dengan baik.
      * Resistor tidak terpasang dengan benar atau nilainya tidak sesuai.
      * Kesalahan dalam pengkabelan antara Arduino dan seven segment.
  * **Software:**
      * Kesalahan dalam penentuan pin pada array `segmentPins`.
      * Pola logika pada `digitPattern` tidak sesuai untuk segmen tertentu..
      * Pin belum dikonfigurasi sebagai `OUTPUT` pada bagian program.
-----

**4. Modifikasi program dengan dua push button (Increment & Decrement)**

**Jawab:**
Berikut adalah modifikasi sistem counter dengan dua tombol (Pin 2 untuk Tambah, Pin 3 untuk Kurang):

### Source Code Modifikasi 2 Push Button

```cpp
const int segmentPins[8] = {7, 6, 5, 11, 10, 8, 9, 4};
const int btnUp = 2;   // Pin untuk tombol Tambah
const int btnDown = 3; // Pin untuk tombol Kurang

int counter = 0;
bool lastBtnUpState = HIGH;
bool lastBtnDownState = HIGH;

byte digitPattern[16][8] = {
  {1,1,1,1,1,1,0,0}, {0,1,1,0,0,0,0,0}, {1,1,0,1,1,0,1,0}, {1,1,1,1,0,0,1,0},
  {0,1,1,0,0,1,1,0}, {1,0,1,1,0,1,1,0}, {1,0,1,1,1,1,1,0}, {1,1,1,0,0,0,0,0},
  {1,1,1,1,1,1,1,0}, {1,1,1,1,0,1,1,0}, {1,1,1,0,1,1,1,0}, {0,0,1,1,1,1,1,0},
  {1,0,0,1,1,1,0,0}, {0,1,1,1,1,0,1,0}, {1,0,0,1,1,1,1,0}, {1,0,0,0,1,1,1,0}
};

void displayDigit(int num) {
  for(int i=0; i<8; i++) {
    digitalWrite(segmentPins[i], !digitPattern[num][i]);
  }
}

void setup() {
  for(int i=0; i<8; i++) pinMode(segmentPins[i], OUTPUT);
  pinMode(btnUp, INPUT_PULLUP);
  pinMode(btnDown, INPUT_PULLUP);
  displayDigit(counter);
}

void loop() {
  bool currentBtnUpState = digitalRead(btnUp);
  bool currentBtnDownState = digitalRead(btnDown);

  // Logika Increment (Tambah)
  if (lastBtnUpState == HIGH && currentBtnUpState == LOW) {
    counter++;
    if(counter > 15) counter = 0;
    displayDigit(counter);
    delay(200); // Debounce
  }

  // Logika Decrement (Kurang)
  if (lastBtnDownState == HIGH && currentBtnDownState == LOW) {
    counter--;
    if(counter < 0) counter = 15;
    displayDigit(counter);
    delay(200); // Debounce
  }

  lastBtnUpState = currentBtnUpState;
  lastBtnDownState = currentBtnDownState;
}
```
