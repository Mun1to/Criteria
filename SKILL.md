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

**Antes de dar la lista por cerrada, pasa cada pregunta por dos filtros, sin excepción:**

- **¿hace falta de verdad?** Si la respuesta no va a cambiar nada de lo que se hace después,
  la pregunta sobra. No se pregunta por rellenar el cuestionario ni "por si acaso": cada
  pregunta de más es tiempo perdido de quien tiene que contestarla.
- **¿se entiende a la primera lectura?** Frase corta, una sola idea, sin jerga que quien
  responde no tenga por qué conocer. Si hace falta contexto para que la pregunta se entienda,
  ese contexto va en `ayuda` (ver debajo), nunca amontonado dentro del título hasta hacerlo
  largo o técnico.

Repasa la lista entera con estos dos filtros antes de pasar al paso 2: quita toda pregunta que
no vaya a cambiar nada, y reescribe toda pregunta que haya que releer dos veces para saber qué
está pidiendo. Un cuestionario de cuarenta preguntas donde media docena son ruido no es más
completo que uno de treinta y cuatro, es solo más largo y más pesado de rellenar.

Cada opción de `opcion` o `multiple` puede ser solo texto, o llevar más: `{ texto, ayuda?,
nota? }`, donde `ayuda` es una frase pequeña debajo (por qué importa esa opción) y `nota` es una
insignia corta al lado (por ejemplo `"recomendada"`). No hay que elegir un formato para toda la
lista: una pregunta puede mezclar opciones simples con otras que llevan ayuda o nota.

Si el cuestionario es largo y las preguntas caen en grupos claros (como "la escalera de precios",
"el móvil", "el dinero"), cualquier pregunta admite un `seccion: "Título del grupo"` opcional. En
cuanto cambia de una pregunta a la siguiente, se inserta sola una cabecera con letra automática
(A, B, C...) antes de esa tarjeta. Es solo agrupación visual: la numeración P1, P2... y el formato
de respuesta no cambian.

**Cómo distinguir `opcion` de `multiple`, sin adivinar:** pregúntate si dos de las respuestas
podrían ser ciertas a la vez. "¿Cuánto cuesta al mes?" es `opcion`, porque un precio excluye a los
demás. "¿Qué justifica pagar esto?" o "¿qué debe hacer la app?" casi siempre son `multiple`, porque
varias razones o varias funciones pueden ser verdad al mismo tiempo. Por defecto, ante la duda,
`multiple` cuesta menos que forzar una sola respuesta a algo que no lo es: peca de dejar elegir de
más, no de menos.

**Esto no es negociable, ni siquiera si el cuestionario necesita más diseño del que trae la
plantilla.** El título, la ayuda y la insignia de arriba ya cubren el caso más común de "necesito
explicar cada opción y marcar la recomendada". Si aun así hace falta algo que de verdad no cabe
(una tabla comparativa entera, un aviso grande), se puede construir a mano un diseño más rico,
pero el comportamiento no se negocia: toda pregunta de opción lleva su "Otra:" con texto libre, y
toda pregunta donde dos respuestas puedan ser ciertas a la vez usa checkboxes, no radios. Un
cuestionario hecho a mano sin esto no es una versión simplificada de Criteria, es un cuestionario
roto que parece Criteria.

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

### 3. Genera el archivo, sin leer la plantilla entera

`references/plantilla.html` no cambia nunca de un cuestionario a otro salvo en tres puntos, así
que no hace falta cargarla en el contexto para tocarlos: se copia el archivo tal cual (`cp` /
`Copy-Item`, nunca dentro del código del proyecto, es un archivo de trabajo) a
`criteria-<tema>.html` en una carpeta temporal de la sesión, y la sustitución de los tres
marcadores se hace con un comando de una sola pasada que lee y escribe el archivo sin que su
contenido pase por el modelo:

```bash
python3 - "<ruta-al-copia>" <<'EOF'
import json, sys
p = sys.argv[1]
html = open(p, encoding="utf-8").read()
html = html.replace("__TITULO__", "...")            # qué se está decidiendo, en cuatro palabras
html = html.replace("__SUBTITULO__", "...")          # una frase: para qué sirve y qué pasa al terminar
html = html.replace("__PREGUNTAS_JSON__", json.dumps([
    # ...las preguntas, con las formas del paso 1...
], ensure_ascii=False))
open(p, "w", encoding="utf-8").write(html)
EOF
```

En PowerShell, lo mismo con `-replace` sobre `Get-Content -Raw` y `Set-Content -Encoding utf8`. Si
el paso 2 exige otra paleta, esos mismos comandos añaden más `-replace`/`.replace()` sobre los
valores del bloque `PALETA`; el bloque `ESTRUCTURA` nunca se toca. No se usan las herramientas de
leer y editar archivo sobre `plantilla.html` ni sobre la copia: no hace falta, y cargar un HTML de
cientos de líneas en el contexto solo para cambiar tres valores es gastar tokens de más.

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
  libre. Es obligatorio en la plantilla, no algo que haya que acordarse de pedir. Escribir ahí la
  marca sola, sin tener que clicar antes su casilla, y la selección se recuerda al recargar la
  página igual que el resto de respuestas.
- **Selección múltiple de verdad, y avisada.** `multiple` usa casillas, no radios: se puede
  marcar más de una a la vez, el bloque final las junta separadas por comas, y la pregunta lleva
  sola la insignia "elige una o varias" para que se note sin tener que probar a marcar dos.
- **Opciones con ayuda e insignia**, cuando la lista sola no basta: cada opción admite una frase
  de explicación debajo y una insignia corta al lado (por ejemplo "recomendada"), sin tener que
  escribir HTML a mano ni salirse de la plantilla.
- **Secciones con letra automática**, para cuestionarios largos: agrupar preguntas bajo un
  `seccion` no cambia su numeración ni el formato de respuesta, solo añade un separador visual.
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
