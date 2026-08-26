---
name: criteria
description: Convierte varias preguntas sueltas (nombres, prioridades, alcance, gustos de diseño) en un cuestionario HTML de un solo archivo, con barra de progreso, opción "Otra" siempre disponible y el lenguaje visual del proyecto donde se pide (o uno neutro si no hay otro), con un botón que junta todas las respuestas en un bloque de texto listo para pegar de vuelta en el chat. Úsalo cuando el usuario diga "hazme un cuestionario", "criteria", "pregúntame con un formulario", "que lo rellene y te lo pego", o cuando haya que decidir varias cosas de tipo distinto (texto libre, elegir una opción, elegir varias, puntuar del 0 al 10) y dictarlas una por una sea más lento que rellenar un formulario y copiar.
disable-model-invocation: true
---

# Criteria

Para cuando hay que preguntar **varias cosas de tipo distinto** de golpe (no solo "elige la
2"), y quien contesta prefiere rellenarlas con teclado y ratón y devolver **todo el bloque de
una vez**, en vez de contestar cada pregunta una por una en el chat.

No sustituye a otras formas de preguntar, las completa:

- Un interrogatorio **en el chat**, pregunta a pregunta, sigue siendo mejor cuando hace falta
  razonar entre una respuesta y la siguiente antes de poder formular la que viene después.
- Un cuestionario en **Markdown** sigue siendo mejor para un tercero (un cliente, un
  colaborador) que no tiene un agente de código instalado y solo puede leer texto plano.
- **Criteria** es un formulario en el **navegador**, para quien ya tiene un agente de código
  delante, cuando las preguntas se pueden hacer todas de golpe y lo que sobra es tener que
  dictarlas o teclearlas una a una.

## Proceso

### 1. Reúne las preguntas

Las que hagan falta para la decisión. Para cada una decide su tipo:

- `texto` - una línea corta (un nombre, una cifra).
- `parrafo` - varias líneas (una explicación, un contexto).
- `opcion` - elegir una sola de una lista. La opción "Otra:" con texto libre se añade siempre
  sola, no hay que pedirla ni marcarla.
- `multiple` - elegir varias de una lista. Misma "Otra:" siempre presente.
- `escala` - puntuar en un rango (0-10, 1-5), con etiquetas en los extremos si ayudan.

Una pregunta por decisión: si una pregunta lleva dos cosas dentro, son dos preguntas.

### 2. Adapta la paleta al proyecto donde estás trabajando

Esto no es opcional: el cuestionario **no lleva siempre el mismo look**, lleva el de la
conversación en la que se pide. Antes de generar el archivo, mira si el proyecto activo tiene su
propio sistema de diseño (un `App.css`, un `globals.css`, tokens de Tailwind, variables de tema
ya definidas) y localiza sus colores, sus radios y su tipografía. Si lo tiene, sustituye en la
plantilla los valores del bloque marcado `PALETA` (los `--bg`, `--text`, `--accent`, `--ok`,
`--warn`, `--bad`, `--radio`, `font-family`...) por los suyos. El bloque `ESTRUCTURA` que viene
debajo no se toca nunca: usa siempre `var(--algo)`, así que hereda el cambio de piel solo. Si el
proyecto no tiene un sistema de diseño propio identificable (una tarea sin web, o un repo sin
frontend), se deja la paleta neutra de la plantilla, ya verificada por contraste WCAG 2.1.

### 3. Genera el archivo

Copia `references/plantilla.html` de esta skill a una carpeta temporal de la sesión (nunca
dentro del código del proyecto: es un archivo de trabajo, no un artefacto del producto) como
`criteria-<tema>.html`, y sustituye estas tres cosas dentro del `<script>` (más la paleta del
paso 2 si aplica), sin tocar el resto del archivo:

```js
const TITULO = "...";           // qué se está decidiendo, en cuatro palabras
const SUBTITULO = "...";        // una frase: para qué sirve esto y qué pasa al terminar
const PREGUNTAS = [ ... ];      // el array de preguntas, con las formas de arriba
```

El resto del archivo (estilos de estructura, render, barra de progreso, copiar) ya funciona: no
se reescribe nunca a mano, para no reintroducir un fallo ya resuelto.

### 4. Ábrelo tú mismo

Abre el archivo en el navegador por su cuenta (en Windows, `Start-Process` sobre la ruta; en
macOS, `open`; en Linux, `xdg-open`), sin pedir permiso y sin limitarse a dejar la ruta escrita
para que otro la abra a mano. En el chat, una sola línea: qué se está preguntando y que se
pegue el bloque cuando se termine. Nunca la lista de preguntas repetida en la terminal: quien
tiene que leerla y responderla ya la tiene delante, en el formulario.

### 5. Cuando llegue el bloque pegado

Viene en el formato `P1. <pregunta>\n→ <respuesta>`. Las que digan `(sin responder)` son las que
se dejaron en blanco: no se inventan, se preguntan de nuevo en el chat si hacen falta para
cerrar la decisión, o se aparcan si no son bloqueantes. Con lo demás, se arma el prompt o se
toma la decisión que motivó el cuestionario.

## Lo que ya trae la plantilla, y por qué no hay que tocarlo

- **Cero CDN, un solo archivo.** Tiene que verse igual sin internet.
- **Tema del sistema, sin botón.** `prefers-color-scheme` decide claro u oscuro solo: esto no es
  un catálogo que haya que mirar en los dos temas a la vez, es un formulario de un uso.
- **La paleta por defecto** (`--bg`, `--panel`, `--accent`...) es la que se usa en el paso 2
  cuando el proyecto no tiene la suya propia, con el contraste ya medido por WCAG 2.1. Si un hex
  se toca alguna vez, se vuelve a medir, no se ajusta mirando.
- **Barra de progreso arriba**, siempre visible mientras se hace scroll (`position: sticky`), con
  el contador de respondidas al lado. No es solo un número perdido al pie de página.
- **"Otra:" siempre presente** en toda pregunta de opción o múltiple, con su hueco de texto
  libre. Es obligatorio en la plantilla, no algo que haya que acordarse de pedir.
- **Nada es obligatorio de responder.** Un formulario donde todo es obligatorio no lo rellena
  nadie rápido; el botón copia igual con preguntas en blanco, marcadas como tal.
- **Copiar con red.** Intenta `navigator.clipboard`, y si el navegador lo bloquea (pasa a veces
  en `file://`), deja el texto seleccionado en una caja visible para que un Ctrl+C manual
  funcione igual.
- **Autoguardado en `localStorage`.** Si se cierra la pestaña sin querer a mitad, lo que ya se
  escribió sigue ahí al reabrir el mismo archivo.

## Cuándo NO usar esto

Si hay una sola cosa que decidir, o si las preguntas dependen unas de otras (la segunda no se
puede formular hasta saber la respuesta de la primera), esto es un interrogatorio en el chat,
no Criteria: aquí todas las preguntas se hacen a la vez y de una sola tacada.
