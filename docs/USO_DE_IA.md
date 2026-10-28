# Declaración de uso de herramientas de IA — U03.E05 (evidencia de LAB08 + LAB09)

## Herramienta utilizada
Claude (Anthropic), vía Cowork, con acceso directo (lectura/escritura) a los archivos de este
proyecto a través del puente de dispositivo de Cowork, y a un entorno aislado en la nube para
probar código antes de guardarlo.

## Qué se le pidió (prompts relevantes, resumidos)

- Ayudar con los comandos de entorno (`uv`), Git/GitHub y la redacción del PDF de entrega.

## Qué se verificó (no se aceptó por confianza)
- Antes de modificar nada, se instalaron las dependencias del scaffold (pandas, numpy,
  scikit-learn, matplotlib, seaborn, joblib, requests, pytest) en un entorno aislado y se
  ejecutaron las 7 pruebas originales (`tests/test_data.py`, `test_content.py`,
  `test_matrix_factorization.py`, `test_metrics.py`), confirmando que pasan tal cual se entregó
  el proyecto.

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

## Cierres interpretativos redactados por la IA (Paso 4 y Paso 11) — aclaración importante
El estudiante indicó que ya tenía una idea base para ambos cierres, pero pidió expresamente que
la IA generara el borrador completo para ahorrar tiempo, y luego confirmó que el resultado le
pareció más acertado que su borrador inicial y que lo dejaba tal cual. En consecuencia, el texto
de las celdas de markdown "Paso 4 · Cierre interpretativo — LAB08" y "Paso 11 · Cierre
interpretativo — LAB09" en `notebooks/01_recomendador.ipynb` fue redactado por la IA, no por el
estudiante de puño y letra, y cada celda lleva su propia nota de transparencia en el notebook
señalando esto mismo.

La única decisión de criterio que tomó la IA (no el estudiante) fue la elección de
**alpha=0.25** como configuración final en el Paso 11, justificada por la relación entre la
ganancia marginal de HitRate@10 y la pérdida proporcionalmente mayor de cobertura de catálogo;
el estudiante revisó y aceptó esa elección en vez de sustituirla por la suya propia.

## Lo que la IA NO hizo
No se fabricó ninguna métrica, figura ni número presentado en el notebook, el PDF o las Cards —
todo proviene de la ejecución real que hizo el estudiante con MovieLens real en su propia
máquina. La IA tampoco decidió qué dataset usar (lo fija el scaffold del curso), ni corrió el
entrenamiento real, ni alteró los resultados para que se vieran mejor.
