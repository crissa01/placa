---
name: Ven Vías — Portal público de prepago por placa
description: Sistema visual heredado de la plataforma institucional Ven Vías para recargar peajes con la placa del vehículo, sin registro.
colors:
  brand: "#0033AB"
  brand-press: "#002888"
  brand-wash: "#EAEFFB"
  field-top: "#FDFDFE"
  field-mid: "#F5F7FC"
  field-low: "#E9EDF8"
  wave: "#EFF3FA"
  surface: "#FFFFFF"
  surface-sunk: "#F7F9FC"
  line: "#9CA3AF"
  line-hover: "#7C8794"
  line-dash: "#BFC7D2"
  line-soft: "#E3E8EF"
  scroll: "#C3CBD8"
  scroll-hover: "#A9B3C4"
  text: "#1B2430"
  text-strong: "#3D4A59"
  text-body: "#637381"
  text-mute: "#9CA3AF"
  logo-blue: "#0056A3"
  logo-ink: "#132243"
  arc-teal: "#039292"
  arc-green: "#34B046"
  arc-amber: "#CB8B18"
  arc-orange: "#FA6C24"
  arc-red: "#F62728"
  ok: "#157A32"
  ok-wash: "#E7F5EA"
  ok-line: "#A6DDB4"
  warn: "#8A5A08"
  warn-wash: "#FCF2DE"
  warn-line: "#EBCE92"
  danger: "#B3161A"
  danger-wash: "#FDECEC"
  danger-line: "#F3B3B3"
  info: "#0A6E6E"
  info-wash: "#E4F4F4"
  info-line: "#9AD5D5"
  ok-deepest: "#0F5C25"
  warn-deepest: "#6E4806"
  danger-deepest: "#8E1114"
  info-deepest: "#085454"
  held-a: "#AEB6C2"
  held-b: "#98A1AF"
  dot-live: "#7FE39B"
  proto-tag: "#8FA8D8"
  toast-ok: "#8FE39B"
  toast-danger: "#FF9E9C"
  plate-face: "#FFFFFF"
  plate-edge: "#C7CDD6"
  plate-ink: "#17202E"
  plate-dim: "#6B7684"
  plate-band-off: "#7C8794"
  plate-rim: "#B9C0CB"
  plate-body: "#FBFCFD"
  plate-bolt: "#C9CFD8"
  plate-band-lift: "#0A3FBE"
  plate-band-bad: "#C8272B"
  plate-band-mute: "#8B95A2"
  plate-flag-yellow: "#F6CE1C"
  plate-flag-blue: "#98A0E4"
  plate-flag-red: "#E0401C"
fonts:
  sans: "Roboto"
  mono: "Roboto Mono"
type:
  display: "2.5rem"
  h2: "1.75rem"
  h3: "1.3125rem"
  lead: "1.0625rem"
  body: "1rem"
  ui: "0.9375rem"
  small: "0.875rem"
  micro: "0.75rem"
  balance: "3rem"
  code: "2.25rem"
rounded:
  lg: "10px"
  md: "8px"
  sm: "6px"
  xs: "4px"
  xxs: "2px"
  plate: "14px"
  pill: "999px"
spacing:
  s1: "4px"
  s2: "8px"
  s3: "12px"
  s4: "16px"
  s5: "24px"
  s6: "32px"
  s7: "48px"
  s8: "64px"
  s9: "96px"
shadows:
  lift-1: "0 1px 2px rgba(27,36,48,.06)"
  lift-2: "0 1px 3px rgba(27,36,48,.08), 0 6px 16px rgba(27,36,48,.06)"
  lift-3: "0 2px 6px rgba(27,36,48,.10), 0 18px 40px rgba(27,36,48,.10)"
---

# Design System: Ven Vías — Portal público de prepago por placa

## Overview

**Norte creativo: "la cara pública de un sistema que ya existe"**

Este portal no es un producto suelto. Es la superficie pública de **Ven Vías, Plataforma de Gestión de Peaje**, y hereda su sistema visual literalmente: campo casi blanco con bandas de onda, columna de formulario a la izquierda sobre un campo abierto, y azul `#0033AB` macizo en el botón primario y los enlaces. Un conductor debe reconocer que está dentro de un sistema del Ministerio **antes de leer una palabra**, porque eso resuelve la pregunta más cara del flujo: si esto es legítimo para entregarle dinero.

Heredar en vez de inventar es la decisión de fondo. Sustituye al sistema anterior de este repositorio (**SEÑAL**, señalización vial sobre campo de asfalto oscuro), retirado por decisión del usuario. No queda nada de aquel mundo salvo el arco del logotipo.

**Ningún color fue elegido por gusto.** Todos están muestreados de la captura de la pantalla real, decodificando el PNG y leyendo píxeles: `#0033AB` es el botón «Iniciar sesión», `#F5F7FC`/`#E9EDF8` son el campo, `#9CA3AF` es el filete de los campos de formulario, `#637381` es el texto de apoyo. Cuando el sistema necesite un color nuevo, se muestrea de la referencia o se deriva del arco; no se inventa.

La estrategia de color es **contenida**: el campo y la superficie son casi neutros y el azul aparece concentrado en la acción, el enlace y la banda de la placa. Es lo contrario de la estrategia comprometida del sistema anterior, y es lo correcto aquí: el visitante viene a completar una tarea con dinero, no a ser persuadido.

**Características clave:**
- Campo claro con ondas, superficie blanca, filete fino y mucho aire. Sin tarjetas de color.
- Paleta íntegramente muestreada de la pantalla institucional de referencia.
- El arco del logotipo sobrevive como **escala de estado** del saldo, reajustado en luminancia para leer sobre campo claro.
- La matrícula sigue siendo el objeto crítico: cambió el mundo, no su función.
- Roboto y Roboto Mono, las de la referencia. Mono reservado a placa, folio, dinero y conteos.
- Todo estado se comunica con icono + texto + color; nunca solo color.
- El campo claro resuelve el uso al sol: bajo luz directa gana la pantalla que emite más luz.

## Colors

### Primario

`#0033AB` gobierna el botón primario, los enlaces, la banda de país de la placa y el estado seleccionado. `#002888` es su estado presionado. `#EAEFFB` es el lavado de fondo para selección y para iconos de apoyo.

### Campo y superficie

El campo es un degradado de `#FDFDFE` a `#F5F7FC` a `#E9EDF8` en 148°, con cuatro bandas de onda SVG fijas entre `#EDF1F9` y `#F7F9FD`. La superficie de contenido es blanco puro con filete `#E3E8EF`; los campos de formulario llevan filete `#9CA3AF`, más marcado, calcado de la referencia.

### El arco del logotipo como escala de estado

Único superviviente del sistema anterior. El barrido de `assets/venezuela_logo.png`, muestreado a lo largo de su recorrido, ya es una escala de sano a agotado: teal `#039292`, verde `#34B046`, ámbar `#CB8B18`, naranja `#FA6C24`, rojo `#F62728`. Sobre campo claro los tonos de texto se oscurecen a `#157A32`, `#8A5A08`, `#B3161A` y `#0A6E6E` para cumplir contraste; el arco original se conserva íntegro en el medidor y en la documentación.

### Reglas nombradas

- **Color muestreado.** Todo valor nuevo se muestrea de la referencia o se deriva del arco. Nunca se elige a ojo.
- **Azul concentrado.** El azul es acción, enlace y banda de placa. No se usa como fondo decorativo ni como relleno de sección.
- **Estado con tres canales.** Icono, texto y color siempre juntos. Ningún estado depende del tono.
- **Filete según función.** `#9CA3AF` marca lo que se puede escribir; `#E3E8EF` sólo separa. No se intercambian.

## Typography

**Roboto** para todo, **Roboto Mono** para matrícula, folio, dinero y conteos. Son las caras de la referencia; usarlas es parte de heredar el sistema, no una elección de gusto.

### Jerarquía

| Nivel | Tamaño | Peso |
|---|---|---|
| Display | 40 px | 700 |
| H2 | 28 px | 700 |
| H3 | 21 px | 500 |
| Lead | 17 px | 400 |
| Cuerpo | 16 px | 400 |
| UI | 15 px | 500 |
| Pequeño | 14 px | 400 |
| Micro | 12 px | 700, versal |

### Reglas nombradas

- **Cero rayado.** La matrícula y todo importe usan `tabular-nums slashed-zero`. Es una medida contra la pérdida de dinero, no un detalle de estilo: impide confundir 0/O y 1/I.
- **Mono para medir.** El monoespaciado es para placa, folio, dinero y conteos. Nunca como disfraz técnico.
- **Piso de 18 px en kiosco.** La §26 de la especificación fija un piso, no una escala: en `[data-scale="kiosk"]` ningún token tipográfico baja de 18 px, incluidos lead, small y micro.
- **Marcador de posición legible.** La referencia usa `#9CA3AF` como marcador de posición (2.6:1). Aquí se oscurece a `#637381` (4.9:1) a propósito. Es una desviación deliberada de la referencia por accesibilidad.

## Layout

Contenido máximo 1280 px. Ritmo espacial de una sola escala (4·8·12·16·24·32·48·64·96). Umbrales: móvil <640, tableta 640–1024, escritorio >1024.

La primera vista replica la composición de la referencia: **columna de formulario estrecha a la izquierda (máx. 520 px) y campo abierto a la derecha**. En ≤1024 px la retícula colapsa a una columna y el aparte pasa debajo.

**No hay cinta institucional.** Se retiró por corrección del usuario; el logotipo de Venezuela en Movimiento vive ahora dentro de la barra del producto, junto al nombre `Ven Vías` y separado por un filete vertical.

**«Peajes habilitados» es una sección independiente** (`/peajes`), no un bloque de la portada. La portada sólo conserva un enlace hacia ella.

## Elevation & Depth

La referencia es plana. Las tres sombras son mínimas, todas con desplazamiento y desenfoque, en un solo azul-negro `rgba(27,36,48,·)`. La jerarquía la llevan el filete y el aire, no la elevación.

## Shapes

Radios 10 / 8 / 6 px y pastilla. Uniformes: la referencia no varía la esquina por componente.

## Components

### Placa (componente firma)
Reproducción de una **placa venezolana real**, no una tarjeta con texto: cuerpo blanco con canto de 14 px, reborde embutido retirado del canto, dos tornillos de sujeción, banda azul superior con `VENEZUELA` flanqueada por estrellas, y caracteres mono en relieve `#17202E` (16.4:1). Tamaños sm/md/lg. Aparece en recarga, consulta, confirmación, comprobante, panel y alertas. Los estados `ZERO_BALANCE` y `BLOCKED` apagan la banda a gris y `BLOCKED` tacha los caracteres, para que el estado sobreviva sin color.

**Las estrellas se dibujan en CSS** (`radial-gradient` repetido), no como texto: el glifo estrella caía a asterisco en Roboto.

### Placa editable
El campo de entrada **es** la placa: el usuario escribe el número dentro de la placa real. Normaliza a mayúsculas y elimina separadores mientras se escribe. Al confirmar un valor emite un pulso breve del filete: el único momento de movimiento con intención del sistema.

### Placa realista (representación vigente)
Variante `realistic` de la placa, usada en todas las superficies con placa: portada, recarga, consulta, confirmación y comprobante. El fondo es una **placa venezolana real** dibujada como vector sin número (`src/assets/plate-venezuela.svg`): onda tricolor `#F6CE1C`/`#98A0E4`/`#E0401C`, ocho estrellas, `REPUBLICA BOLIVARIANA DE VENEZUELA` y `CARACAS`. Se recreó en SVG porque no había herramienta de retoque para borrar el número impreso del PNG de referencia sin dañar la bandera; el vector queda limpio y escala sin pérdida. Los caracteres mono `#17202E` viven en la zona del número; en la variante editable el usuario escribe ahí dentro (el campo **es** la placa). El tamaño del número se ata al ancho de la placa con unidades de contenedor (`cqw`), así funciona a cualquier tamaño. `BLOCKED` la desatura y tacha el número, para que el estado sobreviva sin depender del color. La placa de banda CSS descrita arriba se conserva como respaldo.

### Medidor de alcance (componente firma)
El saldo como extensión medible, con una muesca por tránsito y una marca de punto de retorno. El relleno es un color de estado sólido; el arco vive en la documentación. Traduce dinero a distancia, que es como piensa un conductor.

### Botones
Primario azul macizo a ancho completo, 56 px (72 px en kiosco). Secundario con filete `#9CA3AF` sobre blanco. Un solo control primario por pantalla.

### Campos, fichas y métodos de pago
Filete `#9CA3AF`, altura 56 px, radio 8 px. El estado seleccionado usa `#EAEFFB` con filete azul e `inset` de 1 px, nunca sólo color.

### Progreso
Puntos numerados con rótulo. Sin galones: este mundo es plano. El paso completado muestra un check sobre lavado verde.

### Selección en el panel
La placa activa lleva filete azul **y una etiqueta «Seleccionada»**, para que la selección se lea sin depender del color.

### Sección de privacidad
Panel de ancho completo con el icono y el título en una fila y el cuerpo **justificado** (`text-align: justify` con `hyphens: auto`) debajo, ocupando todo el ancho del panel. En 640 px o menos vuelve a alineación izquierda, porque el justificado abre rios en medidas cortas.

### Comprobante
Superficie blanca con filete, filas de folio en mono y un talón con filete discontinuo que contiene el código de seguridad en azul.

## Do's and Don'ts

### Do
- Muestrear los colores de la referencia; derivar del arco cuando haga falta estado.
- Mantener el azul concentrado en acción, enlace y banda de placa.
- Dejar aire: la referencia respira, y esa respiración es parte del sistema.
- Combinar icono + texto + color en todo estado.
- Mantener 44 px de objetivo táctil, incluido el andamiaje del prototipo.
- Rotular como sintético cualquier dato de demostración.

### Don't
- **No usar `assets/auth-toll-background.png`.** Exclusión deliberada. La fotografía del pórtico pelea con la contención institucional del sistema, y esta superficie es de tarea, no de atmósfera. Registrado aquí para que nadie la reintroduzca al portar.
- **No reintroducir la cinta institucional superior.** Retirada por corrección del usuario. El escudo del Ministerio y la marca de 200 años siguen sin estar en el repositorio y no deben fabricarse.
- **No devolver «Peajes habilitados» a la portada.** Es una sección propia.
- No devolver el marcador de posición a `#9CA3AF` sobre blanco: falla AA.
- No introducir paneles de color, galones, franjas de peligro ni texturas: pertenecen al sistema retirado.
- No añadir sombras fuertes ni tarjetas dentro de tarjetas.
- No usar el azul como relleno de sección.

## Scaffolding, not system

El andamiaje del prototipo **no porta**: la barra superior oscura, el cajón del inspector de flujo de datos, los conmutadores de simulación y los widgets de muestrario existen para revisar la propuesta y desaparecen en la implementación real.

## Pending

La lista real de peajes, tarifas, moneda, comisión, proveedor de pagos y textos legales debe reemplazar los valores sintéticos antes de publicar. Ver `docs/01-revision-spec.md` para las doce decisiones abiertas.
