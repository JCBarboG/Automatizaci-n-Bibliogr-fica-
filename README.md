# BIBNORM / Gnos — Sistema de Normalización Bibliográfica

> Documento de contexto completo para orientar sesiones futuras de IA o colaboradores nuevos.  
> Última actualización: 2026-06-28

---

## ¿Qué es esta plataforma?

**BIBNORM** (nombre de marca: **Gnos**) es una plataforma web institucional para la **normalización y estandarización automática de registros bibliográficos**. Está diseñada para bibliotecarios y catalogadores que necesitan limpiar, corregir y estructurar metadatos bibliográficos en lote, siguiendo los estándares internacionales **MARC21** (58 campos) e **ISO 690:2021**.

- **Repositorio:** https://github.com/JCBarboG/Automatizaci-n-Bibliogr-fica-
- **Versión actual:** v2.0
- **Idioma de la interfaz:** Español
- **Arquitectura:** Single Page Application (SPA) completamente client-side — sin backend, sin base de datos, sin framework JS
- **Despliegue:** Archivos estáticos (cualquier servidor o GitHub Pages)

---

## Stack tecnológico

| Capa | Tecnología |
|------|-----------|
| Lenguaje | Vanilla HTML5 + JavaScript (ES6+) |
| Estilos | CSS inline por archivo (variables CSS, dark mode) |
| Iconos | Tabler Icons vía CDN |
| Build | Ninguno (archivos directos) |
| Backend | Ninguno actualmente |
| Base de datos | Ninguna actualmente |
| Dependencias npm | Ninguna |

**APIs del navegador usadas:**
- Clipboard API (pegar desde Excel con Ctrl+V)
- DOM `contentEditable` para tablas editables in-place

---

## Estructura de archivos

```
proyecto automatización/
│
├── index.html                   # Página de inicio / navegación a módulos
├── bibnorm.html                 # Interfaz principal alternativa
│
└── modulos/
    ├── revistas.html            # ACTIVO — Normalización de artículos de revistas (núcleo)
    ├── referencias.html         # ACTIVO — Generador de referencias APA 7, Chicago, Vancouver
    ├── ocr.html                 # EN DESARROLLO — Extracción OCR desde imágenes
    ├── contacto.html            # ACTIVO — Formulario de contacto/retroalimentación
    ├── libros.html              # PENDIENTE — Placeholder "Próximamente"
    ├── tesis.html               # PENDIENTE — Placeholder "Próximamente"
    ├── audiovisual.html         # PENDIENTE — Placeholder "Próximamente"
    ├── periodicos.html          # PENDIENTE — Placeholder "Próximamente"
    ├── documentos.html          # PENDIENTE — Placeholder "Próximamente"
    ├── mapas.html               # PENDIENTE — Placeholder "Próximamente"
    └── cddvd.html               # PENDIENTE — Placeholder "Próximamente"
```

---

## Estado actual de cada módulo

### index.html — Inicio (ACTIVO)
- Header sticky con logo "Gnos" y menú hamburguesa
- Grid responsivo de tarjetas para cada módulo (3 columnas → 2 → 1)
- Panel lateral con: Inicio, Historial, Ayuda, Contacto
- Banner destacado del módulo OCR
- Dark mode con variables CSS

### modulos/revistas.html — Artículos de Revistas (NÚCLEO — COMPLETO)
**Tamaño:** ~51 KB · Es el módulo más robusto y completamente funcional.

**Flujo de uso:**
1. El usuario pega datos desde Excel (Ctrl+V) en la tabla de entrada
2. El sistema detecta automáticamente el número de columnas
3. Se hace clic en "Iniciar normalización"
4. Animación de progreso (15 → 100%) con etiquetas de etapas
5. Tabla de resultados con campos modificados resaltados en amarillo
6. Insignias de estado: "Normalizado" / "Revisar"
7. Aviso de correcciones generado automáticamente
8. Botones para exportar CSV y generar reporte

**Motor de normalización — `normalizarRegistro(fila)`:**
- Entrada: arreglo de 58 valores (una fila del Excel)
- Salida: `{data: [58 valores normalizados], mod: [índices modificados], ok: true/false}`
- Completamente stateless — determinístico y escalable
- Aplica 8 reglas secuenciales:
  1. Título (245$a) — agrega " :" si no está presente
  2. Volumen/Número (773$g) — extrae solo el patrón "Vol. X, no. X (año)"
  3. Publicación fuente (773$a) — extrae nombre de revista, elimina info de volumen
  4. Descriptores no normalizados (653$a) — combina si col[40] tiene valores no booleanos
  5. Descriptores normalizados (650$a) — mueve desde col[52] si col[51] está vacío
  6. Tipo de documento (col[45]) — mueve desde col[46] si col[45] está vacío o es año
  7. Limpieza de columnas basura — 13 columnas con artefactos de metadatos
  8. Validación general del registro

**Campos MARC21 clave (resaltados en azul):**
- Col 6: Signatura (090$a)
- Col 7: Autor Principal (100$a)
- Col 8: Título (245$a)
- Col 10: País (260$a)
- Col 11: Editorial (260$b)
- Col 12: Año (260$c)
- Col 18: Documento Fuente (773$a)
- Col 19: Volumen/Número (773$g)
- Col 21: Idioma (041$a)
- Col 37: Colección (852$b)
- Col 39: Descriptores No Normalizados (653$a)

**Sección de validación:**
- 7 registros de prueba reales incluidos (revistas de tortura + derecho internacional)
- 23 columnas verificadas por fila
- Insignia Pasa/Falla por cada campo

### modulos/referencias.html — Generador de Referencias (ACTIVO)
**Tamaño:** ~43 KB

- Genera citas bibliográficas formateadas en múltiples estilos
- **APA 7** completamente implementado (sangría francesa, formato autor Apellido, N.)
- Soporte estructural para Chicago y Vancouver
- Campos dinámicos para múltiples autores (agregar/eliminar)
- Selector de fecha (año, mes, día)
- Vista previa en tiempo real de la referencia
- Copiar al portapapeles
- Soporte DOI/URL

### modulos/ocr.html — OCR (EN DESARROLLO ~60-70%)
**Tamaño:** ~30 KB

- Interfaz de arrastrar y soltar para subir imágenes
- Vista previa de imagen
- Checkboxes para seleccionar tipos de datos a extraer:
  - Autor, Título, Fecha, Editorial, ISBN, Descriptores
- Mapeo de campos al estándar MARC21
- **Pendiente:** integración real del motor OCR

### modulos/contacto.html — Contacto (ACTIVO)
- Formulario con: Nombre, Email, Asunto, Mensaje, Categoría
- Validación de campos
- Confirmación de envío exitoso
- SLA de respuesta (2-5 días hábiles)

### modulos/libros.html, tesis.html, audiovisual.html, etc. — PENDIENTES
- Todos comparten el mismo diseño placeholder "Próximamente"
- ~6 KB cada uno
- Incluyen navegación de regreso al inicio

---

## Sistema de diseño

**Colores de marca:**
| Variable | Valor | Uso |
|----------|-------|-----|
| Azul primario | `#0C447C` | Headers, títulos principales |
| Azul secundario | `#185FA5` | Acentos, botones activos |
| Azul claro | `#E6F1FB` | Fondos de sección |
| Verde éxito | `#276749` / `#16a34a` | Badges "Normalizado", confirmaciones |
| Naranja advertencia | `#8a5a08` | Badges "Revisar", alertas |
| Rojo peligro | `#9b1c1c` | Errores, validaciones fallidas |

**Tipografía:** `system-ui, -apple-system, 'Segoe UI', sans-serif`

**Breakpoints responsivos:**
- Desktop: max-width 1100-1300px
- Tablet: 760px (2 columnas → 1)
- Móvil: 480px (tarjetas apiladas)
- Móvil pequeño: 380px (logo ajustado)

---

## Flujo de datos completo (módulo Revistas)

```
Pegar desde Excel (Ctrl+V)
        ↓
Parsear texto separado por tabulaciones
        ↓
Detectar fila de encabezados (compara con REF_COLS)
        ↓
PARA CADA fila de datos:
  ├─ normalizarRegistro() procesa independientemente
  ├─ Retorna: {data[], mod[], ok}
  └─ Acumula en pendingResults[]
        ↓
Animación barra de progreso (15→100%)
        ↓
Renderizar tabla de resultados
  ├─ Resaltar campos modificados (amarillo)
  ├─ Mostrar badges de estado
  └─ Estadísticas agregadas
        ↓
Opcional: Validación contra DEMO_ROWS (7 registros)
        ↓
Exportar CSV o Generar Reporte
```

---

## Decisiones técnicas importantes

1. **Sin framework JS:** toda la aplicación es Vanilla JS. Decisión deliberada para mantener portabilidad y cero dependencias de build.

2. **Sin backend:** los datos no se persisten. Cada sesión es efímera. Esto facilita el despliegue pero limita funcionalidades como historial, usuarios y batch desde archivos.

3. **CSS inline por archivo:** cada módulo incluye sus estilos. Facilita la autonomía de cada módulo pero dificulta la consistencia global y el mantenimiento.

4. **Motor stateless:** `normalizarRegistro()` no tiene estado entre llamadas, lo que permite escalabilidad teórica a miles de registros.

5. **Datos de demo reales:** los 7 registros de prueba son datos bibliográficos reales de revistas de tortura y derecho internacional, lo que garantiza cobertura de casos reales.

---

## Lo que NO existe todavía (áreas de mejora futura)

### Alta prioridad
- [ ] **Backend / API** para persistencia de datos y procesamiento server-side
- [ ] **Módulo Libros** — lógica MARC21 diferente a revistas
- [ ] **Módulo Tesis** — campos específicos (tutor, institución, programa)
- [ ] **OCR real** — integración con Tesseract.js o API externa (Google Vision, etc.)
- [ ] **Exportación avanzada** — Excel, JSON, MARCXML, además de CSV

### Prioridad media
- [ ] **Cuentas de usuario** — autenticación para guardar historial
- [ ] **Historial de sesiones** — poder retomar trabajo anterior
- [ ] **Carga desde archivo** — CSV/Excel directo, no solo pegar
- [ ] **Sistema de CSS compartido** — extraer estilos comunes a un `styles.css`
- [ ] **Módulo Periódicos, Mapas, Audiovisual, Documentos, CD/DVD**

### Mejoras técnicas
- [ ] **Hoja de estilos compartida** en lugar de CSS inline por archivo
- [ ] **LocalStorage** para persistencia básica sin backend
- [ ] **Web Workers** para procesamiento batch sin bloquear la UI
- [ ] **Tests unitarios** para `normalizarRegistro()` (actualmente solo validación visual)
- [ ] **Internacionalización (i18n)** si se requiere soporte multilingüe

---

## Información del proyecto

- **Desarrollador:** Jean Barboza (jeanbarbozag05@gmail.com)
- **Repositorio:** https://github.com/JCBarboG/Automatizaci-n-Bibliogr-fica-
- **Rama principal:** `main`
- **Commits clave:**
  - `67b18ac` — actualización de nombre y módulo OCR
  - `90458f4` — generador de referencias APA 7, botones demo eliminados de revistas
  - `d2aaf9a` — commit inicial: plataforma BIBNORM

---

## Cómo continuar el desarrollo (para Claude u otro colaborador)

1. **Lee este README** para entender el contexto completo.
2. **Revisa `modulos/revistas.html`** — es el módulo modelo; cualquier módulo nuevo debe seguir su patrón.
3. **La función `normalizarRegistro()`** en `revistas.html` es el corazón del sistema; si añades un módulo nuevo (libros, tesis), crea una función equivalente con las reglas MARC21 del tipo de material.
4. **No rompas la compatibilidad con pegar desde Excel** — es el flujo de entrada principal.
5. **CSS inline por ahora** — si se refactoriza, crear `css/shared.css` y migrar gradualmente.
6. **Sin build tools** — mantener archivos estáticos para facilitar despliegue.
