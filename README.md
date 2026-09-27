# 💡 Control de Iluminación por Gestos de la Mano

## 👤 Autor

**Oscar Junco**

---

## 📌 Descripción

Este proyecto consiste en el desarrollo de un sistema de **control de iluminación mediante gestos de la mano**, utilizando una cámara web, Python, MediaPipe y un ESP32.

El sistema permite controlar la intensidad de **tres LEDs de 5 mm** mediante diferentes gestos realizados frente a la cámara.

Python se encarga de detectar la posición de la mano y reconocer el gesto, mientras que el ESP32 recibe las instrucciones mediante comunicación serial y controla los LEDs utilizando **PWM**.

Además del control de iluminación, se implementaron dos modos de funcionamiento especiales mediante los gestos de **pulgar hacia abajo** y **pulgar hacia arriba**.

---

# 🎯 Objetivo

Desarrollar un sistema de iluminación interactivo capaz de interpretar gestos de la mano mediante visión artificial y convertirlos en órdenes de control para un ESP32.

El sistema integra:

- Visión artificial.
- Reconocimiento de gestos.
- Comunicación serial.
- Control PWM.
- Microcontroladores.
- Automatización de iluminación.

---

# ⚙️ Arquitectura general del sistema

```text
             ┌─────────────────────┐
             │      CÁMARA WEB     │
             └──────────┬──────────┘
                        │
                        ▼
             ┌─────────────────────┐
             │       PYTHON        │
             │                     │
             │      OpenCV         │
             │     MediaPipe       │
             └──────────┬──────────┘
                        │
                 Reconocimiento
                    de gesto
                        │
                        ▼
             ┌─────────────────────┐
             │ COMUNICACIÓN SERIAL │
             │        USB          │
             └──────────┬──────────┘
                        │
                        ▼
             ┌─────────────────────┐
             │        ESP32        │
             │                     │
             │        PWM          │
             └──────┬──┬──┬───────┘
                    │  │  │
                    ▼  ▼  ▼
                   LED1 LED2 LED3
