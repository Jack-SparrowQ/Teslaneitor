# Bibliografía

Fuentes consultadas el 2026-10-26. **Primaria** = documento del fabricante del componente. **Secundaria** = copia o resumen hecho por terceros: sirve de referencia, pero no se considera verificación definitiva.

| ID | Documento | Tipo | Enlace | Usado para |
|---|---|---|---|---|
| B1 | Microchip, *ATmega48A/PA/88A/PA/168A/PA/328/P Data Sheet*, DS40002061B (2020) | Primaria | https://ww1.microchip.com/downloads/aemDocuments/documents/MCU08/ProductDocuments/DataSheets/ATmega48A-PA-88A-PA-168A-PA-328-P-DS-DS40002061B.pdf | Máximos absolutos (§29.1), VIH/VIL (Tabla 30-1), grados de velocidad (§29.3), referencia interna de 1,1 V (§29.8) |
| B2 | Arduino, *Arduino UNO R3 User Manual*, SKU A000066 (modificado 21/10/2026) | Primaria (placa oficial) | https://docs.arduino.cc/resources/datasheets/A000066-datasheet.pdf | Tensión en VIN, regulador, memoria, PWM |
| B3 | Arduino, *UNO R3 Full Pinout*, A000066 | Primaria | https://docs.arduino.cc/resources/pinouts/A000066-full-pinout.pdf | Mapa de pines |
| B4 | Espressif, *ESP32 Series Datasheet*, v5.3 | Primaria | https://documentation.espressif.com/esp32_datasheet_en.pdf | Máximos absolutos (Tabla 5-1), características DC (Tabla 5-3), pines de strapping (§3) |
| B5 | ElecFreaks, *Ultrasonic Ranging Module HC-SR04* | Primaria (diseño original del módulo; los clones pueden diferir) | https://cdn.sparkfun.com/datasheets/Sensors/Proximity/HCSR04.pdf | Alimentación, rango, ciclo de medida, área mínima del objeto |
| B6 | Universal-Solder, *Ai-Thinker ESP32-CAM*, v1.0 (17/02/2021), que reproduce las especificaciones de Ai-Thinker | Secundaria | https://atta.szlcsc.com/upload/public/pdf/source/231023/C17416982-30bc871038898b12774324a310c0150b.pdf | Alimentación y consumo de la ESP32-CAM |
| B7 | Ai-Thinker, documentación de la ESP32-CAM | Primaria (no se pudo extraer el contenido) | https://docs.ai-thinker.com/en/esp32-cam | Pendiente |
| B8 | STMicroelectronics, *L298 Dual Full-Bridge Driver* | Primaria (**no se pudo descargar**: el servidor no respondió) | https://www.st.com/resource/en/datasheet/l298.pdf | Pendiente: caída de tensión, VIH de las entradas |
| B9 | Espressif, *esp32-camera* (controlador de la cámara) | Primaria (software) | https://github.com/espressif/esp32-camera | Fase 6 (firmware) |
| B10 | OmniVision, *OV2640 Datasheet* (copia de UCTRONICS) | Secundaria | https://www.uctronics.com/download/cam_module/OV2640DS.pdf | Resolución y formatos de la cámara |
| B11 | Espressif, ESP-IDF, *GPIO* (lista de pines de strapping) | Primaria | https://github.com/espressif/esp-idf/blob/v5.0.6/docs/en/api-reference/peripherals/gpio.rst | Pines a evitar en la ESP32-CAM |

## Pendientes

- **B8 (L298):** descargarlo a mano desde el navegador y guardarlo en `references/datasheets/`.
- **Kit del proveedor:** el código y el diagrama de conexiones (están detrás del QR del paquete).
- **Motores 1:48:** no hay un datasheet de fabricante identificable; los datos se obtendrán midiendo (prueba T2).
