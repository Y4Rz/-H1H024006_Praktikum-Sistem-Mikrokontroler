# Percobaan 2: Komunikasi Task

## Jawaban Pertanyaan Praktikum

### 1. Apakah kedua task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!

Kedua task beroperasi secara bergantian namun memberikan kesan concurrent karena task `read_data` mengirimkan data ke queue melalui `xQueueSend()` lalu melakukan delay selama 100 ms sehingga masuk kondisi Blocked dan CPU digunakan oleh task `display`, sedangkan task `display` menunggu data dari queue menggunakan `xQueueReceive()` dengan `portMAX_DELAY`, sehingga sinkronisasi antar task terjamin dan data tidak terlewat maupun terbaca lebih dari sekali.

### 2. Apakah program ini berpotensi mengalami *race condition*? Jelaskan!

Tidak, program ini tidak berpotensi mengalami race condition karena kondisi race condition umumnya muncul ketika dua atau lebih task mengakses memori atau sumber daya bersama secara simultan tanpa mekanisme sinkronisasi yang memadai, sedangkan pada program ini seluruh pertukaran data antara task `read_data` dan task `display` dilakukan sepenuhnya melalui queue bawaan FreeRTOS.

### 3. Modifikasilah program dengan menggunakan sensor DHT sesungguhnya sehingga informasi yang ditampilkan dinamis. Bagaimana hasilnya?


https://github.com/user-attachments/assets/97e12d0c-88c1-4d19-8f53-9676268dccc5


### source kode
```cpp

#include <DHT.h>

#define DHTPIN 2      // Pin data sensor (S) terhubung ke pin D2
#define DHTTYPE DHT11 

DHT dht(DHTPIN, DHTTYPE);

// Variabel pengganti Scheduler RTOS
unsigned long waktuSebelumnya = 0;
const long jedaWaktu = 2000; // Jeda 2 detik antar pembacaan

void setup() {
  Serial.begin(9600);
  dht.begin();
  Serial.println("Sistem Mulai Membaca Sensor..."); 
}

void loop() {
  // Mengecek waktu saat ini (sebagai pengganti Task RTOS)
  unsigned long waktuSekarang = millis();

  // Jika sudah berlalu 2 detik, jalankan eksekusi
  if (waktuSekarang - waktuSebelumnya >= jedaWaktu) {
    waktuSebelumnya = waktuSekarang;

    // Membaca data dari sensor DHT
    float t = dht.readTemperature();
    float h = dht.readHumidity();

    // Memastikan data valid (berperan seperti QueueReceive)
    if (!isnan(t) && !isnan(h)) {
      Serial.print("Temperature: ");
      Serial.print(t);
      Serial.println(" °C");
      
      Serial.print("Humidity: ");
      Serial.print(h);
      Serial.println(" %");
      Serial.println("---");
    } else {
      Serial.println("Gagal membaca sensor DHT!");
    }
  }
}
```
