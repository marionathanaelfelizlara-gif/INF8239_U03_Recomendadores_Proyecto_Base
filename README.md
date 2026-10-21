# INF-8239 · Unidad 03 · Sistemas recomendadores

Autor académico: Mario Nathanael Feliz Lara

Proyecto inicial de LAB08 y LAB09 (evidencia del Ejercicio 05). El dataset de muestra prueba el
código; la evidencia final usa MovieLens.

## Inicio
```bash
uv python install 3.12
uv sync
uv run pytest -q
uv run python scripts/download_data.py
uv run python scripts/audit_data.py
```

No requiere activar manualmente ningún entorno virtual: `uv run` usa el entorno del proyecto
automáticamente en cada comando.

## Laboratorios
```bash
uv run python scripts/lab08_content.py
uv run python scripts/lab09_hybrid.py --factors 20 --epochs 12 --alpha 0.75
uv run streamlit run app/streamlit_app.py
```

## Notebook ejecutable

`notebooks/01_recomendador.ipynb` integra, de forma interactiva, la descarga/auditoría de
MovieLens, el baseline de popularidad y el recomendador por contenido (LAB08), y la
factorización matricial con corte temporal, el sistema híbrido y su evaluación en tres perfiles
de usuario (LAB09). Reutiliza el código de `src/inf8239_u03/`, no duplica lógica. Córralo con:

```bash
uv add --dev jupyter
uv run jupyter nbconvert --to notebook --execute --inplace notebooks/01_recomendador.ipynb
```

Después de ejecutarlo, complete a mano el cierre interpretativo del notebook (requiere su
criterio, no se completa solo) y luego `docs/DATASET_CARD.md` y `docs/SYSTEM_CARD.md` con los
resultados reales.

## Requirements

Después de `uv sync`, genere el `requirements.txt` exigido por el mandato:

```bash
uv export --format requirements.txt --no-dev --output-file requirements.txt
```

## Declaración de uso de IA

`docs/USO_DE_IA.md` — herramienta usada, qué se le pidió, qué se verificó con pruebas y
ejecución real (no por confianza), y qué correcciones se le hicieron (incluye un bug real
corregido en `scripts/lab09_hybrid.py`).

## Interpretación
No presente una recomendación como verdad. Documente datos, candidatos, puntuación, métricas,
fallback y límites.
