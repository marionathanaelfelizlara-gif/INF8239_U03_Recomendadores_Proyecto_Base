# Declaración de uso de herramientas de IA — U03.E05 (evidencia de LAB08 + LAB09)

## Herramienta utilizada
Claude (Anthropic), vía Cowork, con acceso directo (lectura/escritura) a los archivos de este
proyecto a través del puente de dispositivo de Cowork, y a un entorno aislado en la nube para
probar código antes de guardarlo.

## Qué se le pidió (prompts relevantes, resumidos)
- Evaluar si el manual de LAB08/LAB09 compartido correspondía al mandato de U03.E05 antes de
  proceder.
- Revisar el scaffold `INF8239_U03_Recomendadores_Proyecto_Base` (provisto por el curso, sin
  modificar su lógica salvo la corrección descrita abajo) y detectar inconsistencias.
- Construir un notebook ejecutable adicional que reutilice ese código para consolidar LAB08
  (popularidad y contenido) y LAB09 (factorización, ranking, híbrido) en un solo producto
  reproducible.
- Ayudar con los comandos de entorno (`uv`), Git/GitHub y la redacción del PDF de entrega.

## Qué se verificó (no se aceptó por confianza)
- Antes de modificar nada, se instalaron las dependencias del scaffold (pandas, numpy,
  scikit-learn, matplotlib, seaborn, joblib, requests, pytest) en un entorno aislado y se
  ejecutaron las 7 pruebas originales (`tests/test_data.py`, `test_content.py`,
  `test_matrix_factorization.py`, `test_metrics.py`), confirmando que pasan tal cual se entregó
  el proyecto.
- Se generó un dataset sintético con la misma forma que MovieLens (60 películas, 40 usuarios,
  ~980 valoraciones) para ejecutar de punta a punta `scripts/audit_data.py`,
  `scripts/lab08_content.py` y `scripts/lab09_hybrid.py` (con `alpha=0.25` y `alpha=0.75`) antes
  de tocar nada. GroupLens no es accesible desde el entorno aislado de la IA, así que la
  descarga real del dataset y toda la ejecución con MovieLens real la hizo el estudiante en su
  propia máquina — los números, figuras y el checkpoint finales del PDF, el notebook y las Cards
  son resultado de esa ejecución real, no de la IA.

## Correcciones realizadas
- Se encontró un error real en `scripts/lab09_hybrid.py` (del scaffold, no introducido por la
  IA): al construir el perfil de contenido de cada usuario, `genre_matrix[...].sum(axis=0)`
  sobre una matriz dispersa de TF-IDF devuelve un objeto `numpy.matrix`, y la versión de
  scikit-learn fijada en `pyproject.toml` (1.5–2, resuelve a 1.9.1) ya no acepta ese tipo en
  `cosine_similarity` — produce un `TypeError` real y reproducible. Se corrigió envolviendo esa
  línea con `np.asarray(...)`, y se validó contra datos sintéticos (`alpha=0.25` y `alpha=0.75`)
  que el script corre sin errores y que las 7 pruebas originales siguen pasando después del
  cambio.
- Se encontró un segundo error real en el `.gitignore` original del scaffold: excluía toda la
  carpeta `reports/*` (solo conservaba `.gitkeep`), lo cual contradice tanto el mandato
  ("Incluya ... reports ...") como las propias instrucciones de Git del manual de LAB08/LAB09
  (`git add src scripts tests reports docs ...`). El primer commit del proyecto se hizo antes de
  notar esto y por lo tanto no incluyó los reportes reales (popularidad, recomendaciones,
  comparación híbrida, Pareto). Se corrigió el `.gitignore` (quitando la exclusión de
  `reports/*`, manteniendo excluidos solo `data/raw/*` y `models/*`) y se hizo un commit nuevo
  con los reportes ya versionados.

## Lo que la IA NO hizo
No se fabricó ninguna métrica, figura ni número presentado en el notebook, el PDF o las Cards —
todo proviene de la ejecución real que hizo el estudiante con MovieLens real en su propia
máquina. La IA tampoco decidió qué dataset usar (lo fija el scaffold del curso), ni corrió el
entrenamiento real, ni alteró los resultados para que se vieran mejor. El cierre interpretativo
del notebook (ambos bloques, LAB08 y LAB09) requiere explícitamente el criterio del estudiante y
se deja para que él lo complete después de ver sus propios resultados reales — salvo que, como en
el proyecto anterior (CV), el estudiante pida expresamente un borrador; en ese caso se seguirá el
mismo protocolo de transparencia usado allí.
