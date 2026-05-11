# Percobaan 1 : Multitasking (FreeRTOS)

## Jawaban Pertanyaan Praktikum

### 1. Apakah ketiga task berjalan secara bersamaan atau bergantian? Jelaskan mekanismenya!

Ketiga task berjalan secara bergantian karena FreeRTOS menggunakan scheduler preemptive berbasis prioritas dan time-slicing, sehingga task dengan prioritas sama akan memperoleh giliran CPU secara bergiliran, sementara `vTaskDelay()` membuat task masuk ke kondisi Blocked agar CPU dapat digunakan oleh task lain sehingga tampak berjalan bersamaan.


### 2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!

Untuk menambahkan task keempat pada FreeRTOS, deklarasikan terlebih dahulu prototipe fungsi task baru, tambahkan `xTaskCreate()` di dalam `setup()` sebelum `vTaskStartScheduler()`, lalu buat implementasi fungsi task menggunakan perulangan `while(1)` dan `vTaskDelay()` agar penggunaan CPU tetap teratur.


### 3. Modifikasilah program dengan menambah sensor (potensiometer), lalu gunakan nilainya untuk mengontrol kecepatan LED! Bagaimana hasilnya?

link modifikasi : [https://wokwi.com/projects/463715247595730945](https://wokwi.com/projects/463734665312327681)
<img width="797" height="595" alt="image" src="https://github.com/user-attachments/assets/75b050e8-0c1a-4d1e-82c6-181a6e9f894a" />

### B. Kode Program Modifikasi

```cpp
#include <Arduino_FreeRTOS.h>

// Variabel global untuk mengatur kecepatan (delay) semua task
volatile int blinkDelay = 500; 

void TaskBlink1(void *pvParameters);
void TaskBlink2(void *pvParameters);
void Taskprint(void *pvParameters);
void TaskPotensio(void *pvParameters); // Task tambahan untuk baca sensor

void setup() {
  Serial.begin(9600);

  xTaskCreate(TaskBlink1, "task1", 128, NULL, 1, NULL);
  xTaskCreate(TaskBlink2, "task2", 128, NULL, 1, NULL);
  xTaskCreate(Taskprint, "task3", 128, NULL, 1, NULL);
  xTaskCreate(TaskPotensio, "task4", 128, NULL, 1, NULL); // Task keempat

  vTaskStartScheduler();
}

void loop() {}

// Task baca potensiometer untuk mengubah kecepatan
void TaskPotensio(void *pvParameters) {
  while(1) {
    int sensorValue = analogRead(A0);
    // Map nilai 0-1023 jadi delay 50ms - 1000ms
    blinkDelay = map(sensorValue, 0, 1023, 50, 1000); 
    vTaskDelay(100 / portTICK_PERIOD_MS); 
  }
}

void TaskBlink1(void *pvParameters) {
  pinMode(8, OUTPUT);
  while(1) {
    digitalWrite(8, HIGH);
    vTaskDelay(blinkDelay / portTICK_PERIOD_MS); // Pakai variabel blinkDelay
    digitalWrite(8, LOW);
    vTaskDelay(blinkDelay / portTICK_PERIOD_MS);
  }
}

void TaskBlink2(void *pvParameters) {
  pinMode(7, OUTPUT);
  while(1) {
    digitalWrite(7, HIGH);
    vTaskDelay((blinkDelay + 100) / portTICK_PERIOD_MS); // Variasi delay
    digitalWrite(7, LOW);
    vTaskDelay((blinkDelay + 100) / portTICK_PERIOD_MS);
  }
}

void Taskprint(void *pvParameters) {
  int counter = 0;
  while(1) {
    counter++;
    Serial.print("Counter: ");
    Serial.print(counter);
    Serial.print(" | Delay Aktif: ");
    Serial.println(blinkDelay);
    vTaskDelay(1000 / portTICK_PERIOD_MS); // Print tetap per 1 detik
  }
}

```
