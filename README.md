# Proyecto 2 y 3 — Análisis, Limpieza y Modelado

**Grupo DatosSinCiencia** · Siniestralidad vial en Santiago de Cali, 2016–2024

Continuación del Proyecto 1 sobre el mismo dataset: análisis exploratorio, limpieza y normalización, y modelos de
Machine Learning supervisados y no supervisados para entender la gravedad de los siniestros viales reportados en Cali.

## Estructura

```
├── entrega/
│   ├── fase_a_eda.ipynb          ← Fase A: análisis exploratorio
│   ├── fase_b_limpieza.ipynb     ← Fase B: limpieza y normalización
│   ├── fase_c_modelos.ipynb      ← Fase C: modelos predictivos y clustering
│   ├── datos_limpios.parquet     ← salida de la Fase B
│   ├── informe_p2p3.pdf          ← Fase D: informe final
│   └── presentacion.pdf          ← Fase D: presentación
├── .env.example                  ← plantilla de configuración (ruta del CSV)
├── requirements.txt
└── README.md
```

## Cómo correr el proyecto

1. **Clonar el repositorio**

   ```bash
   git clone git@github.com:guzmancama/DatosSinCiencia2y3.git
   cd DatosSinCiencia2y3
   ```

2. **Descargar el dataset.** El CSV no está en el repositorio por su tamaño (≈27 MB). Descargar
   `siniestralidad_2016_2024.csv` (el dataset del Proyecto 1, ver `proyectos/catalogo_datasets.md` del curso) y
   guardarlo en la raíz del proyecto, junto a este README.

3. **Configurar la ruta del CSV**

   ```bash
   cp .env.example .env
   ```

   Si el CSV está en otra carpeta, editar `RUTA_CSV` en `.env` (la ruta es relativa a la carpeta `entrega/`).

4. **Crear el entorno e instalar dependencias** (Python 3.10 o superior)

   ```bash
   python -m venv .venv
   source .venv/bin/activate        # Windows: .venv\Scripts\activate
   pip install -r requirements.txt
   ```

5. **Ejecutar los notebooks en orden**, desde la carpeta `entrega/`:

   ```bash
   cd entrega
   jupyter notebook
   ```

   | Orden | Notebook | Entrada | Salida |
   |---|---|---|---|
   | 1 | `fase_a_eda.ipynb` | CSV original | Gráficos e insights |
   | 2 | `fase_b_limpieza.ipynb` | CSV original | `datos_limpios.parquet` |
   | 3 | `fase_c_modelos.ipynb` | `datos_limpios.parquet` | Métricas y comparación de modelos |

## Sobre el dataset

- Un registro por siniestro reportado a la Secretaría de Movilidad de Cali (≈371 mil reportes, 1-ene-2016 a 2-nov-2024).
- El archivo viene en codificación **Latin-1**, separado por `;`, y los faltantes están marcados con `"."`.
- Variable objetivo: `Tipo confirmado` (Negativo, Solo daños, Con lesionado, Con fallecido).
