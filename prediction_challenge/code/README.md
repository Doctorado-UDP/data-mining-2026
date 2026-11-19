# Código de los envíos

En esta carpeta cada participante entrega el código de **su mejor envío** del desafío de predicción. La nota solo se valida si ese código, ejecutado por el profesor, vuelve a generar **exactamente el mismo archivo** de predicciones (ver [Instrucciones](../README.md#instrucciones)).

## Plazo

- **Desde:** el cierre del desafío, el lunes 30 de noviembre de 2026 a las 23:59 (hora de Chile).
- **Hasta:** el martes 1 de diciembre de 2026 a las 23:59 (hora de Chile).

No suban el código antes del cierre: el repositorio es público y quedaría a la vista del resto.

## Qué entregar

- **Un solo archivo:** el notebook (`.ipynb`) o script (`.py`) de Python que genera su mejor envío.
- **Nombre:** el mismo del envío, con la extensión del código. Por ejemplo, el envío `submission_Nombre_Modelo_3.csv` se entrega como `submission_Nombre_Modelo_3.ipynb`.
- **No suban** datos, archivos de envío ni carpetas de entorno (`.venv`).

## Requisitos para la verificación

El código debe:

1. ejecutarse desde la carpeta `prediction_challenge/`, con rutas relativas (`data/…`, `submissions/…`), igual que el [código de ejemplo](../baseline_model.ipynb);
2. leer solo los datos del desafío y, si los usa, los datos externos permitidos, declarados con la URL de la fuente y la fecha de acceso;
3. declarar la lista de predictores (como máximo cinco variables de origen, incluidas las externas);
4. fijar todas las semillas (`random_state`) y cualquier otro valor elegido durante el ajuste (hiperparámetros, umbrales o pesos), de modo que el resultado no dependa del azar;
5. escribir el archivo de envío con el mismo nombre y el mismo contenido que el que subieron a `submissions/`.

Si el notebook se preparó en Google Colab, descárguenlo con "Archivo → Descargar → Descargar .ipynb". Rige la misma condición: al ejecutarlo debe volver a generar el mismo archivo.

## Cómo subirlo

1. Abran esta carpeta en GitHub ([prediction_challenge/code](https://github.com/Doctorado-UDP/data-mining-2026/tree/main/prediction_challenge/code)).
2. Elijan "Add file → Upload files", arrastren el archivo y confirmen con "Commit changes".

Dudas: escriban al profesor (bastian.gonzalez.b@mail.udp.cl) o a la ayudante, Camila Luardo (camila.luardo@mail.udp.cl).
