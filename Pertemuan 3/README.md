# Pertanyaan Praktikum Percobaan 3A: Komunikasi Serial (UART)

## 1. Jelaskan proses dari input keyboard hingga LED menyala/mati!

Saat pengguna memasukkan karakter pada Serial Monitor lalu menekan Enter, data tersebut dikirim melalui koneksi USB menuju buffer UART pada Arduino. Fungsi Serial.available() digunakan untuk memeriksa apakah terdapat data di dalam buffer. Jika ada, Serial.read() akan membaca satu karakter dan menyimpannya ke variabel data. Selanjutnya, program memeriksa isi variabel tersebut. Apabila nilainya `1`, maka digitalWrite(PIN_LED, HIGH) akan memberikan keluaran HIGH pada pin 12 sehingga LED menyala. Sebaliknya, jika nilainya `0`, maka digitalWrite(PIN_LED, LOW) akan mengubah pin menjadi LOW sehingga LED padam.

---

## 2. Mengapa digunakan Serial.available() sebelum membaca data? Apa yang terjadi jika baris tersebut dihilangkan? 

Fungsi `Serial.available()` berperan untuk menunjukkan berapa banyak byte data yang sedang tersedia pada buffer penerima UART. Proses pembacaan data hanya dilakukan ketika nilai yang dikembalikan lebih besar dari nol, yang berarti memang ada data masuk ke buffer.

Apabila baris tersebut dihapus, fungsi `Serial.read()` akan tetap dijalankan pada setiap perulangan `loop()` walaupun sebenarnya tidak ada data yang tersedia. Dalam kondisi buffer kosong, Serial.read() akan menghasilkan nilai `-1`, yang setelah dikonversi ke tipe char menjadi karakter yang tidak valid. Akibatnya, program terus memproses karakter tersebut sebagai input, sehingga kondisi `else if (data != '\n' && data != '\r')` akan terus bernilai benar. Hal ini menyebabkan Serial Monitor menampilkan pesan "Perintah tidak dikenal" secara berulang-ulang tanpa henti.

---

## 3. Modifikasi program agar LED berkedip (blink) ketika menerima input '2' dengan kondisi jika ‘2’ aktif maka LED akan terus berkedip sampai perintah selanjutnya diberikan dan berikan penjelasan disetiap baris kode nya!

### Kode Program

```cpp
const int PIN_LED = 12;

bool modeBlink = false;           // status apakah mode blink aktif
unsigned long waktuSebelumnya = 0; // menyimpan waktu terakhir LED berubah state
const long intervalBlink = 500;   // interval kedip dalam milidetik
bool statusLED = false;           // menyimpan state LED saat ini (nyala/mati)

void setup() {
  Serial.begin(9600);
  Serial.println("Ketik '1' nyala, '0' mati, '2' blink");
  pinMode(PIN_LED, OUTPUT); // set pin 12 sebagai output
}

void loop() {
  // Cek apakah ada input baru dari Serial
  if (Serial.available() > 0) {
    char data = Serial.read(); // ambil 1 karakter dari buffer

    if (data == '1') {
      modeBlink = false;              // matikan mode blink
      digitalWrite(PIN_LED, HIGH);   // nyalakan LED
      Serial.println("LED ON");
    }
    else if (data == '0') {
      modeBlink = false;             // matikan mode blink
      digitalWrite(PIN_LED, LOW);   // matikan LED
      Serial.println("LED OFF");
    }
    else if (data == '2') {
      modeBlink = true;              // aktifkan mode blink
      Serial.println("LED BLINK");
    }
    else if (data != '\n' && data != '\r') {
      // filter karakter newline, tampilkan error jika bukan 1, 0, atau 2
      Serial.println("Perintah tidak dikenal");
    }
  }

  // Eksekusi blink jika mode aktif, tanpa memblokir loop
  if (modeBlink) {
    unsigned long waktuSekarang = millis(); // baca waktu saat ini

    // cek apakah sudah melewati interval
    if (waktuSekarang - waktuSebelumnya >= intervalBlink) {
      waktuSebelumnya = waktuSekarang;  // perbarui waktu referensi
      statusLED = !statusLED;           // balik state LED
      digitalWrite(PIN_LED, statusLED ? HIGH : LOW); // terapkan state
    }
  }
}
```

---

## 4. Tentukan apakah menggunakan delay() atau milis()! Jelaskan pengaruhnya terhadap sistem!

**yang digunakan adalah `millis()`**

Penjelasan:
`delay()` memblokir seluruh eksekusi program. Selama jeda itu, `Serial.available()` tidak dicek, sehingga input `'0'` atau `'1'` dari pengguna tidak akan diproses sampai delay selesai. Sistem menjadi tidak responsif.

`millis()` tidak memblokir. Program terus menjalankan `loop()` secara penuh setiap siklus. Perubahan state LED hanya terjadi ketika selisih waktu antara `millis()` sekarang dan `waktuSebelumnya` sudah melewati `intervalBlink`. Akibatnya, Serial tetap terbaca di setiap iterasi dan perintah baru langsung diproses tanpa menunggu kedip selesai.

# Pertanyaan Praktikum Percobaan 3B:  Inter-Integrated Circuit (I2C)

## 1. Jelaskan bagaimana cara kerja komunikasi I2C antara Arduino dan LCD pada rangkaian tersebut!

Komunikasi I2C bekerja menggunakan dua jalur utama, yaitu SDA sebagai saluran data dan SCL sebagai saluran clock. Pada rangkaian ini, Arduino berfungsi sebagai master, sedangkan modul I2C pada LCD yang menggunakan IC PCF8574 berperan sebagai slave dengan alamat 0x20.

Saat proses pengiriman data berlangsung, Arduino terlebih dahulu membangkitkan kondisi START pada jalur SDA, kemudian mengirimkan alamat slave 7-bit beserta bit write. Setelah itu, data dikirimkan secara bertahap per byte dengan sinkronisasi dari sinyal clock pada jalur SCL. Data yang diterima oleh PCF8574 selanjutnya diubah dari bentuk komunikasi serial melalui dua kabel menjadi sinyal paralel 8-bit yang dapat diteruskan ke pengendali LCD HD44780. Dengan mekanisme tersebut, Arduino hanya perlu memanfaatkan dua pin, yaitu A4 dan A5, untuk mengontrol LCD yang secara fisik memiliki 16 pin.

---

## 2. Apakah pin potensiometer harus seperti itu? Jelaskan yang terjadi apabila pin kiri dan pin kanan tertukar!

Potensiometer memiliki 3 kaki:
- **Kaki kiri** → VCC (5V)
- **Kaki tengah (wiper)** → A0 (input ADC)
- **Kaki kanan** → GND

**Jika kaki kiri dan kanan tertukar (VCC dan GND dibalik):**
Potensiometer tetap berfungsi, tetapi arah putarannya terbalik. Yang semula putar kiri = nilai ADC 0, menjadi putar kiri = nilai ADC 1023. Tidak merusak komponen, hanya membalik respons.

**Jika kaki tengah tidak terhubung ke A0:**
Pin A0 floating, nilai ADC acak dan tidak stabil.

---

## 3. Modifikasi program dengan menggabungkan antara UART dan I2C (keduanya sebagai output) sehingga:
- Data tidak hanya ditampilkan di LCD tetapi juga di Serial Monitor
- Adapun data yang ditampilkan pada Serial Monitor sesuai dengan table berikut:
  
  | ADC:0 | Volt:0,00 V | Persen:0% |
  
Tampilan jika potensiometer dalam kondisi diputar paling kiri
- ADC: 0 0% | setCursor(0, 0) dan Bar (level) | setCursor(0, 1)
- Berikan penjelasan disetiap baris kode nya

**Kode Program:**

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>

LiquidCrystal_I2C lcd(0x20, 16, 2); // inisialisasi LCD I2C alamat 0x20, 16 kolom, 2 baris

const int pinPot = A0; // pin analog potensiometer

void setup() {
  Serial.begin(9600);       // inisialisasi komunikasi serial 9600 baud
  lcd.init();               // inisialisasi LCD
  lcd.backlight();          // nyalakan backlight LCD
}

void loop() {
  int nilai = analogRead(pinPot); // baca nilai ADC dari potensiometer (0-1023)

  float volt = (nilai / 1023.0) * 5.0;  // konversi ADC ke tegangan (0.00-5.00 V)
  int persen = (nilai / 1023.0) * 100;  // konversi ADC ke persentase (0-100%)
  int panjangBar = (nilai / 1023.0) * 16; // konversi ADC ke panjang bar (0-16)

  // Tampilkan ke Serial Monitor sesuai format tabel
  Serial.print("ADC: ");
  Serial.print(nilai);
  Serial.print(" | Volt: ");
  Serial.print(volt, 2);   // 2 angka desimal
  Serial.print(" V | Persen: ");
  Serial.print(persen);
  Serial.println("%");

  // Baris 1 LCD: nilai ADC dan persentase
  lcd.setCursor(0, 0);               // cursor ke kolom 0, baris 0
  lcd.print("ADC:");
  lcd.print(nilai);
  lcd.print(" ");
  lcd.print(persen);
  lcd.print("%  ");                  // spasi untuk clear sisa karakter

  // Baris 2 LCD: bar level
  lcd.setCursor(0, 1);               // cursor ke kolom 0, baris 1
  for (int i = 0; i < 16; i++) {
    if (i < panjangBar) {
      lcd.print('#');                // karakter bar aktif
    } else {
      lcd.print(' ');                // kosong jika tidak aktif
    }
  }

  delay(200); // jeda 200ms sebelum pembacaan berikutnya
}
```

**Format Serial Monitor:**
```
ADC: 0 | Volt: 0.00 V | Persen: 0%
ADC: 512 | Volt: 2.50 V | Persen: 50%
ADC: 1023 | Volt: 5.00 V | Persen: 100%
```

---

## 4. Lengkapi table berikut berdasarkan pengamatan pada Serial Monitor

| ADC | Volt (V) | Persen (%) |
|-----|----------|------------|
| 1   | 0.00 V   | 0%         |
| 21  | 0.10 V   | 2%         |
| 49  | 0.24 V   | 4%         |
| 74  | 0.36 V   | 7%         |
| 96  | 0.47 V   | 9%         |

Nilai dihitung dengan rumus:
- Volt = (ADC / 1023) × 5.0
- Persen = (ADC / 1023) × 100
