# ☕ MultiAgent AdRouter CafeAI

> Sistema multi-agente con **LangGraph** y **Ollama (Llama 3.2)** para enrutar tareas publicitarias de **Cafe.AI** hacia el especialista correcto: Creativo, Redactor o Diseñador.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/cjudithrb/MultiAgent-LangGraph-AdRouter-CafeAI/blob/main/campanapub.ipynb)

---

## 📌 Descripción del proyecto

**Cafe.AI** es un café inteligente donde la tecnología y el sabor convergen: un espacio para conectar, crear e innovar, una taza a la vez. Diseñado para innovadores, desarrolladores y soñadores.

Este notebook implementa el **equipo publicitario virtual** de Cafe.AI para su primera campaña en redes sociales. El sistema usa un **grafo multi-agente con LangGraph** que recibe una solicitud de marketing y la **enruta automáticamente** al especialista más adecuado:

| Rol | Función |
|---|---|
| 🎨 **Creativo** | Genera conceptos e ideas innovadoras para la campaña |
| ✍️ **Redactor** | Escribe copys persuasivos para redes sociales |
| 🖼️ **Diseñador** | Define la línea visual, estética y guía de composición |
| 🔀 **Enrutador** | Analiza la solicitud y decide qué especialista debe actuar |

---

## 🧠 Arquitectura del grafo

```
Solicitud de entrada
       │
       ▼
  ┌─────────────┐
  │  Enrutador  │  ← LLM con salida estructurada (Route)
  └──────┬──────┘
         │
  ┌──────┼──────────────┐
  ▼      ▼              ▼
Creativo  Redactor   Diseñador
  │          │           │
  └──────────┴───────── END
```

El enrutador usa `with_structured_output(Route)` para decidir entre `"Creativo"`, `"Redactor"` o `"Disenador"` según el contenido de la solicitud.

---

## 🛠️ Tecnologías utilizadas

| Tecnología | Uso |
|---|---|
| [LangGraph](https://github.com/langchain-ai/langgraph) | Orquestación del grafo de agentes |
| [LangChain Ollama](https://python.langchain.com/docs/integrations/llms/ollama) | Integración con modelo local via Ollama |
| [Ollama](https://ollama.com) | Servidor local del modelo LLM |
| **Llama 3.2** (`llama3.2:latest`) | Modelo de lenguaje base |
| Pydantic | Esquema de salida estructurada (`Route`) |
| Python 3.12+ | Lenguaje principal |
| Google Colab | Entorno de ejecución en la nube |

---

## 📂 Estructura del proyecto

```
MultiAgent-LangGraph-AdRouter-CafeAI/
│
├── campanapub.ipynb    # Notebook principal con el pipeline completo
└── README.md           # Este archivo
```

---

## ⚙️ Instalación y uso

### Opción 1: Google Colab (recomendado)

Haz clic en el badge de arriba o accede directamente al notebook. Instala automáticamente Ollama y descarga el modelo `llama3.2:latest`.

### Opción 2: Entorno local

```bash
# 1. Clonar el repositorio
git clone https://github.com/cjudithrb/MultiAgent-LangGraph-AdRouter-CafeAI.git
cd MultiAgent-LangGraph-AdRouter-CafeAI

# 2. Instalar dependencias Python
pip install langchain_ollama langgraph pydantic python-dotenv ipython

# 3. Instalar Ollama y descargar el modelo
curl -fsSL https://ollama.com/install.sh | sh
ollama serve &
ollama pull llama3.2:latest

# 4. Abrir el notebook
jupyter notebook campanapub.ipynb
```

---

## 🚀 Cómo funciona

### 1. Modelo y esquema de enrutamiento

```python
from langchain_ollama import ChatOllama
from pydantic import BaseModel, Field
from typing_extensions import Literal

llm = ChatOllama(model="llama3.2:latest", temperature=0.2)

class Route(BaseModel):
    step: Literal["Creativo", "Redactor", "Disenador"] = Field(
        None, description="El especialista que debe actuar"
    )

router = llm.with_structured_output(Route)
```

### 2. Estado y nodos del equipo

El grafo maneja un estado con tres campos: `input`, `decision` y `output`. Cada nodo recibe ese estado y genera su respuesta especializada:

- **`enrutador`** → analiza la solicitud y decide el rol con structured output.
- **`rol_creativo`** → actúa como Director Creativo, genera conceptos innovadores.
- **`rol_redactor`** → actúa como Copywriter Senior, escribe posts persuasivos.
- **`rol_disenador`** → actúa como Director de Arte, describe la línea visual.

### 3. Compilación y ejecución

```python
state = router_workflow.invoke({
    "input": "Necesito un copy persuasivo para Instagram para el lanzamiento de Cafe.AI"
})
print(state["output"])
```

**Ejemplo de salida** (enrutada al Creativo):

```
ROL CREATIVO
¡Descubre el futuro del café!

¿Sabías que tu café podría ser perfecto cada vez? Con Cafe.AI,
nuestra plataforma revolucionaria, puedes experimentar un nuevo
nivel de sabor y conexión con tus sentidos...

#CafeAI #LaRevolutionDelCafe #SaborAutentico
```

---

## 🗺️ Visualización del grafo

El notebook genera automáticamente una imagen del grafo compilado:

```python
display(Image(router_workflow.get_graph().draw_mermaid_png()))
```

---

## 📝 Notas técnicas

- El modelo corre **localmente con Ollama**, sin API keys externas.
- En Google Colab, Ollama se instala vía `bash` y corre como proceso en segundo plano con `subprocess.Popen`.
- La temperatura del modelo es `0.2` para respuestas más consistentes.
- El enrutamiento no es 100% determinista: el LLM decide el rol según el contexto de la solicitud.

---

## 👩‍💻 Autora

**Judith RB** — [@cjudithrb](https://github.com/cjudithrb)

*Proyecto desarrollado como ejercicio práctico de sistemas multi-agente con LangGraph aplicados a marketing con IA.*
