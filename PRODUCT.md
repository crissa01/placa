# Product

<!-- impeccable:product-schema 1 -->

## Platform

web

## Stack

Prototipo autónomo HTML/CSS/JS sin build ni dependencias, confirmado por el usuario. El artefacto cumple doble función: prototipo navegable para aprobar la propuesta, y especificación visual ejecutable para portarla después. El workspace contiene convenciones de un panel administrativo Vue 3 (Options API) + Tailwind para el dominio de peajes (`.claude/skills/`), lo que hace de Vue 3 el destino probable de la implementación real, pero esa decisión queda abierta.

## Users

- **Conductor sin cuenta (primario).** Persona que va a circular por un peaje habilitado y necesita saldo en su vehículo ahora. Escena real: de pie en una estación de servicio o dentro del carro estacionado antes de entrar a la autopista, teléfono en una mano, con sol sobre la pantalla y conexión móvil irregular. No quiere abrir una cuenta ni recordar una contraseña. Trabajo: poner saldo en su placa y salir.
- **Tercero que recarga por otro.** Familiar, empleado o encargado de bodega que recarga una placa que no es suya. Solo conoce la matrícula. Este usuario es la razón por la que la confirmación de placa es crítica.
- **Operador de kiosco / terminal de atención.** Atiende a personas sin teléfono o sin medio de pago digital. Opera de pie, con alta rotación, y no debe dejar datos del cliente anterior en pantalla.
- **Titular registrado (opcional, secundario).** Administra varias placas, autorecarga, alertas y TAG. Es una extensión del servicio, nunca un requisito.

## Product Purpose

Permitir el pago prepago de peajes usando la **placa del vehículo como identificador de una billetera**, sin registro, sin cuenta y sin TAG. El saldo se consume automáticamente al circular por los peajes habilitados.

Éxito significa: una persona que nunca ha usado la plataforma indica una placa, elige un monto, paga y recibe confirmación en cuatro pantallas o menos, sin haber escrito jamás un correo, un teléfono ni una contraseña.

## Positioning

La matrícula es la cuenta. Ningún portal de peajes de la región permite acreditar saldo a un vehículo sin crear identidad previa: el resto obliga a registrarse, comprar un TAG o vincular un vehículo a una persona. Aquí el vehículo es el titular y la persona es opcional. El principio rector declarado en la spec: *recargar una placa debe sentirse tan simple como recargar un teléfono móvil.*

Esa misma decisión crea el problema central del producto: la placa es un dato **público**. Cualquiera puede leerla en la calle. Por eso la placa alcanza para *acreditar* saldo y para consultar un total, pero nunca para ver movimientos ni modificar nada. El segundo factor es el código de seguridad impreso en el comprobante de la última recarga.

## Operating Context

- **Superficies:** portal web público (móvil primero), área registrada opcional, variante kiosco para terminales de atención.
- **Momento de uso:** minutos antes de circular, con urgencia real. También recarga anticipada y recarga por terceros.
- **Condiciones físicas:** pantalla al sol, una sola mano, conexión intermitente, batería baja. En kiosco: de pie, público detrás, sesión que debe autodestruirse.
- **El momento de verdad ocurre fuera de la pantalla:** en el pórtico del peaje, cuando el sistema lee la placa y descuenta. La interfaz nunca presencia su propio resultado, así que la confirmación previa debe cargar toda la confianza.
- **Jurisdicción:** la placa se interpreta según país; Venezuela es el caso base y el formato de matrícula varía por jurisdicción.

## Capabilities and Constraints

**Área pública (sin registro):** recargar una placa, consultar saldo, consultar estado de una recarga, ver comprobante, ayuda y preguntas frecuentes, puntos de recarga, iniciar reclamación de placa.

**Área registrada (opcional):** administrar varias placas, autorecarga, asociar TAG, historial completo, descargar comprobantes, alertas, métodos de pago, facturación, recuperación de saldo.

**Métodos de pago declarados:** tarjeta, pago móvil, transferencia, efectivo o código.

**Máquinas de estado (vocabulario fijo del dominio):**
- Billetera: `ACTIVE`, `LOW_BALANCE`, `ZERO_BALANCE`, `BLOCKED`, `REVIEW`
- Recarga: `PENDING`, `PROCESSING`, `COMPLETED`, `FAILED`, `REVERSED`
- Tránsito: `AUTHORIZED`, `DEBITED`, `PENDING`, `INSUFFICIENT_BALANCE`, `POSTPAID`, `REVIEW`

**Restricciones duras:**
- Ningún flujo público puede pedir nombre, correo, teléfono ni contraseña.
- Todo estado debe comunicarse con icono + texto + color; nunca solo color.
- La consulta sensible (movimientos, modificaciones) exige segundo factor: código de comprobante, OTP, PIN temporal o token de recarga.
- La consulta básica está protegida por CAPTCHA adaptativo, rate limiting y anti-bots. Las reglas antifraude internas nunca se muestran.
- El historial sin cuenta no muestra nombres de personas, datos de medios de pago, información financiera sensible ni ubicaciones históricas completas.
- El botón de rechazo al registro (`Ahora no`) debe tener visibilidad suficiente; los patrones oscuros están prohibidos explícitamente.
- Máximo cuatro pantallas principales entre el inicio y la operación completada.
- Kiosco: timeout de sesión, borrado automático de datos y pantalla de privacidad al finalizar.

**Terminología del producto:** placa / matrícula, billetera, recarga, tránsito, peaje habilitado, comprobante, código de seguridad, reclamación de placa, TAG, autorecarga.

**Decisiones abiertas (no inventar):**
- Moneda de operación y su presentación. La spec ejemplifica en dólares; no está confirmado.
- Comisión por recarga. La spec la muestra en $0.00; no está confirmado como política.
- Tarifa real de peaje. El ejemplo usa $2.00 por tránsito.
- Lista real de peajes habilitados y puntos de recarga.
- Si el saldo puede exponerse en consulta básica; la propia spec lo condiciona a política de seguridad.
- Proveedor de pagos y mecánica concreta de pago móvil, transferencia y efectivo/código.

## Brand Commitments

- **El servicio se llama `Ven Vías`**, «Plataforma de Gestión de Peaje», confirmado por la captura de la pantalla institucional que el usuario aportó como referencia. Resuelve la decisión abierta sobre el nombre comercial.
- **`Venezuela en Movimiento` es la marca institucional** que acompaña al servicio, junto con el Ministerio del Poder Popular para el Transporte y la marca conmemorativa de 200 años.
- **El sistema visual del portal está fijado por el usuario** a la pantalla real de Ven Vías: campo claro con ondas, azul `#0033AB`, superficie blanca con filete fino, Roboto. Sustituye al sistema anterior de este repositorio (SEÑAL).
- Activo existente: `assets/venezuela_logo.png`. Colores muestreados del archivo, no inventados:
  - Azul institucional `#0056A3` (palabra "Movimiento")
  - Tinta azul profunda `#132243` (palabra "Venezuela en")
  - Arco de movimiento, muestreado por el núcleo más saturado de cada columna: `#039292` → `#34B046` → `#CB8B18` → `#FA6C24` → `#F62728`
- Activo existente: `assets/auth-toll-background.png`, fotografía de un pórtico de peaje. **Exclusión deliberada:** no se usa; pelea con la contención institucional del sistema.
- **Activos que faltan y no deben fabricarse:** el escudo del Ministerio del Poder Popular para el Transporte y la marca conmemorativa «200 años [2022–2030]». Se muestran como recuadros de reemplazo rotulados hasta que lleguen los archivos reales.
- Idioma del producto: español.
- La spec prohíbe explícitamente la estética de portal financiero o banca tradicional. El producto debe leerse como servicio de movilidad.
- Vocabulario prohibido en la interfaz: "crear billetera", "abrir cuenta obligatoria", "regístrese para continuar", "complete su perfil".

## Evidence on Hand

- `specs/plate-wallet-web-ui-spec.md`: especificación UI/UX completa, 32 secciones, con wireframes en texto, copy recomendado, componentes, estados y reglas de accesibilidad. Es la fuente de verdad funcional.
- `assets/venezuela_logo.png` y `assets/auth-toll-background.png`.
- `.claude/skills/` y `.claude/agents/`: convenciones de arquitectura de un panel administrativo Vue del mismo dominio.

**No existe y no debe fabricarse:** clientes, testimonios, métricas de uso, volumen de transacciones, acuerdos con operadores de peaje, cobertura real, precios, licencias ni afirmaciones de disponibilidad. Todo dato de demostración en el prototipo es sintético y debe estar rotulado como tal.

## Product Principles

1. **La placa es la cuenta.** El dato crítico de toda operación es la matrícula; debe ser el elemento más grande, más legible y más verificable de cada pantalla donde aparece.
2. **El registro se gana, nunca se exige.** Cada pantalla pública debe seguir funcionando completa si el usuario nunca crea una cuenta. La invitación a registrarse llega después del éxito y se puede rechazar sin costo.
3. **Un dato público no es una credencial.** Acreditar saldo es abierto; leer movimientos o modificar la billetera exige un segundo factor. La interfaz debe explicar esa asimetría en lenguaje corriente.
4. **Error de digitación es pérdida de dinero.** Prevenir la placa equivocada vale más que ahorrar un paso: confirmación explícita antes de pagar, tipografía inequívoca, y repetición de placa en montos altos.
5. **Se usa al sol, con una mano, con prisa.** Legibilidad y tamaño de objetivo táctil ganan a densidad y elegancia en cualquier conflicto.

## Accessibility & Inclusion

WCAG 2.1 AA como mínimo, requisito explícito de la spec. Contraste suficiente, navegación por teclado, labels visibles, compatibilidad con lectores de pantalla, estados que no dependen solo del color, mensajes de error descriptivos y área táctil mínima de 44×44 px. La variante kiosco exige texto de 18 px o más y objetivos ampliados.
