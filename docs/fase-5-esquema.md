# Teslaneitor — Fase 5: Esquema eléctrico (borrador 0.1)

> Estado: **BORRADOR para revisión.** Comprobado con el ERC de KiCad y revisado red por red contra la fase 3. **No validado en hardware.**
> Fecha: 2026-10-26 · KiCad 10.0.7

## Archivos

| Archivo | Contenido |
|---|---|
| `electronics/kicad/teslaneitor.kicad_pro` | Proyecto de KiCad (abrir este) |
| `electronics/kicad/teslaneitor.kicad_sch` | Esquema (una hoja A3) |
| `electronics/kicad/teslaneitor.pdf` | Exportación para leerlo sin KiCad |

## Cómo está dibujado

- **Conexiones por etiquetas:** cada pin tiene un tramo corto de cable con el nombre de su red. Los pines con la misma etiqueta están unidos, aunque no haya una línea entre ellos. Así el esquema se lee por bloques.
- **Módulos como conectores:** el L298N, el buck, el adaptador de nivel, la ESP32-CAM y el HC-SR04 son módulos comprados, así que se dibujan como conectores con el nombre de cada pin. El Arduino usa el símbolo oficial de KiCad.
- **Marcas ×:** pines que se dejan sin conectar a propósito.

## Bloques

| Bloque | Componentes | Redes principales |
|---|---|---|
| Alimentación | BT1 (6×AA NiMH), SW1 (interruptor general), J5 (buck 5 V), C1 470 µF, C2 100 nF | BAT_RAW, VBAT, +5V_CAM, GND |
| Medida de batería | R1 100 k, R2 10 k, C3 100 nF | VBAT_SENSE → A0 |
| Control | A1 (Arduino Uno), SW2 (inicio) | D2–D12, A0, VIN, +5V |
| Motores | J1–J3 (L298N), M1, M2, R3/R4 10 k (pull-down) | ENA, ENB, IN1–IN4, MOT_A±, MOT_B± |
| Sensor | J4 (HC-SR04) | TRIG, ECHO, +5V |
| Cámara | J6/J7 (adaptador BSS138), J8/J9 (ESP32-CAM) | CAM_TX_5V/3V3, UNO_TX_5V/3V3, +3V3_CAM |

## Verificación realizada

1. **ERC (comprobación de reglas eléctricas) de KiCad: 0 errores de conexión.** Los únicos 24 avisos dicen que la configuración no incluye las librerías de símbolos. Es porque KiCad aún no tiene su tabla global de librerías, y desaparecen al abrir KiCad la primera vez (acepta la tabla por defecto). Los símbolos ya van incluidos dentro del esquema.
2. **Netlist revisada red por red** frente al mapa de pines aprobado (D-09). Las 24 redes coinciden. Por ejemplo, `VBAT` une SW1, VIN del Uno, la entrada del L298N, la entrada del buck y R1.

## Pendiente de verificar con las placas reales

- Orden de pines de la ESP32-CAM, del adaptador BSS138 y del buck: se dibujó el orden habitual, pero varía entre fabricantes.
- Bornas del módulo L298N (+12V / GND / +5V) y sus puentes.
- Regulador del Uno clónico (VIN).

## Siguientes pasos

- Revisión del esquema por tu parte. Si cambias algo en KiCad, se vuelve a pasar el ERC y la revisión de la netlist.
- Fase 6: diseño del firmware.
