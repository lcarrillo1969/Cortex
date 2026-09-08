# Cortex: LLM External Brain

**Cortex** transforma un modelo de lenguaje (LLM) sin estado en un **entorno de trabajo estructurado y persistente**: un "cerebro externo" que gestiona la memoria, el contexto, la configuración de comportamiento y el guardado, dejando que la IA se dedique a razonar.

A diferencia de un chat tradicional, Cortex está construido para **sesiones largas y complejas**: mantiene el contexto de la conversación mediante resúmenes automáticos, guarda todo localmente en tu navegador, y se adapta a cualquier propósito — storyteller, generador de prompts, chatbot, discusión técnica, ingeniero de prompts para imágenes, etc.

> 🔗 **Generador:** https://perchance.org/seven-cortex-gem-v5
> 📂 **Repositorio / Documentación:** https://github.com/lcarrillo1969/Cortex

---

## Índice

1. [Primeros pasos](#1-primeros-pasos)
2. [La interfaz](#2-la-interfaz)
3. [Agentes y Absolute Premises (el corazón de Cortex)](#3-agentes-y-absolute-premises)
4. [IMAGINER: generación de imágenes y prompts visuales](#4-imaginer-generación-de-imágenes-y-prompts-visuales)
5. [Gestión de memoria: el Termómetro y el Resumen Ejecutivo](#5-gestión-de-memoria)
6. [Sesiones: guardado, recuperación y compartir](#6-sesiones)
7. [Session Archive: la conversación tal como sucedió](#7-session-archive)
8. [Scratchpad (privado)](#8-scratchpad-privado)
9. [Configuración (⚙️)](#9-configuración)
10. [Preguntas frecuentes](#10-preguntas-frecuentes)
11. [Arquitectura técnica (para desarrolladores)](#11-arquitectura-técnica)

---

## 1. Primeros pasos

Abre el generador. **No necesitas configurar nada** para empezar: Cortex viene con un agente por defecto — un asistente general, directo y útil.

1. Escribe tu mensaje en la caja de texto de la parte inferior.
2. Pulsa **Send** (o `Enter`). Usa `Shift + Enter` para saltos de línea.
3. A partir del primer mensaje se crea automáticamente una **sesión** (sin botón de guardar: todo se autoguarda).

Todo lo que escribas, cada respuesta de la IA y cada resumen de memoria se guarda solo en tu navegador (IndexedDB). Puedes cerrar la pestaña y retomar donde lo dejaste.

---

## 2. La interfaz

Cortex divide la pantalla en dos áreas:

### 🖥️ Canvas principal (izquierda)
El chat: historial de mensajes, streaming de respuestas en tiempo real y la caja de entrada.

### 🧠 External Brain (derecha)
El panel de control modular. Contiene, de arriba a abajo:

| Módulo | Función |
|---|---|
| **Sesiones** | Nueva, importar, lista de conversaciones, renombrar, exportar, compartir, eliminar |
| **1. Absolute Premises** | La "ley" que define quién es la IA y cómo responde |
| **1.5 IMAGINER** | El rol visual que convierte el contexto en prompts para imágenes |
| **2. Executive Summary** | La memoria comprimida de la conversación (editable) |
| **Session Archive** | Copia fiel y numerada de cada respuesta, tal como se generó |
| **3. Scratchpad (Private)** | Notas personales que **nunca** se envían a la IA |

### 📐 Panel retráctil
El botón **▸ / ◂** en el borde superior derecho colapsa o expande el External Brain para que el canvas principal ocupe todo el ancho. El estado se recuerda entre visitas.

### Barra superior
- **Termómetro de memoria** — uso del contexto en tiempo real.
- **📖 Docs** — abre esta misma documentación (se descarga en vivo desde GitHub).
- **⚙️ Settings** — configuración del sistema.

---

## 3. Agentes y Absolute Premises

### ¿Qué es un Agente?

Un **Agente** es una configuración de comportamiento guardada: un conjunto de reglas (la *Absolute Premise*) que le dice a la IA quién es, cómo piensa y en qué formato responde. Cuando guardas una premisa como plantilla, estás creando un **agente reutilizable**.

El término correcto en Cortex es **Agente**: cada plantilla guardada es un "especialista" que puedes invocar con un clic. La app muestra el nombre del agente activo junto al título de los módulos *1. Absolute Premises* y *1.5 IMAGINER* (por defecto: `Cortex` e `IMAGINER`; si editas el texto sin guardarlo como plantilla, aparece como `Custom`).

### La Absolute Premise es ley inquebrantable

Todo lo que escribas en la caja *1. Absolute Premises* es la directiva de más alta prioridad para el modelo: se lee **primero, en cada turno**. Define roles, formato estricto, tono, restricciones absolutas, etc.

El cambio es **inmediato**: en cada `Send`, Cortex lee la caja en ese instante. Puedes cambiar de personalidad a mitad de una charla sin perder la memoria de la sesión — la IA recuerda todo lo hablado, pero responde bajo las nuevas reglas.

### El Gestor de Plantillas (🗂️) — tu biblioteca de agentes

El botón **🗂️** junto al título del módulo abre el gestor:

- **💾 Save current** — guarda la premisa actual como un agente con nombre.
- **Usar** — carga un agente guardado en la caja al instante.
- **🔎 Search** y filtros **All / Absolute Premises / IMAGINER**.
- **⬇️ Export** — descarga todos tus agentes como JSON (copia de seguridad / migración).
- **📂 Import** — importa un archivo JSON de agentes (detecta automáticamente si el archivo es de sesiones o de plantillas).
- **✏️ Edit / 🗑️ Delete** — editar o eliminar un agente guardado.

> **Consejo de flujo:** construye una premisa, pruébala, y cuando consigas el comportamiento ideal guárdala. Con el tiempo acumulas un equipo de especialistas a la carta: tu traductor estricto, tu ingeniero de prompts, tu novelista, tu revisor de código… todos a un clic.

### Nuevo *manteniendo* el agente

El botón **🔄** junto al título de *Absolute Premises* crea una sesión nueva en blanco **conservando exactamente** la premisa actual. Útil para empezar un tema nuevo con el mismo agente, sin perder la personalidad. (El botón **✨ New** del módulo de sesiones, en cambio, restaura la premisa por defecto.)

---

## 4. IMAGINER: generación de imágenes y prompts visuales

Cortex incluye un módulo de imagen para **probar prompts**, **construir prompts a partir del contexto de la conversación** y **generar imágenes** — todo sin salir del chat.

### El rol IMAGINER (módulo 1.5)

Es un **rol visual paralelo**: su texto define cómo se convierte el contexto del chat en un prompt de imagen (estilo, iluminación, composición, idioma del prompt, etc.). Por defecto es *"You are IMAGINER, a visual prompt creator…"*, y puedes editarlo para cambiar el estilo de imagen. Clave:

- Es un **camino paralelo**: no se envía al modelo en las respuestas normales.
- **No toca** el historial, el resumen ni el Session Archive.
- Tiene su propio gestor de plantillas (🗂️) para guardar varios "estilos visuales".

### El botón 🎨 Image

Junto al botón Send. Abre el **modal de imagen** usando como escena lo que tengas escrito (o, si está vacío, el último mensaje de la conversación). El modal ofrece:

- **✨ Create from context** — el rol IMAGINER + el contexto reciente + el resumen ejecutivo producen un prompt visual nuevo (también regenera el actual).
- **📝 From user prompt** — carga tu texto tal cual, saltando la transformación del IMAGINER.
- **📋 Copy** — copia el prompt.
- **🎨 Generate** — genera de 1 a 4 imágenes.
- **Aspecto** — `1:1` (cuadrado), `▭ Horizontal`, `▯ Vertical`.
- **🗑️ Clear** — limpia los resultados.

### Cada imagen guarda su propia receta

Al hacer clic en una imagen se abre un **visor a tamaño completo** con su metadato (prompt, negative prompt, seed, resolución y timestamp) en texto seleccionable/copiable. Al **descargar** la imagen, ese mismo metadato se incrusta dentro del archivo PNG (chunk `tEXt` `parameters`, el estándar que leen herramientas como PNG Info / Stable Diffusion): la receta viaja con la imagen.

---

## 5. Gestión de memoria

### El problema
Los modelos de lenguaje tienen un límite estricto de tokens que pueden recordar a la vez. En Cortex el presupuesto por defecto es **4000 tokens**.

### El Termómetro
La barra superior muestra en tiempo real cuánto de esa memoria activa está ocupada (verde → amarillo → rojo).

### El Resumen Ejecutivo (compresión automática)
Cuando el historial sin resumir supera el umbral (por defecto **1500 tokens**), Cortex invoca al LLM en silencio para que lea los mensajes antiguos y los convierta en un **resumen ejecutivo**, liberando espacio. Verás en el chat un aviso del sistema:

```
--- Previous history compressed into Executive Summary ---
```

Los resúmenes se apilan en el módulo *2. Executive Summary*, separados por `== RESUMEN ==`. En cada `Send`, Cortex ensambla el prompt maestro así:

1. **Absolute Premises** (reglas, primero siempre).
2. **Executive Summary** (memoria comprimida, los bloques más recientes primero).
3. **Historial reciente** (los mensajes sin resumir).

Si el espacio se agota, se descartan los resúmenes más antiguos — nunca se corta a la mitad, manteniendo la estabilidad del prompt.

### Edición manual y Force Sync
El resumen es **editable**: si la IA omitió un detalle o resumió mal, corrígelo a mano — tus ediciones se inyectan en el siguiente prompt. El botón **Force Sync** fuerza una síntesis inmediata del historial pendiente.

### Configuración fina
En **⚙️ Settings** puedes ajustar el umbral de auto-resumen, el presupuesto de entrada de la síntesis y la longitud mínima del resumen.

---

## 6. Sesiones

### Autoguardado invisible
No existe botón "Guardar": en el momento en que envías tu primer mensaje se crea una sesión y desde entonces **todo se autoguarda** en tu navegador (IndexedDB): historial, premisas, IMAGINER, resumen, scratchpad y archive.

### La lista de sesiones (panel derecho)
- **Título automático** — la sesión se bautiza con las primeras palabras de tu primer mensaje (`[N] …`).
- **✏️ Rename** — dale un nombre reconocible ("Debate sobre IAs", "Ideas del fin de semana").
- **✨ New** — lienzo en blanco para un tema nuevo (tu sesión anterior queda guardada en la lista).
- **🔄** — nueva sesión en blanco *manteniendo* el agente actual (ver sección 3).
- **Clic en una sesión** — la recuperas al instante, con todo el contexto, como si no hubiera pasado un segundo.
- **🗑️ Delete** — elimina la sesión seleccionada definitivamente.

### 💾 Exportar una sesión
Descarga un JSON completo: historial, premisas, IMAGINER, resumen, scratchpad y archive. Ideal para copias de seguridad o migración.

### 📂 Importar
Acepta tanto **sesiones** como **plantillas de agentes** (los detecta automáticamente). La sesión importada se añade como nueva, sin sobrescribir nada.

### 🔗 Compartir entre dispositivos (Share)
El botón **🔗 Share** publica la sesión actual en un enlace permanente:

```
https://perchance.org/seven-cortex-gem-v5?import=<shareName>
```

Abre ese enlace en **cualquier dispositivo** y la sesión se importa a la base de datos local de ese dispositivo. El enlace se copia al portapapeles. La sesión compartida nunca sobrescribe sesiones existentes.

### Reabrir la última sesión
Por defecto, al volver a abrir el generador se reabre automáticamente la última sesión activa (se puede desactivar en ⚙️ Settings).

---

## 7. Session Archive

Es la **versión histórica fiel** de la conversación, distinta del historial dinámico:

- Guarda una **copia numerada y secuencial** de cada respuesta de la IA, etiquetada con la pregunta del usuario (`USER`) que la provocó — **tal como sucedió**, sin resúmenes ni compresión.
- **No se envía al modelo** — es un registro puro.
- Sirve para copiar o exportar la salida completa de la sesión.

Desde el módulo *Session Archive* (botón **Show**) puedes:

- **📋 Copy all** — copiar toda la conversación.
- **⬇️ Export all** — descargar la conversación completa como texto plano.
- **⬇️ Export responses only** — descargar **solo las respuestas de la IA** (sin las preguntas). Útil, por ejemplo, si eres un storyteller y quieres únicamente la narración final, limpia, sin el texto original comprimido.

---

## 8. Scratchpad (Private)

Tu bloc de notas privado dentro de la sesión. El texto aquí **jamás se envía a la IA** ni entra en el contexto. Úsalo para enlaces, ideas temporales o notas de trabajo. Se autoguarda con la sesión.

---

## 9. Configuración

### Pestaña General
- **Auto-summarize trigger (tokens)** — umbral que dispara la compresión del historial (por defecto 1500).
- **Synthesis input budget (tokens)** — presupuesto de tokens para la llamada de síntesis (por defecto 5000).
- **Minimum summary length (chars)** — longitud mínima para aceptar un resumen nuevo (por defecto 20).
- **Reopen last session** — reabrir la última sesión al abrir el generador (por defecto activado).
- **Restore defaults** — vuelve a los valores por defecto.
- Estadísticas de uso de la base de datos local.

### Zona de peligro
- **Delete all sessions** — borra todas las sesiones (primero descarga automáticamente una copia de seguridad de todo).
- **Full reset** — borra la base de datos completa y los ajustes. Debes escribir `RESET` para confirmar.

---

## 10. Preguntas frecuentes

**¿Dónde se guardan mis datos?**
Localmente, en tu navegador (IndexedDB). No dependen de servidores externos. El único uso de servidor es el enlace **Share** (para transferir una sesión entre dispositivos) y, por supuesto, las llamadas al LLM y la generación de imágenes.

**¿Cortex pierde el hilo en conversaciones largas?**
No. El resumen ejecutivo comprime el pasado y mantiene la coherencia. Con límite de tokens tienes todos los sistemas (todo sistema tiene su límite), pero Cortex lo estira al máximo.

**¿Puedo cambiar de personalidad sin perder el contexto?**
Sí. Cambia la Absolute Premise y el siguiente `Send` ya usa las nuevas reglas; la memoria de la sesión se conserva.

**¿Sirve para historias?**
Sí. El Session Archive con *Export responses only* te da la narración final limpia, y el IMAGINER te genera los prompts visuales de tus escenas.

**¿Puedo llevar mi configuración a otro dispositivo?**
Exporta tus **agentes** (🗂️ → Export) y/o la **sesión** (💾 Export) como JSON e impórtalos donde quieras. O usa el enlace **🔗 Share** para mover una sesión completa.

---

## 11. Arquitectura técnica

Cortex es un generador de Perchance. El código vive en `index.html` (una sola aplicación IIFE) y `main.pjs` (imports de plugins y metadatos).

### Persistencia
- **IndexedDB** (`CortexDB`) — base de datos local: `sessions` y `premiseTemplates`.
- **localStorage** — ajustes (`cortexSettings`), última sesión activa, estado del panel retráctil.
- **Editable uploads** (upload-plugin) — publicación del enlace **Share**; cada enlace se lee con `?import=<name>` y se importa como sesión nueva.

### Plugins de Perchance usados
- `ai-text-plugin` — motor del LLM (chat con streaming, síntesis de resúmenes en silencio).
- `text-to-image-plugin` — generación de imágenes del IMAGINER.
- `upload-plugin` — publicación de enlaces Share.
- `super-fetch-plugin` — respaldo de red para leer el README / enlaces Share sin CORS.

### Módulos principales (objetos)
| Objeto | Responsabilidad |
|---|---|
| `State` | Estado global (sesión activa, historial, ajustes de tokens) |
| `DB_Service` | Capa de datos IndexedDB |
| `Session_Manager` | Ciclo de vida de sesiones: crear, listar, renombrar, exportar, importar, compartir |
| `Premise_Manager` | Gestor de plantillas de agentes (Absolute Premises e IMAGINER) |
| `LLM_Service` | Llamadas al modelo, streaming, watchdog de inactividad |
| `Memory_Manager` | Termómetro, síntesis de resúmenes, ventana de contexto |
| `Image_Handler` | IMAGINER: prompts visuales, generación, metadatos PNG |
| `UI_Handlers` | Render del chat (markdown), archive, scroll, panel retráctil |
| `Config_Manager` | Ajustes de usuario y persistencia |

### Cómo funciona el prompt maestro (por turno)
```
1. Absolute Premises      ← reglas, primero y siempre
2. Executive Summary      ← memoria comprimida (bloques más recientes primero)
3. Historial reciente     ← mensajes sin resumir
```
El presupuesto se reparte con una ventana "más reciente primero": si no cabe todo, se descartan los bloques de resumen más antiguos, nunca se parte un mensaje a la mitad.

### Seguridad / saneado
- Las respuestas del modelo se renderizan como **markdown** (`marked`), pero todo HTML crudo se escapa — una instrucción inyectada en la salida del LLM no se ejecuta.
- El **Scratchpad** y el **Session Archive** nunca se envían al modelo.

### Documentación en vivo
El botón **📖 Docs** descarga este mismo README desde `https://raw.githubusercontent.com/lcarrillo1969/Cortex/main/README.md` y lo renderiza dentro de la app, de modo que la documentación del repositorio y la del generador siempre coinciden.

---

*Cortex: un cerebro externo que gestiona la logística — almacenamiento, contexto, formato y optimización de tokens — para que la inteligencia artificial se dedique a razonar.*
