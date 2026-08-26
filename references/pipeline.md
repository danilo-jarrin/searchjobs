# Conectar una fuente de vacantes

El método necesita **un lote**: un CSV con una fila por vacante. Nada más. Cómo se llene ese CSV es indiferente al método, y esa independencia es deliberada — las fuentes cambian, se rompen y se bloquean; el método no.

## El contrato

Columnas mínimas:

| Columna | Contenido |
|---|---|
| `title` | Título tal como lo publica la empresa, sin traducir |
| `company` | Empresa contratante |
| `location` | Ubicación tal cual aparece |
| `date` | Fecha de publicación |
| `url` | Enlace a la oferta completa |

`id` y una columna con la búsqueda que la encontró ayudan a deduplicar, pero no son obligatorias.

El lote mínimo útil son unas **30 vacantes**. Por debajo, la operación 1 no tiene de dónde destilar: amplía el rango de fechas o añade búsquedas antes de seguir.

## Tres formas de llenarlo

**A mano.** Perfectamente válido y es como conviene empezar. Media hora copiando treinta vacantes de un portal a una hoja de cálculo da un lote con el que el método funciona entero. Si el usuario duda de si esto le sirve, esta es la vía para comprobarlo sin instalar nada.

**Semiautomática.** Exportar desde el portal o el ATS donde ya busque, y normalizar las columnas al contrato de arriba.

**Automatizada.** Un agregador que consulte varias búsquedas y deduplique. Hay proyectos de código abierto que lo hacen; también APIs oficiales de portales de empleo, que en la mayoría de países son gratuitas para uso personal y son la vía sin riesgo de bloqueo.

## Antes de automatizar, dos avisos

**Los términos de servicio.** Casi todos los portales de empleo prohíben en sus condiciones el acceso automatizado y el envío automático de candidaturas. Las APIs oficiales existen justamente para eso y no tienen ese problema. Este skill no incluye ningún raspador ni recomienda saltarse las condiciones de ningún servicio: quien monte una fuente automatizada decide bajo su responsabilidad qué usa.

**El envío automático.** Postular es irreversible y va con el nombre del usuario. El método asume revisión humana antes de cada envío, siempre, aunque el usuario haya dado permiso general. No hay ninguna ganancia de velocidad que compense una candidatura mala enviada en su nombre.

## Ritmo

Un lote por semana es suficiente para casi cualquier búsqueda. Más frecuencia devuelve las mismas vacantes y gasta tiempo; menos, y las buenas se cierran antes de verlas.

Guarda **cada CSV con su fecha**. La comparación entre lotes es lo que alimenta la frase de patrón de la operación 2 — qué apareció, qué desapareció, hacia dónde se mueve el mercado — y eso no se puede reconstruir después.
