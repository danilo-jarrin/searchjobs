# Método del corpus — del lote a la oferta firmada

La mayoría de los métodos de búsqueda de empleo optimizan una postulación a la vez: coges una vacante, ajustas el CV, envías, repites. Funcionan cuando consigues las vacantes a mano y tienes cinco.

Este método parte de otra situación: **un lote de decenas de vacantes reales, renovado cada semana**. Eso cambia el problema de sitio. Con corpus no hace falta adivinar qué busca el mercado: se mide. Y el recurso escaso deja de ser la vacante y pasa a ser el tiempo, así que la primera operación no es escribir — es descartar.

De ahí las dos ideas que sostienen todo lo demás:

1. **El vocabulario se destila del corpus, no de una descripción suelta.** Un anuncio te dice qué quiere una empresa; setenta y cinco te dicen qué paga el mercado.
2. **Ningún documento inventa nada.** Todo lo que entra en un CV sale de un libro de evidencias verificado con el candidato. Si no está en el libro, no se escribe.

## Los cuatro archivos vivos

Ninguno se genera de nuevo cada vez. Se acumulan, y por eso el sistema mejora con las semanas en vez de repetir el mismo esfuerzo.

| Archivo | Qué contiene | Cada cuánto |
|---|---|---|
| `mercado.md` | El vocabulario destilado del corpus: términos por frecuencia, plataformas nombradas, rangos salariales, requisitos que se repiten | Semanal, al correr el pipeline |
| `evidencias.md` | Los hechos verificados de su carrera: cifras con denominador, fechas, tamaños de equipo, presupuestos | Una vez, luego incremental |
| `brechas.md` | Las 3 o 4 debilidades estructurales del perfil, cada una con su mejor respuesta | Se revisa cuando cambia el perfil |
| `historial.md` | Estado de cada postulación + la autopsia de cada rechazo | Cada sesión |

---

## Operación 1 · Destilar el mercado

**Cuándo:** después de cada ejecución del pipeline. **Entrada:** el CSV. **Salida:** `mercado.md`.

Cuenta lo que se repite. No lo estimes — el CSV lo dice:

```bash
python3 -c "
import csv,collections,re
r=list(csv.DictReader(open('CSV')))
w=collections.Counter()
for x in r:
    for t in re.findall(r'[a-zA-Z&]+', x['title'].lower()):
        if t not in {'manager','and','the','of','for','at','to','a','uk','senior','lead','emea','eu'}: w[t]+=1
print(w.most_common(25))
"
```

Los títulos dan la superficie. La sustancia está en las descripciones, y el CSV solo guarda URLs: baja con `WebFetch` **entre 5 y 8 descripciones completas** de las vacantes mejor puntuadas del lote y extrae de ellas:

- **Plataformas con nombre propio** — GA4, HubSpot, Klaviyo, Looker, n8n. Son cadenas literales: el ATS las busca tal cual y no infiere sinónimos.
- **Años de experiencia exigidos.** Es el filtro más duro y el menos negociable. Anótalo por vacante.
- **La frase que se repite entre empresas distintas.** Cuando dos compañías sin relación piden lo mismo con las mismas palabras, eso es el mercado hablando, no una preferencia.
- **Bandas salariales**, cuando el anuncio las publique.

*Ejemplo real* — lote de 75 vacantes de marketing en Londres, agosto de 2026. La destilación dio: `marketing` 48 · `growth` 15 · `performance` 14 · `CRM` 12 · `paid/media` 15 · `AI` 6 · `lifecycle` 4. Y una coincidencia que decidió el posicionamiento: dos empresas sin relación entre sí — una de herramientas para desarrolladores, otra de IA legal — pedían con las mismas palabras **experiencia práctica usando IA para automatización de marketing**. Eso no se deduce leyendo un anuncio.

`mercado.md` no es un informe para leer: es la lista de palabras que después tienen permiso de entrar en el CV.

---

## Operación 2 · Triaje

**Cuándo:** inmediatamente después. **Salida:** de 75 a 8, en el historial.

Con decenas de vacantes, postular a todas es peor que postular a ocho. Descarta en dos pasadas:

**Pasada 1 — eliminación mecánica.** Fuera sin leer: agencias de reclutamiento que republican vacantes de terceros; duplicados de la misma empresa con título distinto; ubicaciones fuera del radio real; y cualquier vacante cuyos años exigidos superen su experiencia en más de dos.

**Pasada 1b — familias sin evidencia.** Aquí es donde el libro de evidencias paga su coste. Hay familias enteras de vacantes cuyo requisito central el candidato **no puede acreditar con una cifra**, y postular a ellas no es optimismo: es gastar el turno. La regla:

> Si el requisito que define una familia de puestos no tiene entrada verificada en `evidencias.md`, esa familia baja al final de la cola — y se descarta del todo si el anuncio lo pone como requisito duro.

Detectarla es mecánico: coge los términos que `mercado.md` sacó del corpus, y para cada uno pregunta qué evidencia lo respalda. El que no tenga ninguna define una familia despriorizada. Anótalas en `evidencias.md` con la fecha y el motivo, para no volver a decidirlo cada semana.

Dos matices que evitan que la regla haga daño:

- **Despriorizar no es dejar de descubrir.** Esas vacantes siguen entrando al pipeline: alimentan `mercado.md`, muestran qué plataformas y qué bandas paga ese segmento, y avisan si el mercado se desplaza hacia allí. Lo que se retira es el *tiempo de postulación*, no la observación.
- **La familia se reabre cuando aparece la evidencia.** Es una consecuencia del libro, no un juicio sobre el candidato. En cuanto entre la cifra que falta, vuelve a la cola.

**Pasada 2 — el criterio de los tres ejes.** De lo que sobreviva, ordena por:

- **Vocabulario** — cuántos términos de `mercado.md` comparte con `evidencias.md`
- **Escasez** — cuánta gente puede competir por eso. Una vacante que pide marketing *y* construir automatizaciones tiene un décimo de candidatos que una de marketing a secas
- **Puerta de entrada** — empresa de producto directa pesa más que intermediario

Entrega ocho, cada una en una línea, y **una frase sobre el patrón del lote**: qué se repite esta semana, qué desapareció respecto a la anterior. Ese patrón es lo que ningún método sin corpus puede darle.

Cuando una familia despriorizada represente un tercio o más del corpus, dilo en voz alta: significa que el embudo está trayendo mercado que el perfil no puede convertir, y la corrección no va en el triaje sino en las queries de la fuente.

---

## Operación 3 · Libro de evidencias

**Cuándo:** una vez al principio, y luego cada vez que aparezca un dato nuevo. **Salida:** `evidencias.md`.

Esta operación existe porque es el fallo que aparece en casi todos los CV: métricas sin denominador. *"reduje el tiempo hasta un 80%"*, *"varios clientes"*, *"4.8/5 de satisfacción"*. Suenan bien y no resisten una repregunta en la entrevista.

Entrevístale para llenar el libro. Una pregunta cada vez, nunca un cuestionario. Cada entrada necesita **cifra y denominador**:

| No sirve | Sirve |
|---|---|
| "reduje el tiempo un 80%" | "de 12 h a 2,5 h semanales, en 4 de 7 clientes" |
| "ROI positivo en 45 días" | "3 de 5 encargos recuperaron la inversión antes de 45 días" |
| "4.8/5 de satisfacción" | "4.8/5 sobre 11 clientes valorados" |

Regla que no se rompe: **si no está en el libro, no entra en el CV**. Nada de números plausibles, nada de redondeos generosos, nada de rellenar un hueco porque queda bien. Cuando falte un dato, se le pregunta; hasta que responda, el documento queda bloqueado y se lo dices.

El libro se escribe una vez y se reutiliza en cada postulación, cada carta y cada entrevista. Es lo que evita que vuelva a contestar las mismas once preguntas la semana que viene.

---

## Operación 4 · Dossier de brechas

**Cuándo:** al montar el libro de evidencias. **Salida:** `brechas.md`.

Todo perfil tiene tres o cuatro debilidades estructurales que no se arreglan escribiendo mejor. Escóndelas y salen en la entrevista; ignóralas y salen en la criba. Documéntalas y se convierten en material preparado.

Cada brecha lleva cuatro campos: **qué es**, **quién la va a ver** (ATS, criba humana, entrevista), **la mejor respuesta con evidencia del libro**, y **cuándo no vale la pena pelearla**.

Las tres que aparecen más a menudo, a modo de ejemplo:

1. **Menos años de los que pide el anuncio.** Respuesta: la experiencia adyacente que sí cuenta — dirigir equipo, controlar coste, responder ante cliente — más lo que el perfil trae y esos años no suelen traer. No vale la pena pelearla cuando el anuncio pone los años como requisito legal o de banda salarial cerrada.
2. **Un sector anterior que domina el historial.** Se ve en tres segundos. Respuesta: reencuadrarlo por la competencia transferible y comprimirlo a dos líneas, no cinco, para que deje de ocupar el espacio de lo relevante.
3. **Un hueco temporal sin explicar.** Respuesta: solo la que dé el usuario. Es la brecha que nunca se rellena por él.

La cuarta columna importa tanto como las otras: saber cuándo una vacante no es peleable ahorra más tiempo que cualquier plantilla.

---

## Operación 5 · Ensamblar el documento

**Cuándo:** por vacante, solo para las ocho del triaje. **Salida:** el directorio de la postulación.

El CV no se "adapta": se **ensambla** a partir de piezas que ya existen. El vocabulario sale de `mercado.md`, los hechos de `evidencias.md`, el manejo de debilidades de `brechas.md`. Eso es lo que hace la operación rápida y lo que garantiza que nada se invente.

Secuencia:

1. Baja la descripción completa con `WebFetch` → `vacante.md`. Sin ella no hay nada que ensamblar.
2. Selecciona del libro las evidencias que responden a los requisitos de *esta* vacante, ordenadas por lo que la empresa pide primero.
3. Redacta cada línea con **acción → resultado medido → mecanismo**. Verbo al principio; nunca "responsable de".
4. Inyecta el vocabulario de `mercado.md` que la vacante confirme — en inglés y literal. Si una palabra no está en la vacante o en el corpus, no entra.
5. Aplica el dossier de brechas: la que vaya a ver el lector, se enfrenta; la que no, no se menciona.
6. Antes de dar nada por listo, **lista los datos que faltan** y bloquea el envío hasta que él los confirme.

Dos comprobaciones finales, ambas con el documento delante:

- **Lectura de máquina.** Una columna, sin tablas, sin cajas de texto, sin imágenes, fuente estándar. Qué términos de la vacante aparecen y cuáles no.
- **Lectura de seis segundos.** Qué se lee sin desplazar. Si en ese espacio no está lo que hace a este candidato distinto de los otros doscientos, el orden está mal.

Para el mensaje que acompaña — carta, nota o InMail — la misma materia prima y tres movimientos: algo concreto y reciente de la empresa, dos evidencias del libro que respondan a sus dos requisitos principales, y la brecha más visible enfrentada de cara. Cierra pidiendo la conversación. Bajo 200 palabras.

---

## Operación 6 · Entrevista y cierre económico

**Cuándo:** al cambiar el estado en el historial.

Para la entrevista, la preparación sale otra vez de los mismos archivos: las preguntas previsibles son las brechas del dossier más los requisitos de `vacante.md`, y las respuestas son evidencias del libro dichas en voz alta. Investiga la empresa por lo que hace, no por lo que dice de sí misma: qué vende, a quién, contra quién compite y qué le aprieta ahora.

Después, **simula**: una pregunta, espera su respuesta, corrige, siguiente. Entregarle diez respuestas escritas no le prepara; contestarlas sí.

Para el cierre económico, el corpus vuelve a servir: las bandas que publicaron las vacantes de su lote son datos de mercado de su nivel exacto, recogidos esta semana — mejor referencia que cualquier agregador. Contrástalas con Glassdoor UK, Reed Salary Checker y Adzuna. Prepara un número concreto, no un rango; qué se negocia cuando la base es fija (vacaciones, aportación a pensión, días de oficina, formación); y el punto en que se retira. Si le piden expectativas antes de la oferta, la respuesta es devolver la pregunta: cuál es la banda presupuestada.

---

## Operación 7 · Autopsia

**Cuándo:** con cada rechazo o cada silencio de tres semanas. Es la operación que hace que esto sea un sistema y no una plantilla.

Anota **en qué escalón se cayó**, porque cada escalón acusa a un archivo distinto:

| Se cayó en | Falló | Qué se corrige |
|---|---|---|
| Sin respuesta al CV | el ensamblado o el vocabulario | `mercado.md` y la operación 5 |
| Criba telefónica | el posicionamiento o una brecha | `brechas.md` |
| Entrevista técnica | evidencias flojas | `evidencias.md` |
| Final | competencia, o encaje real | el triaje: apuntabas mal |

Cada cuatro semanas, relee las autopsias juntas. Si tres rechazos apuntan al mismo archivo, ahí está el trabajo — y probablemente también haya que tocar las queries de `searches.yaml`, porque el problema empezó en qué vacantes entran al embudo.

Sin esta operación, la semana veinte es idéntica a la primera.
