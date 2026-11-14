# SIMTELE-RISK

Laboratorio académico de métodos numéricos inspirado en el transporte por cable de La Paz y El Alto. Los modelos, mediciones y escenarios son sintéticos: no describen fallas reales ni establecen recomendaciones de mantenimiento para Mi Teleférico.

- **Aplicación:** [itzmanix.github.io/teleferico](https://itzmanix.github.io/teleferico/)
- **Informe de la semana 1:** [informe.md](informe.md)
- **Repositorio:** [github.com/iTzManix/teleferico](https://github.com/iTzManix/teleferico)

## Casos de estudio

| Módulo | Problema | Estado |
|---|---|---|
| Detención de emergencia | Tiempo y distancia de parada según velocidad, reacción y frenado. | Planteamiento disponible; simulador en desarrollo. |
| Deterioro mecánico y mantenimiento | Proyección de un índice hipotético de deterioro de un rodamiento. | Simulador, tres escenarios y comparación de sistemas lineales. |
| Oscilaciones por viento | Respuesta idealizada de una cabina ante fuerzas externas. | Planteamiento disponible; simulador en desarrollo. |
| Reconstrucción de sensores | Estimación de datos faltantes en una señal conocida. | Planteamiento disponible; simulador en desarrollo. |

La portada enlaza a una página independiente para cada módulo. El informe describe las ecuaciones, supuestos, escenarios y resultados de esta etapa; las demás familias numéricas y la evidencia transversal de error para los cuatro casos siguen pendientes.

## Ejecutar el sitio

Se requiere Python 3 para servir archivos localmente y una conexión a Internet para cargar las fuentes y, cuando se utiliza el historial, el cliente de Supabase.

```bash
python -m http.server 8000
```

Abre [http://localhost:8000](http://localhost:8000). No abras `index.html` directamente como archivo local: los módulos JavaScript requieren un servidor HTTP.

Para comprobar los cálculos con Node.js:

```bash
npm test
```

El sitio está publicado desde la raíz de la rama `main` mediante GitHub Pages.

## Modelo de deterioro

El índice se define como `D(t) = D₀ + αt + βt²` y la lectura observada como `y(t) = D(t) + s`. El sesgo constante `s` se obtiene mediante una **calibración independiente del sensor**: sin ella, `D₀` y `s` no pueden distinguirse. Tres lecturas y la calibración definen `Cx = y`; la solución directa de este sistema sirve de referencia. Para comparar métodos se construye `A = CᵀC` y `b = Cᵀy`.

El simulador aplica Jacobi, Gauss-Seidel, LU con pivoteo y gradiente conjugado a `Ax = b`. Presenta las aproximaciones, iteraciones finitas, residuos, errores y condición de `A`. Los iterativos parten de cero, se detienen con `||b − Ax||₂ ≤ tolerancia` y tienen un límite de 1.000 iteraciones; también se interrumpen ante desbordamiento. Un residuo pequeño no garantiza error pequeño en una matriz mal condicionada. La formación de ecuaciones normales puede aumentar el condicionamiento.

El escenario normal usa mediciones a 0, 1 y 2 meses; estrés usa los mismos tiempos con un deterioro sintético más rápido. El mal condicionado usa 0, 0,02 y 0,04 meses para estudiar sensibilidad matemática. La comparación entre escenarios que muestra la página utiliza sus valores predeterminados; la simulación principal utiliza las entradas actuales del usuario.

El umbral de 60 puntos es un valor educativo. El cálculo de cruce admite crecimiento monótono con `α ≥ 0` y `β ≥ 0`; si el índice inicial ya alcanza el umbral, devuelve cero. La calibración no determina por sí sola una falla real.

## Historial y datos

Supabase Auth gestiona el registro y el inicio de sesión. La tabla `public.simulaciones` conserva por usuario las entradas, resultados, escenario, versión y fecha. Las políticas RLS restringen las filas al propietario autenticado; la clave de cliente incorporada en el código es pública y no otorga privilegios de servicio. La confirmación del correo depende de la configuración del proyecto Supabase.

El [DER conceptual](figuras/der-propuesto.png) describe el diseño propuesto con casos, escenarios, simulaciones, métodos, iteraciones y resultados. El esquema actualmente aplicado, documentado en [schema.sql](schema.sql), guarda una instantánea JSON por simulación y **no implementa aún todas esas entidades como tablas separadas**. El historial repite las entradas con la versión actual del modelo; todavía falta comprobar con una cuenta real el flujo completo de guardar, cerrar, recuperar y repetir.

## Archivos principales

| Archivo | Función |
|---|---|
| `index.html` | Portada y acceso a los cuatro casos. |
| `deterioro.html`, `detencion.html`, `viento.html`, `sensores.html` | Páginas de los módulos. |
| `app.js` | Interfaz del simulador e integración de historial. |
| `math.js` | Modelo de calibración y métodos numéricos. |
| `math.test.js` | Pruebas de referencia, sensibilidad, validación e iteraciones. |
| `style.css` | Diseño adaptable y tokens para temas claro y oscuro. |
| `schema.sql` | Esquema y políticas de acceso de Supabase. |
| `informe.md` y `figuras/` | Informe científico de avance, DER y gráficos de resultados. |

## Fotografía de portada

[Línea Roja de Mi Teleférico en La Paz, Bolivia](https://commons.wikimedia.org/wiki/File:L%C3%ADnea_Roja_de_Mi_Telef%C3%A9rico_en_La_Paz,_Bolivia.jpg), EEJCC, [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). La imagen se presenta con un degradado superpuesto mediante CSS.
