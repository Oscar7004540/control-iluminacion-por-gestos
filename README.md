# 💡 Control de Iluminación por Gestos de la Mano

<p align="center">

**Sistema de control de iluminación mediante visión artificial, MediaPipe, Python y ESP32**

</p>

---

## 👤 Autor

**Oscar Junco**

---

## 📌 Descripción del proyecto

Este proyecto consiste en el desarrollo de un sistema de **control de iluminación mediante gestos de la mano**, utilizando una cámara web, Python, MediaPipe y un ESP32.

El sistema permite controlar la intensidad de **tres LEDs de 5 mm** mediante diferentes gestos realizados frente a una cámara.

Python se encarga del procesamiento de la imagen y del reconocimiento de los gestos mediante **MediaPipe Hand Landmarker**. Una vez identificado el gesto, Python envía un comando mediante comunicación serial al ESP32.

El ESP32 interpreta el comando recibido y controla los tres LEDs mediante **PWM (Pulse Width Modulation)**.

Además del control de intensidad, se implementaron dos modos especiales de iluminación mediante los gestos de **pulgar hacia abajo** y **pulgar hacia arriba**.

---

# 🎯 Objetivo

Desarrollar un sistema de iluminación interactivo capaz de interpretar gestos de la mano mediante visión artificial y convertirlos en órdenes de control para un ESP32.

El proyecto integra diferentes áreas:

- Visión artificial.
- Procesamiento de imágenes.
- Reconocimiento de gestos.
- Comunicación serial.
- Microcontroladores.
- Control PWM.
- Automatización.
- Programación en Python y C++.

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
                               ▼
                    ┌─────────────────────┐
                    │ RECONOCIMIENTO DEL  │
                    │       GESTO         │
                    └──────────┬──────────┘
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
```

---

# ✋ Gestos implementados

El sistema reconoce cinco gestos principales.

| Gesto | Función | Comando |
|---|---|---|
| ✊ Puño cerrado | Iluminación al 30 % | `30` |
| ✌️ Dos dedos | Iluminación al 70 % | `70` |
| 🖐️ Mano abierta | Iluminación al 100 % | `100` |
| 👎 Pulgar abajo | Modo 1 | `M1` |
| 👍 Pulgar arriba | Modo 2 | `M2` |

---

## ✊ Puño cerrado — 30 %

Cuando se detecta un puño cerrado, los tres LEDs funcionan aproximadamente al **30 % de intensidad mediante PWM**.

```text
✊
 ↓
30
 ↓
ESP32
 ↓
PWM ≈ 30 %
 ↓
LED1 + LED2 + LED3
```

---

## ✌️ Dos dedos — 70 %

Cuando se detectan el dedo índice y el dedo medio levantados, los tres LEDs funcionan aproximadamente al **70 %**.

```text
✌️
 ↓
70
 ↓
ESP32
 ↓
PWM ≈ 70 %
 ↓
LED1 + LED2 + LED3
```

---

## 🖐️ Mano abierta — 100 %

Cuando los cuatro dedos analizados están levantados, el sistema interpreta una mano abierta.

```text
🖐️
 ↓
100
 ↓
ESP32
 ↓
PWM = 100 %
 ↓
LED1 + LED2 + LED3
```

---

## 👎 Pulgar abajo — Modo 1

El gesto de pulgar hacia abajo activa el **Modo 1**.

Los LEDs se encienden secuencialmente:

```text
LED 1
  ↓
LED 2
  ↓
LED 3
  ↓
APAGADO
```

Cada LED permanece encendido aproximadamente **300 ms**.

---

## 👍 Pulgar arriba — Modo 2

El gesto de pulgar hacia arriba activa el **Modo 2**.

Los tres LEDs realizan una secuencia de encendido y apagado:

```text
TODOS ENCENDIDOS
        ↓
     APAGADOS
        ↓
TODOS ENCENDIDOS
        ↓
     APAGADOS
```

Los tiempos utilizados son:

- Encendido: 500 ms
- Apagado: 300 ms
- Segundo encendido: 500 ms

---

# 🧰 Materiales

## Hardware

- ESP32
- Computador
- Cámara web
- 3 LEDs de 5 mm
- 3 resistencias de 220 Ω
- Protoboard
- Cables jumper
- Cable USB para ESP32

---

# 🔌 Conexiones

Los tres LEDs se conectan a los siguientes GPIO del ESP32.

| Componente | GPIO | Resistencia |
|---|---:|---:|
| LED 1 | GPIO 25 | 220 Ω |
| LED 2 | GPIO 26 | 220 Ω |
| LED 3 | GPIO 27 | 220 Ω |

### Conexión

```text
                 ESP32

GPIO 25 ─── 220 Ω ───► LED 1 ───► GND

GPIO 26 ─── 220 Ω ───► LED 2 ───► GND

GPIO 27 ─── 220 Ω ───► LED 3 ───► GND
```

> ⚠️ Las resistencias de 220 Ω se utilizan para limitar la corriente de los LEDs.

---

# 💻 Software utilizado

| Software / Librería | Función |
|---|---|
| Python 3.13 | Programación principal |
| OpenCV | Captura y procesamiento de video |
| MediaPipe | Detección de la mano |
| PySerial | Comunicación serial |
| Arduino IDE | Programación del ESP32 |
| ESP32 Arduino Core | Control del microcontrolador |

---

# 🖐️ Detección de la mano con MediaPipe

El proyecto utiliza **MediaPipe Hand Landmarker** para detectar la mano.

El modelo proporciona **21 puntos de referencia (landmarks)**.

Estos puntos permiten conocer la posición de diferentes partes de la mano.

```text
                    8
                    ●
                    │
                    6
                    ●

             12 ●
                │
             10 ●

       16 ●
          │
       14 ●

  20 ●
     │
  18 ●

       4 ●
         \
          ● 2
```

Algunos puntos importantes utilizados por el programa son:

| Landmark | Parte de la mano |
|---:|---|
| 2 | Base del pulgar |
| 4 | Punta del pulgar |
| 6 | Articulación del índice |
| 8 | Punta del índice |
| 10 | Articulación del dedo medio |
| 12 | Punta del dedo medio |
| 14 | Articulación del anular |
| 16 | Punta del anular |
| 18 | Articulación del meñique |
| 20 | Punta del meñique |

---

# 🧠 Lógica de reconocimiento

Para reconocer los gestos se analizan principalmente los dedos:

```text
Índice
Medio
Anular
Meñique
```

Para cada dedo se comparan las coordenadas verticales de la punta y de la articulación.

Por ejemplo:

```python
mano[8].y < mano[6].y
```

indica que el dedo índice se encuentra levantado.

---

## Reconocimiento del puño

```text
Índice  = 0
Medio   = 0
Anular  = 0
Meñique = 0
```

Resultado:

```text
30
```

---

## Reconocimiento de dos dedos

```text
Índice  = 1
Medio   = 1
Anular  = 0
Meñique = 0
```

Resultado:

```text
70
```

---

## Reconocimiento de mano abierta

```text
Índice  = 1
Medio   = 1
Anular  = 1
Meñique = 1
```

Resultado:

```text
100
```

---

## Reconocimiento del pulgar

Para determinar la posición del pulgar se analiza el desplazamiento vertical de la punta respecto a su base.

```text
Pulgar arriba
       ↑
       │
       ●
       │
       ●
      base
```

```text
Pulgar abajo
      base
       ●
       │
       ●
       │
       ↓
```

Los resultados utilizados son:

```text
ARRIBA
ABAJO
CENTRO
```

---

# 🔄 Comunicación entre Python y ESP32

La comunicación se realiza mediante el puerto serial USB.

Configuración utilizada:

```text
Puerto: COM7
Baudrate: 115200
```

Los comandos enviados desde Python son:

| Comando | Función |
|---|---|
| `30` | LEDs al 30 % |
| `70` | LEDs al 70 % |
| `100` | LEDs al 100 % |
| `M1` | Ejecutar Modo 1 |
| `M2` | Ejecutar Modo 2 |

Python envía el comando utilizando:

```python
esp32.write((comando + "\n").encode())
```

También se implementó una variable para evitar enviar repetidamente el mismo comando mientras el usuario mantiene el mismo gesto.

```python
ultimo_comando = ""
```

---

# 💡 Control PWM

El ESP32 utiliza PWM para controlar la intensidad de los LEDs.

La configuración utilizada es:

```cpp
#define PWM_FREQ 5000
#define PWM_RESOLUTION 8
```

La resolución de 8 bits proporciona un rango:

```text
0 ─────────────────── 255
```

La conversión de porcentaje a PWM se realiza mediante:

```cpp
int pwm = map(porcentaje, 0, 100, 0, 255);
```

### Tabla de conversión

| Intensidad | Valor PWM aproximado |
|---:|---:|
| 0 % | 0 |
| 30 % | 77 |
| 70 % | 179 |
| 100 % | 255 |

---

# 🔴 Funcionamiento del Modo 1

Cuando Python detecta:

```text
👎
```

envía:

```text
M1
```

El ESP32 ejecuta:

```text
LED1 = 255
LED2 = 0
LED3 = 0

        ↓ 300 ms

LED1 = 0
LED2 = 255
LED3 = 0

        ↓ 300 ms

LED1 = 0
LED2 = 0
LED3 = 255

        ↓ 300 ms

TODOS APAGADOS
```

---

# 🟢 Funcionamiento del Modo 2

Cuando Python detecta:

```text
👍
```

envía:

```text
M2
```

El ESP32 ejecuta:

```text
LED1 = 255
LED2 = 255
LED3 = 255

        ↓ 500 ms

TODOS APAGADOS

        ↓ 300 ms

LED1 = 255
LED2 = 255
LED3 = 255

        ↓ 500 ms

TODOS APAGADOS
```

---

# 📥 Instalación

## 1. Instalar Python

El proyecto fue desarrollado utilizando:

```text
Python 3.13
```

Para comprobar la versión:

```bash
python --version
```

También puede utilizarse:

```bash
py -3.13 --version
```

---

## 2. Instalar las librerías

Abrir una terminal y ejecutar:

```bash
py -3.13 -m pip install opencv-python mediapipe pyserial
```

Las librerías utilizadas son:

```text
opencv-python
mediapipe
pyserial
```

---

# 📦 Modelo de MediaPipe

El proyecto utiliza el archivo:

```text
hand_landmarker.task
```

Este archivo debe estar ubicado en la misma carpeta que el programa Python.

### Descargar el modelo

```text
https://storage.googleapis.com/mediapipe-models/hand_landmarker/hand_landmarker/float16/1/hand_landmarker.task
```

Después de descargarlo, la estructura local debe ser:

```text
GestureLighting/
│
├── control_final.py
│
└── hand_landmarker.task
```

> ⚠️ El archivo `hand_landmarker.task` es necesario para que MediaPipe pueda detectar la mano.

---

# 📁 Estructura del repositorio

El repositorio de GitHub está organizado de forma sencilla:

```text
control-iluminacion-por-gestos/
│
└── README.md
```

Todo el código fuente se encuentra documentado dentro de este README.

Para ejecutar el proyecto localmente se deben crear los siguientes archivos a partir de los códigos incluidos:

```text
GestureLighting/
│
├── control_final.py
│
├── control_iluminacion.ino
│
└── hand_landmarker.task
```

---

# 🐍 Código Python completo

El siguiente código corresponde al programa principal encargado de:

1. Abrir la cámara.
2. Detectar la mano.
3. Obtener los landmarks.
4. Reconocer el gesto.
5. Mostrar el gesto en pantalla.
6. Enviar el comando correspondiente al ESP32.

<details>
<summary><strong>🐍 Ver código Python completo</strong></summary>

```python
import cv2
import mediapipe as mp
import serial
import time


# ==========================================
# CONFIGURACIÓN DEL PUERTO SERIAL
# ==========================================

PUERTO = "COM7"
BAUDRATE = 115200


# ==========================================
# CONEXIÓN CON EL ESP32
# ==========================================

esp32 = serial.Serial(
    PUERTO,
    BAUDRATE,
    timeout=1
)

time.sleep(2)


# ==========================================
# CONFIGURACIÓN DE MEDIAPIPE
# ==========================================

BaseOptions = mp.tasks.BaseOptions

HandLandmarker = (
    mp.tasks.vision.HandLandmarker
)

HandLandmarkerOptions = (
    mp.tasks.vision.HandLandmarkerOptions
)

RunningMode = (
    mp.tasks.vision.RunningMode
)


options = HandLandmarkerOptions(

    base_options=BaseOptions(
        model_asset_path="hand_landmarker.task"
    ),

    running_mode=RunningMode.IMAGE,

    num_hands=1,

    min_hand_detection_confidence=0.5,

    min_hand_presence_confidence=0.5,

    min_tracking_confidence=0.5
)


detector = (
    HandLandmarker.create_from_options(
        options
    )
)


# ==========================================
# VARIABLE PARA EVITAR REPETIR COMANDOS
# ==========================================

ultimo_comando = ""


# ==========================================
# FUNCIÓN PARA ENVIAR COMANDOS
# ==========================================

def enviar_comando(comando):

    global ultimo_comando

    if comando != ultimo_comando:

        esp32.write(
            (comando + "\n").encode()
        )

        print(
            "Enviado:",
            comando
        )

        ultimo_comando = comando


# ==========================================
# DETECTAR DEDOS LEVANTADOS
# ==========================================

def dedos_levantados(mano):

    dedos = []


    # -----------------------------
    # ÍNDICE
    # -----------------------------

    dedos.append(
        1
        if mano[8].y < mano[6].y
        else 0
    )


    # -----------------------------
    # MEDIO
    # -----------------------------

    dedos.append(
        1
        if mano[12].y < mano[10].y
        else 0
    )


    # -----------------------------
    # ANULAR
    # -----------------------------

    dedos.append(
        1
        if mano[16].y < mano[14].y
        else 0
    )


    # -----------------------------
    # MEÑIQUE
    # -----------------------------

    dedos.append(
        1
        if mano[20].y < mano[18].y
        else 0
    )


    return dedos


# ==========================================
# DETECTAR POSICIÓN DEL PULGAR
# ==========================================

def detectar_pulgar(mano):

    base = mano[2]

    articulacion = mano[3]

    punta = mano[4]


    dx = punta.x - base.x

    dy = punta.y - base.y


    # -----------------------------
    # PULGAR ARRIBA
    # -----------------------------

    if (
        dy < -0.05
        and abs(dy) > abs(dx)
    ):

        return "ARRIBA"


    # -----------------------------
    # PULGAR ABAJO
    # -----------------------------

    elif (
        dy > 0.05
        and abs(dy) > abs(dx)
    ):

        return "ABAJO"


    return "CENTRO"


# ==========================================
# RECONOCIMIENTO DEL GESTO
# ==========================================

def reconocer_gesto(mano):

    dedos = dedos_levantados(
        mano
    )


    indice = dedos[0]

    medio = dedos[1]

    anular = dedos[2]

    menique = dedos[3]


    pulgar = detectar_pulgar(
        mano
    )


    # ======================================
    # PULGAR ARRIBA → MODO 2
    # ======================================

    if (
        pulgar == "ARRIBA"
        and not indice
        and not medio
        and not anular
        and not menique
    ):

        return (
            "MODO 2 - PULGAR ARRIBA",
            "M2"
        )


    # ======================================
    # PULGAR ABAJO → MODO 1
    # ======================================

    if (
        pulgar == "ABAJO"
        and not indice
        and not medio
        and not anular
        and not menique
    ):

        return (
            "MODO 1 - PULGAR ABAJO",
            "M1"
        )


    # ======================================
    # MANO ABIERTA → 100 %
    # ======================================

    if (
        indice
        and medio
        and anular
        and menique
    ):

        return (
            "MANO ABIERTA - 100%",
            "100"
        )


    # ======================================
    # DOS DEDOS → 70 %
    # ======================================

    elif (
        indice
        and medio
        and not anular
        and not menique
    ):

        return (
            "DOS DEDOS - 70%",
            "70"
        )


    # ======================================
    # PUÑO → 30 %
    # ======================================

    elif (
        not indice
        and not medio
        and not anular
        and not menique
    ):

        return (
            "PUÑO - 30%",
            "30"
        )


    # ======================================
    # GESTO NO RECONOCIDO
    # ======================================

    return (
        "GESTO NO RECONOCIDO",
        None
    )


# ==========================================
# INICIAR CÁMARA
# ==========================================

camara = cv2.VideoCapture(0)


if not camara.isOpened():

    detector.close()

    esp32.close()

    exit()


# ==========================================
# BUCLE PRINCIPAL
# ==========================================

while True:


    # --------------------------------------
    # CAPTURAR IMAGEN
    # --------------------------------------

    ret, frame = camara.read()


    if not ret:

        break


    # --------------------------------------
    # INVERTIR IMAGEN COMO ESPEJO
    # --------------------------------------

    frame = cv2.flip(
        frame,
        1
    )


    # --------------------------------------
    # CONVERTIR BGR → RGB
    # --------------------------------------

    rgb = cv2.cvtColor(
        frame,
        cv2.COLOR_BGR2RGB
    )


    # --------------------------------------
    # CREAR IMAGEN PARA MEDIAPIPE
    # --------------------------------------

    mp_image = mp.Image(

        image_format=
        mp.ImageFormat.SRGB,

        data=rgb
    )


    # --------------------------------------
    # DETECTAR MANO
    # --------------------------------------

    resultado = detector.detect(
        mp_image
    )


    # ======================================
    # SI SE DETECTA UNA MANO
    # ======================================

    if resultado.hand_landmarks:


        mano = (
            resultado.hand_landmarks[0]
        )


        # ----------------------------------
        # DIBUJAR LOS 21 LANDMARKS
        # ----------------------------------

        for i, punto in enumerate(
            mano
        ):

            x = int(
                punto.x *
                frame.shape[1]
            )

            y = int(
                punto.y *
                frame.shape[0]
            )


            cv2.circle(

                frame,

                (x, y),

                5,

                (0, 255, 0),

                -1
            )


        # ----------------------------------
        # RECONOCER GESTO
        # ----------------------------------

        nombre, comando = (
            reconocer_gesto(mano)
        )


        # ----------------------------------
        # ENVIAR COMANDO
        # ----------------------------------

        if comando is not None:

            enviar_comando(
                comando
            )


        # ----------------------------------
        # MOSTRAR GESTO
        # ----------------------------------

        cv2.putText(

            frame,

            nombre,

            (20, 50),

            cv2.FONT_HERSHEY_SIMPLEX,

            0.8,

            (0, 255, 0),

            2
        )


    # ======================================
    # SI NO SE DETECTA UNA MANO
    # ======================================

    else:

        cv2.putText(

            frame,

            "NO HAY MANO",

            (20, 50),

            cv2.FONT_HERSHEY_SIMPLEX,

            0.8,

            (0, 0, 255),

            2
        )


    # --------------------------------------
    # MOSTRAR CÁMARA
    # --------------------------------------

    cv2.imshow(

        "Control de Iluminacion",

        frame
    )


    # --------------------------------------
    # SALIR CON ESC
    # --------------------------------------

    if (
        cv2.waitKey(1) & 0xFF
    ) == 27:

        break


# ==========================================
# CERRAR RECURSOS
# ==========================================

camara.release()

esp32.close()

detector.close()

cv2.destroyAllWindows()
```

</details>

---

# 🔧 Código ESP32 completo

El siguiente código corresponde al programa cargado en el ESP32.

El ESP32 recibe los comandos provenientes de Python y controla los LEDs mediante PWM.

<details>
<summary><strong>🔧 Ver código ESP32 completo</strong></summary>

```cpp
// ==========================================
// DEFINICIÓN DE LEDs
// ==========================================

#define LED1 25
#define LED2 26
#define LED3 27


// ==========================================
// CONFIGURACIÓN PWM
// ==========================================

#define PWM_FREQ 5000

#define PWM_RESOLUTION 8


// ==========================================
// CONFIGURACIÓN INICIAL
// ==========================================

void setup() {


  // ----------------------------------------
  // COMUNICACIÓN SERIAL
  // ----------------------------------------

  Serial.begin(115200);


  // ----------------------------------------
  // CONFIGURAR PWM LED 1
  // ----------------------------------------

  ledcAttach(
    LED1,
    PWM_FREQ,
    PWM_RESOLUTION
  );


  // ----------------------------------------
  // CONFIGURAR PWM LED 2
  // ----------------------------------------

  ledcAttach(
    LED2,
    PWM_FREQ,
    PWM_RESOLUTION
  );


  // ----------------------------------------
  // CONFIGURAR PWM LED 3
  // ----------------------------------------

  ledcAttach(
    LED3,
    PWM_FREQ,
    PWM_RESOLUTION
  );


  // ----------------------------------------
  // APAGAR LEDs AL INICIAR
  // ----------------------------------------

  apagarLuces();


  // ----------------------------------------
  // MENSAJE INICIAL
  // ----------------------------------------

  Serial.println(
    "ESP32 LISTO"
  );
}


// ==========================================
// BUCLE PRINCIPAL
// ==========================================

void loop() {


  // ----------------------------------------
  // COMPROBAR DATOS SERIAL
  // ----------------------------------------

  if (Serial.available() > 0) {


    // --------------------------------------
    // LEER COMANDO
    // --------------------------------------

    String comando =
      Serial.readStringUntil(
        '\n'
      );


    // --------------------------------------
    // ELIMINAR ESPACIOS
    // --------------------------------------

    comando.trim();


    // ======================================
    // 30 %
    // ======================================

    if (
      comando == "30"
    ) {

      controlarLuces(
        30
      );

      Serial.println(
        "LUZ 30%"
      );
    }


    // ======================================
    // 70 %
    // ======================================

    else if (
      comando == "70"
    ) {

      controlarLuces(
        70
      );

      Serial.println(
        "LUZ 70%"
      );
    }


    // ======================================
    // 100 %
    // ======================================

    else if (
      comando == "100"
    ) {

      controlarLuces(
        100
      );

      Serial.println(
        "LUZ 100%"
      );
    }


    // ======================================
    // MODO 1
    // ======================================

    else if (
      comando == "M1"
    ) {

      Serial.println(
        "MODO 1"
      );

      modo1();
    }


    // ======================================
    // MODO 2
    // ======================================

    else if (
      comando == "M2"
    ) {

      Serial.println(
        "MODO 2"
      );

      modo2();
    }
  }
}


// ==========================================
// CONTROL DE INTENSIDAD
// ==========================================

void controlarLuces(
  int porcentaje
) {


  // ----------------------------------------
  // CONVERTIR PORCENTAJE A PWM
  // ----------------------------------------

  int pwm = map(

    porcentaje,

    0,

    100,

    0,

    255
  );


  // ----------------------------------------
  // APLICAR PWM LED 1
  // ----------------------------------------

  ledcWrite(

    LED1,

    pwm
  );


  // ----------------------------------------
  // APLICAR PWM LED 2
  // ----------------------------------------

  ledcWrite(

    LED2,

    pwm
  );


  // ----------------------------------------
  // APLICAR PWM LED 3
  // ----------------------------------------

  ledcWrite(

    LED3,

    pwm
  );
}


// ==========================================
// APAGAR TODAS LAS LUCES
// ==========================================

void apagarLuces() {


  ledcWrite(

    LED1,

    0
  );


  ledcWrite(

    LED2,

    0
  );


  ledcWrite(

    LED3,

    0
  );
}


// ==========================================
// MODO 1
// ==========================================

void modo1() {


  // ----------------------------------------
  // ENCENDER LED 1
  // ----------------------------------------

  ledcWrite(
    LED1,
    255
  );

  ledcWrite(
    LED2,
    0
  );

  ledcWrite(
    LED3,
    0
  );

  delay(300);


  // ----------------------------------------
  // ENCENDER LED 2
  // ----------------------------------------

  ledcWrite(
    LED1,
    0
  );

  ledcWrite(
    LED2,
    255
  );

  ledcWrite(
    LED3,
    0
  );

  delay(300);


  // ----------------------------------------
  // ENCENDER LED 3
  // ----------------------------------------

  ledcWrite(
    LED1,
    0
  );

  ledcWrite(
    LED2,
    0
  );

  ledcWrite(
    LED3,
    255
  );

  delay(300);


  // ----------------------------------------
  // APAGAR TODO
  // ----------------------------------------

  apagarLuces();
}


// ==========================================
// MODO 2
// ==========================================

void modo2() {


  // ----------------------------------------
  // PRIMER ENCENDIDO
  // ----------------------------------------

  ledcWrite(
    LED1,
    255
  );

  ledcWrite(
    LED2,
    255
  );

  ledcWrite(
    LED3,
    255
  );

  delay(500);


  // ----------------------------------------
  // APAGAR
  // ----------------------------------------

  apagarLuces();

  delay(300);


  // ----------------------------------------
  // SEGUNDO ENCENDIDO
  // ----------------------------------------

  ledcWrite(
    LED1,
    255
  );

  ledcWrite(
    LED2,
    255
  );

  ledcWrite(
    LED3,
    255
  );

  delay(500);


  // ----------------------------------------
  // APAGAR
  // ----------------------------------------

  apagarLuces();
}
```

</details>

---

# ▶️ Ejecución del proyecto

## 1. Programar el ESP32

Abrir **Arduino IDE**.

Seleccionar la placa ESP32 correspondiente.

Copiar el código del ESP32 incluido anteriormente y cargarlo en la placa.

Una vez cargado, abrir el **Monitor Serial** a:

```text
115200 baudios
```

El ESP32 debe mostrar:

```text
ESP32 LISTO
```

---

## 2. Conectar el ESP32

Conectar el ESP32 al computador mediante USB.

En el código Python se configuró:

```python
PUERTO = "COM7"
```

Si el ESP32 aparece en otro puerto, cambiar esta línea.

Por ejemplo:

```python
PUERTO = "COM5"
```

---

## 3. Preparar los archivos

Crear una carpeta:

```text
GestureLighting
```

Dentro de ella colocar:

```text
GestureLighting/
│
├── control_final.py
│
└── hand_landmarker.task
```

El archivo `.ino` se puede abrir directamente desde Arduino IDE.

---

## 4. Ejecutar Python

Desde la terminal ejecutar:

```bash
py -3.13 control_final.py
```

También puede utilizarse:

```bash
python control_final.py
```

si Python 3.13 está configurado como versión principal.

Al ejecutar el programa aparecerá una ventana con la cámara.

Los puntos de referencia de la mano aparecerán sobre la imagen.

---

# 🛑 Cómo cerrar el programa

Para cerrar el programa de Python:

```text
Presionar ESC
```

Esto cerrará:

- La cámara.
- La conexión serial.
- El detector de MediaPipe.
- La ventana de OpenCV.

---

# 🎥 Video del proyecto

El funcionamiento completo del sistema se puede observar en el siguiente video:

### ▶️ [🎥 Ver video de funcionamiento en Google Drive](https://drive.google.com/file/d/1hGN5RbSzZA0iTgScKoGG_EQzYSEqdTJu/view?usp=sharing)

---

# 📊 Resumen general del sistema

| Gesto | Comando | PWM | Acción |
|---|---|---:|---|
| ✊ Puño | `30` | 77 | Iluminación al 30 % |
| ✌️ Dos dedos | `70` | 179 | Iluminación al 70 % |
| 🖐️ Mano abierta | `100` | 255 | Iluminación al 100 % |
| 👎 Pulgar abajo | `M1` | — | Secuencia de LEDs |
| 👍 Pulgar arriba | `M2` | — | Secuencia de encendido |

---

# 🔄 Flujo completo del proyecto

```text
┌──────────────────────┐
│      USUARIO         │
│                      │
│ Realiza un gesto     │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      CÁMARA WEB      │
│                      │
│ Captura la imagen    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│       OPENCV         │
│                      │
│ Procesa la imagen    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      MEDIAPIPE       │
│                      │
│ Detecta la mano      │
│ y sus 21 landmarks   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│      PYTHON          │
│                      │
│ Reconoce el gesto    │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   COMUNICACIÓN USB   │
│       SERIAL         │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│        ESP32         │
│                      │
│ Interpreta comando   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│         PWM          │
│                      │
│ Control de LEDs      │
└──────┬──────┬────────┘
       │      │
       ▼      ▼
     LED1   LED2   LED3
```

---

# 🚀 Posibles mejoras

El proyecto puede ampliarse posteriormente mediante:

- Incorporación de más LEDs.
- Control de bombillos de mayor potencia.
- Uso de MOSFETs para cargas de mayor corriente.
- Incorporación de relés.
- Control de ventiladores.
- Implementación de nuevos gestos.
- Comunicación Bluetooth.
- Comunicación Wi-Fi.
- Control mediante una aplicación móvil.
- Creación de una interfaz gráfica.
- Implementación de diferentes escenas de iluminación.
- Integración con sistemas de domótica.
- Incorporación de sensores adicionales.

---

# 📚 Tecnologías utilizadas

```text
Python
OpenCV
MediaPipe
PySerial
ESP32
Arduino IDE
C++
PWM
Computer Vision
Serial Communication
```

---

# 🏆 Resultado

El sistema permite controlar tres LEDs mediante gestos de la mano detectados por una cámara web.

La arquitectura combina:

```text
Visión artificial
        +
Reconocimiento de gestos
        +
Python
        +
Comunicación serial
        +
ESP32
        +
PWM
        ↓
Control de iluminación
```

El proyecto demuestra la integración entre **software, visión artificial, comunicación serial y electrónica**, permitiendo crear una interfaz de control sin contacto físico.

---

# 👤 Autor

**Oscar Junco**

Proyecto académico de control de iluminación mediante visión artificial, reconocimiento de gestos y ESP32.

---

## ⭐ Tecnologías principales

<p align="center">

`Python` · `OpenCV` · `MediaPipe` · `PySerial` · `ESP32` · `Arduino` · `PWM` · `Computer Vision`

</p>

---

**© 2026 Oscar Junco**
