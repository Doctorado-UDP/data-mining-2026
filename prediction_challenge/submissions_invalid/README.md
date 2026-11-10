En esta carpeta se encuentran las entregas **inválidas**, que no se evalúan ni cuentan para el leaderboard. Una entrega es inválida si:

* El nombre del archivo no sigue el formato `submission_<Nombre>_<Modelo>_<N>.csv`.
* Las columnas no son exactamente `id,predicted_label`.
* Falta algún `id` del test set, hay `id` repetidos o `id` que no existen en el test set.
* Hay etiquetas vacías o distintas de `Low`, `Medium-Low`, `Medium`, `Medium-High` y `High`.
* Se subió fuera del plazo del challenge.

Corrijan el archivo y vuelvan a subirlo a `submissions/` (cuenta como el envío del día en que se sube).
