# buscar-trabajo

Un skill de [Claude Code](https://claude.com/claude-code) para buscar empleo con método, no a golpe de plantilla.

La mayoría de las guías optimizan **una postulación a la vez, a ciegas**: coges una vacante, ajustas el CV, envías, repites. Funcionan cuando tienes cinco vacantes y las conseguiste a mano.

Este skill parte de otra situación: **un lote de decenas de vacantes reales**. Y eso cambia el problema de sitio.

- El vocabulario del mercado **se mide, no se adivina**. Un anuncio te dice qué quiere una empresa; setenta y cinco te dicen qué paga el mercado.
- El recurso escaso deja de ser la vacante y pasa a ser **tu tiempo**, así que la primera operación no es escribir: es descartar.
- **Ningún documento inventa nada.** Todo lo que entra en un CV sale de un libro de evidencias verificado contigo. Si no está en el libro, no se escribe.

## Cómo funciona

Cuatro archivos que se acumulan — no documentos que se tiran — y siete operaciones encima.

| Archivo | Qué contiene |
|---|---|
| `mercado.md` | El vocabulario destilado del lote: términos por frecuencia, plataformas nombradas, años exigidos, bandas salariales |
| `evidencias.md` | Tus hechos verificados. Cada cifra con su denominador |
| `brechas.md` | Tus debilidades estructurales, cada una con su respuesta preparada |
| `historial.md` | Estado de cada postulación y la autopsia de cada rechazo |

1. **Destilar el mercado** — cuenta lo que se repite en el lote y baja unas cuantas descripciones completas
2. **Triaje** — del lote a ocho, en tres pasadas
3. **Libro de evidencias** — una entrevista contigo, cifra a cifra
4. **Dossier de brechas** — tus tres o cuatro debilidades, con respuesta
5. **Ensamblar** — el CV no se adapta: se ensambla con piezas que ya existen
6. **Entrevista y cierre económico**
7. **Autopsia** — cada rechazo acusa a un archivo distinto, y ese archivo se corrige

La séptima es la que convierte esto en un sistema. Sin ella, la semana veinte es idéntica a la primera.

## Instalación

```bash
git clone https://github.com/danilo-jarrin/searchjobs.git ~/.claude/skills/buscar-trabajo
```

No hay dependencias. Abre Claude Code y di *"vamos a buscar trabajo"*, o invócalo con `/buscar-trabajo`.

La primera vez te preguntará el mercado, la familia de puestos y de dónde salen las vacantes. Con eso crea `~/.claude/buscar-trabajo/` y ahí vive tu estado — **fuera del skill**, para que no se pierda al actualizarlo ni acabe en ningún repositorio por accidente.

## Qué necesitas

**Un lote de vacantes en CSV**, con título, empresa, ubicación, fecha y enlace. Unas treinta como mínimo.

Cómo lo llenes es indiferente al método, y esa independencia es deliberada: las fuentes cambian, se rompen y se bloquean; el método no. Media hora copiando treinta vacantes de un portal a una hoja de cálculo basta para empezar. `references/pipeline.md` explica el contrato y las tres formas de llenarlo.

**Tu CV**, en un formato legible. Y paciencia para la operación 3: te va a preguntar los denominadores que faltan, y hasta que los des, los documentos quedan bloqueados. Es a propósito.

## Idiomas

El skill conversa contigo en tu idioma y escribe los documentos **en el de la vacante**. Las palabras clave nunca se traducen: el ATS busca cadenas literales y no infiere sinónimos.

La documentación está en español. El método es agnóstico de mercado — la configuración inicial fija país, fuentes salariales y bloqueadores legales.

## Lo que este skill no hace

- **No raspa portales de empleo ni envía candidaturas solo.** Casi todos los portales lo prohíben en sus condiciones, y postular es irreversible y va con tu nombre. El método asume revisión humana antes de cada envío, siempre.
- **No inventa métricas.** Si no le das la cifra, te la pide y espera.
- **No promete entrevistas.** Ordena tu búsqueda y evita que gastes semanas en vacantes que no ibas a convertir.

## Licencia

MIT. Ver [LICENSE](LICENSE).
