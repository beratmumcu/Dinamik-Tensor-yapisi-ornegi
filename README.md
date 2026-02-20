# Dinamik-Tensor-yapisi-ornegi
# 🧠 TinyML Gömülü Tensör Motoru (Embedded Tensor Engine)

ESP32, Arduino (AVR/ARM) ve STM32 gibi **RAM kısıtlı mikrodenetleyiciler** üzerinde Yapay Zeka (AI) modellerini çalıştırmak için sıfırdan C diliyle geliştirilmiş, bellek dostu bir Tensör Yönetim ve Çıkarım (Inference) motorudur.

Bu proje, TensorFlow Lite for Microcontrollers (TFLM) kütüphanesinin kalbinde yatan **Quantization (Nicemleme)** ve **Dinamik Bellek Yönetimi** mimarisinin "Bare-metal" (donanıma yakın) ve sıfır bağımlılıklı bir simülasyonudur.

## 🚀 Özellikler

* **Sıfır Bağımlılık (Zero Dependency):** Sadece standart C kütüphaneleri (`stdio.h`, `stdlib.h`, `math.h`, `stdint.h`) kullanılmıştır. Dışarıdan hiçbir ML kütüphanesi gerektirmez.
* **ANSI C (C89) Uyumluluğu:** Dev-C++ dahil olmak üzere en eski mikrodenetleyici derleyicilerinde bile (eski GCC/MinGW) sorunsuz derlenir.
* **Hibrit Tensör Mimarisi:** Aynı `struct` yapısı içerisinde polimorfik olarak `FLOAT32` (Hassas Sensör Verisi), `FLOAT16` ve `INT8` (Sıkıştırılmış Ağırlıklar) barındırabilir.
* **%75 Bellek Tasarrufu:** Model ağırlıklarını 32-bit Float yerine 8-bit Integer (INT8) olarak saklayarak RAM kullanımını çeyreğine düşürür.
* **Otomatik De-Quantization:** Matematiksel işlemler sırasında INT8 verileri "On-the-fly" (anında) Float32'ye çevirerek işlemci dostu bir akış sunar.

## 🧩 Mimari ve Çalışma Mantığı

Proje, bellek yönetimini bir "Joker İşaretçi" (`void*`) ve Enum tabanlı tip etiketleme sistemi ile çözer.



### Quantization (Nicemleme) Nasıl Çalışır?
Gömülü sistemlerde büyük ondalıklı sayıları saklamak lükstür. Bu motor, sayıları saklarken şu formülü kullanır:
`Gerçek_Değer = (Kayıtlı_INT8 - Zero_Point) * Scale`

Böylece `2.5` gibi bir float değeri, `0.05` scale (ölçek) faktörü ile RAM'e sadece `50` (1 Byte) olarak yazılır.

## 🌡️ Örnek Uygulama: Yapay Zeka Destekli Akıllı Termostat

Proje içerisinde örnek bir "Dense Layer" (Tam Bağlı Katman) ve "Sigmoid Aktivasyon" fonksiyonu bulunmaktadır. Sistem, ortamın **Sıcaklık** ve **Nem** değerlerini okuyarak klimanın açılıp açılmayacağına karar veren basit bir *Perceptron* (Yapay Sinir Hücresi) çalıştırır.

### Modelin Karar Mekanizması:
* **Girdiler (Inputs):** Sıcaklık ve Nem (Sensörlerden gelir, `FLOAT32` formatındadır).
* **Ağırlıklar (Weights):** Eğitilmiş modelden gelir, bellekte `INT8` formatında sıkıştırılmış olarak tutulur.
* **İşlem:** `(Girdiler x Ağırlıklar) + Bias`
* **Çıktı:** Sigmoid fonksiyonu ile %0 ile %100 arası bir olasılık değeri (Olasılık > 0.5 ise Klima Açılır).

## 🛠️ Kurulum ve Derleme

Kod, standart bir C derleyicisine sahip herhangi bir IDE (Dev-C++, VS Code, CLion) veya terminal üzerinden derlenebilir.

**Terminal (GCC) ile derlemek için:**
```bash
gcc main.c -o tinyml_engine -lm
./tinyml_engine
