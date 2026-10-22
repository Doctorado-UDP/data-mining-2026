# Minería de Datos 2026 · Doctorado UDP

Repositorio Data Mining Doctorado UDP 2026: materiales del curso Minería de Datos (DOA0006_CA01, Doctorado en Ciencias de la Administración, Universidad Diego Portales).

## Contenido del repositorio

- `code/`: notebooks de demostración de cada sesión.
- `refs/`: programa del curso.
- `prediction_challenge/`: desafío de predicción (desde el jueves 5 de noviembre).
- `requirements.txt`: versiones probadas de las librerías, para todo el repositorio.

## Notebooks

Todos los notebooks funcionan igual **en su computador** y en **Google Colab**.

| Notebook | Sesión | Tema | Colab |
|---|---|---|---|
| Clustering (próximamente) | Sesión de autoestudio | Modelamiento descriptivo: k-means, clustering jerárquico, DBSCAN, silueta y PCA | Próximamente |
| [`code/1_modelos_clasificacion.ipynb`](code/1_modelos_clasificacion.ipynb) | Sesión 8 · jueves 15 de octubre | Modelos de clasificación: regresión logística, Naive Bayes, k-NN, SVM, MLP, random forest y XGBoost | [![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Doctorado-UDP/data-mining-2026/blob/main/code/1_modelos_clasificacion.ipynb) |
| Regresión (próximamente) | Sesión de autoestudio | Modelos de regresión: regresión lineal, ridge y lasso, k-NN, SVR, MLP, random forest y XGBoost | Próximamente |

Los notebooks de las sesiones siguientes se agregarán aquí antes de cada clase.

## En su computador

1. Python 3.11 o superior.
2. Clonar o descargar este repositorio y crear un entorno virtual en su carpeta principal (donde está `requirements.txt`):
   ```
   python -m venv .venv
   .venv\Scripts\activate        (Windows)
   source .venv/bin/activate     (macOS/Linux)
   ```
3. Instalar las versiones probadas del curso: `pip install -r requirements.txt`.
4. Abrir los notebooks de la carpeta `code/` en Jupyter (`jupyter notebook`) o en VS Code.

## En Google Colab (no requiere instalar nada)

1. Use el botón **"Abrir en Colab"** de la tabla o del inicio de cada notebook.
2. La primera celda detecta Colab, instala **solo lo que falte** y muestra las versiones. No hace falta reiniciar la sesión.
3. Los datos se descargan automáticamente de fuentes públicas.
4. Para no perder sus cambios: **Archivo → Guardar una copia en Drive**.

Colab usa sus propias versiones de las librerías (en octubre de 2026: Python 3.13, scikit-learn 1.6.1, pandas 2.2.3). Algunos resultados pueden diferir en milésimas de los que muestran los notebooks; las conclusiones no cambian.

## Sesión de autoestudio: clustering (próximamente)

El notebook y la presentación se publicarán más adelante; las lecturas ya están disponibles:

- Tan, P.-N., Steinbach, M., Karpatne, A. y Kumar, V. (2019). *Introduction to Data Mining* (2.ª ed.), cap. 7, "Cluster Analysis: Basic Concepts and Algorithms". Capítulo gratuito de los autores: https://www-users.cse.umn.edu/~kumar001/dmbook/ch7_clustering.pdf
- James, G., Witten, D., Hastie, T., Tibshirani, R. y Taylor, J. (2023). *An Introduction to Statistical Learning with Applications in Python*, cap. 12. Libro gratuito: https://www.statlearning.com

## Sesión de autoestudio: modelos de regresión (próximamente)

Es la contraparte de la Sesión 8 para una variable objetivo continua. El notebook y la presentación se publicarán más adelante; la lectura ya está disponible:

- James, G., Witten, D., Hastie, T., Tibshirani, R. y Taylor, J. (2023). *An Introduction to Statistical Learning with Applications in Python*, cap. 3 (regresión lineal), sección 6.2 (ridge y lasso) y sección 8.2 (bagging, random forest y boosting). Libro gratuito: https://www.statlearning.com

## Dudas

Escriban al profesor (bastian.gonzalez.b@mail.udp.cl) o a la ayudante, Camila Luardo (camila.luardo@mail.udp.cl). Horario de atención: https://savvycal.com/udp/meetings.
