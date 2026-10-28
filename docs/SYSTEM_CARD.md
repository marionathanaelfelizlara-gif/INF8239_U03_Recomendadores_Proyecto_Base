# System Card · Recomendador

## Usuarios y propósito
Sistema académico de recomendación de películas sobre MovieLens Latest Small (610 usuarios
reales, identificadores anónimos sin demografía). Propósito didáctico: comparar baselines,
señales de contenido, factorización colaborativa y un esquema híbrido, bajo una óptica de costo
vs. utilidad (Green AI). No es un sistema de producción ni debe usarse para perfilar personas
reales.

## Catálogo y candidatos
9,742 películas (9,724 con al menos una valoración observada). Los candidatos para un usuario se
limitan a películas que no ha valorado en el conjunto de entrenamiento (`seen` se excluye
explícitamente en `MatrixFactorization.top_n`).

## Señales utilizadas
- **Popularidad suavizada:** media de valoraciones por película, ponderada contra la media
  global según la cantidad de valoraciones (evita que una sola valoración alta domine el
  ranking).
- **Contenido:** TF-IDF sobre géneros + similitud coseno. Ejemplo real: al pedir similares a
  *Toy Story (1995)*, las 10 recomendaciones obtuvieron `content_score = 1.0` exacto — comparten
  el mismo combo de géneros (`Adventure|Animation|Children|Comedy|Fantasy`), un caso real de
  empate y de sobre-especialización (la señal solo "ve" géneros, no otras diferencias entre
  películas).
- **Colaborativa:** factorización matricial por SGD (20 factores, 12 épocas, corte temporal
  leave-one-out por usuario).
- **Híbrida:** combinación lineal `alpha * colaborativa_normalizada + (1-alpha) * contenido`,
  evaluada con `alpha=0.25` y `alpha=0.75`.

## Modelos y fallback
| Modelo | RMSE | HitRate@10 | Cobertura de catálogo |
|---|---|---|---|
| Factorización (ratings) | 1.026 | — | — |
| Híbrido alpha=0.25 | — | 0.032 | 0.106 |
| Híbrido alpha=0.75 | — | 0.037 | 0.078 |

Más peso colaborativo (alpha alto) mejora ligeramente el acierto pero reduce la cobertura del
catálogo — un costo real, no una mejora gratuita.

**Fallback de usuarios nuevos (cold start):** cuando el `userId` no existe en el entrenamiento,
no hay señal colaborativa; el sistema cae a la lista de popularidad suavizada (no personalizada),
documentado en `reports/cold_start_fallback.csv`.

## Métricas offline
Sobre 587/610 usuarios evaluables (23 quedaron fuera porque su única valoración retenida era de
una película sin otra valoración en el entrenamiento):

- RMSE (factorización, alpha irrelevante para esta métrica): 1.026, estable entre 3 semillas
  (1.0259–1.0264).
- HitRate@10: varía 0.029–0.039 entre semillas — la aleatoriedad de inicialización importa y debe
  reportarse como rango, no como un solo número.
- Entrenamiento: ~21–23 s por corrida (factors=20, epochs=12), CPU.

## Cold start, cobertura y diversidad
- Usuario con historial amplio (2,697 valoraciones): recomendaciones colaborativas coherentes con
  clásicos de alta calificación.
- Usuario con historial pequeño (19 valoraciones): recomendaciones razonables pero con menos
  señal disponible.
- Usuario nuevo (fuera del entrenamiento): fallback de popularidad funciona (devuelve una lista),
  pero el sistema declara explícitamente que no es personalizada.
- Cobertura de catálogo baja en ambos alphas (7.8%–10.6%): el sistema concentra recomendaciones en
  un subconjunto pequeño del catálogo — riesgo de burbuja de filtro si se usara en producción.

## Riesgos y monitoreo
- Popularidad dominante y burbuja de filtro: cobertura baja implica que muchos usuarios reciben
  recomendaciones de un conjunto reducido de películas.
- Sesgo de selección: solo se observan valoraciones de películas que el usuario decidió consumir.
- Ausencia de demografía: no pueden afirmarse resultados por grupos poblacionales.
- Retroalimentación: en un sistema en producción, las recomendaciones futuras podrían modificar
  las interacciones que luego entrenan el modelo (no evaluado aquí, es un dataset estático).
- Monitoreo recomendado antes de cualquier uso real: revisar cobertura y HitRate por segmento de
  usuario (historial amplio vs. pequeño vs. nuevo), no solo el promedio global.
