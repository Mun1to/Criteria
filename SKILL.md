---
name: criteria
description: Convierte varias preguntas sueltas (nombres, prioridades, alcance, gustos de diseño) en un cuestionario HTML de un solo archivo que enseña una pregunta cada vez, con las opciones como baldosas de palabra y frase, barra de progreso, opción "Otra" siempre disponible y el lenguaje visual del proyecto donde se pide (o uno neutro si no hay otro), con un botón que junta todas las respuestas en un bloque de texto listo para pegar de vuelta en el chat. Úsalo cuando el usuario diga "hazme un cuestionario", "criteria", "pregúntame con un formulario", "que lo rellene y te lo pego", o cuando haya que decidir varias cosas de tipo distinto (texto libre, elegir una opción, elegir varias, puntuar del 0 al 10) y dictarlas una por una sea más lento que rellenar un formulario y copiar.
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

Cada pregunta admite `ayuda` (por qué se pregunta ahora, en una línea) y `siNoDices` (qué pasa si
se queda sin contestar). Las dos se ven debajo de la pregunta, que va arriba y entera.

Cada opción de `opcion` o `multiple` puede ser solo texto, o llevar más:
`{ texto, ayuda?, recomendada?, porque?, malo?, bueno?, cuesta?, noHace?, icono?, nota? }`.

- `texto` es la palabra, grande: de una a tres palabras rinde mejor que una frase entera.
- `ayuda` es la frase de debajo: qué pasa si se elige esa opción, con verbo.
- `recomendada: true` en una sola, y `porque` dice por qué. **Nunca sale marcada de antemano**:
  la recomendación se dice con palabras y quien contesta elige.
- `malo`, `bueno` y `cuesta`, una línea cada uno y siempre los tres, para que las opciones se
  comparen solas. Salen al pulsar la opción, debajo de las demás.
- `noHace`, lo que esa opción no incluye, cuando se puede leer de dos maneras.
- `icono`, uno de los que trae la plantilla: `si`, `no`, `ya`, `esperar`, `pregunta`, `gente`,
  `dinero`, `texto`, `igual`, `estrella`, `lapiz`, `flecha`, `escudo`.
- `nota`, una o dos palabras debajo en el color de acento (por ejemplo `"la más barata"`).

No hay que elegir un formato para toda la lista: una pregunta puede mezclar opciones simples con
otras que lo llevan todo. Una opción que solo trae un texto largo se ve con letra normal, no en
negrita grande.

Si el cuestionario es largo y las preguntas caen en grupos claros (como "la escalera de precios",
"el móvil", "el dinero"), cualquier pregunta admite un `seccion: "Título del grupo"` opcional. Sale
encima de la pregunta con letra automática (A, B, C...) en cuanto cambia de una pregunta a la
siguiente. Es solo agrupación: la numeración P1, P2... y el formato de respuesta no cambian.

**Cómo distinguir `opcion` de `multiple`, sin adivinar:** pregúntate si dos de las respuestas
podrían ser ciertas a la vez. "¿Cuánto cuesta al mes?" es `opcion`, porque un precio excluye a los
demás. "¿Qué justifica pagar esto?" o "¿qué debe hacer la app?" casi siempre son `multiple`, porque
varias razones o varias funciones pueden ser verdad al mismo tiempo. Por defecto, ante la duda,
`multiple` cuesta menos que forzar una sola respuesta a algo que no lo es: peca de dejar elegir de
más, no de menos.

**Esto no es negociable, ni siquiera si el cuestionario necesita más diseño del que trae la
plantilla.** La palabra, la frase, la recomendada con su porqué y lo malo, lo bueno y lo que cuesta
ya cubren el caso más común de "necesito explicar cada opción y marcar la recomendada". Si aun así hace falta algo que de verdad no cabe
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
plantilla los valores del bloque marcado `PALETA` (los `--bg`, `--text`, `--accent`, `--accent-text`,
`--ok`, `--warn`, `--bad`, `--radio`, `font-family`, `--letra-titulos`...) por los suyos. `--accent` es
el fondo de lo elegido y del botón principal, con `--sobre` encima; `--accent-text` es el acento cuando
va sobre el fondo de la página. El bloque `ESTRUCTURA` que viene
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

Viene en el formato `P1. <pregunta>\n→ <respuesta>`. En las preguntas de opción, cada elegida va
con su número (`→ 2 · 99 €`) y lo escrito en el hueco libre, detrás de `Otra: `. Las que digan
`(sin responder)` son las que se dejaron en blanco: no se inventan, se preguntan de nuevo en el chat
si hacen falta para cerrar la decisión, o se aparcan si no son bloqueantes. Con lo demás, se arma el
prompt o se toma la decisión que motivó el cuestionario.

Si las mismas preguntas se mandan también por otro canal (una notificación al móvil, otra
herramienta de preguntas), van en el mismo orden y con las opciones en el mismo orden: así «P2, la 3»
quiere decir lo mismo en los dos sitios.

## Lo que ya trae la plantilla, y por qué no hay que tocarlo

- **Cero CDN, un solo archivo.** Tiene que verse igual sin internet.
- **Tema del sistema, sin botón.** `prefers-color-scheme` decide claro u oscuro solo: esto no es
  un catálogo que haya que mirar en los dos temas a la vez, es un formulario de un uso.
- **La paleta por defecto** (`--bg`, `--panel`, `--accent`...) es la que se usa en el paso 2
  cuando el proyecto no tiene la suya propia, con el contraste ya medido por WCAG 2.1. Si un hex
  se toca alguna vez, se vuelve a medir, no se ajusta mirando.
- **Una pregunta cada vez.** Muchas preguntas densas a la vista a la vez cuestan de entender y de
  decidir; de una en una, quien contesta piensa una sola cosa. Arriba la pregunta, entera; debajo,
  por qué se pregunta y qué pasa si no contesta. «Atrás» y «Siguiente» para moverse, y en la última,
  «Ver lo que has contestado»: un repaso de todas, cada una con su «Cambiar». `Enter` en una respuesta
  corta pasa a la siguiente.
- **Las opciones son baldosas de palabra y frase**: la palabra grande con su número, la frase debajo
  más suave, y al pulsarla se llena del color de acento. La recomendada lo dice con palabras, y al
  pulsarla dice por qué. Debajo sale lo malo, lo bueno y lo que cuesta de la elegida, una línea cada
  uno. En pantalla grande van en columnas y en el móvil una debajo de otra.
- **Un solo «Copiar respuestas»**, abajo y siempre a mano, que pasa a ser el botón principal cuando ya
  está todo contestado o en el repaso final.
- **Barra de progreso arriba**, siempre visible mientras se hace scroll (`position: sticky`), con
  el contador de respondidas al lado. No es solo un número perdido al pie de página.
- **"Otra:" siempre presente** en toda pregunta de opción o múltiple, con su hueco de texto
  libre. Es obligatorio en la plantilla, no algo que haya que acordarse de pedir. Lo que se escribe
  ahí cuenta solo, sin pulsar nada antes, y no quita lo que ya estaba elegido: vale para matizar.
- **Selección múltiple de verdad, y avisada.** `multiple` usa casillas (cuadradas, no redondas): se
  puede marcar más de una a la vez, el bloque final las junta separadas por comas, y la pregunta dice
  "Elige una o varias" para que se note sin tener que probar a marcar dos.
- **Secciones con letra automática**, para cuestionarios largos: agrupar preguntas bajo un
  `seccion` no cambia su numeración ni el formato de respuesta, solo dice encima a qué grupo va.
- **Sin insignias ni etiquetas en caja.** Lo que hay que destacar se destaca con peso, tamaño o color
  del texto, nunca con una píldora alrededor. Letra de 17 px y nada por debajo de 14.
- **Nada es obligatorio de responder.** Un formulario donde todo es obligatorio no lo rellena
  nadie rápido; el botón copia igual con preguntas en blanco, marcadas como tal.
- **Copiar con red.** Intenta `navigator.clipboard`, y si el navegador lo bloquea (pasa a veces
  en `file://`), deja el texto seleccionado en una caja visible para que un Ctrl+C manual
  funcione igual.
- **Autoguardado en `localStorage`.** Si se cierra la pestaña sin querer a mitad, lo que ya se
  escribió sigue ahí al reabrir el mismo archivo, y vuelve a la pregunta en la que estaba.

## Cuándo NO usar esto

Si hay una sola cosa que decidir, o si las preguntas dependen unas de otras (la segunda no se
puede formular hasta saber la respuesta de la primera), esto es un interrogatorio en el chat,
no Criteria: aquí todas las preguntas se hacen a la vez y de una sola tacada.
