# Deploy Guide — K_MICROPYTHON
**Tier:** TIER_9_ROBOTICS_IOT | **Stack:** MicroPython 1.22+, MQTT, Anticloud edge API (REST over WiFi), AIOSS_FORMAT (micro edition)
**Air-gap capable after initial setup.**

## Prerequisites
MicroPython 1.22+, MQTT broker. ESP32, Raspberry Pi Pico W, or STM32F4. SPIFFS/LittleFS.

## Environment
ESP32-WROOM or Raspberry Pi Pico W. MicroPython firmware flashed. 256KB+ RAM. WiFi required for MQTT.

## AIOSS Integration
```bash
aioss init --module K_MICROPYTHON --output ./k_micropython.aioss
aioss append --chain ./k_micropython.aioss --payload ./output.bin --module K_MICROPYTHON
aioss verify --chain ./k_micropython.aioss
```

## Air-Gap Setup
```bash
pip download -r requirements.txt -d ./wheels/
pip install --no-index --find-links ./wheels/ -r requirements.txt
```

## PAX 27B Harness Wiring
```python
from anticloud_pax import PAXHarness
harness = PAXHarness(
    model_path="./pax-27b-q4.gguf",
    module="K_MICROPYTHON",
    aioss_chain="./K_MICROPYTHON.aioss",
    classification="L5_NARROW_L2_GENERAL"
)
result = harness.process(input_data)
```

## Verification
```bash
aioss verify --chain ./K_MICROPYTHON.aioss --verbose
python -m K_MICROPYTHON.tests.smoke
```
