# Tüm İndirme Linkleri ve Kurulum Rehberi

## 🔗 TEMEL İNDİRMELER

### 1. ESP-IDF (Espressif IoT Development Framework)

**İndirme:**
```bash
git clone -b release/v5.0 https://github.com/espressif/esp-idf.git
```

**Kurulum:**
```bash
cd esp-idf
./install.sh esp32s3  # ESP32-S3 için
source export.sh
```

**Link:** https://github.com/espressif/esp-idf

**Dokümanlar:** https://docs.espressif.com/projects/esp-idf/en/latest/esp32s3/

---

### 2. Porcupine Wake Word Engine

**İndirme:**
```bash
git clone https://github.com/Picovoice/porcupine.git
```

**Link:** https://github.com/Picovoice/porcupine

**Önemli Klasörler:**
- `porcupine/sdk/c/` - C SDK
- `porcupine/sdk/esp32/` - ESP32 örnekleri
- `porcupine/demo/esp32/` - Demo projesi

**Uyandırma Kelimesi Oluştur:**
https://console.picovoice.ai/

**Adımlar:**
1. Siteye git
2. Giriş yap (GitHub hesabıyla)
3. "Custom Wake Words" seç
4. "Create New Wake Word" tıkla
5. "Hey Jarvis" veya istediğin kelimeyi gir
6. Hedef: ESP32 (ESP32-S3) seç
7. .ppn dosyasını indir

---

### 3. ESP-Skainet (Espressif Speech Recognition)

**İndirme:**
```bash
git clone --recursive https://github.com/espressif/esp-skainet.git
```

**Link:** https://github.com/espressif/esp-skainet

**Önemli Klasörler:**
- `esp-skainet/components/esp-sr/` - Ses tanıma motoru
- `esp-skainet/examples/` - Örnek projeler
- `esp-skainet/examples/en_speech_commands_recognition/` - İngilizce komut tanıma

**Modelleri İndir:**
https://github.com/espressif/esp-skainet/releases

**Komut Tanıma Modeli:**
```bash
cd esp-skainet
# release sayfasından MultiNet7 modelini indir
```

---

### 4. Python Paketleri

**esptool (Flash etmek için):**
```bash
pip install esptool
```

**pyserial (Seri iletişim):**
```bash
pip install pyserial
```

**Tüm gereklilikler:**
```bash
pip install esptool pyserial click pyyaml cryptography ecdsa
```

---

### 5. CMake ve Ninja

**macOS:**
```bash
brew install cmake ninja
```

**Ubuntu/Debian:**
```bash
sudo apt-get install cmake ninja-build
```

**Windows:**
- CMake: https://cmake.org/download/
- Ninja: https://github.com/ninja-build/ninja/releases

---

## 📋 ADIM ADIM KURULUM

### Adım 1: Klasör Hazırla

```bash
mkdir -p ~/esp32-projects
cd ~/esp32-projects
```

### Adım 2: ESP-IDF Kur

```bash
git clone -b release/v5.0 https://github.com/espressif/esp-idf.git
cd esp-idf
./install.sh esp32s3
source export.sh
cd ..
```

### Adım 3: Porcupine İndir

```bash
git clone https://github.com/Picovoice/porcupine.git
```

### Adım 4: ESP-Skainet İndir

```bash
git clone --recursive https://github.com/espressif/esp-skainet.git
```

### Adım 5: Python Paketlerini Kur

```bash
pip install esptool pyserial
```

### Adım 6: Uyandırma Kelimesi Oluştur

1. https://console.picovoice.ai/ aç
2. Giriş yap
3. Custom Wake Word oluştur
4. "Hey Jarvis" yazılsın
5. ESP32-S3 hedefini seç
6. .ppn dosyasını indir
7. Projeye koy

---

## 🎯 TAMAMLAMA KONTROLÜ

```bash
# ESP-IDF yüklü mü?
idf.py --version

# Python paketleri yüklü mü?
pip list | grep esptool

# Git kurulu mu?
git --version

# CMake kurulu mu?
cmake --version
```

Tüm komutlar çıktı verirse hazırsın!

---

## 🔐 ÖNEMLİ NOTLAR

1. **Porcupine .ppn dosyası**: Picovoice Console'dan indirmen gerekir
2. **ESP32-S3 modeli seçini**: ESP32 (ESP32-S3) seçmeyi unutma
3. **ESP-IDF PATH**: `source export.sh` her terminal açışında çalıştır
4. **Port**: `/dev/ttyUSB0` (Linux) veya `/dev/cu.usbserial-*` (macOS)

---

## 📞 SORUN GİDERME

### "idf.py: command not found"
```bash
source ~/esp32-projects/esp-idf/export.sh
```

### "libpv_porcupine.a not found"
- Porcupine'ı doğru klasöre kopyaladığını kontrol et
- CMakeLists.txt'te path'i kontrol et

### "Serial port not found"
```bash
# Port bul
lsof /dev/tty* # macOS
ls /dev/ttyUSB* # Linux
```

### "Model file not found"
- Modeli `main/` altında veya `SPIFFS`'e yükle
- Path'i code'da doğru yazığını kontrol et

---

## 🚀 SONRAKİ ADIM

Tüm bu indirmeler yapıldıktan sonra, `CODEX_AUTOMATION_PROMPT.md` dosyasındaki prompt'u Code X'e yapıştırarak otomatik proje oluşturmaya başla!
