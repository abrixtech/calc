# Calculadora Financiera de Planificación de Retiro

Una herramienta web interactiva para planificar la sostenibilidad financiera a lo largo de las distintas etapas de la vida: fase laboral, retiro anticipado y pensión pública.

---

## Demo

Abre el archivo `Calc_TF_v1,2.html` directamente en cualquier navegador moderno. No requiere servidor, instalación ni dependencias locales.

> Requiere conexión a internet para cargar Chart.js desde CDN. Ver sección [Uso offline](#uso-offline) para una alternativa sin conexión.

---

## Qué hace

La calculadora simula año a año la evolución del patrimonio personal teniendo en cuenta tres fases de vida diferenciadas:

- **Fase laboral** — desde la edad actual hasta la edad de retiro. El salario mensual se compara con el gasto mensual; el superávit o déficit se suma o resta al patrimonio junto con el rendimiento de la inversión.
- **Retiro sin pensión** — desde la edad de retiro hasta los 67 años. El patrimonio cubre íntegramente los gastos, que crecen cada año por inflación.
- **Retiro con pensión** — a partir de los 67 años. La pensión pública actúa como ingreso que reduce la presión sobre el patrimonio.

Todos los cálculos incorporan inflación acumulada sobre gastos, salario y pensión.

---

## Parámetros de entrada

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
| Pensión pública mensual | Importe mensual estimado de pensión pública (0–4.000 €), aplicada desde los 67 años |

---

## Resultados y visualización

### Métricas principales
- Duración estimada del patrimonio desde el retiro
- Patrimonio acumulado en el momento del retiro
- Saldo final al alcanzar el horizonte temporal
- Edad crítica estimada (año en que el patrimonio se agotaría)

### Gráfico de evolución patrimonial
Muestra la curva del patrimonio año a año con marcadores visuales para el inicio del retiro y el inicio de la pensión. Incluye tooltip interactivo con el valor exacto a cada edad.

### Análisis dinámico
Texto explicativo que se actualiza en tiempo real describiendo:
- Situación durante la fase laboral (ahorro o déficit anual)
- Patrimonio proyectado al momento del retiro
- Margen real entre rendimiento e inflación
- Impacto de la pensión pública
- Conclusión sobre sostenibilidad

### Fases del plan
Desglose narrativo de cada etapa de vida con cifras concretas y estimaciones ajustadas por inflación acumulada.

---

## Lógica de cálculo

La simulación itera año a año aplicando la siguiente lógica:

```
Para cada año i desde edad_actual hasta horizonte:

  Si edad < edad_retiro:
    patrimonio = patrimonio * (1 + rendimiento) + salario_anual - gasto_anual

  Si edad >= edad_retiro y edad < 67:
    patrimonio = patrimonio * (1 + rendimiento) - gasto_anual

  Si edad >= 67:
    patrimonio = patrimonio * (1 + rendimiento) - max(0, gasto_anual - pension_anual)

  Al final de cada año:
    gasto_anual *= (1 + inflacion)
    salario_anual *= (1 + inflacion)
```

La **tasa de retiro anual** y el **gasto mensual** están vinculados bidireccionalmente: modificar uno actualiza automáticamente el otro en función del patrimonio actual.

---

## Tecnologías

- HTML5 + CSS3 + JavaScript vanilla — sin frameworks ni dependencias locales
- [Chart.js 4.4.1](https://www.chartjs.org/) — gráfico de evolución patrimonial (cargado desde CDN)
- Layout responsive con CSS Grid — columna izquierda (calculadora) y columna derecha (dashboard)

---

## Uso offline

Si necesitas usar la herramienta sin conexión a internet, descarga el archivo de Chart.js y reemplaza la línea del script en el `<head>`:

```html
<!-- Reemplazar esto: -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>

<!-- Por esto (con la ruta local donde hayas guardado el archivo): -->
<script src="./chart.umd.js"></script>
```

Puedes descargar Chart.js desde: https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js

---

## Limitaciones y advertencias

- Los cálculos son proyecciones estimadas basadas en tasas constantes. En la realidad, los rendimientos, la inflación y los gastos fluctúan cada año.
- La herramienta asume que la pensión pública comienza exactamente a los 67 años y que su importe crece con la misma tasa de inflación configurada.
- No tiene en cuenta impuestos sobre rendimientos del capital, plusvalías ni variaciones en el tipo marginal.
- No constituye asesoramiento financiero. Para decisiones de inversión o planificación de jubilación, consulta con un asesor financiero certificado.

---

## Historial de versiones

| Versión | Cambios |
|---|---|
| v1 | Calculadora básica: patrimonio, rendimiento, inflación, gasto mensual, años de sostenibilidad |
| v2 | Añadida pensión pública (desde los 67) y tasa de retiro anual vinculada con gasto mensual |
| v3 | Dashboard a dos columnas con gráfico de evolución, métricas clave, análisis dinámico y fases del plan |
| v4 | Añadidos salario mensual y edad de retiro; simulación completa por fases de vida; layout más compacto |

---

## Licencia

MIT — libre para usar, modificar y distribuir con o sin atribución.
