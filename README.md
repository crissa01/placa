# toll-by-plate

**Ven Vías** — portal público de prepago de peajes donde **la placa del vehículo es
la billetera**, sin registro obligatorio.

## Aplicación Vue

La implementación está en `src/` y sigue la arquitectura por dominios definida
en `ARCHITECTURE_RULES.md`: vistas → composables → servicios → mocks JSON.

```bash
npm install
npm run dev
```

Para generar la versión de producción:

```bash
npm run build
```

La aplicación incluye el flujo público de cuatro pasos para recarga, consulta
de saldo y el listado de peajes. Los valores se sirven desde mocks tipados; no
hay integración con backend en esta etapa.

## Propuesta de frontend

| | |
|---|---|
| **Prototipo navegable** | [`design/index.html`](design/index.html) — ábrelo en el navegador, no requiere instalación |
| **Revisión de la especificación** | [`docs/01-revision-spec.md`](docs/01-revision-spec.md) |
| **Propuesta de layout** | [`docs/02-propuesta-layout.md`](docs/02-propuesta-layout.md) |
| **Verdad de producto** | [`PRODUCT.md`](PRODUCT.md) |
| **Especificación original** | [`specs/plate-wallet-web-ui-spec.md`](specs/plate-wallet-web-ui-spec.md) |

El sistema visual hereda la pantalla institucional de Ven Vías: campo claro con
ondas, azul `#0033AB`, superficie blanca con filete fino y Roboto. Toda la paleta
está muestreada de esa captura.

El prototipo corre un backend simulado en memoria: máquinas de estado reales,
latencia, guardas antifraude y modos de fallo. Abre el panel **Flujo de datos**
y déjalo abierto mientras navegas.

**Todos los datos del prototipo son sintéticos.** Ninguna cifra, peaje, tarifa ni
cobertura representa un acuerdo o un precio real. Faltan dos logotipos
institucionales, marcados en pantalla como recuadros de reemplazo.

### Placas de prueba

| Placa | Estado |
|---|---|
| `AB123CD` | Activa, $12.50 — código de seguridad `784921` |
| `AC456EF` | Saldo bajo, $7.20 |
| `AD789GH` | Sin saldo |
| `AE012IJ` | Bloqueada |
| `AF345KL` | En revisión |
