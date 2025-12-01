# Ranking de Consenso Exponencial (ECR)
---
Para concluir nuestro estudio de Virtual Screening, integraremos los resultados de las tres herramientas utilizadas (**Vina, PLANTS y DiffDock**) para obtener una predicción final robusta.

Utilizaremos el método de **Exponential Consensus Ranking (ECR)**, propuesto por *Palacio-Rodríguez et al. (2019)[cite_start]*[cite: 6]. Este método supera las limitaciones de promediar scores (ya que cada programa usa unidades diferentes) utilizando el **Rango (Rank)** de cada molécula.

La fórmula del ECR asigna una probabilidad a cada molécula ($i$) basada en su posición ($r$) en cada programa ($j$):

$$P(i) = \frac{1}{\sigma} \sum_{j} \exp\left(-\frac{r_{i}^{j}}{\sigma}\right)$$

Donde $\sigma$ es un parámetro que define el enfoque en los mejores candidatos (usaremos el 10% del tamaño de la base de datos).

---

# Tutorial
## Requisitos
### Archivos de Resultados Previos:
Necesitamos las rutas a los archivos CSV generados en los tutoriales anteriores:
1.  **Vina:** `screening_results.csv`
2.  **PLANTS:** `ranking.csv`
3.  **DiffDock:** `ranking_sorted.csv` (o el generado por tu script de análisis).

---

## Procedimiento

### 📂 Preparación del Entorno
Trabajaremos en una nueva carpeta para el consenso, pero accederemos a los archivos de los directorios anteriores.

1. Crea el directorio de consenso:
```bash
cd /DISCO1/Aylin/luisc/
mkdir consensus_analysis
cd consensus_analysis
```

2. Instala la librería pandas en tu entorno (si no la tienes):
```Bash
# Usa cualquier entorno activo (ej. Vina)
conda install pandas
```

### Script de Cálculo ECR
El siguiente script de Python, lee los tres formatos diferentes, extrae el ID numérico de cada ligando (para asegurar que `lig1` = `cand1` = `entry_01`), calcula los rankings individuales y genera el puntaje ECR final.

1. Crea el script:

```Bash
nano run_ecr.py
```

2. Copia y pega el siguiente código. Asegúrate de verificar que las rutas a los archivos (`paths`) sean correctas para tu servidor.

``` Python
import pandas as pd
import numpy as np
import re
import os

# --- CONFIGURACIÓN DE RUTAS (AJUSTA ESTO) ---
path_vina = "../docking_capsaicin/screening_results.csv"
path_plants = "../docking_capsaicin/results_screening_plants/ranking.csv"
path_diffdock = "../DiffDock/ranking_sorted.csv"

# Parámetro sigma (Enfoque en el top). El paper sugiere el 5-10% del total de datos.
SIGMA_PERCENT = 0.10 

print("--- Iniciando Análisis de Consenso (ECR) ---")

# ==========================================
# 1. FUNCIÓN DE LIMPIEZA DE DATOS
# ==========================================
def extract_id(text, prefix):
    """Extrae el número entero de cadenas como 'lig1', 'cand1', 'entry_0001'"""
    # Busca números en el texto
    numbers = re.findall(r'\d+', str(text))
    if numbers:
        # Para PLANTS (ej. DB_464_entry_00001), el ID secuencial suele ser el segundo número o el entry
        # Asumimos que la consistencia se mantiene por el orden de generación (1, 2, 3...)
        # Para seguridad, usaremos el mapeo directo si el prefijo coincide.
        return int(numbers[-1]) if prefix in text else int(numbers[0])
    return -1

# ==========================================
# 2. CARGAR Y PROCESAR VINA
# ==========================================
print(f"Cargando Vina desde: {path_vina}")
df_vina = pd.read_csv(path_vina)
# Limpiar: Quitar filas de ERROR
df_vina = df_vina[df_vina['Affinity_kcal_mol'] != 'ERROR'].copy()
df_vina['Affinity_kcal_mol'] = pd.to_numeric(df_vina['Affinity_kcal_mol'])
# Extraer ID (lig1 -> 1)
df_vina['ID'] = df_vina['Ligand_File'].apply(lambda x: extract_id(x, 'lig'))
# Ranking Vina: Menor es mejor (Ascendente)
df_vina['Rank_Vina'] = df_vina['Affinity_kcal_mol'].rank(ascending=True)
df_vina = df_vina[['ID', 'Affinity_kcal_mol', 'Rank_Vina']].set_index('ID')

# ==========================================
# 3. CARGAR Y PROCESAR PLANTS
# ==========================================
print(f"Cargando PLANTS desde: {path_plants}")
df_plants = pd.read_csv(path_plants)
# PLANTS genera muchas conformaciones por ligando. Nos quedamos con la MEJOR score por ligando.
# El nombre es tipo 'DB_464_entry_00001_conf_01'. Extraemos 'entry_00001'.
df_plants['ID'] = df_plants['LIGAND_ENTRY'].apply(lambda x: int(re.search(r'entry_(\d+)', x).group(1)))
# Agrupar por ID y tomar el mínimo score
df_plants_best = df_plants.groupby('ID')['TOTAL_SCORE'].min().reset_index()
# Ranking PLANTS: Menor es mejor (Ascendente)
df_plants_best['Rank_Plants'] = df_plants_best['TOTAL_SCORE'].rank(ascending=True)
df_plants_best = df_plants_best.set_index('ID')

# ==========================================
# 4. CARGAR Y PROCESAR DIFFDOCK
# ==========================================
print(f"Cargando DiffDock desde: {path_diffdock}")
df_diff = pd.read_csv(path_diffdock)
# Extraer ID (cand1 -> 1)
df_diff['ID'] = df_diff['Ligand'].apply(lambda x: extract_id(x, 'cand'))
# Ranking DiffDock: MAYOR confianza (más cercano a 0 o positivo) es mejor. 
# En tu archivo los mejores son -0.10, los peores -0.60.
# Por lo tanto: Descendente (Descending)
df_diff['Rank_Diff'] = df_diff['Confidence_Score'].rank(ascending=False)
df_diff = df_diff[['ID', 'Confidence_Score', 'Rank_Diff']].set_index('ID')

# ==========================================
# 5. CÁLCULO DEL CONSENSO (ECR)
# ==========================================
# Unir todas las tablas por ID
df_final = df_vina.join(df_plants_best, how='inner', lsuffix='_v').join(df_diff, how='inner', rsuffix='_d')

n_compounds = len(df_final)
sigma = n_compounds * SIGMA_PERCENT
print(f"Total compuestos en común: {n_compounds}. Sigma: {sigma}")

# Aplicar Fórmula ECR
# P(i) = (1/sigma) * sum( exp( -rank / sigma ) )
def calculate_ecr(row):
    term_vina = np.exp(-row['Rank_Vina'] / sigma)
    term_plants = np.exp(-row['Rank_Plants'] / sigma)
    term_diff = np.exp(-row['Rank_Diff'] / sigma)
    return (1/sigma) * (term_vina + term_plants + term_diff)

df_final['ECR_Score'] = df_final.apply(calculate_ecr, axis=1)

# Ordenar por ECR (Mayor es mejor probabilidad)
df_final = df_final.sort_values(by='ECR_Score', ascending=False)

# Guardar
output_csv = "consensus_results.csv"
df_final.to_csv(output_csv)
print(f"--- ¡Hecho! Resultados guardados en {output_csv} ---")

# Mostrar Top 5
print("\n=== TOP 5 CANDIDATOS DE CONSENSO ===")
print(df_final[['ECR_Score', 'Rank_Vina', 'Rank_Plants', 'Rank_Diff']].head(5))
```
Guarda y sal de nano.

## 🚀 Ejecutar el Análisis
1. Ejecuta el script con python:
```bash
python run_ecr.py
````

## 📊 Interpretación de Resultados
El script generará un archivo `consensus_results.csv`` y te mostrará el Top 5 en pantalla.
- ECR_Score: Es un valor de probabilidad. El número más alto gana.
- Consistencia: Observa los rangos individuales.
    - Si un ligando es Rank #1 en las 3 herramientas, su ECR será máximo.
    - Si un ligando es Rank #1 en PLANTS y DiffDock, pero Rank #50 en Vina, el ECR aún lo mantendrá alto (gracias a la propiedad de "voto exponencial"), rescatando al candidato.

### Identificar al Ganador
Toma el ID del compuesto #1 en la tabla de consenso (ej. ID 24). Para saber su nombre real, búsco en tu archivo original de DrugBank:
```bash
# Si el ID ganador es 24 (que corresponde a la entrada 24)
# Usamos un script rápido o grep para ver la entrada 24 del SDF original
grep -n "DB_" ../docking_capsaicin/drugbank_library.sdf | head -n 24 | tail -n 1
```
