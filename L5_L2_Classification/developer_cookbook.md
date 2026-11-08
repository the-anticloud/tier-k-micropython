# Developer Cookbook — K_MICROPYTHON
**Stack:** MicroPython 1.22+, MQTT, Anticloud edge API (REST over WiFi), AIOSS_FORMAT (micro edition)
**Domain:** MicroPython: Python runtime for embedded IoT hardware in Anticloud

## Anticloud IoT node (MicroPython)
```python
# main.py on ESP32 (MicroPython)
from k_micropython_anticloud import AnticloudNode

node = AnticloudNode(
    node_id="sensor_bay_01",
    mqtt_broker="192.168.1.100",
    aioss_chain_path="/aioss.bin"  # on SPIFFS
)

while True:
    reading = node.read_sensor(pin=34, type="temperature")
    node.publish(reading)
    node.aioss_append(reading)  # micro-AIOSS chain
    node.sleep_ms(60_000)
```

## Receive PAX inference result
```python
@node.on_message("pax/response")
def handle_pax(msg):
    if msg.anomaly:
        node.trigger_alarm()
```

## AIOSS Chain Append
```python
import hashlib, time

def aioss_append(chain_path, payload: bytes, module_id: str):
    entry_hash = hashlib.sha3_256(payload).digest()
    ts = int(time.time_ns()).to_bytes(8, 'big')
    with open(chain_path, 'rb') as f:
        f.seek(-32, 2); prev_hash = f.read(32)
    new_hash = hashlib.sha3_256(prev_hash + entry_hash + ts).digest()
    with open(chain_path, 'ab') as f:
        f.write(ts + entry_hash + new_hash)
    return new_hash.hex()
```
