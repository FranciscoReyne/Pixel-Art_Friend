# Pixel Art Friend
Una app para Pixel Panels genérico (típicamente de China), ¡con el que podremos interactuar!  ¡¡ :D !!

--- 

SI tienes uno de esos Pixel Paneles genéricos que imitan a los **Divoom** o se comunican por **Bluetooth BLE** con protocolo cerrado… **no significa que no se pueda hackear** para este proyecto.

Vamos a hacer lo siguiente por etapas simples para avanzar:

---

## 🧪 ETAPA 1: Identificar cómo se comunica tu pantalla

Necesitamos saber si se puede controlar **desde afuera de la app "idotmatrix"**. Para eso:

### 🔍 Instala una app espía de Bluetooth en tu celular:

#### En Android:
1. Instala **[nRF Connect (Play Store)](https://play.google.com/store/apps/details?id=no.nordicsemi.android.mcp)**


<p align="center">
  
<img src="nrf connect for mobile.png" alt="nRF Connect" width="1278"/>

</p>

2. Enciende tu pantalla
3. Abre nRF Connect y escanea dispositivos
4. Busca algo como `DM-XXXX`, `LED16`, `Pixoo`, `Display`, etc.
5. Toca ese dispositivo y explora:
   - **Services** y **Characteristics**
   - Si puedes ver características con `Write` o `Notify`, ¡es buena señal!

> Si puedes, mándame una captura de eso o copia los UUIDs que veas (por ejemplo `0000ffe1-0000-1000-8000-00805f9b34fb`).

---

## 🖥️ ETAPA 2: Hacemos un código en Python que le hable

Si vemos que es posible mandar datos por BLE (Bluetooth Low Energy), puedo ayudarte con un ejemplo en Python usando la librería `bleak`:

```bash
pip install bleak
```

Y podríamos hacer algo como esto:

```python
import asyncio
from bleak import BleakClient

# UUID de ejemplo, este cambia según el dispositivo
CHARACTERISTIC_UUID = "0000ffe1-0000-1000-8000-00805f9b34fb"
ADDRESS = "XX:XX:XX:XX:XX:XX"  # Dirección MAC del dispositivo

async def send_data():
    async with BleakClient(ADDRESS) as client:
        if client.is_connected:
            print("Conectado a la pantalla")
            # Mandamos algo simple (ej: texto o comando)
            await client.write_gatt_char(CHARACTERISTIC_UUID, b'\x01\x02\x03...')

asyncio.run(send_data())
```

🔧 Este código lo adaptamos cuando sepamos cómo la pantalla espera recibir los datos.

---

## 🔊 ETAPA 3: Usamos el micrófono (Python)

Podemos detectar tu voz o el ritmo de la música con este código base:

```python
import sounddevice as sd
import numpy as np

def callback(indata, frames, time, status):
    volume_norm = np.linalg.norm(indata) * 10
    print("Volumen:", int(volume_norm))
    if volume_norm > 5:
        print("¡Detectada voz o sonido!")

stream = sd.InputStream(callback=callback)
with stream:
    input("Hablá al micrófono. Presioná Enter para salir...\n")
```

Luego, cada vez que detecte sonido fuerte, podríamos:
- Generar una imagen 16x16 (`numpy`, `Pillow`)
- Convertirla a bytes
- Mandarla por Bluetooth a la pantalla

---

## BONUS: Si no se puede controlar por Bluetooth...

Entonces podríamos **usar una Raspberry Pi con su propia pantalla LED 16x16**, y controlarla directamente con Python + micrófono.

Te puedo recomendar una pantalla LED como la **WS2812 (NeoPixel)** con un ESP32 o Pi Pico, que permite libertad total para este tipo de proyectos.

---
