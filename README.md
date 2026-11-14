# SIMTELE-RISK

Laboratorio académico inspirado en el transporte por cable de La Paz. Los escenarios y datos son hipotéticos y no representan fallas, límites ni recomendaciones operativas de Mi Teleférico.

## Inicio

Sirve esta carpeta por HTTP, por ejemplo `python -m http.server 8000`, y abre http://localhost:8000. La aplicación usa JavaScript nativo, Google Fonts y el cliente público de Supabase desde CDN. Ejecuta las pruebas con `npm test` (Node.js).

## Alcance

La página presenta cuatro módulos. El simulador disponible estudia el deterioro hipotético de un rodamiento. Los módulos de detención, viento y sensores están en desarrollo. El escenario normal usa mediciones a 0, 1 y 2 meses; estrés usa una evolución mayor; mal condicionado acerca las mediciones a 0, 0,02 y 0,04 meses. Los valores y el umbral son sintéticos.

## Modelo y calibración

D(t) = D₀ + αt + βt²; las lecturas observadas son y(t) = D(t) + s. D₀ es el índice inicial, α y β expresan su evolución, y s es un sesgo constante. **El sesgo requiere una calibración independiente**: sin ella D₀ y s no pueden identificarse por separado. Las tres lecturas y la calibración conforman Cx = y con cuatro incógnitas. Se comparan Jacobi, Gauss-Seidel, LU con pivoteo y gradiente conjugado sobre A = CᵀC, b = Cᵀy, con la solución directa de Cx = y como referencia. Las ecuaciones normales pueden amplificar el mal condicionamiento. Los iterativos tienen máximo de 1000 iteraciones y criterio de parada por norma del residuo. Se informa cuando no convergen. La condición es κ₂(A), estimada con una descomposición simétrica por rotaciones de Jacobi.

El cálculo del umbral admite crecimiento monótono con α ≥ 0 y β ≥ 0, o indica que no se calcula. Si el índice inicial ya supera el umbral, el tiempo es cero. El resultado es una proyección educativa, no una predicción de falla.

## Persistencia

Supabase Auth gestiona registro e inicio de sesión. Las simulaciones se guardan en `public.simulaciones`, con entradas, resultados, versión y usuario. RLS permite a cada usuario consultar, insertar y eliminar únicamente sus propias filas. La clave pública del cliente no es una clave secreta; nunca usar `service_role` en el navegador. El registro puede requerir confirmación por correo según la configuración de Supabase. El historial abre y repite con el modelo actual; la versión guardada permite detectar cambios futuros, pero esta primera versión no reconstruye versiones anteriores del algoritmo.

`schema.sql` documenta la estructura aplicada. El DER inicialmente propuesto es una meta de evolución: esta primera versión guarda una instantánea JSON por simulación, no implementa aún todas sus entidades normalizadas ni las tablas de iteraciones. Tampoco sustituye las pruebas y el informe requeridos en la semana 1.

## Imagen

[Línea Roja de Mi Teleférico en La Paz, Bolivia](https://commons.wikimedia.org/wiki/File:L%C3%ADnea_Roja_de_Mi_Telef%C3%A9rico_en_La_Paz,_Bolivia.jpg), EEJCC, [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). Se muestra sin modificar, con un degradado superpuesto mediante CSS.
