# 01.01 Cortex: Intoduccion

La intención principal de este código es transformar un modelo de lenguaje (LLM) crudo y sin estado en un entorno de trabajo estructurado y persistente, similar a la arquitectura de un IDE personalizado equipado con paneles de propiedades y navegadores de estado.

El sistema, llamado Cortex, resuelve los dos problemas más grandes al interactuar con LLMs en sesiones largas: la pérdida de memoria cuando se agota la ventana de contexto y la falta de persistencia de los datos.

Aquí tienes el desglose de lo que el código busca lograr a través de sus componentes principales:

## 1. Gestión Inteligente de Memoria (El "Termómetro" y el Resumen Automático)
El núcleo de la innovación de este código está en el objeto Memory_Manager. Dado que los modelos de IA tienen un límite estricto de palabras que pueden recordar (establecido aquí en 4000 tokens), el código implementa un sistema para no "olvidar" nunca el contexto:
* **Monitoreo visual:** Un "termómetro" en la interfaz muestra qué porcentaje de la memoria de la IA se está utilizando.
* **Compresión de estado:** Cuando el historial de chat nuevo supera cierto límite (1500 tokens), el sistema invoca al LLM en segundo plano (generateSilent) para que lea los mensajes antiguos y los convierta en un resumen ejecutivo.
* **Inyección dinámica:** En cada nuevo mensaje del usuario, el código ensambla un prompt maestro que incluye las reglas absolutas, el resumen comprimido y el historial reciente. Si el espacio se agota, sacrifica los resúmenes más antiguos pero nunca corta a la mitad, manteniendo la estabilidad del prompt.

## 2. Arquitectura de Interfaz Dividida
El código divide la pantalla en dos áreas funcionales distintas para separar la interacción de la configuración:
* **Canvas Principal (Izquierda):** Maneja la interacción típica de chat, el historial de mensajes y el streaming de texto en tiempo real (UI_Handlers.streamToLastMessage).
* **External Brain (Derecha):** Funciona como un panel de control modular. Permite inyectar directivas irrompibles (Absolute Premises), editar el resumen dinámico si la IA omitió algo importante, y mantener un Scratchpad privado (notas del usuario que jamás se envían al modelo).

## 3. Persistencia de Datos Locales (IndexedDB)
A través del DB_Service y Session_Manager, el código implementa un sistema de base de datos local directamente en el navegador del usuario.
* Permite crear múltiples áreas de trabajo (sesiones), guardarlas automáticamente y navegar entre ellas sin perder datos.
* Incluye lógica para importar, exportar (en formato JSON) y renombrar sesiones, asegurando que el progreso del usuario no dependa de servidores externos y se mantenga entre recargas de la página.

## 4. Auditoría y Trazabilidad (Session Archive)
El módulo "Session Archive" es una característica puramente de registro. Guarda una copia exacta, numerada y secuencial de cada par "Pregunta del Usuario / Respuesta de la IA". A diferencia del historial dinámico que se comprime para ahorrar tokens, este archivo se mantiene intacto para que el usuario pueda copiar o exportar la conversación completa en texto plano.

En resumen, la intención del desarrollador fue construir un "cerebro externo" que maneja toda la logística de almacenamiento, formato y optimización de tokens mediante código, dejando que la inteligencia artificial se dedique exclusivamente a razonar sobre la información limpia que recibe.

---

# 02.01 - Cortex: El inicio

# Manual de Usuario: Cortex - LLM External Brain
Cortex es un entorno de trabajo diseñado para interactuar con Inteligencia Artificial. A diferencia de un chat tradicional, está construido para manejar sesiones largas y complejas sin que la IA "olvide" de qué estaban hablando.

# 1. Tu Primera Vez en Cortex (El panel principal)
Al abrir Cortex por primera vez, verás una interfaz dividida, pero no necesitas configurar absolutamente nada para empezar. Por defecto, Cortex está configurado con una directiva inicial que lo convierte en un asistente general, directo y muy útil. Su comportamiento inicial está comprobado para resolver la inmensa mayoría de las consultas.

Aquí te explicamos cómo es el flujo de trabajo natural:

### 1) Interactuando con Cortex
La comunicación es tan sencilla como en cualquier otra IA. En la parte inferior de la pantalla encontrarás la caja de texto. Simplemente escribe tu pregunta, duda, o pega tu código, y haz clic en el botón Send (o presiona Enter). Si necesitas hacer saltos de línea mientras escribes, usa Shift + Enter.

### 2) Coherencia y Contexto Prolongado
A partir de tu primer mensaje, Cortex entra en acción. Mientras tú conversas normalmente, el sistema se encarga de que la IA mantenga un hilo conductor perfecto. No importa si tu proyecto toma horas o requiere decenas de idas y vueltas; Cortex mantendrá el contexto de la charla coherente en todo momento, recordando los detalles que ya discutieron.

### 3) El "Termómetro" de Memoria (Superando los límites)
Todos los modelos de lenguaje (como el motor de Perchance que impulsa a Cortex) tienen un límite estricto de cuántas palabras o "tokens" pueden recordar a la vez. Cortex soluciona este problema y expande esa capacidad a través de un control visual: el termómetro de Context Memory ubicado en la parte superior.

Esta barra te indica en tiempo real cuánto espacio de la memoria "activa" de la IA estás ocupando. A medida que la charla avanza, verás que la barra empieza a llenarse (pasando de verde a amarillo).

### 4) El Resumen Automático (Cuando la barra se llena)
Cortex no deja que la memoria colapse. Cuando el termómetro alcanza niveles críticos (alrededor del 80-85%), el sistema pausa un segundo y actúa por su cuenta. Cortex lee tus mensajes antiguos, los comprime en un resumen ejecutivo para extraer lo verdaderamente importante, y libera espacio en la memoria para que puedas seguir trabajando.

Cuando esto ocurre, notarás un mensaje automático del sistema insertado en tu historial de chat que dice:
`--- Previous history compressed into Executive Summary ---`
Este mensaje te confirma que Cortex ha empaquetado el pasado con éxito. La ventana de memoria vuelve a tener espacio disponible y tú puedes continuar interactuando con la IA sin perder la coherencia ni el contexto general de tu proyecto. ¡Tú solo preocúpate por chatear, el sistema gestionará la memoria!

---

# 02.02 Cortex: Sessiones

# 2. Sesiones y Autoguardado: Nunca pierdas el hilo de tus charlas
Una de las mayores ventajas de Cortex frente a un chat tradicional es que no tienes que preocuparte por perder tu progreso si cierras el navegador por accidente o si quieres pausar una conversación para seguirla mañana. Todo funciona a través de un sistema de autoguardado invisible.

### 1) El nacimiento de una charla
Al abrir Cortex, verás un lienzo en blanco con el mensaje:
> "System initialized. Awaiting context or input."

En el instante exacto en que envías tu primer mensaje —ya sea para resolver una duda, explorar una idea o simplemente para quejarte de lo malo que es Gemini—, Cortex crea de manera automática una Sesión independiente en segundo plano. No hay un botón de "Guardar"; a partir de ese momento, cada cosa que escribas, cada respuesta de la IA y cada compresión de su memoria se guarda sola en tu navegador.

### 2) Identificando y Renombrando tu charla
En la parte superior del panel derecho encontrarás la lista de tus sesiones. En cuanto inicies la charla, verás aparecer una barra en la parte inferior de esta lista con los controles:
* **Título automático:** Cortex leerá tu primer mensaje y usará esas primeras palabras para bautizar la sesión temporalmente (por ejemplo: [1] Hola, quiero hablar sobre...).
* **Botón ✏️ Rename (Renombrar):** Como es probable que acumules varias conversaciones diferentes, es muy recomendable hacer clic aquí para darle un nombre que tú reconozcas rápidamente, como "Debate sobre IAs" o "Ideas para el fin de semana".

### 3) El ciclo de vida: Empezar, Recuperar y Eliminar
Cortex está diseñado para que mantengas varias charlas al mismo tiempo sin mezclar los temas ni confundir a la IA. Tienes control total sobre tus sesiones:
* **✨ Botón New (Cambiar de tema):** Si estás inmerso en una conversación y de repente quieres preguntar algo que no tiene nada que ver, haz clic en New. Esto limpiará el panel principal para que empieces de cero. Tu charla anterior no se borra, simplemente se guarda y se queda esperando en tu lista.
* **Retomar el hilo (Tus charlas guardadas):** Este es el corazón de Cortex. En tu lista verás todas tus conversaciones pasadas. Si haces clic en cualquiera de ellas, la interfaz se actualiza al instante. Recuperarás todo tu historial de chat y podrás seguir hablando con la IA exactamente donde lo dejaste, con ella recordando todo el contexto anterior como si no hubiera pasado un segundo.
* **🗑️ Botón Delete (Limpieza):** Si una charla llegó a su fin definitivo o solo estabas haciendo pruebas, asegúrate de tener seleccionada esa sesión y haz clic en Delete para eliminarla para siempre y mantener tu lista ordenada.

---

# 02.03 Cortex: Absolute Premises

# 3. Absolute Premises: Cambiando la personalidad de la IA
Aunque el nombre "Absolute Premises" (Premisas Absolutas) suena un poco abstracto, en la práctica es el panel más poderoso de Cortex. Esta caja de texto dicta quién es la IA y cómo debe comportarse. Todo lo que escribas aquí se convierte en la ley inquebrantable para el modelo.

### 1) El cambio es inmediato (En tiempo real)
Una particularidad clave de Cortex es que no "congela" la personalidad al inicio de la charla. Cada vez que haces clic en Send, el sistema lee lo que está escrito en la caja de Absolute Premises en ese exacto milisegundo.

Esto significa que el cambio es inmediato. Si estás a la mitad de una charla normal y decides borrar la premisa para escribir: "A partir de ahora, responde siempre como si fueras un pirata sarcástico", la IA adoptará esa personalidad en su próxima respuesta, sin necesidad de reiniciar nada.

### 2) Cómo iniciar una nueva charla con una personalidad distinta
Si quieres empezar un tema de cero con una instrucción muy específica (por ejemplo, para que actúe como un traductor estricto o un experto en código), es muy importante seguir el orden correcto de los pasos.

Existe una pequeña trampa: el botón "New" siempre restaura el texto por defecto. Si escribes tu nueva personalidad y luego le das a "New", el sistema borrará lo que escribiste.

Para hacerlo correctamente, sigue estos 4 pasos exactos:
1. Haz clic en el botón ✨ New para limpiar el chat y preparar un lienzo en blanco.
2. Ve a la caja de Absolute Premises, borra el texto que aparece por defecto, y escribe o pega tus nuevas instrucciones (ej. "Eres un experto en bases de datos. No uses saludos, solo dame el código").
3. Ve a la caja de chat principal en la esquina inferior izquierda y escribe tu primera pregunta.
4. Haz clic en Send.

Al hacerlo en este orden, la IA nacerá en esa nueva sesión respetando exactamente las reglas que le acabas de imponer.

### 3) Caso de uso: Cambiando de rol a mitad de la historia
Para entender el verdadero poder de cambiar la personalidad sobre la marcha, debes recordar algo clave: cambiar la premisa no borra la memoria de la sesión. La IA recuerda todo lo que han hablado, pero cambia radicalmente su forma de procesarlo y responder.

Imagina que estás escribiendo un relato histórico. Para lograr el tono adecuado, en tu caja de Absolute Premises colocas lo siguiente:
> "Eres Miguel de Cervantes. Escribes aventuras épicas y poéticas, utilizando un vocabulario clásico del Siglo de Oro español."

Conversas con la IA, desarrollan a los personajes y llegan a una escena visualmente increíble donde el protagonista se enfrenta a una tormenta. En ese momento, decides que quieres ilustrar esa escena exacta usando otra IA generadora de imágenes (como Midjourney o DALL-E).

El problema es que un generador de imágenes no sabe de qué trata tu cuento, necesita un prompt técnico y descriptivo. Sin salir de tu sesión, vas a la caja de Absolute Premises, borras la instrucción de Cervantes y colocas esta nueva:
> "Eres un Experto en Prompt Engineering para IA de imágenes. Analiza la escena actual de nuestra historia y redacta un prompt descriptivo en inglés. Enfócate en la iluminación, el estilo artístico, los colores y la composición fotográfica. No escribas narrativa."

Al enviar tu siguiente mensaje en el chat (por ejemplo: "Genera el prompt para la escena de la tormenta"), el cambio es instantáneo. La IA dejará de hablar como un novelista del siglo XVII. Sin embargo, como aún conserva todo el contexto del cuento en su memoria, sabrá exactamente quién es el personaje, cómo está vestido y dónde se encuentra, entregándote un prompt técnico perfecto.

Una vez que tengas el texto para tu imagen, simplemente vuelves a escribir la premisa de Cervantes en la caja, y continúan escribiendo la novela como si nada hubiera pasado.

### 4) Gestor de Plantillas: Cómo no perder tus personalidades (El ícono 🗂️)
En el ejemplo anterior, borraste la instrucción de Cervantes para escribir la del Experto en Prompts. Pero, ¿qué pasa cuando quieres volver a Cervantes? Estar copiando y pegando textos desde un bloc de notas externo es molesto y rompe el ritmo de trabajo.

Para solucionar esto, Cortex incluye un Gestor de Plantillas (Premise Templates) integrado. Si miras el título del módulo 1. Absolute Premises, verás un pequeño botón con el ícono de un archivero: 🗂️. Al hacer clic allí, se abrirá tu biblioteca personal de comportamientos.

¿Cómo funciona el flujo de guardado y recuperación? Siguiendo el mismo caso de la novela:
1. **Guardar antes de borrar:** Antes de borrar tu instrucción de Cervantes, haces clic en 🗂️ y luego en el botón 💾 Save current (Guardar actual). El sistema te pedirá un nombre; le pones "Cervantes Histórico" y listo, ya quedó guardado para siempre en tu base de datos local.
2. **Crear nuevas sobre la marcha:** Ahora sí, borras la caja, escribes tu instrucción de Experto en Prompts y generas tu imagen. Si te gustó cómo funcionó ese rol técnico, vuelves a abrir el archivero (🗂️) y lo guardas como "Creador de Prompts Visuales".
3. **Recuperar con un clic:** Cuando estés listo para seguir escribiendo tu novela, abres el archivero 🗂️. Verás tu lista de plantillas guardadas. Simplemente buscas "Cervantes Histórico" y haces clic en el botón Usar.

Cortex reemplazará automáticamente el texto de la caja con tu premisa original de Cervantes. En cuestión de segundos y sin salir de la pantalla, habrás saltado de un novelista clásico a un ingeniero técnico y de regreso al novelista, sin perder el hilo de tu historia ni tener que reescribir una sola instrucción.

### 5) Premise Templates Construyendo tu arsenal: Un verdadero Gestor de Personalidades
En la práctica, este sistema de plantillas trasciende la simple función de "guardar textos" para convertirse en tu propio Gestor de Personalidades. Su mayor valor no radica solo en salvarte de un apuro en la sesión actual, sino en el uso subsecuente a lo largo del tiempo.

Crear la premisa perfecta —lograr que la IA responda exactamente con el tono, nivel técnico y formato que deseas— requiere ensayo y error. Una vez que consigues ese comportamiento ideal, no querrás perderlo.

Al guardar estas premisas exitosas, estás construyendo una biblioteca de especialistas. Con el tiempo, dejarás de ver el ícono 🗂️ como un cajón de textos y lo empezarás a ver como tu equipo de expertos a la carta. Ya sea que abras Cortex mañana, la próxima semana o en un proyecto completamente nuevo, tendrás a un solo clic de distancia a tu experto en código, tu ingeniero de prompts, tu traductor estricto o tu novelista clásico, todos listos para "entrar a trabajar" en cualquier momento y en cualquier charla.
¿Le gustaría que genere un cuestionario de repaso técnico basado en este manual para evaluar el conocimiento del sistema?

Fuentes:
02.02 Cortex: Sessiones
02.03 Cortex: Absolute Premises
01.01 Cortex: Intoduccion
02.01 - Cortex: El inicio
