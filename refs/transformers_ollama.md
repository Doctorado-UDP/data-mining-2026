# Transformers y Ollama: guía de instalación (Sesión 13)

**Data Mining - Doctorado UDP 2026** · Bastián González-Bustamante · actualizada en noviembre de 2026

Esta guía prepara su computador para los dos notebooks de la Sesión 13:
- `5_BERT_y_semisupervision.ipynb` usa modelos tipo BERT de Hugging Face con `transformers` y `torch`. **También corre en Google Colab** sin instalar nada.
- `6_LLM_clasificador.ipynb` usa un LLM local con **Ollama**. **Corre solo en su computador.**

## 1. Python y el entorno virtual del curso

1. Instale Python 3.11 o superior desde https://www.python.org. En Windows, marque "Add Python to PATH".
2. En la carpeta principal del repositorio del curso (donde está `requirements.txt`), cree y active un entorno virtual:
   ```
   python -m venv .venv
   .venv\Scripts\activate        (Windows)
   source .venv/bin/activate     (macOS/Linux)
   ```
3. Instale las versiones probadas del curso:
   ```
   pip install -r requirements.txt
   ```
   El archivo instala, entre otros:
   - **PyTorch para CPU** (`torch`, unos 200 MB, desde el índice oficial de PyTorch; en macOS, la versión estándar);
   - `transformers`;
   - el cliente `ollama` para Python.

   No hace falta una GPU: los notebooks funcionan en la CPU. Si tiene una GPU NVIDIA y quiere usarla, instale la versión de PyTorch para su GPU según https://pytorch.org/get-started/locally/.

## 2. Probar `transformers`

```python
from transformers import pipeline

clasificador = pipeline("text-classification", model="bgonzalezbustamante/bert-spanish-toxicity")
print(clasificador(["Que tengas un excelente día :)", "Eres un maldito infeliz"]))
```

La primera vez se descarga el modelo, unos 440 MB. El notebook 5 usa tres modelos, unos 2 GB en total. Quedan guardados en la caché de Hugging Face, así que la segunda vez no se descargan.

## 3. Instalar Ollama y descargar Llama 3.2

1. Descargue e instale Ollama desde https://ollama.com/download (Windows, macOS o Linux). Use la versión 0.5 o posterior, necesaria para la salida estructurada en JSON.
2. Abra la aplicación Ollama, o ejecute `ollama serve` en una terminal. Debe quedar funcionando mientras use el notebook.
3. En una terminal, descargue el modelo:
   ```
   ollama pull llama3.2
   ```
   Es Llama 3.2 de 3.000 millones de parámetros (3,21 B según la ficha de Meta): 2,0 GB, cuantizado en 4 bits (Q4_K_M). Para un computador con poca memoria existe `llama3.2:1b` (1,3 GB).
4. Pruébelo: `ollama run llama3.2 "Hola"` (salga con `/bye`).

Llama 3.2 se distribuye con la licencia propia de Meta (*Llama 3.2 Community License*). Su ficha indica el español entre los ocho idiomas que admite oficialmente.

## 4. Probar el cliente de Python

```python
import ollama

esquema = {"type": "object",
           "properties": {"categoria": {"type": "string", "enum": ["TOXIC", "NONTOXIC"]}},
           "required": ["categoria"]}
respuesta = ollama.chat(model="llama3.2",
                        messages=[{"role": "user", "content": "text: Eres un idiota\nRespond in JSON."}],
                        format=esquema, options={"temperature": 0, "seed": 86})
print(respuesta.message.content)   # por ejemplo: {"categoria": "TOXIC"}
```

## 5. Problemas frecuentes

- **"No se encontró Ollama":** abra la aplicación Ollama, o ejecute `ollama serve`, y vuelva a ejecutar la celda.
- **"Falta el modelo":** ejecute `ollama pull llama3.2` en una terminal.
- **El notebook 6 es lento:** sin GPU, cada mensaje tarda de uno a tres segundos, y más con la cadena de razonamiento. Reduzca `N_MENSAJES` en el notebook.
- **La descarga de modelos de Hugging Face falla en una red institucional:** pruebe en otra red, o use el notebook 5 en Google Colab.

## Cambios respecto de la guía de 2025

- Se agregan el cliente `ollama` y los paquetes que usan los notebooks, todos en `requirements.txt`.
- PyTorch para CPU por defecto, en vez del índice CUDA 11.8.
- Ollama ≥ 0.5, para la salida estructurada.
- El notebook de BERT también corre en Google Colab.
