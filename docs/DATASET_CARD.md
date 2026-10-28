# Dataset Card · MovieLens

## Fuente, fecha, versión y hash

- Fuente: GroupLens Research, University of Minnesota.
- Versión: MovieLens Latest Small (`ml-latest-small.zip`).
- URL: https://files.grouplens.org/datasets/movielens/ml-latest-small.zip
- Descargado a: `data/raw/` (no se redistribuye en este repositorio).
- SHA-256 de los bytes descargados: `696d65a3dfceac7c45750ad32df2c259311949efec81f0f144fdfb91ebc9e436`
- GroupLens advierte que este es un conjunto de desarrollo que puede cambiar; no debe tratarse
  como una versión archivada para resultados científicos compartidos.

## Licencia y cita
Harper, F. M., & Konstan, J. A. (2015). The MovieLens Datasets: History and Context. *ACM
Transactions on Interactive Intelligent Systems*, 5(4). DOI: 10.1145/2827872.

Uso académico/investigación bajo las condiciones del README oficial de GroupLens. No se
redistribuye el dataset en este repositorio — solo el script de descarga.

## Archivos y diccionario

| Archivo | Columnas | Descripción |
|---|---|---|
| `ratings.csv` | `userId`, `movieId`, `rating`, `timestamp` | Valoración explícita de 0.5 a 5.0 estrellas, con marca temporal Unix. |
| `movies.csv` | `movieId`, `title`, `genres` | Título (con año) y lista de géneros separados por `\|`. |

Estadísticas reales de esta descarga (`scripts/audit_data.py` / notebook, Paso 1):

- Ratings: 100,836 — Películas: 9,742 — Usuarios: 610 — Ítems valorados: 9,724.
- Rango de valoraciones: 0.5–5.0 (media 3.50, mediana 3.5).
- Rango temporal: timestamps Unix 828124615–1537799250 (1996–2018 aprox.).
- Densidad observada: 1.70% de las combinaciones usuario–película posibles (la gran mayoría de
  combinaciones no tiene valoración; eso no significa rechazo, solo ausencia de interacción
  observada).

## Procedimiento de descarga
`scripts/download_data.py` descarga el ZIP, calcula el hash SHA-256 de los bytes recibidos antes
de descomprimir, valida que ninguna ruta dentro del ZIP escape del directorio destino, y
descomprime a `data/raw/`. `scripts/audit_data.py` valida después el contrato de columnas, claves
foráneas (`ratings.movieId` ⊆ `movies.movieId`) y el rango de `rating`.

## Población observada y exclusiones
Según la documentación oficial de GroupLens, los usuarios incluidos en MovieLens Latest Small
habían valorado al menos 20 películas — es decir, el dataset ya excluye usuarios con muy pocas
interacciones. Los identificadores son anónimos y no incluyen demografía (edad, género, ubicación
u otros atributos personales). Esto define el universo observado, no toda la población de
usuarios de películas ni todo el catálogo cinematográfico existente.

## Calidad, sesgos y usos prohibidos
- Sesgo de selección: las personas valoran lo que decidieron consumir, no una muestra aleatoria
  del catálogo — la distribución de ratings puede reflejar autoselección, no calidad objetiva.
- Densidad muy baja (1.70%): la mayoría de pares usuario–película no tiene valoración.
- No incluye demografía, por lo que no pueden afirmarse resultados por grupos poblacionales.
- Uso prohibido: no redistribuir el dataset crudo; no usarlo para inferir atributos personales
  de usuarios reales a partir de su historial de valoraciones; no presentar una recomendación
  generada a partir de este dataset como una verdad universal sobre el gusto de una persona.
