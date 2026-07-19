# Requirements Analysis — Preguntas de Clarificación

> Etapa 2.3 · Fase Inception · Alcance: MVP (profundidad Estándar)
> Enfoque: huecos a nivel de requisito, aprovechando lo ya decidido
> (Android/Expo/TS + Supabase, humedad-only, polling, dark-first, i18n).
> Responde escribiendo la(s) letra(s) en `[Answer]:`.

---

## P1. ¿Qué datos guarda cada PLANTA? (elige todas las que apliquen)

A. Nombre (obligatorio)
B. Especie/tipo (texto libre, opcional)
C. Foto de la planta
D. Notas / descripción libre
E. Fecha de registro (automática)
X. Other (especificar)

[Answer]: A,B,C,E

---

## P2. Cardinalidades del modelo (importante para el diseño)

¿Cómo se relacionan planta, sensor y zona? (elige la combinación correcta)

A. Una planta pertenece a UNA sola zona; una planta tiene UN sensor de humedad
B. Una planta pertenece a UNA sola zona; una planta puede tener VARIOS sensores
C. Una planta puede estar en VARIAS zonas; un sensor por planta
D. Una planta puede estar en VARIAS zonas; varios sensores por planta
X. Other (especificar)

[Answer]: B

---

## P3. Semántica del umbral de humedad (MVP usa umbral fijo)

A. Solo mínimo: alerta cuando la humedad BAJA de un valor fijo (p.ej. 30%)
B. Rango min–max: alerta si baja del mínimo O sube del máximo
C. Otro criterio (especificar en Other)
X. Other (especificar)

[Answer]: A

---

## P4. Frescura de la lectura de humedad

A. "Humedad actual" = última lectura registrada; polling cada ~X minutos (sugiero 5 min)
B. Igual, pero además marcar "sin dato" si la última lectura tiene más de cierto tiempo (p.ej. >1h)
C. No importa la frecuencia por ahora; solo mostrar la última que haya
D. Recomiéndame
X. Other (especificar)

[Answer]:A

---

## P5. Método de autenticación (Supabase Auth)

A. Solo correo + contraseña
B. Correo + contraseña, y además Google (OAuth)
C. Correo + contraseña con verificación de correo obligatoria
D. Recomiéndame lo más simple para el MVP
X. Other (especificar)

[Answer]: A

---

## P6. Comportamiento de las alertas de humedad baja

A. Se dispara una push al cruzar el umbral; en la app se ve en "alertas recientes"; se resuelve sola cuando la humedad vuelve a normal
B. Igual, pero además se puede descartar/marcar como vista manualmente
C. Recordatorio repetido mientras siga baja (p.ej. cada cierto tiempo)
D. Recomiéndame
X. Other (especificar)

[Answer]: A
