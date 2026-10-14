# Plan de Implementación: Fotorrealismo y Acabado FIA para Carteles de Pista

Transformar los carteles de señalización de frenada situados en las barreras del circuito (actualmente con marcos y bordes amarillos saturados tipo juguete y caras plásticas) en elementos de señalización fotorrealistas con especificación FIA, integrados estéticamente con el asfalto, las barreras y la iluminación del circuito.

---

## 1. Análisis del Problema Visual Actual

En la captura se aprecian dos carteles situados junto a los muros de hormigón a la izquierda de la trazada:
1. **Marco exterior y bisel 3D**: Presenta un color amarillo plano y saturado (`#f2cc1a`) con baja rugosidad (`roughness: 0.35`, casi sin metalicidad), lo que simula plástico moldeado de juguete brillante en lugar de metal de circuito.
2. **Borde perimetral en la textura 2D**: La textura frontal incorpora un marco adicional de 48 píxeles en amarillo chillón que amplifica el efecto caricaturesco.
3. **Superficie y cara frontal**: El interior se percibe oscuro/apagado sin la tipografía de frenada legible adecuada ni la reflectancia microprismática que caracteriza los carteles reales de Fórmula 1 y GT3.
4. **Respuesta lumínica PBR**: El brillo especular concentrado crea una apariencia plástica que desentona con el asfalto y las barreras de hormigón desgastadas.

---

## 2. Decisiones de Diseño Acordadas con el Usuario

- **Acabado del marco exterior**: Aluminio anodizado grafito mate (`#22252a`, `metalness: 0.82`, `roughness: 0.78`) con sutiles acentos en amarillo industrial FIA desgastado (`#bfa026`, `roughness: 0.80`) en aristas biseladas.
- **Cara frontal del cartel**: Panel de distancia de frenado FIA (200 m, 150 m, 100 m, 50 m) con números retro-reflectantes de alta visibilidad, textura compuesta de microprismas, línea de patrocinio técnico y sutil desgaste por partículas de goma de neumáticos desgastados (*tire marbles* y proyecciones de pista).
- **Respuesta PBR y brillos**: Superficies difusas y mates de alta rugosidad (`roughness: 0.78 - 0.85`), eliminando cualquier brillo plástico artificial y armonizando con la luz atmosférica y los atardeceres del circuito.

---

## 3. Plan de Cambios por Componente

### A. Generador de Texturas PBR de Alta Resolución (`TrackBuilder.ts` / Script)
- Diseñar un generador de texturas procedurales de alta definición (1024x1024) integrado en runtime (garantizando independencia de fuentes del sistema operativo y nitidez absoluta a cualquier distancia):
  - Fondo de composite de fibra/aluminio negro carbón con sutil grano mate.
  - Eliminación del marco amarillo fluorescente chillón de 48 px. En su lugar, un fino cordón estanco de goma negra EPDM mate y un perfil perimetral metálico sutil.
  - Tipografía de señalización FIA nítida y visible para cada distancia (200, 150, 100, 50) con acabado blanco retro-reflectante tipo *diamond grade*.
  - Capa de pátina de competición: manchas sutiles de polvo de asfalto y marcas aerodinámicas de goma proyectada.

### B. Ajuste de Materiales PBR en `TrackBuilder.ts`
- Modificar la inspección y carga de los modelos de señalización en `TrackBuilder.consolidateSignModel` y `setupStraightMarkers`:
  - **Material del bisel/marco (`MatMarkerSafetyYellow`)**: Reconfigurar a aluminio grafito mate industrial (`color: 0x22262c`, `metalness: 0.80`, `roughness: 0.78`) con vivos de seguridad ámbar mate atenuados (`0xb59218`, `roughness: 0.82`).
  - **Material del panel frontal (`MatBrakeFace`)**: Asignar textura nítida con `roughness: 0.75` y respuesta difusa sin brillo especular plástico.
  - **Postes de soporte y fijaciones**: Acero galvanizado mate desgastado (`color: 0x6e747c`, `metalness: 0.85`, `roughness: 0.45`) con fuelles de goma oscura (`color: 0x121417`, `roughness: 0.90`).

### C. Actualización de Modelos y Scripts de Soporte (`scripts/`)
- Actualizar `generate_photorealistic_circuit_signage.py` y `generate_signage_textures.py` para sincronizar los parámetros originales con la nueva estética realista.

---

## 4. Rendimiento y Optimización Gráfica (Directivas de Arquitecto)

- **Zero Garbage Collection (GC)**: Las texturas se generan y almacenan en caché en el inicio (`preloadAssets`), sin asignaciones de memoria dinámicas durante el loop de renderizado.
- **Draw Call Batching**: Mantenimiento estricto del método `consolidateSignModel()`, que fusiona geometrías por material común, reduciendo más de 20 sub-mallas a solo 2-3 draw calls por sección de pista.
- **VRAM y Ancho de Banda**: Compresión y mipmapping nativo en Three.js con filtrado anisotrópico para mantener nitidez en ángulo rasante sin caídas de framerate, asegurando 60-120 FPS estables.

---

## 5. Plan de Verificación

1. **Compilación y Linteo**:
   - Ejecutar `compile_applet` y validar que el build de Vite y TypeScript se complete con código 0 sin advertencias.
2. **Inspección Visual en Cámara de Conducción**:
   - Verificar la aproximación a las curvas 1, 2, 3 y 4.
   - Constatar que los carteles ya no parecen bloques de Lego o juguetes plásticos amarillos.
   - Comprobar que los marcos de aluminio grafito y los números de frenada se leen claramente y se integran con la estética sombría y técnica del circuito.
