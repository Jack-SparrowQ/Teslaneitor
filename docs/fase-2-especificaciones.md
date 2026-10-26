# Teslaneitor — Fase 2: Verificación de especificaciones

> Estado: **BORRADOR.** Verificado = contrastado con el documento citado (ver `references/bibliography.md`). Nada se ha medido todavía en el hardware real (el kit aún no ha llegado).
> Fecha: 2026-10-26

## 1. Especificaciones verificadas

| Componente | Parámetro | Valor | Fuente | Estado |
|---|---|---|---|---|
| ATmega328P (Uno) | Tensión máxima en cualquier pin (salvo RESET) | −0,5 V a VCC + 0,5 V | B1 §29.1 | ✅ Verificado |
| ATmega328P | Corriente DC por pin de E/S | 40 mA (máximo absoluto) | B1 §29.1 | ✅ |
| ATmega328P | VIH (entrada alta), VCC = 2,4–5,5 V | **mín. 0,6·VCC → 3,0 V a 5 V** | B1 Tabla 30-1 | ✅ |
| ATmega328P | Frecuencia frente a VCC | 16 MHz requiere VCC ≳ 3,8 V (interpolando la Fig. 29-1) | B1 §29.3 | ✅ (cálculo propio) |
| ATmega328P | Referencia interna del ADC | 1,1 V (mín. 1,0, máx. 1,2) | B1 §29.8 | ✅ |
| Arduino Uno R3 | Entrada VIN | 6–20 V | B2 §2.2 | ✅ para la placa **oficial**; el clon CH340 puede llevar otro regulador → [VERIFICAR] al llegar |
| Arduino Uno R3 | Memoria | 32 kB de flash, 2 kB de SRAM | B2 | ✅ |
| Arduino Uno R3 | Canales PWM | 6 | B2 | ✅ |
| ESP32 | Alimentación máxima (VDD3P3) | 3,6 V | B4 Tabla 5-1 | ✅ |
| ESP32 | VIH | 0,75·VDD a **VDD + 0,3 V (máx. 3,6 V)** | B4 Tabla 5-3 | ✅ → **una señal de 5 V supera el máximo** |
| ESP32 | VOH (salida alta) | **mín. 0,8·VDD = 2,64 V** | B4 Tabla 5-3 | ✅ |
| ESP32 | Pines de strapping | GPIO0, 2, 5, 12, 15 | B4 §3, B11 | ✅ |
| ESP32-CAM | Alimentación | 4,75–5,25 V | B6 | 🟡 Fuente secundaria |
| ESP32-CAM | Consumo | 180 mA sin flash; 310 mA con flash al máximo; 6 mA en deep-sleep (a 5 V) | B6 | 🟡 Secundaria; son valores medios, no picos |
| ESP32-CAM | Memoria | 520 KB de SRAM + 8 MB de PSRAM | B6 | 🟡 |
| ESP32-CAM | Formato de imagen | JPEG (solo con OV2640) | B6 | 🟡 |
| HC-SR04 | Alimentación / consumo | 5 V DC / 15 mA | B5 | ✅ |
| HC-SR04 | Rango | 2 cm – 4 m; ángulo de 15° | B5 | ✅ |
| HC-SR04 | Disparo | Pulso TTL de 10 µs | B5 | ✅ |
| HC-SR04 | Ciclo de medida | **> 60 ms** recomendado | B5 | ✅ |
| HC-SR04 | Conversión | µs / 58 = cm | B5 | ✅ |
| HC-SR04 | Objeto medible | **Área ≥ 0,5 m² y superficie lo más lisa posible** | B5 "Attention" | ✅ |
| L298 | Caída de tensión, VIH de las entradas | — | B8 | ❌ Pendiente (no se pudo descargar) |
| Motorreductor 1:48 | Tensión, rpm, corriente | — | Sin fuente | ❌ Se medirá en T2 |

## 2. Hallazgos que cambian el diseño

### H1 — El enlace de 3,3 V → 5 V **no está garantizado** (corrige E4)

La ESP32-CAM garantiza solo **2,64 V** como "1" lógico de salida (VOH mín.), y el Uno necesita al menos **3,0 V** para leer un "1" (VIH mín. a 5 V). 2,64 V < 3,0 V, así que la conexión directa *puede* funcionar (la tensión real sin carga suele estar cerca de 3,3 V), pero el datasheet **no la garantiza**.

**Cambio:** usar un **adaptador de nivel bidireccional** (módulo de 4 canales con MOSFET BSS138, ~1 USD) para TX y RX. Sustituye el divisor de la propuesta E3 y la conexión directa de E4: un solo componente resuelve los dos sentidos.

> **Concepto — márgenes de ruido:** se compara siempre el *peor caso* de la salida (VOH mín.) con el *peor caso* de la entrada (VIH mín.). Si la salida mínima no supera la entrada mínima, el circuito funciona "por suerte" con unas piezas y falla con otras.

### H2 — El HC-SR04 necesita objetos grandes y lisos

El fabricante pide un objeto de al menos 0,5 m² (p. ej. 70 × 70 cm) y liso. Una pata de silla, una botella o un objeto de tela pueden **no detectarse**.

**Cambio:** los obstáculos de prueba se definen en el plan de validación (cajas de cartón grandes y paredes). Esto queda documentado como **limitación** de la v1 y refuerza el riesgo R-03.

### H3 — Alimentación del Uno: VIN desde la batería, no desde el 5 V del L298N (corrige E1)

- El regulador lineal de 5 V del módulo L298N necesita varios voltios de margen. Con pilas NiMH (7,2 V nominal, cayendo con la descarga) su salida puede quedar por debajo de 5 V. Esto es [SUPUESTO] hasta tener el módulo; es típico de reguladores de la familia 78xx.
- **Cambio:** el Uno se alimenta por **VIN** directamente desde la batería, a través de su propio regulador (rango oficial de 6–20 V, B2). El HC-SR04 se alimenta desde el pin de 5 V del Uno (15 mA). La ESP32-CAM, con su buck dedicado (sin cambios).
- Se cumple RNF-06: los motores y la lógica tienen reguladores distintos y comparten la masa.

### H4 — Medir la batería con la referencia interna de 1,1 V (corrige E6)

Si el ADC usa la referencia por defecto (VCC), cuando VCC cae la lectura de la batería también se desplaza. La referencia interna (1,0–1,2 V, B1) no depende de VCC, pero su tolerancia de ±10 % obliga a **calibrar** con el multímetro.

**Cambio:** divisor de 100 kΩ / 10 kΩ (9,6 V → 0,87 V, por debajo de 1,1 V) + condensador de 100 nF en A0 + calibración en T0.

### H5 — Tarjeta microSD

La especificación (B6) indica un soporte máximo de **4 GB**. Se usan tarjetas mayores habitualmente, pero eso es [SUPUESTO]. **Cambio en el BOM:** comprar una de 4–8 GB y formatearla en FAT32.

### H6 — Pines de strapping en la ESP32-CAM

GPIO0, 2, 12 y 15 se leen al arrancar (B4). GPIO12 a nivel alto en el arranque cambia la tensión de la flash y puede impedir que arranque. **Regla:** no conectar nada externo a GPIO0, 2, 12 ni 15 (ver el mapa de pines de la fase 3).

## 3. Pendientes de la fase 2

- Datasheet del L298 (B8): confirmar la caída de tensión y que una entrada de 5 V es válida (VIH).
- Clon del Uno con CH340: identificar el regulador real al recibir la placa.
- Motores: medir la tensión en el motor, la corriente en vacío y la de bloqueo, y las rpm (T2).
