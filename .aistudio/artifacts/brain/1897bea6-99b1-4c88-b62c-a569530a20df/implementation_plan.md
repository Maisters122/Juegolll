# Plan de Implementación: Motor de Audio Procedural F1 V10 (Estilo Ferrari / BMW)

Sustituir los archivos de audio por un sintetizador procedural puro en tiempo real utilizando la Web Audio API, diseñado según la física acústica de los motores atmosféricos 3.0L V10 de Fórmula 1 (era Ferrari 053 / BMW P84), con cero dependencias de archivos externos, latencia nula y respuesta interactiva instantánea.

---

## 1. Física Acústica del Motor V10 de Competición
- **Orden de encendido y armónicos fundamentales (10 cilindros, 4 tiempos):**
  - Frecuencia fundamental ($f_0$): 5 detonaciones por vuelta de cigüeñal (`(RPM / 60) * 5.0`).
    - En ralentí (1.000 RPM): 83,3 Hz (ronquido grave y compacto).
    - En corte (9.500 RPM): 791,7 Hz (aullido estridente y afilado característico).
  - **Estructura armónica multitímbrica:**
    - **Fundamental (5º orden):** Pulso de combustión en cámara.
    - **2º armónico (10º orden):** Resonancia de colectores de escape dobles 5 en 1.
    - **3º armónico (15º orden):** Aullido metálico estridente de trompetas de escape abiertas (el timbre clásico de Ferrari/BMW V10).
    - **4º armónico (20º orden):** Brillo acústico de alta frecuencia.

---

## 2. Modelado de Cadena de Audio Procedural (`EngineSound.ts`)
- **Generación de Onda de Combustión:**
  - Banco de osciladores procedurales polifónicos en fase con modulación continua de frecuencia libre de cortes (*click-free*).
  - Curva de transferencia de saturación no lineal (*WaveShaperNode*) para simular el soplado supersónico de las válvulas de escape.
- **Resonadores de Colector y Trompetas:**
  - Filtro de resonancia de escape (*peaking filter*) con frecuencia central variable de 800 Hz a 2.600 Hz según RPM.
  - Filtro de admisión de aire (*Airbox induction*) que inyecta el gruñido gutural al pisar el acelerador a fondo.
- **Transmisión y Caja Secuencial:**
  - Corte de encendido (*ignition-cut*) instantáneo de 45 ms al cambiar de marcha con chasquido metálico procedural.
  - Silbido de engranajes rectos (*Straight-cut gearbox whine*) con tono proporcional a los km/h de las ruedas.
- **Capa Aerodinámica:**
  - Viento dinámico de ruido rosa filtrado que entra a partir de 80 km/h y ruge con fuerza a 200–350 km/h.
- **Retención y Frenada (Overrun):**
  - Descenso armónico inmediato al levantar el pie del gas con caída de volumen de compresión sin silencios bruscos.

---

## 3. Integración y Audio Espacial 3D
- **Coche del Jugador:** Canal estéreo de alta resolución con respuesta inmediata a las RPM, pedal de acelerador y marchas (1ª a 6ª).
- **Rival IA:** El mismo modelo procedural V10 aplicado al monoplaza rival con atenuación cuadrática de distancia (hasta 95 metros) y paneo estéreo 3D.
- **Efectos Secundarios:** Conservación íntegra de chirrido de derrapes, choques, pinchazos y radios de boxes.

---

## 4. Verificación y Pruebas
- Probar el sonido en parado (ralentí limpio a 1.000 RPM sin aceleraciones involuntarias).
- Acelerar de 1ª a 6ª marcha hasta 350 km/h verificando la progresión de tono, los cambios secuenciales secos y el rugido V10.
- Verificar el comportamiento al frenar y reducir marchas.
- Ejecutar `lint_applet` y `compile_applet`.
