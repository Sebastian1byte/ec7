# EC7 · Laboratorio 09

Notebook para las **partes 2 y 3** del laboratorio de Bag of Words e índice invertido.

- **Parte 2:** minúsculas, tokenización, stopwords de PostgreSQL, stemming en español, conteo de frecuencias y actualización de `noticias.bag_of_words`.
- **Parte 3:** búsqueda booleana lineal y con índice invertido, operadores `AND`, `OR`, `AND-NOT`, paréntesis y seis comparaciones entre ambos métodos.

## Ejecución

1. Usa la base PostgreSQL local `ec7` que ya creaste. Debe contener los datos cargados en `noticias(id, contenido, bag_of_words JSONB)` y `stopwords(word)`.
2. Instala las dependencias en el entorno del kernel:

   ```bash
   python -m pip install -r requirements.txt
   ```

3. Copia `.env.example` a `.env` y configura tu usuario y contraseña de PostgreSQL. También puedes usar variables de entorno o `.pgpass`. `.env` está excluido de Git.
4. Abre `ec7.ipynb` en Jupyter o VS Code, selecciona ese entorno de Python y ejecuta las celdas en orden.

El notebook actualiza solamente `noticias.bag_of_words`. No crea la base ni las tablas, y no vuelve a importar el CSV. Ejecutarlo otra vez recalcula el BoW sin duplicar las noticias. Si las tablas están vacías, informa que falta completar la carga previa.

## Decisiones de implementación

Se consulta `noticias`: el nombre `noticias_bow` aparece en un comentario de la guía, pero el esquema de la guía define `bag_of_words` en `noticias`. Se recupera la colección completa y se muestran las primeras diez filas.

Los términos de las consultas pasan por el mismo preprocesamiento que las noticias. `AND` y `AND-NOT` tienen igual prioridad, superior a `OR`; los paréntesis permiten cambiar el agrupamiento. Las consultas inválidas o con términos eliminados como stopwords producen un mensaje de error.

El índice guarda postings `(doc_id, tf)` ordenados por ID, IDF `ln(N / df)` y normas TF-IDF de documentos, tal como pide la construcción del índice en la parte 3. Los métodos de búsqueda por coseno, los experimentos de tiempo y el análisis de las partes 4 a 6 quedan fuera del alcance solicitado.

La conexión usa [psycopg2](https://www.psycopg.org/docs/), y el stemming usa [Snowball en español de NLTK](https://www.nltk.org/api/nltk.stem.snowball.html). No hace falta descargar modelos o corpus de NLTK.

## Validación

El notebook se ejecutó completo contra la base local `ec7`: 1.217 noticias y 608 stopwords. Los 1.217 BoW quedaron guardados como objetos JSONB y el índice contiene 13.339 raíces distintas. Las seis consultas principales devolvieron los mismos IDs con ambos métodos; sus salidas están guardadas en el notebook.

Además, se comprobaron consultas inválidas, prioridad de operadores, listas y documentos vacíos, y palabras ausentes del vocabulario. Una validación independiente con el CSV proporcionado verificó otras 100 consultas y 600 operaciones sobre listas de postings.
