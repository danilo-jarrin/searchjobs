---
name: buscar-trabajo
description: Método del corpus para búsqueda de empleo — destila el vocabulario real del mercado a partir de un lote de vacantes, tría lo que merece tu tiempo, y ensambla CV, cartas y preparación de entrevista a partir de un libro de evidencias verificado, sin inventar una sola cifra. Úsalo cuando el usuario hable de buscar trabajo, ofertas, vacantes o postular — "busca ofertas", "qué salió esta semana", "adapta mi CV a esta vacante", "prepárame la entrevista", "cómo va mi búsqueda", "/buscar-trabajo" — y también si pregunta por el estado de sus postulaciones o quiere retomar la búsqueda tras una pausa.
---

# Buscar trabajo — el método del corpus

La mayoría de los métodos de búsqueda de empleo optimizan una postulación a la vez, a ciegas, porque asumen que las vacantes se consiguen a mano. Este parte de otra situación: **tienes un lote de decenas de vacantes reales**. Eso cambia el problema de sitio — el vocabulario del mercado se mide en vez de adivinarse, y el recurso escaso deja de ser la vacante y pasa a ser el tiempo del candidato.

No es documentación para leer: es la sesión de trabajo. El objetivo de cada turno es que el usuario termine con algo concreto en la mano — ocho vacantes, un CV ensamblado, una entrevista preparada — no con una explicación de cómo funciona el método.

## Archivos

- **`references/metodo.md`** — las siete operaciones sobre los cuatro archivos vivos. Es el núcleo. Ábrelo en cuanto haya un lote de vacantes.
- **`references/pipeline.md`** — cómo conectar una fuente de vacantes que produzca el CSV: automatizada, semiautomática o a mano. Ábrelo solo si el usuario aún no tiene lote.
- **`~/.claude/buscar-trabajo/`** — el estado del usuario. Vive fuera del skill a propósito, para que no se pierda al actualizarlo ni se suba a ningún repositorio.

## Paso 0 — Configuración

Lee `~/.claude/buscar-trabajo/config.md` en silencio antes de responder nada. Si no existe, es la primera vez: créalo con `plantillas/config.md` y pregunta **solo** lo que no puedas deducir de la conversación:

- **Mercado y ubicación** — país, ciudad, si acepta remoto. Determina las fuentes salariales, el idioma del CV y los bloqueadores legales que hay que comprobar.
- **Familia de puestos** — a qué se postula, con qué nivel.
- **Fuente de vacantes** — dónde está el CSV del lote, o si todavía no hay ninguno.

Una pregunta cada vez, nunca un cuestionario. Con la configuración puesta, no vuelvas a preguntarlo.

**El idioma importa dos veces y no significa lo mismo.** Conversa con el usuario en el suyo. Pero el CV, las palabras clave y todo lo que lea un ATS van **en el idioma de la vacante**, literalmente como aparecen ahí. Nunca traduzcas una palabra clave.

## Paso 1 — El lote

Si hay fuente configurada, tráete el lote y guarda el CSV con fecha. Si no la hay, abre `references/pipeline.md` y móntala; si el usuario no quiere montar nada, el método funciona igual con un CSV pegado a mano o con una carpeta de enlaces.

El lote mínimo útil son unas 30 vacantes. Por debajo de eso, la operación 1 no tiene de dónde destilar y conviene ampliar el rango de fechas o las búsquedas antes de seguir.

## Paso 2 — El método

Abre `references/metodo.md` y ejecuta sus operaciones en orden:

1. **Destilar el mercado** — cuenta lo que se repite en el lote y baja 5-8 descripciones completas. Sale `mercado.md`: las palabras que tienen permiso de entrar en el CV.
2. **Triaje** — del lote a ocho, en tres pasadas. Incluye despriorizar las familias de vacantes cuyo requisito central no tenga evidencia.
3. **Libro de evidencias** — la primera vez, entrevista al usuario para llenar `evidencias.md`. Cada cifra con su denominador.
4. **Dossier de brechas** — sus debilidades estructurales, cada una con su respuesta preparada.
5. **Ensamblar** — por vacante, solo para las ocho.
6. **Entrevista y cierre económico.**
7. **Autopsia** de cada rechazo, que es lo que hace que la semana veinte no sea igual que la primera.

## Las tres reglas

- **Si no está en el libro de evidencias, no entra en el documento.** Cuando falte un dato, pregúntaselo y deja el documento bloqueado hasta que responda. Nunca des por buena una métrica que no te hayan confirmado, por plausible que parezca.
- **El documento va en el idioma de la vacante.** La conversación, en el del usuario.
- **Pausa antes de enviar cualquier cosa.** Postular es irreversible y va con el nombre del usuario. Muéstrale siempre la versión final antes de que salga, aunque te haya dado permiso general.

## Paso 3 — Cierre

Antes de terminar, actualiza `historial.md`: qué lote se corrió, cuántas vacantes salieron, cuáles marcó y en qué estado quedó cada postulación abierta. Dos líneas bastan. Ese registro es lo que evita repetirle las mismas vacantes la semana siguiente y lo que alimenta la autopsia.
