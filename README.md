# Praktikum Mikrokontroler - Modul VI: Interrupt dan Timer


**Nama:** Ibnu Abbas  
**NIM:** H1H024038  
**Mata Kuliah:** TK244005 - Praktikum Mikrokontroler  
**Program Studi:** Informatika, Universitas Jenderal Soedirman  

---

## 📌 Deskripsi Repositori
Repositori ini berisi source code, skematik rangkaian, dan dokumentasi hasil praktikum Modul 3 yang berfokus pada implementasi protokol komunikasi serial menggunakan antarmuka UART (Universal Asynchronous Receiver-Transmitter) dan I2C (Inter-Integrated Circuit) pada platform mikrokontroler Arduino Uno.

Praktikum ini dirancang untuk memahami bagaimana mikrokontroler dapat menerima komando interaktif dari antarmuka komputer melalui Serial Monitor (UART), serta bagaimana membaca nilai sensor analog (potensiometer) dan mentransmisikan datanya secara sinkron ke dalam modul display eksternal seperti LCD I2C.

---
## 🔬 Analisis Percobaan 1 (ADC)
### 1.Jelaskan proses bagaimana tombol dapat mengubah kondisi LED menggunakan interrupt!
Saat penekanan tombol dilakukan, level tegangan pada pin 2 bertransisi dari kondisi HIGH menuju LOW (sisi FALLING) akibat konfigurasi mode INPUT_PULLUP. Fluktuasi sinyal tersebut menginisiasi interupsi yang menyebabkan unit pemrosesan menghentikan eksekusi rutin utama guna memproses fungsi ISR tombolInterrupt() secara langsung. Dalam prosedur tersebut, status variabel ledState dibalikkan nilainya secara logika. Pasca penyelesaian instruksi pada ISR, sistem kembali menjalankan alur kerja utama di mana perintah digitalWrite(13, ledState) pada bagian loop() akan melakukan pemutakhiran visual pada LED sesuai dengan data terbaru dalam ledState.

### 2. Bagaimana cara menambahkan task keempat? Jelaskan langkahnya!
Fungsi attachInterrupt() digunakan untuk melakukan registrasi atau menghubungkan rutinitas layanan interupsi (ISR) ke pin fisik tertentu dengan penentuan mode pemicuan yang spesifik. Melalui baris kode berikut:
attachInterrupt(digitalPinToInterrupt(2), tombolInterrupt, FALLING);
Sistem mikrokontroler akan mengeksekusi fungsi tombolInterrupt() secara otomatis apabila terdeteksi adanya perubahan level logika dari tinggi ke rendah pada pin 2. Eksistensi perintah attachInterrupt() menjadi krusial agar perangkat dapat mengidentifikasi subrutin yang tepat saat terjadi peristiwa interupsi.
### 3.
Pelaksanaan Interrupt Service Routine (ISR) menuntut efisiensi waktu yang sangat tinggi, mengingat aktivitas program utama maupun interupsi lainnya akan terhenti selama ISR berlangsung. Terdapat dua argumen teknis yang mendasari hal tersebut:
Fungsi delay() bergantung pada pemanfaatan timer internal yang operasionalnya terhenti di dalam lingkup ISR, sehingga berisiko mengakibatkan sistem mengalami kegagalan proses atau kondisi hang.
Instruksi Serial.print() bekerja melalui mekanisme buffer serta interupsi UART secara spesifik. Penggunaannya dalam ISR dapat memicu deadlock dikarenakan interupsi komunikasi tersebut tidak dapat dieksekusi secara simultan dengan ISR yang sedang aktif.
### 4.Apa fungsi keyword volatile pada variabel ledState?
Penerapan kualifikasi volatile berfungsi menginstruksikan kompilator bahwa status data pada variabel ledState bersifat fluktuatif dan dapat dimodifikasi secara asinkronus di luar eksekusi alur utama, yakni melalui mekanisme ISR. Hal ini krusial guna mengeliminasi optimasi penyimpanan pada register internal yang berisiko mengabaikan pembaruan nilai riil. Tanpa penggunaan atribut volatile tersebut, rutinitas loop() berpotensi terus merujuk pada data statis dalam memori cache, sehingga mengakibatkan kegagalan sistem dalam merespons aktivasi saklar tekan secara akurat.
### 3.   Modifikasi: Mode Interrupt RISING, CHANGE, dan LOW
```#include <Arduino.h>

volatile bool ledState = false;

void tombolInterrupt() {
  ledState = !ledState; // Toggle LED saat tombol dilepas
}

void setup() {
  pinMode(13, OUTPUT);
  pinMode(2, INPUT_PULLUP);
  // Interrupt dipicu saat sinyal naik dari LOW ke HIGH (tombol dilepas)
  attachInterrupt(digitalPinToInterrupt(2), tombolInterrupt, RISING);
}

void loop() {
  digitalWrite(13, ledState);
}
```




---

## 📸 Dokumentasi Praktikum
### 1. Dokumentasi Percobaan
Percobaan 1:
![Lampiran percobaan 1 Praktikum](587878913-d09394eb-b248-451f-9853-12f4221aa1a4.jpg)
Percobaan 2: 
![Lampiran percobaan 2 Praktikum](587879293-8a848cbe-2a8f-4c07-9303-132b897876d6.jpg)


