
# Percobaan 1: Analog to Digital Converter (ADC)

## Jawaban Pertanyaan Praktikum

### Soal 1: Apa fungsi perintah analogRead() pada rangkaian praktikum ini?

`analogRead()` membaca tegangan analog yang masuk pada pin A0 dari potensiometer, kemudian mengubahnya menjadi data digital 10-bit dalam rentang 0–1023. Nilai 0 
merepresentasikan tegangan sedangkan nilai 0V (GND), 1023 merepresentasikan tegangan 5V.

---

### Soal 2: Mengapa diperlukan fungsi map() dalam program tersebut?

Fungsi `analogRead()` menghasilkan nilai dalam rentang 0–1023 (ADC 10-bit), sementara fungsi `analogWrite()` hanya dapat menerima nilai 0–255 (PWM 8-bit), dan `myservo.write()` hanya menerima nilai 0–180 (derajat). Ketidaksesuaian rentang nilai antar domain tersebut tidak memungkinkan penggunaan langsung tanpa konversi. Fungsi `map()` hadir untuk menyelesaikan masalah ini dengan melakukan pemetaan linear secara proporsional dari satu rentang ke rentang lainnya tanpa membutuhkan perhitungan manual 

---

### Soal 3: Modifikasi program agar servo bergerak 30°–150°
Link simulasi Modifikasi: [https://www.tinkercad.com/things/c31qlPVEV9C-analog-to-digital-converter-adc](https://www.tinkercad.com/things/f5NLsSi87Vd-adc)
<img width="1115" height="755" alt="image" src="https://github.com/user-attachments/assets/b4fd56a4-9df6-414b-98e3-1ae3583c5cfc" />



```cpp
#include <Servo.h>

Servo myservo;

const int potensioPin = A0;
const int servoPin = 9;

int pos = 0;
int val = 0;

void setup() {
  myservo.attach(servoPin);
  Serial.begin(9600);
}

void loop() {
  // Baca ADC 10-bit lalu geser kanan 2 bit → 8-bit (0–255)
  val = analogRead(potensioPin) >> 2;

  // Map 8-bit (0–255) ke sudut servo (0–180)
  pos = map(val,
            0,    // nilai minimum ADC
            1023,  // nilai maksimum ADC
            30,    // sudut minimum servo
            150); // sudut maksimum servo

  myservo.write(pos);

  Serial.print("ADC Potensio: ");
  Serial.print(val);
  Serial.print(" | Sudut Servo: ");
  Serial.println(pos);

  delay(15);
}
```

