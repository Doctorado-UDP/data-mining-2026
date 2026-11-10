# CASEN 2024 Income Prediction Challenge

## Data Mining - Doctorado UDP 2026

### Bastián González-Bustamante

![Project Status: Active – The project has reached a stable, usable state and is being actively developed.](https://raw.githubusercontent.com/Doctorado-UDP/data-mining-2026/refs/heads/main/prediction_challenge/badges/active.svg) ![License](https://raw.githubusercontent.com/Doctorado-UDP/data-mining-2026/refs/heads/main/prediction_challenge/badges/mit.svg)

> [!NOTE]
> La licencia MIT aplica al código de esta carpeta. Los datos derivan de la Encuesta Casen 2024 y se distribuyen bajo **CC BY 4.0** (ver [Atribución y licencia](#atribución-y-licencia)).

## Contexto

¿Se puede anticipar el nivel de ingreso de un hogar a partir de quién lo compone, cómo trabaja su jefatura y en qué vivienda vive? Esa pregunta está detrás de muchas decisiones públicas, y este challenge la convierte en un problema de clasificación.

* **Administración y políticas públicas.** El ingreso autónomo per cápita es la base de la medición de la pobreza por ingresos en Chile. Los programas sociales deben ordenar a los hogares según su situación socioeconómica para focalizar beneficios (como lo hace el Registro Social de Hogares), muchas veces sin observar directamente el ingreso o con ingresos declarados con error. Predecir el tramo de ingreso a partir de características observables es un ejercicio de focalización: ¿qué variables discriminan mejor?, ¿qué errores (excluir a un hogar del quintil más bajo o incluir a uno que no lo es) son más costosos?
* **Ingeniería.** Buena parte de los predictores describe infraestructura: materialidad y hacinamiento de la vivienda, origen del agua y sistema de saneamiento, acceso a la red eléctrica, combustibles para cocinar y calefaccionar (la leña sigue siendo central en el centro-sur del país) y conectividad (internet fija o móvil). El challenge permite estudiar cuánto revelan la calidad de la vivienda, la energía, el agua y la conectividad sobre las brechas de ingreso, un insumo para priorizar inversión en infraestructura.

## Instrucciones

En la carpeta [prediction_challenge/data](https://github.com/Doctorado-UDP/data-mining-2026/tree/main/prediction_challenge/data) se encuentran los conjuntos de datos, construidos a partir de la **Encuesta de Caracterización Socioeconómica Nacional (Casen) 2024** del Ministerio de Desarrollo Social y Familia. La unidad de análisis es el **hogar**: hay una fila por hogar, con características del hogar, de su jefatura y de la vivienda (*n* = 78.654 hogares, a partir de 218.367 personas encuestadas). Se incluyen **50 predictores** (41 categóricos y 9 numéricos) más una columna `id`.

El objetivo es predecir la variable **`ypchautcor`** (ingreso autónomo per cápita del hogar corregido, en pesos chilenos por mes), discretizada en **cinco clases** (quintiles): `Low`, `Medium-Low`, `Medium`, `Medium-High` y `High`. El ingreso autónomo incluye los ingresos del trabajo, de la propiedad de activos, las jubilaciones y pensiones contributivas y las transferencias entre privados, pero **excluye las transferencias monetarias del Estado y el alquiler imputado**.

El conjunto de datos se dividió en una proporción de 70/15/15 (semilla 86) para crear **training set** (55.057 hogares), **validation set** (11.798) y **test set** (11.799). Cada conjunto se dividió en dos para contener, de forma separada, los predictores (`X_*`) y la variable dependiente (`y_*`). El **conjunto reservado**, no disponible en GitHub, que se utilizará para calcular las métricas del leaderboard, contiene los valores reales (*ground-truth*) de la variable dependiente del **test set**.

| Archivo | Contenido | Filas | Tamaño |
|---|---|---:|---:|
| [X_train_part1.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part1.csv) | Predictores, training set (parte 1) | 27.529 | 11,3 MB |
| [X_train_part2.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part2.csv) | Predictores, training set (parte 2) | 27.528 | 11,3 MB |
| [y_train.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/y_train.csv) | `id`, `ypchautcor` (training set) | 55.057 | 0,7 MB |
| [X_val.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_val.csv) | Predictores, validation set | 11.798 | 4,8 MB |
| [y_val.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/y_val.csv) | `id`, `ypchautcor` (validation set) | 11.798 | 0,1 MB |
| [X_test.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_test.csv) | Predictores, test set | 11.799 | 4,8 MB |
| [codebook.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/codebook.csv) | Libro de códigos (ver [Codebook](#codebook)) | 52 | — |

> [!CAUTION]
> El conjunto de predictores del training set se dividió en dos archivos; únanlos con `pd.concat` (ver el código de ejemplo).
> La columna `id` numera las filas dentro de cada partición (0, 1, 2, …; en el training set continúa de una parte a la otra, de 0 a 55.056), une cada `X_*` con su `y_*` y es la que deben usar en el envío. **No es un predictor.**
> Se eliminaron todas las variables de ingresos, subsidios, pobreza, deciles y quintiles, gastos monetarios, factores de expansión, identificadores y variables de diseño muestral, y la geografía fina (comuna y provincia). Solo se conservan `region` y `area`.

### Variable objetivo y clases

Los archivos `y_*` contienen el ingreso **continuo** (CLP/mes), no las clases. Las clases se construyen con los **quintiles del training set** (no de la población), en el código de ejemplo y en la evaluación; copien esa celda del código de ejemplo sin cambios. Los puntos de corte del ingreso autónomo per cápita son:

| Clase | Rango de `ypchautcor` (CLP/mes) | % train | % validation |
|---|---|---:|---:|
| `Low` | ≤ 132.852 | 20,0 | 20,6 |
| `Medium-Low` | 132.852 – 250.000 | 21,6 | 21,6 |
| `Medium` | 250.000 – 389.118 | 18,4 | 18,5 |
| `Medium-High` | 389.118 – 655.000 | 20,0 | 19,5 |
| `High` | > 655.000 | 20,0 | 19,8 |

> [!NOTE]
> * El 5,4% de los hogares del training set tiene ingreso autónomo igual a 0 (por ejemplo, hogares que viven solo de transferencias o de la Pensión Garantizada Universal). Estos hogares son reales y quedan en `Low`.
> * El corte de 250.000 coincide con un valor redondo muy frecuente (1,7% de los hogares del training set). Por eso las clases no tienen exactamente el 20% cada una.
> * Los intervalos incluyen el límite superior (`pd.cut` con `right=True`).

### Valores perdidos

Hay valores perdidos **por diseño**: las variables laborales de la jefatura (`ocupacion_jefe`, `rama_jefe`, `categoria_ocup_jefe`, `horas_trabajo_jefe`, `tipo_trabajo_jefe`, `tamano_empresa_jefe`) solo existen para jefaturas ocupadas (~38% de perdidos), `contrato_jefe` solo para asalariados (~56%) y `area_estudio_jefe` solo para jefaturas con educación superior (~69%). Las respuestas "no sabe / no responde" también se registraron como perdidos. En los CSV, todos los perdidos son celdas vacías (`NaN` en pandas), sin códigos especiales. Decidir cómo tratarlos (imputar, crear una categoría, combinarlos con `actividad_jefe`) es parte del desafío. Si usan `actividad_jefe` para recodificarlos, esta cuenta como uno de los cinco predictores.

Las etiquetas de las variables categóricas están en español, **sin tildes** y abreviadas (el [Codebook](#codebook) lista todos los valores). `edad_jefe` está acotada en 90 (90 = 90 años o más) y `n_personas` en 10 (10 = 10 o más personas).

**Quienes participen deberán:**

1. Utilizar [X_train_part1.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part1.csv), [X_train_part2.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part2.csv) e [y_train.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/y_train.csv) para entrenar sus modelos y [X_val.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_val.csv) e [y_val.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/y_val.csv) para validarlos.
2. Obtener las etiquetas predichas utilizando [X_test.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_test.csv) y subirlas a la carpeta [prediction_challenge/submissions](https://github.com/Doctorado-UDP/data-mining-2026/tree/main/prediction_challenge/submissions).
3. Pueden utilizar como script base el [código de ejemplo](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/baseline_model.ipynb) y preparar modelos afinados. Solo necesita pandas, NumPy y scikit-learn (probado con Python 3.12 y 3.13, pandas 2.2 y 3.0, y scikit-learn 1.6 y 1.9; su primera celda exige Python ≥ 3.11, scikit-learn ≥ 1.6, pandas ≥ 2.2 y NumPy ≥ 2.0) y se ejecuta desde la carpeta `prediction_challenge/`, porque las rutas son relativas (`data/…`, `submissions/…`). También funciona en Google Colab (ver [Usar Google Colab](#usar-google-colab)).
4. El siguiente [código](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/submissions_eval.ipynb) será utilizado, por el profesor, para calcular el [**leaderboard**](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/leaderboard.csv).
5. El leaderboard se calculará diariamente, excepto los domingos (aunque quizás algunos sí), hasta que se cierre el challenge.

**Formato de envío (también disponible en el código de ejemplo):**

Es importante completar la metadata en el código para el envío y verificar el formato del archivo con predicciones: **`submission_<Nombre>_<Modelo>_<N>.csv`**, con exactamente dos columnas, **`id,predicted_label`**, una fila por cada `id` de `X_test.csv` y etiquetas `Low`, `Medium-Low`, `Medium`, `Medium-High` o `High`.

* participant = "Baseline" - Nombre de quien participa: **exactamente** uno de los nombres de la lista de participantes registrados (abajo), sin tildes, espacios ni guiones bajos.
* model_name = "RF100" - Nombre del modelo (sin espacios ni guiones bajos): Naive Bayes (NB), Support Vector Machine (SVM), Random Forest (RFX) donde X es el número de árboles, XGBoost, LightGBM, u otro.
* submission_number = "1" - El número de envío, mantener las comillas, por favor.

**Participantes registrados:** `Carolina`, `Karla`, `MariaCamila`, `Marian`, `Matias`, `Tamara`. Solo se evalúan los envíos con uno de estos nombres; un nombre distinto (por ejemplo, con un número agregado o con tilde) se moverá a `submissions_invalid`. Quien prefiera participar con un alias debe pedirlo al profesor antes de su primer envío.

Los archivos con otro nombre, otras columnas, `id` faltantes o repetidos, o etiquetas distintas se moverán a [prediction_challenge/submissions_invalid](https://github.com/Doctorado-UDP/data-mining-2026/tree/main/prediction_challenge/submissions_invalid) y no se evaluarán.

> [!IMPORTANT]
> **Durante el challenge NO se subirán los códigos de cada envío. Sin embargo, la calificación está sujeta a reproducibilidad:**
>
> * Al cierre, cada participante entregará el notebook o script que genera **su mejor envío**. La nota solo se valida si ese código, ejecutado por el profesor, **vuelve a generar el mismo archivo** de predicciones y lee únicamente los datos del challenge y los datos externos permitidos y declarados.
> * Guarden el código de cada envío (por ejemplo, con un notebook por envío, con `random_state` fijo).
> * Se podrá solicitar el código de cualquier envío con un puntaje sospechosamente alto, en particular **si el F1-Score supera 0,525**. **Un F1-Score sobre 0,56**, por encima de lo alcanzable incluso con los 50 predictores, se presumirá una falta a la integridad académica salvo que el código demuestre lo contrario.

**Cuestiones logísticas:**

1. Se puede realizar máximo **un envío por día** (hora de Chile). Los adicionales quedarán en standby en [prediction_challenge/submissions_standby](https://github.com/Doctorado-UDP/data-mining-2026/tree/main/prediction_challenge/submissions_standby) y se procesarán al día siguiente. La fecha de un envío es la del commit que agrega el archivo a `submissions/`.
2. Cada modelo tiene un límite de **cinco predictores**. Las transformaciones de esos predictores están permitidas (índices, razones, recodificaciones, interacciones entre ellos); **cada variable externa cuenta como uno de los cinco**. Declaren la lista de predictores en el código (el código de ejemplo verifica que no sean más de cinco).
3. Pueden crear índices, recodificar y transformar predictores utilizando los datos de entrenamiento y probar distintos hiperparámetros y modelos.
4. Pueden incorporar **datos externos** solo bajo las condiciones de la sección siguiente.
5. El challenge comienza el **martes 10 de noviembre de 2026** y termina el **lunes 30 de noviembre de 2026 a las 23.59 horas** de Santiago de Chile (UTC−3). Los envíos fuera de ese plazo se consideran inválidos.

### Datos externos

> [!CAUTION]
> **Permitido:** datos públicos **agregados** (por ejemplo, estadísticas regionales de empleo, costo de vida, clima o cobertura de servicios) unidos al conjunto mediante claves que **ya existen** en los datos, como `region` o `area`. Cada variable externa cuenta como un predictor y debe declararse en el código con la **URL de la fuente y la fecha de acceso**.
>
> **Prohibido:** usar los microdatos originales de Casen 2024 (o de cualquier otra versión de Casen) o cualquier dato a nivel de hogar o persona que permita emparejar filas del conjunto con registros externos, porque eso recuperaría las etiquetas privadas del test set. También está prohibida cualquier variable derivada del objetivo (ingresos, quintiles, deciles, pobreza). **Hacerlo constituye una falta a la integridad académica.**

### Preguntas frecuentes

* **¿La columna `id` cuenta como predictor?** No. Solo sirve para unir X con y y para identificar las filas en el envío.
* **¿Puedo crear un índice con varias variables?** Sí, pero cada variable de origen cuenta como uno de los cinco predictores.
* **¿Puedo usar ensambles (voting, averaging, stacking, boosting de varios modelos)?** Sí. El límite de **cinco predictores aplica al ensamble completo**: la unión de todas las variables de origen que usa cualquiera de sus componentes, más las variables externas, no puede superar cinco. Cinco modelos con cinco variables distintas cada uno suman 25 predictores y no están permitidos.
* **¿Puedo reentrenar con training + validation para el envío final?** Sí. Una vez elegidos el modelo y sus hiperparámetros con el validation set, pueden reajustar el mismo pipeline con training + validation antes de predecir `X_test`.
  * Los cortes de los quintiles no cambian: se usan los del training set (código de ejemplo), también para las etiquetas del validation set.
  * Los parámetros del preprocesamiento (imputación, codificación, escalamiento) pueden aprenderse de training + validation, **nunca de `X_test`**.
  * El código entregado al cierre debe mostrar ese reajuste.
* **¿Puedo cambiar los cortes de los quintiles?** No. La evaluación usa los cortes del training set que fija el código de ejemplo.
* **¿Puedo trabajar en R u otro lenguaje?** No. Los envíos deben generarse con **Python**, y el código que se entrega al cierre debe ser un notebook o un script de Python.
* **¿Qué pasa si no envío un día?** Nada. Cuenta el **mejor** F1-Score, no el promedio.

### Usar Google Colab

Quienes no quieran instalar Python en su computador pueden trabajar en Google Colab:

1. Abran el [código de ejemplo en Colab](https://colab.research.google.com/github/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/baseline_model.ipynb) (o con el botón "Abrir en Colab" al inicio del notebook) y guarden su propia copia con "Archivo → Guardar una copia en Drive". Sus cambios quedan en esa copia.
2. La primera celda de código detecta Colab e instala solo lo que falte, sin actualizar los paquetes preinstalados (pandas, NumPy y scikit-learn ya vienen instalados), y verifica las versiones.
3. Los datos se leen directamente desde la carpeta `prediction_challenge/data` de GitHub; no hace falta subirlos a Colab.
4. Al final, el notebook escribe el envío en `submissions/` y lo descarga a su computador. Para enviarlo, abran la carpeta [prediction_challenge/submissions](https://github.com/Doctorado-UDP/data-mining-2026/tree/main/prediction_challenge/submissions) en GitHub, elijan "Add file → Upload files", arrastren el archivo y confirmen con "Commit changes".
5. El código que entregan al cierre puede ser ese mismo notebook de Colab (descárguenlo con "Archivo → Descargar → Descargar .ipynb"): es un notebook de Python. Rige la misma condición de reproducibilidad: al ejecutarlo debe volver a generar el mismo archivo de envío.

## Leaderboard actualizado

El leaderboard se publicará a partir del lanzamiento del challenge.

Participant | Date | Submission | Model | Accuracy | Precision | Recall | F1-Score
--- | :-: | :-: | :-: | :-: | :-: | :-: | :-:
Carolina :trophy: | 2026-11-10 | 1 | XGBoost | 0.513 | 0.506 | 0.512 | 0.507
Matias | 2026-11-10 | 1 | RF500 | 0.509 | 0.502 | 0.508 | 0.504
Karla | 2026-11-10 | 1 | RF100 | 0.371 | 0.351 | 0.370 | 0.354
Baseline | 2026-11-10 | 1 | RF100 | 0.257 | 0.196 | 0.253 | 0.210
Tamara | 2026-11-10 | 1 | RF100 | 0.257 | 0.196 | 0.253 | 0.210


**Modelo base:** el [código de ejemplo](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/baseline_model.ipynb) (Random Forest de 100 árboles con un solo predictor, `region`) obtiene un **F1-Score macro de 0,216** en el validation set (accuracy 0,263).

## Fórmula para la calificación

Las métricas del leaderboard son accuracy y precision, recall y F1-Score con promedio **macro** (cada clase pesa lo mismo), calculadas sobre el test set reservado. La calificación usa **el mejor F1-Score macro** obtenido durante el challenge:

$$y = \alpha + (X - 0.001) \times k$$

Donde $y$ es la nota de 1.0 a 7.0; $\alpha$ = 1.0; $X$ es **el mejor F1-Score obtenido** durante el challenge; *k* = **11.81**. La nota se acota a 7.0.

Este año el problema es más difícil que en 2025: con cinco clases de ingreso, incluso un modelo con todos los predictores difícilmente supera un F1-Score de 0,55. Por eso *k* es mayor que el de 2025 (8.320). Notas de referencia:

| F1-Score macro | 0.20 | 0.25 | 0.30 | 0.35 | 0.40 | 0.45 | 0.50 | ≥ 0.51 |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Nota (*k* = 11.81) | 3.35 | 3.94 | 4.53 | 5.12 | 5.71 | 6.30 | 6.89 | 7.0 |

De esta forma, la nota máxima (7.0) se alcanza con un F1-Score de 0,509. El modelo base (F1-Score ≈ 0,21 en el test set) obtiene un 3.47.

## Consistencia en el preprocesamiento y evitar data leakage

Deberían aplicar los pasos de recodificación y transformaciones no solo a [X_train_part1.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part1.csv) y [X_train_part2.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part2.csv), también a [X_val.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_val.csv) y [X_test.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_test.csv). Esto asegura la consistencia para la validación y los cálculos finales de las métricas.

> [!CAUTION]
> Aunque ciertas transformaciones (e.g., scaling, encoding, imputación) deben ser aplicadas a todos los conjuntos, es importante que **los parámetros (e.g., media, SD, mediana, categorías, target encoding) sean obtenidos solamente del training set ([X_train_part1.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part1.csv) y [X_train_part2.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/X_train_part2.csv))** mientras comparan modelos con el validation set. Para el reajuste final pueden obtenerse de training + validation (ver [Preguntas frecuentes](#preguntas-frecuentes)), **pero nunca de `X_test`**.

Esto evita **data leakage** debido a la influencia de los patrones del **validation set** y del **test set** sobre el conjunto de datos de entrenamiento, garantizando una mejor generalización del modelo y evitando métricas infladas. Lo mismo vale para los quintiles del objetivo: **no modifiquen la discretización del código de ejemplo**.

Si una transformación es puramente determinista (e.g., `np.log1p`, `n_ocupados_hogar / n_personas`), puede aplicarse de forma idéntica a todas las divisiones.

No es necesario imputar la variable objetivo, ya que no tiene valores perdidos. Es posible imputar los predictores.

> [!TIP]
> A continuación, hay algunos **ejemplos** de **transformaciones consistentes**, **imputaciones** y **pipeline** para el preprocesamiento.

```python
import glob
import pandas as pd
from sklearn.preprocessing import StandardScaler, OrdinalEncoder
from sklearn.impute import SimpleImputer
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer

## Load the data (as in the baseline code, from the prediction_challenge folder)
parts = sorted(glob.glob("data/X_train_part*.csv"))
X_train = pd.concat((pd.read_csv(p) for p in parts), ignore_index=True)
X_val = pd.read_csv("data/X_val.csv")
X_test = pd.read_csv("data/X_test.csv")

numeric_columns = ['edad_jefe', 'n_personas']          ## Use for numerical features (ejemplo ilustrativo)
categorical_columns = ['region', 'area']               ## Use for categorical features (ejemplo ilustrativo)

## Numerical: median -> scale
numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())
])

## Categorical: missing as its own category, safe encoding (handles unseen with -1)
categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='Sin dato')),
    ('encoder', OrdinalEncoder(handle_unknown='use_encoded_value', unknown_value=-1))
])

## Apply correct transforms per column group (fit on train, transform validation/test)
preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_columns),
        ('cat', categorical_transformer, categorical_columns)
    ],
    remainder='drop' ## Keep only specified columns
)

X_train_processed = preprocessor.fit_transform(X_train)
X_val_processed = preprocessor.transform(X_val)
X_test_processed = preprocessor.transform(X_test)

## Or chain preprocessing and model: model.fit(X_train, y_train) fits everything on train only
## (y_train: the classes from the baseline code), then model.predict(X_val) / model.predict(X_test)
from sklearn.ensemble import RandomForestClassifier
model = Pipeline(steps=[('prep', preprocessor), ('rf', RandomForestClassifier(n_estimators=100, random_state=86))])
```

## Codebook

Unidad: hogar (una fila por jefatura de hogar). La columna "Variable Casen" indica la variable de origen en la base Casen 2024, para consultar el [libro de códigos y los cuestionarios oficiales](https://bid-ckan.ministeriodesarrollosocial.gob.cl/dataset/base-de-datos-casen-2024). El mismo contenido está en [codebook.csv](https://github.com/Doctorado-UDP/data-mining-2026/blob/main/prediction_challenge/data/codebook.csv) (UTF-8: léanlo con `pd.read_csv`; si lo abren directamente en Excel, las tildes de las descripciones pueden verse mal). Su columna `tipo` (`categorica`, `numerica`) sirve para separar los predictores en el pipeline.

| Columna | Descripción | Variable Casen | Tipo | Valores / rango | % perdidos |
|---|---|---|---|---|---:|
| `id` | Identificador de fila dentro de cada partición (0..n-1); une X con y y se usa en el envío | — | Entero | 0..n-1 | 0.00 |
| `region` | Región de residencia del hogar | `region` | Categórica | Antofagasta · Arica y Parinacota · Atacama · Aysen · Biobio · Coquimbo · La Araucania · Los Lagos · Los Rios · Magallanes · Maule · Metropolitana · Nuble · O'Higgins · Tarapaca · Valparaiso | 0.00 |
| `area` | Área geográfica (urbano/rural) | `area` | Categórica | Rural · Urbano | 0.00 |
| `n_personas` | Número de personas en el hogar (acotada: 10 = 10 o más) | `numper` | Numérica | 1 – 10 | 0.00 |
| `tipo_hogar` | Tipo de hogar según composición de parentesco | `tipohogar` | Categórica | Censal · Extenso Biparental · Extenso Monoparental · Nuclear Biparental · Nuclear Monoparental · Unipersonal | 0.00 |
| `hay_menores_18` | Presencia de menores de 18 años en el hogar | `men18c` | Categórica | No · Si | 0.00 |
| `hay_mayores_60` | Presencia de personas de 60 años o más en el hogar | `may60c` | Categórica | No · Si | 0.00 |
| `n_ocupados_hogar` | Número de personas ocupadas en el hogar | `n_ocupados` | Numérica | 0 – 9 | 0.00 |
| `n_inactivos_hogar` | Número de personas inactivas en el hogar | `n_inactivos` | Numérica | 0 – 9 | 0.00 |
| `allegamiento_interno` | Allegamiento interno (más de un núcleo familiar en el hogar) | `allega_int` | Categórica | No · Si | 0.00 |
| `edad_jefe` | Edad de la jefatura de hogar en años (acotada: 90 = 90 o más) | `edad` | Numérica | 16 – 90 | 0.00 |
| `sexo_jefe` | Sexo de la jefatura de hogar | `sexo` | Categórica | Hombre · Mujer | 0.00 |
| `estado_civil_jefe` | Estado conyugal o civil de la jefatura | `ecivil` | Categórica | Anulado(a) · Casado(a) · Conviviente civil (AUC) · Conviviente sin AUC · Divorciado(a) · Separado(a) · Soltero(a) · Viudo(a) | 0.00 |
| `anios_escolaridad_jefe` | Años de escolaridad de la jefatura | `esc` | Numérica | 0 – 31 | 0.21 |
| `nivel_educ_jefe` | Nivel educacional agregado de la jefatura | `educc` | Categórica | Basica completa · Basica incompleta · Media completa · Media incompleta · Sin educacion formal · Superior completa · Superior incompleta | 0.21 |
| `area_estudio_jefe` | Campo amplio de estudio (CINE-F 2013); solo jefaturas con educación superior | `cinef13_area` | Categórica | Administracion y Derecho · Agropecuaria y Veterinaria · Artes y Humanidades · Ciencias Naturales y Matematicas · Ciencias Sociales y Periodismo · Educacion · Ingenieria y Construccion · Salud y Bienestar · Servicios · TIC | 68.75 |
| `actividad_jefe` | Condición de actividad de la jefatura (ocupado, desocupado, inactivo) | `activ` | Categórica | Desocupados · Inactivos · Ocupados | 0.00 |
| `ocupacion_jefe` | Ocupación (CIUO-08, gran grupo, 1 dígito); solo ocupados | `oficio1_08` | Categórica | 0 Fuerzas armadas · 1 Directivos y gerentes · 2 Profesionales · 3 Tecnicos nivel medio · 4 Apoyo administrativo · 5 Servicios y vendedores · 6 Agropecuarios calificados · 7 Artesanos y operarios · 8 Operadores de maquinas · 9 Ocupaciones elementales | 38.36 |
| `rama_jefe` | Rama de actividad económica (CAENES, sección); solo ocupados | `rama1` | Categórica | A Agropecuario y pesca · B Mineria · C Manufactura · D Electricidad y gas · E Agua y residuos · F Construccion · G Comercio · H Transporte · I Alojamiento y comidas · J Informacion y comunicaciones · K Financieras y seguros · L Inmobiliarias · M Profesionales y cientificas · N Servicios administrativos · O Administracion publica · P Ensenanza · Q Salud y asistencia social · R Artes y entretenimiento · S Otros servicios · T Hogares como empleadores · U Organismos extraterritoriales | 38.37 |
| `categoria_ocup_jefe` | Categoría ocupacional en el trabajo principal; solo ocupados | `o15` | Categórica | Asalariado empresa publica · Asalariado sector privado · Asalariado sector publico · Cuenta propia · Empleador · FF.AA. y del Orden · Familiar no remunerado · Servicio domestico puertas afuera | 38.35 |
| `horas_trabajo_jefe` | Horas trabajadas habitualmente por semana; solo ocupados | `o10` | Numérica | 1 – 168 | 38.62 |
| `tipo_trabajo_jefe` | Tipo de trabajo (permanente, temporada, ocasional, ...); solo ocupados | `o12` | Categórica | A prueba · De temporada o estacional · Ocasional o eventual · Permanente · Por plazo o tiempo determinado | 38.40 |
| `contrato_jefe` | Tiene contrato de trabajo; solo asalariados | `contrato` | Categórica | No · Si | 55.62 |
| `cotiza_jefe` | Cotizó en el sistema de pensiones el mes pasado | `cotiza` | Categórica | No · Si | 0.27 |
| `tamano_empresa_jefe` | Número de personas que trabajan en la empresa; solo ocupados | `o25` | Categórica | 1 persona · 10 a 49 · 2 a 5 · 200 y mas · 50 a 199 · 6 a 9 | 39.47 |
| `prevision_salud_jefe` | Sistema previsional de salud de la jefatura | `s13` | Categórica | FF.AA. y del Orden · FONASA · Isapre · Ninguno · Otro | 0.17 |
| `seguro_salud_compl_hogar` | Algún integrante del hogar tiene seguro de salud complementario | `s15a` | Categórica | No · Si | 0.45 |
| `discapacidad_jefe` | Situación de discapacidad de la jefatura (Washington Group) | `disc_wg` | Categórica | No · Si | 0.01 |
| `pueblo_indigena_jefe` | Pertenencia de la jefatura a pueblos indígenas | `pueblos_indigenas` | Categórica | No · Si | 0.00 |
| `nacido_extranjero_jefe` | Jefatura nacida fuera de Chile | `lugar_nac` | Categórica | No · Si | 0.48 |
| `n_vehiculos` | Número de vehículos de uso laboral o particular del hogar (0 si no tiene) | `r15` | Numérica | 0 – 16 | 0.03 |
| `internet_fija` | Acceso a internet en la vivienda: banda ancha fija | `r17a` | Categórica | No · Si | 0.00 |
| `internet_movil_modem` | Acceso a internet en la vivienda: banda ancha móvil (módem, wifi o USB) | `r17b` | Categórica | No · Si | 0.00 |
| `internet_celular` | Acceso a internet en la vivienda: teléfono móvil | `r17c` | Categórica | No · Si | 0.00 |
| `tipo_vivienda` | Tipo de vivienda | `v1` | Categórica | Casa aislada · Casa pareada ambos lados · Casa pareada un lado · Depto con ascensor · Depto sin ascensor · Mediagua o emergencia · Pieza o conventillo · Vivienda precaria | 0.00 |
| `material_muros` | Material predominante de los muros exteriores | `v2` | Categórica | Adobe o artesanal · Albanileria · Hormigon armado · Materiales precarios · Tabique forrado · Tabique sin forro | 0.00 |
| `material_piso` | Material predominante del piso | `v4` | Categórica | Alfombra · Baldosa de cemento · Ceramico o similar · Madera o similar · Radier · Tierra | 0.00 |
| `m2_vivienda` | Superficie de la vivienda (tramos de m²) | `v12` | Categórica | 101 a 150 m2 · 30 a 40 m2 · 41 a 60 m2 · 61 a 100 m2 · Mas de 150 m2 · Menos de 30 m2 | 0.84 |
| `tenencia_vivienda` | Condición de tenencia de la vivienda | `ten_viv` | Categórica | Arrendada · Cedida · Irregular u otro · Propia | 0.00 |
| `n_dormitorios` | Número de dormitorios de uso exclusivo | `v27a` | Numérica | 0 – 28 | 0.03 |
| `n_banos` | Número de baños | `v27b` | Numérica | 0 – 10 | 0.06 |
| `hacinamiento` | Índice de hacinamiento (personas por dormitorio) | `ind_hacina` | Categórica | Alto · Critico · Medio · Sin hacinamiento | 0.03 |
| `indice_materialidad` | Índice de materialidad de la vivienda | `ind_mat` | Categórica | Aceptable · Irrecuperable · Recuperable (resto) · Recuperable (solo muro) | 0.00 |
| `fuente_agua` | Origen del agua de la vivienda | `v20` | Categórica | Camion aljibe · Otra fuente · Pozo o noria · Red publica · Rio, vertiente o lluvia | 0.00 |
| `sistema_excretas` | Sistema de eliminación de excretas | `v23_sistema` | Categórica | Bano quimico · Cajon · Letrina pozo negro · WC alcantarillado · WC fosa septica | 1.36 |
| `electricidad` | Disponibilidad y fuente de energía eléctrica | `v24` | Categórica | No dispone · Red y generador comunitario · Red y generador propio · Red, medidor compartido · Red, medidor propio · Red, sin medidor · Solo generador comunitario · Solo generador propio | 0.00 |
| `combustible_cocina` | Combustible o fuente de energía para cocinar | `v34a` | Categórica | Electricidad · Energia solar · Gas licuado · Gas por red · Lena, carbon o derivados · No tiene sistema · No usa · Parafina o petroleo | 0.00 |
| `combustible_calefaccion` | Combustible o fuente de energía para calefaccionar | `v34b` | Categórica | Electricidad · Energia solar · Gas licuado · Gas por red · Lena, carbon o derivados · No tiene sistema · No usa · Parafina o petroleo | 0.00 |
| `cerca_transporte_publico` | Vivienda a menos de 8 cuadras (1 km) del transporte público | `v35a` | Categórica | No · Si | 0.04 |
| `balaceras_sector` | Frecuencia con que ha presenciado balaceras o disparos en el sector | `v36e` | Categórica | Muchas veces · Nunca · Pocas veces · Siempre | 0.00 |
| `estado_sector` | Estado de edificios y casas del sector | `p2` | Categórica | Bueno · Malo · Muy bueno · Muy malo · Satisfactorio | 0.00 |
| `ypchautcor` | Objetivo (solo en y_*): ingreso autónomo per cápita del hogar corregido, CLP/mes | `ypchautcor` | Numérica (objetivo) | 0 – 30000000 | 0.00 |

## Resultados muestrales

Los datos no incluyen los factores de expansión de Casen. Por lo tanto, cualquier estadística calculada con ellos (proporciones, promedios, métricas) corresponde a **resultados muestrales, sin expansión**, y no a estimaciones poblacionales oficiales. Los quintiles de este challenge son quintiles de la muestra de entrenamiento y **no** coinciden con los quintiles oficiales de ingreso autónomo publicados por el Ministerio.

## Atribución y licencia

Fuente: Ministerio de Desarrollo Social y Familia, Encuesta de Caracterización Socioeconómica Nacional (Casen) 2024, base de datos publicada en BIDAT ([bid-ckan.ministeriodesarrollosocial.gob.cl](https://bid-ckan.ministeriodesarrollosocial.gob.cl/dataset/base-de-datos-casen-2024)), licencia [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.es). Datos modificados: selección de jefaturas de hogar, recodificación de variables y partición train/val/test. Resultados muestrales, sin factor de expansión.

El Ministerio de Desarrollo Social y Familia no participa en este challenge ni respalda sus resultados.
