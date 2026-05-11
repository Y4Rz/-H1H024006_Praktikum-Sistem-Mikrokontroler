# Percoaan 2: Pulse Width Modulation (PWM)

## Jawaban Pertanyaan Praktikum

### Soal 1: Jelaskan mengapa LED dapat diatur kecerahannya menggunakan fungsi analogWrite()!

karena Fungsi `analogWrite()` digunakan untuk menghasilkan sinyal PWM pada pin digital Arduino dengan mengatur duty cycle dari nilai 0–255 sehingga LED dapat tampak redup hingga terang secara bertahap melalui kedipan sangat cepat yang tidak terlihat oleh mata manusia.

---

### Soal 2: Apa hubungan antara nilai ADC (0–1023) dan nilai PWM (0–255)?

Karena ADC Arduino memiliki resolusi 10-bit (0–1023) sedangkan `analogWrite()` menggunakan resolusi 8-bit (0–255), maka nilai ADC perlu dipetakan secara linear menggunakan fungsi `map()` agar setiap kenaikan sekitar 4 nilai ADC setara dengan kenaikan 1 nilai PWM.

```
PWM = (ADC / 1023) × 255
```
---

### Soal 3: Modifikasi program agar LED hanya menyala pada rentang PWM 50–200
Link simulasi Modifikasi: [https://www.tinkercad.com/things/bNjO7M68HYG-pulse-width-modulation-pwm](https://www.tinkercad.com/things/gOaZBV13O0a-pwm)
<img width="1139" height="525" alt="image" src="https://github.com/user-attachments/assets/e8b94281-12c6-4ed8-95c9-e89e5fe958e4" />


```cpp
const int potPin = A0;
const int ledPin = 9;

int nilaiADC = 0;
int pwm = 0;

void setup() {
  pinMode(ledPin, OUTPUT);
  Serial.begin(9600);
}

void loop() {
  nilaiADC = analogRead(potPin);

  pwm = map(nilaiADC,
            0,    // minimum ADC
            1023, // maksimum ADC
            50,    // PWM minimum
            200); // PWM maksimum

  analogWrite(ledPin, pwm);

  Serial.print("ADC: ");
  Serial.print(nilaiADC);
  Serial.print(" | PWM: ");
  Serial.println(pwm);

  delay(50);
}
```

