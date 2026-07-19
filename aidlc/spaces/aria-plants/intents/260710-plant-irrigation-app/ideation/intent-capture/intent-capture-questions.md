# Intent Capture — Preguntas de Clarificación

> Etapa 1.1 · Fase Ideación · Alcance: MVP (profundidad Estándar)
> Responde cada pregunta escribiendo la(s) letra(s) en el tag `[Answer]:`.
> Cada pregunta incluye `X. Other` para respuesta libre. Puedes elegir varias
> letras donde diga "(elige todas las que apliquen)".

---

## P1. ¿Cuál es la naturaleza y motivación principal de esta iniciativa?

A. Producto comercial (SaaS) para vender a terceros
B. Herramienta interna para una empresa/organización (agrícola, invernadero, laboratorio)
C. Proyecto personal / hobby (monitorear mis propias plantas)
D. Proyecto de investigación / académico / prueba de concepto tecnológica
E. Portafolio / demostración técnica
X. Other (especificar)

[Answer]: C — Proyecto personal / hobby (monitorear mis propias plantas)

---

## P2. ¿Quién es el usuario objetivo principal de la app?

A. Aficionados a plantas en casa (jardín doméstico)
B. Operadores de invernadero / agricultura comercial
C. Técnicos / agrónomos que monitorean uno o varios sitios
D. Un equipo interno de una empresa/organización
E. Combinación de varios perfiles
X. Other (especificar)

[Answer]: A — Aficionados a plantas en casa (jardín doméstico)

---

## P3. ¿Cómo se ve el éxito? ¿Qué resultado importa más? (elige todas las que apliquen)

A. Visualizar en tiempo (casi) real la salud de cada planta
B. Reducir pérdidas de plantas / optimizar el riego
C. Centralizar en un solo lugar la información de todos los sensores y zonas
D. Recibir alertas tempranas ante condiciones anómalas (humedad/luz fuera de rango)
E. Lograr adopción / desplegar en producción para usuarios reales
X. Other (especificar)

[Answer]: B, C, D — Optimizar riego, Centralizar información, Alertas tempranas. NOTA: la visualización de "salud" de la planta (opción A) se implementará más adelante y NO forma parte del MVP.

---

## P4. ¿Cuál es el detonante de esta iniciativa? (¿por qué ahora?)

A. Ya existen (o llegarán pronto) los sensores y falta el software para visualizarlos
B. Oportunidad de negocio / demanda de clientes
C. Necesidad interna de la organización
D. Aprendizaje / exploración tecnológica (multiplataforma, IoT)
E. Reemplazar un proceso manual actual (hojas de cálculo, registros en papel)
X. Other (especificar)

[Answer]: D — Aprendizaje / exploración tecnológica (multiplataforma, IoT)

---

## P5. ¿Cuál es la escala inicial esperada? (señal de alcance)

A. Pequeña: 1 usuario, menos de 10 plantas, 1 zona de riego
B. Media: pocos usuarios, decenas de plantas, varias zonas
C. Grande: múltiples usuarios u organizaciones, cientos+ de plantas
D. Aún no definida / crecerá con el tiempo
X. Other (especificar)

[Answer]: A — Pequeña. Detalle del usuario: 1 usuario, 11 plantas, 1 zona de riego al inicio; puede escalar a más adelante.

---

## P6. ¿De dónde vendrán los datos de los sensores? (frontera de ESTE repositorio)

> Dijiste que este repo es únicamente la app. Esto define de dónde llega la data.

A. Ya existe un backend/API que la app solo consumirá (fuera de este repo)
B. El backend se construirá por separado; este repo es solo la app (el contrato se define después)
C. La app debe recibir los datos directamente de los sensores (MQTT/HTTP), sin backend intermedio
D. Aún no hay sensores físicos; por ahora se usarán datos simulados / mock
E. No definido todavía
X. Other (especificar)

[Answer]: B — El backend se construirá por separado; este repo es solo la app. El contrato de datos (API) se define después.

---

## P7. ¿Es un green-field o reemplaza algo existente?

A. Green-field: no hay una solución previa
B. Reemplaza hojas de cálculo / registros manuales
C. Reemplaza o complementa una app / sistema existente
X. Other (especificar)

[Answer]: A — Green-field: no hay una solución previa.
