# ESP32-S3 Jarvis Offline Voice Assistant - Code X Automation Prompt

## KULLAN BU PROMPT'U CODE X'E KOPYALA-YAPIŞDIR:

```
Sen bir dağıtık yazılım uzmanı ve ESP32-S3 firmware otomasyon asistanısın. 
Görevin: Tam çevrimdışı, internet bağlantısı gerektirmeyen, ESP32-S3 üzerinde 
çalışan bir Jarvis benzeri ses asistanı kurmak.

=== AÇIK HEDEF ===
- ESP32-S3 üzerinde çalışır
- Tam çevrimdışı (internet yok)
- Mikrofon ile sürekli dinleme
- Porcupine ile uyandırma kelimesi algılama
- ESP-Skainet ile komut tanıma
- Tanınan komutlara göre GPIO/LED/sensör kontrolü
- Çalışır, derlenebilir, temiz ve açıklayıcı proje

=== BAŞLANGIÇ KOŞULLARI ===
- Bilgisayar: Linux/macOS/Windows PowerShell
- İnternet: Var (indirmeler için)
- Proje adı: esp32-s3-jarvis-offline

=== ADIM 1: ÇEVRE HAZIRLIĞI ===

1.1) ESP-IDF Kurulumu
   Komutlar:
   ```bash
   mkdir -p ~/esp32-dev
   cd ~/esp32-dev
   git clone -b release/v5.0 https://github.com/espressif/esp-idf.git
   cd esp-idf
   ./install.sh
   source export.sh
   ```

1.2) Porcupine SDK İndirme
   ```bash
   cd ~/esp32-dev
   git clone https://github.com/Picovoice/porcupine.git
   cd porcupine
   # C SDK dosyalarını not et
   ```

1.3) ESP-Skainet İndirme
   ```bash
   cd ~/esp32-dev
   git clone --recursive https://github.com/espressif/esp-skainet.git
   ```

1.4) Python Kütüphaneleri
   ```bash
   pip install esptool pyserial
   ```

=== ADIM 2: PROJE KLASÖR YAPISI ===

Bu yapıyı oluştur:
```
esp32-s3-jarvis-offline/
├── CMakeLists.txt
├── sdkconfig.defaults
├── README.md
├── main/
│   ├── CMakeLists.txt
│   ├── main.c
│   ├── porcupine_handler.c
│   ├── porcupine_handler.h
│   ├── skainet_handler.c
│   ├── skainet_handler.h
│   ├── action_handler.c
│   ├── action_handler.h
│   ├── i2s_mic.c
│   ├── i2s_mic.h
│   └── Kconfig.projbuild
├── components/
│   ├── porcupine/
│   │   ├── include/
│   │   ├── lib/
│   │   └── CMakeLists.txt
│   ├── esp-sr/
│   │   ├── include/
│   │   └── CMakeLists.txt
│   └── spiffs/
│       └── CMakeLists.txt
└── tools/
    └── download_models.py
```

=== ADIM 3: KRİTİK DOWNLOAD LİNKLERİ ===

3.1) Porcupine SDK (C)
   - GitHub: https://github.com/Picovoice/porcupine
   - ESP32 SDK klasörü: porcupine/sdk/esp32/
   - İlgili dosyalar:
     - porcupine/sdk/include/pv_porcupine.h
     - porcupine/sdk/lib/libpv_porcupine.a (ESP32-S3 için)

3.2) ESP-Skainet
   - GitHub: https://github.com/espressif/esp-skainet
   - Modeller: https://github.com/espressif/esp-skainet/releases
   - İndir: en_speech_commands_recognition model (MultiNet7)
   - Dosyalar:
     - esp-skainet/components/esp-sr/model_path/

3.3) Uyandırma Kelimesi (.ppn dosyası)
   - Picovoice Console: https://console.picovoice.ai/
   - Adımlar:
     a) Siteye giriş yap
     b) "Create access key" seç
     c) "Custom Wake Words" git
     d) "Hey Jarvis" için yeni kelime oluştur
     e) Hedef: "ESP32 (ESP32)"
     f) .ppn dosyasını indir

3.4) ESP-IDF v5.0
   - GitHub: https://github.com/espressif/esp-idf
   - Branch: release/v5.0
   - Komut:
     ```bash
     git clone -b release/v5.0 https://github.com/espressif/esp-idf.git
     ```

=== ADIM 4: DONANIM KONFIGÜRASYONU ===

4.1) I2S Mikrofon Pinleri (ESP32-S3)
```c
// İnceleme kontrol listesi:
#define I2S_BCLK_PIN    8    // GPIO 8
#define I2S_LRCLK_PIN   6    // GPIO 6
#define I2S_DOUT_PIN    2    // GPIO 2 (çıkış-hoparlör)
#define I2S_DIN_PIN     9    // GPIO 9 (giriş-mikrofon)
#define I2S_SAMPLE_RATE 16000
#define I2S_CHANNEL     I2S_CHANNEL_MONO
#define I2S_BITS_PER_SAMPLE I2S_BITS_PER_SAMPLE_16BIT
```

4.2) GPIO Tanımları
```c
#define LED_PIN         10   // LED kontrol
#define BUTTON_PIN      11   // Opsiyonel buton
```

=== ADIM 5: KODLAMA ===

5.1) main/CMakeLists.txt
```cmake
idf_component_register(
    SRCS "main.c" "porcupine_handler.c" "skainet_handler.c" 
         "action_handler.c" "i2s_mic.c"
    INCLUDE_DIRS "."
    REQUIRES esp_idf driver spi_flash fatfs
)
```

5.2) CMakeLists.txt (Kök)
```cmake
cmake_minimum_required(VERSION 3.5)
include($ENV{IDF_PATH}/tools/cmake/project.cmake)
project(esp32-s3-jarvis-offline)
```

5.3) sdkconfig.defaults
```
CONFIG_ESP_CONSOLE_UART_BAUDRATE=115200
CONFIG_ESP_CONSOLE_USB_SERIAL_JTAG=y
CONFIG_IDF_TARGET="esp32s3"
CONFIG_SPIRAM=y
CONFIG_SPIRAM_MODE_OCT=y
CONFIG_I2S_ENABLED=y
CONFIG_FREERTOS_HZ=1000
CONFIG_MALLOC_ALWAYSINCLUDE_SPIRAM_BUFFER=y
```

5.4) main/main.c (ÖZETİ)
- i2s_init() fonksiyonu
- porcupine_init() fonksiyonu
- skainet_init() fonksiyonu
- porcupine_task() - sürekli dinleme
- skainet_task() - komut tanıma
- handle_command() - aksiyonlar
- app_main() - başlangıç

=== ADIM 6: PORCUPINE ENTEGRASYONU ===

6.1) Header dosyasını ekle
```c
#include "pv_porcupine.h"
```

6.2) İnit kodu
```c
pv_porcupine_init(
    "libpv_porcupine_params.pv",
    1,
    &keyword_file_path,
    NULL,
    &handle);
```

6.3) Process loop
```c
while(true) {
    i2s_read(...);
    pv_porcupine_process(handle, pcm_buffer, &keyword_index);
    if (keyword_index >= 0) {
        // Uyandırma kelimesi tespit edildi!
    }
}
```

=== ADIM 7: ESP-SKAINET ENTEGRASYONU ===

7.1) Header dosyası
```c
#include "esp_sr_iface.h"
#include "esp_sr_models.h"
```

7.2) İnit
```c
esp_sr_iface_t *sr = esp_sr_create_from_preset(
    SR_LANG_ENGLISH, 
    SR_MODE_HIGH_PERF);
```

7.3) Komut tanıma
```c
int cmd_id = sr->detect(sr, pcm_buffer);
if (cmd_id >= 0) {
    handle_command(cmd_id);
}
```

=== ADIM 8: KOMUT TANIMLARI ===

```c
typedef enum {
    CMD_LIGHT_ON = 1,
    CMD_LIGHT_OFF = 2,
    CMD_STATUS = 3,
    CMD_TEMPERATURE = 4,
    CMD_FAN_ON = 5,
    CMD_FAN_OFF = 6,
} command_id_t;

void handle_command(int cmd_id) {
    switch(cmd_id) {
        case CMD_LIGHT_ON:
            gpio_set_level(LED_PIN, 1);
            printf("LED AÇIK\n");
            break;
        case CMD_LIGHT_OFF:
            gpio_set_level(LED_PIN, 0);
            printf("LED KAPALI\n");
            break;
        // ... diğer komutlar
    }
}
```

=== ADIM 9: DERLEME ===

9.1) Hedef ayarla
```bash
cd ~/esp/esp32-s3-jarvis-offline
idf.py set-target esp32s3
```

9.2) Build yapılandırması
```bash
idf.py build
```

9.3) Hata çözümü
- Dosya bulunamıyor → path kontrol et
- Kütüphane hatası → CMakeLists.txt'i kontrol et
- Derleme hatası → kod söz dizimini kontrol et
- Tekrar dene: idf.py fullclean && idf.py build

=== ADIM 10: FLASH İŞLEMİ ===

10.1) Port bulma
```bash
ls /dev/ttyUSB* # Linux
# veya
ls /dev/cu.* # macOS
```

10.2) Flash
```bash
idf.py -p /dev/ttyUSB0 flash
```

10.3) Monitor
```bash
idf.py -p /dev/ttyUSB0 monitor
```

=== ADIM 11: TEST ===

- Seri monitörü açık tut
- "Hey Jarvis" söyle
- Komut tanındı mı kontrol et
- LED yanıp sönüyor mu?

=== DOSYALAR ===

Üretilecek dosyalar:
1. CMakeLists.txt
2. sdkconfig.defaults
3. main/CMakeLists.txt
4. main/main.c (450+ satır)
5. main/porcupine_handler.c/h
6. main/skainet_handler.c/h
7. main/action_handler.c/h
8. main/i2s_mic.c/h
9. README.md
10. Konfigürasyon notları

=== KURALLAR ===

✓ Sadece gerçek, derlenebilir, çalışır kod
✓ Hatalar oluşursa sen çöz
✓ Tüm linkler geçerli ve doğru
✓ Adım adım ilerle
✓ Sonunda çalışır proje teslim et
✓ README.md'de kurulum adımlarını yaz

=== BAŞLA ===

Şimdi başla ve:
1. Tüm klasörleri oluştur
2. Tüm dosyaları yazıl
3. Derlemeyi yap
4. Hataları çöz
5. Çalışır projeyi teslim et
```

---

## İndirmeler Özeti

| Bileşen | Link | Açıklama |
|---------|------|----------|
| ESP-IDF v5.0 | https://github.com/espressif/esp-idf/tree/release/v5.0 | Geliştirme ortamı |
| Porcupine SDK | https://github.com/Picovoice/porcupine | Uyandırma kelimesi |
| ESP-Skainet | https://github.com/espressif/esp-skainet | Konuşma tanıma |
| Uyandırma Kelimesi | https://console.picovoice.ai/ | .ppn dosyası oluştur |
| Skainet Modeli | https://github.com/espressif/esp-skainet/releases | MultiNet7 modeli |
| esptool | `pip install esptool` | Flash aracı |

---

## Nasıl Kullanılır

1. Tüm prompt'u kopyala
2. Code X'e yapıştır
3. Çalıştır
4. Sonuç: Tamamen hazır, derlenen, çalışan ESP32-S3 Jarvis projesi
