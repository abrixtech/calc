# Calculadora Financiera de Planificación de Retiro

Una herramienta web interactiva de dos pestañas para planificar la sostenibilidad financiera a lo largo de las distintas etapas de la vida y diagnosticar la salud financiera actual.

---

## Demo

Abre el archivo `Calc_TF_v1.3.html` directamente en cualquier navegador moderno. No requiere servidor, instalación ni dependencias locales.

> Requiere conexión a internet para cargar Chart.js desde CDN. La función de análisis con IA requiere además estar en [claude.ai](https://claude.ai). Ver sección [Uso offline](#uso-offline) para la alternativa sin conexión.

---

## Estructura: dos pestañas

### Pestaña 1 — Planificador

Simula año a año la evolución del patrimonio personal a lo largo de tres fases de vida:

- **Fase laboral** — desde la edad actual hasta la edad de retiro. El exceso de salario sobre gastos se acumula en una bolsa de ahorro separada con su propio rendimiento configurable (por defecto 2%). Si el gasto supera al salario, el déficit se cubre primero del ahorro y luego del patrimonio invertido.
- **Retiro sin pensión** — desde la edad de retiro hasta los 67 años. Los gastos se cubren primero del ahorro acumulado y luego del patrimonio invertido.
- **Retiro con pensión** — a partir de los 67 años. La pensión pública se aplica como ingreso que reduce el gasto neto sobre el patrimonio.

Todos los cálculos incorporan inflación acumulada sobre gastos y salario. La pensión se trata como importe nominal fijo para mantener coherencia con la práctica habitual de planificación conservadora.

### Pestaña 2 — Diagnóstico financiero

Análisis retrospectivo de la situación financiera actual basado en la trayectoria laboral y patrimonial. Incluye indicadores en tiempo real y análisis narrativo opcional con IA.

---

## Parámetros de entrada — Planificador

### Datos personales
| Campo | Descripción |
|---|---|
| Edad actual | Edad de partida de la simulación |
| Edad de retiro | Año en que cesa el salario y comienza el retiro |
| Horizonte temporal | Expectativa de vida en años |

### Patrimonio e inversión
| Campo | Descripción |
|---|---|
| Patrimonio invertido | Capital inicial disponible e invertido |
| Rendimiento anual | Rentabilidad anual esperada de la cartera (%) |
| Inflación anual | Tasa de inflación anual estimada (%) |

### Ingresos y gastos
| Campo | Descripción |
|---|---|
| Salario mensual actual | Ingreso neto mensual durante la fase laboral |
| Gasto mensual | Gasto de vida mensual (crece con inflación cada año) |
| Tasa de retiro anual | Porcentaje del patrimonio retirado anualmente — vinculado bidireccionalmente con el gasto mensual |
| Pensión pública mensual | Importe mensual de pensión pública (0–4.000 €), aplicada desde los 67 años |

### Ahorro durante fase laboral
| Campo | Descripción |
|---|---|
| Rendimiento cuenta ahorro | Rentabilidad de la bolsa de ahorro separada (por defecto 2%) |

---

## Resultados y visualización — Planificador

### Métricas principales (panel superior derecho)
- Duración estimada del patrimonio desde el retiro
- Patrimonio total (invertido + ahorro) en el momento del retiro
- Saldo final al alcanzar el horizonte temporal
- Edad crítica estimada (año en que todos los recursos se agotan)

### Resultados detallados (panel izquierdo)
- Rendimiento anual del patrimonio invertido
- Gasto anual actual
- Ahorro/déficit neto anual (salario menos gasto)
- Tasa de retiro sobre patrimonio actual — indicador estático del año 1
- Tasa de retiro efectiva al retiro — porcentaje real que presiona la cartera una vez agotado el ahorro acumulado
- Patrimonio invertido al retiro y ahorro acumulado al retiro (desglosados)
- Años de sostenibilidad desde el retiro
- Saldo total al llegar al horizonte temporal

### Gráfico de evolución patrimonial
Muestra dos líneas: el patrimonio invertido (sólida) y el total incluyendo ahorro acumulado (discontinua). Marcadores en la edad de retiro y el inicio de la pensión, con tooltip interactivo por edad.

### Análisis dinámico y fases del plan
Texto explicativo actualizado en tiempo real con la situación de cada fase, el impacto de la pensión, el margen rendimiento-inflación y la conclusión de sostenibilidad.

---

## Parámetros de entrada — Diagnóstico financiero

| Campo | Descripción |
|---|---|
| Edad inicio laboral | Año en que comenzaste a trabajar |
| Edad actual | Edad actual |
| Salario inicial neto | Primer salario mensual neto |
| Salario actual neto | Salario mensual neto actual |
| Inversiones y ahorro líquido | Patrimonio invertido o en cuentas, excluida vivienda |
| Valor tasación vivienda habitual | Valor de mercado actual de la vivienda (0 si no se tiene) |
| Hipoteca pendiente total | Importe total pendiente de la hipoteca |
| Gasto mensual actual | Gasto de vida mensual |

Se asume una progresión lineal de salario entre el inicial y el actual para estimar los ingresos totales de la vida laboral.

---

## Indicadores del diagnóstico

| Indicador | Cómo se calcula | Rating A | Rating B | Rating C | Rating D |
|---|---|---|---|---|---|
| Tasa de ahorro histórica | Patrimonio líquido ÷ ingresos totales estimados | ≥ 25% | ≥ 12% | ≥ 5% | < 5% |
| Meses de cobertura | Patrimonio líquido ÷ gasto mensual | ≥ 24m | ≥ 6m | ≥ 3m | < 3m |
| Liquidez patrimonial | Patrimonio líquido ÷ patrimonio neto total | ≥ 60% | ≥ 35% | ≥ 15% | < 15% |
| Ahorro mensual actual | (Salario − gasto) ÷ salario | ≥ 20% | ≥ 10% | ≥ 0% | < 0% |

Cada indicador recibe una valoración A/B/C/D con texto explicativo contextualizado.

### Análisis con IA
El botón "Analizar con Claude" envía los datos a la API de Claude y devuelve un diagnóstico narrativo personalizado de 3-4 párrafos: valoración global, puntos fuertes, riesgos o áreas de mejora, y comparativa con la media española. **Solo funciona en [claude.ai](https://claude.ai)**, no en local.

---

## Lógica de cálculo — Planificador

La simulación usa una única pasada temporal coherente:

```
Inicio: w = patrimonio_invertido, sv = 0 (bolsa de ahorro)

Para cada año i desde edad_actual hasta horizonte:

  // 1. Rendimientos
  w  = w  * (1 + rendimiento)
  sv = sv * (1 + rendimiento_ahorro)

  // 2. Flujos según fase
  Si edad < edad_retiro:
    flujo = salario_anual - gasto_anual
    Si flujo >= 0: sv += flujo          // exceso va al ahorro
    Si flujo < 0:                       // déficit: primero ahorro, luego cartera
      sv -= min(sv, -flujo)
      w  -= déficit_restante

  Si edad >= edad_retiro:
    pension = pension_anual si edad >= 67, si no 0
    neto = max(0, gasto_anual - pension)
    sv -= min(sv, neto)                 // primero del ahorro
    w  -= neto_restante                 // luego de la cartera

  // 3. Inflación para el año siguiente
  gasto_anual   *= (1 + inflacion)
  salario_anual *= (1 + inflacion)
```

La simulación de sostenibilidad parte exactamente del mismo estado que deja la simulación principal al llegar a `edad_retiro`, usando la misma lógica e inflación acumulada, garantizando coherencia total entre el saldo final y la duración estimada.

---

## Tecnologías

- HTML5 + CSS3 + JavaScript vanilla — sin frameworks ni dependencias locales
- [Chart.js 4.4.1](https://www.chartjs.org/) — gráfico de evolución patrimonial (CDN)
- API de Anthropic (claude-sonnet) — análisis narrativo en diagnóstico (solo en claude.ai)
- Layout responsive con CSS Grid

---

## Uso offline

La herramienta funciona offline excepto por Chart.js y el botón de análisis IA. Para usar sin conexión, descarga Chart.js y reemplaza la línea del script:

```html
<!-- Reemplazar esto: -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>

<!-- Por esto: -->
<script src="./chart.umd.js"></script>
```

Descarga Chart.js desde: https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js

El botón de análisis IA no funcionará en local independientemente de la conexión, ya que requiere la gestión de API key que proporciona claude.ai.

---

## Limitaciones y advertencias

- Los cálculos son proyecciones basadas en tasas constantes. En la realidad, rendimientos, inflación y gastos fluctúan cada año.
- La pensión pública se trata como importe nominal fijo (sin inflación), lo que produce proyecciones conservadoras para el retiro tardío.
- La progresión lineal de salario en el diagnóstico es una estimación. Carreras con saltos no lineales pueden diferir significativamente.
- No se tienen en cuenta impuestos sobre rendimientos del capital, plusvalías ni variaciones en el tipo marginal.
- No constituye asesoramiento financiero. Para decisiones de inversión o planificación de jubilación, consulta con un asesor financiero certificado.

---

## Historial de versiones

| Versión | Cambios principales |
|---|---|
| v1 | Calculadora básica: patrimonio, rendimiento, inflación, gasto mensual, años de sostenibilidad |
| v2 | Pensión pública desde los 67, tasa de retiro anual vinculada bidireccionalmente con gasto mensual |
| v3 | Dashboard a dos columnas con gráfico de evolución, métricas clave, análisis dinámico y fases del plan |
| v4 | Salario mensual y edad de retiro; simulación completa por fases de vida; layout panorámico |
| v5 | Bolsa de ahorro separada con rendimiento configurable; dos tasas de retiro (estática y efectiva); sección colapsable y tooltips; pestaña de diagnóstico financiero con KPIs, barras de composición, ratings A/B/C/D y análisis narrativo con IA |

---

## Licencia

MIT — libre para usar, modificar y distribuir con o sin atribución.
