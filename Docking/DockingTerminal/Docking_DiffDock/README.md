# Bienvenido al Tutorial 
# Acoplamiento molecular utilizando DiffDock (Deep Learning)

DiffDock es un modelo de aprendizaje profundo (Deep Learning) de vanguardia para el acoplamiento molecular ciego (blind docking). Representa un cambio de paradigma respecto a las herramientas tradicionales: en lugar de utilizar algoritmos de búsqueda iterativa sobre una función de energía física, DiffDock trata el acoplamiento como un problema de modelado generativo.

### Introducción a DiffDock
La arquitectura de DiffDock se basa en Modelos Generativos Basados en Puntuación (Score-based Generative Models o SGMs), específicamente modelos de difusión. El algoritmo aprende a revertir un proceso de difusión: comienza con una distribución aleatoria de posiciones del ligando y, mediante pasos iterativos de "eliminación de ruido" (denoising), refina la posición, orientación y torsión del ligando hasta que encaja en el sitio de unión de la proteína.

Este proceso ocurre en el espacio matemático de los grados de libertad del ligando (traslación, rotación y torsión). DiffDock entrena dos redes neuronales principales:

Modelo de Puntuación (Score Model): Predice el gradiente necesario para mover el ligando hacia la pose correcta en cada paso de difusión.

Modelo de Confianza (Confidence Model): Una vez generadas las poses, esta red estima la probabilidad de que la pose sea correcta (predicción del RMSD), asignando un Score de Confianza en lugar de una energía de unión en kcal/mol.

A diferencia de Vina o PLANTS, que pueden quedarse atrapados en mínimos locales durante la búsqueda, DiffDock muestrea directamente de la distribución posterior de poses probables, lo que le permite superar significativamente a los métodos tradicionales en términos de precisión (top-1 accuracy) y velocidad, especialmente en escenarios de docking ciego donde el sitio de unión no se conoce a priori.

**Aplicaciones**: DiffDock se utiliza para el cribado virtual masivo y rápido, la identificación de sitios de unión crípticos (ya que explora toda la superficie de la proteína de manera eficiente) y la validación cruzada de resultados obtenidos por métodos clásicos, ofreciendo una perspectiva independiente basada en datos masivos del PDB en lugar de campos de fuerza predefinidos.



## Requisitos
### Paqueteria necesaria:
- DiffDock
- Open Babel

## Procedimiento 
## Data
### 📂 Entorno de trabajo
Para empezar este tutorial, primero tenemos que preparar el entorno de trabajo
1. Asegurate de estar en el entorno de Diffdock.
```bash 
conda activate diffdock
```

3. Dirigete al directorio de DiffDock: (A diferencia de Vina y PLANTS, los scripts de DiffDock se ejecutan desde dentro de su propia carpeta de proyecto).

```bash 
cd /DISCO1/Aylin/luisc/DiffDock
```

---

## Descarga la Proteína (NorA + Fab36)
```bash 
wget https://files.rcsb.org/download/7LO8.pdb
```
### Extraer la Proteína (NorA)
1. **Limpiar el archivo PDB:** por el momento, nos interesa únicamente  la bomba NorA que corresponde a las cadenas Alfa y Beta contenidas en la `cadena Z` del archivo, para lo que, tenemos que limpiar la molecula, utilizando el siguiente comando: 

```bash 
awk '$1 == "ATOM" && $5 == "Z"' 7LO8.pdb > protein_NorA.pdb
```

Esto crea un nuevo archivo, `protein_NorA.pdb` con la proteína de interes. 

2. **Verifica**: antes de continuar, verifica que el arhcivo se haya creado con éxito.
```bash 
ls -l protein_NorA.pdb
```
Deberías de ver un tamaño de archivo de varios KBs.
#

### Extraer el Ligando de Referencia (Fab36)
1. **Volver a limpiar el archivo PDB:** como ya sabemos en el archivo `7L08.pdb` se encuentran las cadenas `H` y `L` correspondientes al ligando de `Fab36`, por lo que, tenemos que volver a limpiar la molecula inicial, utilizando el siguiente comando:

```bash 
awk '$1 == "ATOM" && ($5 == "H" || $5 == "L")' 7LO8.pdb > ligand_Fab36.pdb
```

2. **Verifica**: antes de continuar, verifica que el arhcivo se haya creado con éxito.
```bash 
ls -l ligand_Fab36.pdb
```
Deberías de ver un tamaño de archivo de varios KBs.
### Descarga el Ligando (Capsaicina)
```bash 
wget "https://pubchem.ncbi.nlm.nih.gov/rest/pug/compound/cid/1548943/SDF?record_type=3d" -O capsaicin_3d.sdf
```
#

## ✍️ Preparar el Comando de Docking
Esta vez, no crearemos un archivo `config.txt`. En su lugar, le pasamos todos los argumentos directamente al script de Python (`inference.py`).

Para esto, podemos re-utilizar exactamente las mismas coordenadas de la caja que calculamos en el tutorial de Vina/PLANTS. 

Este es el comando completo para ejecutar el docking:
```bash 
python -m inference --protein_path protein_NorA.pdb \
                    --ligand_description capsaicin_3d.sdf \
                    --out_dir results_diffdock \
                    --inference_steps 20 \
                    --samples_per_complex 10 \
                    --center_x 137.580 \
                    --center_y 140.468 \
                    --center_z 110.404 \
                    --size_x 56.372 \
                    --size_y 60.447 \
                    --size_z 99.953
```

## 🏁 Ejecutar el Docking
Simplemente copia y pega el bloque de comando completo en tu terminal y presiona Enter.

⚠️ Esto será MUCHO más lento. Vina y PLANTS tardaron segundos. DiffDock, al ser un modelo de aprendizaje profundo, puede tardar varios minutos (5-15 min) en correr para un solo ligando.

Verás un montón de texto en la terminal mientras el modelo "razona" y prueba las poses.





4. 📊 Revisar los Resultados
DiffDock creará una nueva carpeta llamada results_diffdock. Dentro de esta, creará otra carpeta con un nombre largo (basado en tus archivos de entrada).

Navega a la carpeta de resultados:

```bash 
cd results_diffdock/
ls
```
(Verás el nombre de la carpeta de resultados, entra en ella)

``` bash
cd complex_... (el nombre que te aparezca)
ls
```
Encuentra los resultados: Verás varios archivos. Los más importantes son:

`ligand_capsaicin_3d_confidence_..._rank1.sdf`: Esta es tu pose ganadora. Es el archivo `.sdf` de la pose 3D con la mejor confianza.

`ligand_capsaicin_3d_..._all.sdf`: Contiene todas las 10 poses que le pedimos.

Para ver el Score (Confianza): El score de confianza está en el nombre del archivo. Será un número como -1.5 o -2.3. Al igual que en Vina/PLANTS, el número más negativo (más bajo) es el mejor.

```bash
tail nohup.out
```


# Técnicas Experimentales: Validación con IA usando DiffDock (NorA)
---
Para finalizar nuestra investigación, realizaremos un cribado virtual totalmente independiente utilizando **Inteligencia Artificial**.

Utilizaremos **DiffDock**, un modelo de difusión generativa, para analizar nuestra librería de candidatos de DrugBank. El objetivo es evaluar toda la librería "a ciegas" (sin conocer los resultados de Vina o PLANTS) y determinar qué compuesto selecciona la IA como el mejor candidato para inhibir la bomba NorA, basándonos en su **Score de Confianza**.

**Nota sobre el rendimiento:** DiffDock es computacionalmente intensivo. El análisis de toda la librería puede tomar tiempo considerable (horas), por lo que ejecutaremos el proceso en segundo plano.

---

## Data
### Archivos necesarios
Trabajaremos en el directorio de instalación de DiffDock, pero necesitamos traer los archivos que generamos en los tutoriales anteriores.

* `protein_NorA.pdb` (El receptor limpio)
* `capsaicin_3d.sdf` (El ligando de referencia)
* `drugbank_library.sdf` (La librería de donde extraeremos al ganador)

---

## Procedimiento

### 📂 1. Preparación del Entorno
DiffDock es sensible a las dependencias, por lo que debemos ser estrictos con su entorno.

1. Activa DiffDock:
```bash
conda activate diffdock
```

2. Muévete al directorio del programa (DiffDock se ejecuta desde su propia carpeta):
```bash 
cd /DISCO1/Aylin/luisc/DiffDock
```

3. Copia la libreria de drugbank desde tu carpeta de proyectos anteriores:
```bash 
cp /DISCO1/Aylin/luisc/docking_capsaicin/drugbank_library.sdf .
```

### 📂 Preparación de la Librería
DiffDock requiere procesar un ligando a la vez o mediante un archivo CSV. Para este screening, separaremos nuestra librería en archivos individuales .sdf.

1. Crea una carpeta para los ligandos individuales:
```bash 
mkdir library_sdfs
```

2. Usa Open Babel para "explotar" la librería en archivos separados:
```bash 
obabel drugbank_library.sdf -O library_sdfs/cand.sdf -m
```
**Resultado**: Tendrás archivos `cand1.sdf`, `cand2.sdf`, etc., dentro de la carpeta `library_sdfs`.

### 🚀 Ejecución del Screening (Script Automático)
Crearemos un script de Bash que tome cada archivo `.sdf` de la carpeta, ejecute DiffDock y organice los resultados.

1. Crea el script de ejecución:
```bash 
nano run_diffdock_screening.sh
```

2. Pega el siguiente código:
```bash 
#!/bin/bash

# --- Configuración ---
PROTEIN="protein_NorA.pdb"
LIGAND_DIR="library_sdfs"
OUTPUT_BASE="results_screening"

# Crear carpeta base de resultados
mkdir -p $OUTPUT_BASE

echo "=== INICIANDO SCREENING CON DIFFDOCK ==="

# --- Bucle sobre cada ligando ---
for LIGAND_FILE in $LIGAND_DIR/*.sdf; do
    
    # Obtener nombre base (ej. cand1)
    BASENAME=$(basename "$LIGAND_FILE" .sdf)
    
    echo "Procesando: $BASENAME"
    
    # Definir carpeta de salida específica para este ligando
    OUT_DIR="${OUTPUT_BASE}/${BASENAME}"
    
    # Ejecutar DiffDock
    # Nota: Ya no tiene la barra "\" al final
    python -m inference --protein_path "$PROTEIN" \
                        --ligand_description "$LIGAND_FILE" \
                        --out_dir "$OUT_DIR" \
                        --inference_steps 20 \
                        --samples_per_complex 10 \
                        --batch_size 1
                        
    echo "Terminado: $BASENAME"
done

echo "=== SCREENING FINALIZADO ==="
```

4. Ejecutar en Segundo Plano (**IMPORTANTE**): Dado que este proceso tardará horas, usaremos nohup para que siga corriendo aunque te desconectes.
```bash 
chmod +x run_diffdock_screening.sh
```
```bash 
nohup ./run_diffdock_screening.sh > progress_log.txt 2>&1 &
```

4.4. Para moniterar el avance del proceso utiliza el siguiente comando:
```bash 
tail -f progress_log.txt
```

### 📊 Análisis de Resultados
Una vez que el proceso termine, tendremos muchas carpetas con resultados dispersos. DiffDock pone el Score de Confianza en el nombre del archivo de salida (ej. confidence_-1.24_rank1.sdf).

Por lo que, necesitamos un script para recolectar esos scores y ver quién ganó.

1. Crea el script de análisis:
```bash 
nano analyze_results.sh
```

2. Pega el código:
```bash 
#!/bin/bash

echo "Ligand,Confidence_Score" > ranking_diffdock.csv

# Buscar en todas las carpetas el archivo 'rank1' con confianza
# El patrón busca archivos que empiecen con "rank1_confidence"
find results_screening -name "rank1_confidence*.sdf" | while read filepath; do
    
    # El nombre es tipo: .../rank1_confidence-1.21.sdf
    
    # 1. Extraer el nombre del archivo (rank1_confidence-1.21.sdf)
    FILENAME=$(basename "$filepath")
    
    # 2. Extraer el score
    # Usamos 'sed' para borrar todo hasta "confidence" y luego borrar el ".sdf"
    SCORE=$(echo "$FILENAME" | sed 's/.*confidence//' | sed 's/.sdf//')
    
    # 3. Extraer el nombre del ligando de la ruta (ej. cand1)
    # La ruta es results_screening/cand1/complex_0/...
    # Usamos awk con separador "/" y tomamos la columna 2
    LIGAND=$(echo "$filepath" | awk -F/ '{print $2}')
    
    echo "$LIGAND,$SCORE" >> ranking_diffdock.csv
done

# Ordenar los resultados
# NOTA IMPORTANTE PARA DIFFDOCK:
# A diferencia de Vina, en DiffDock un score MAYOR (más positivo o menos negativo) 
# suele indicar mayor confianza.
# Ordenamos de Mayor a Menor (-r) para ver los mejores arriba.
sort -t ',' -k2 -nr ranking_diffdock.csv > ranking_sorted.csv

echo "Ranking generado en: ranking_sorted.csv"
echo "--- TOP 10 CANDIDATOS (Mayor Confianza) ---"
head -n 10 ranking_sorted.csv
```

3. Ejecuta el análisis:
```bash 
bash analyze_results.sh
```

### Interpretación
El archivo ranking_sorted.csv te mostrará el Top 10 según la Inteligencia Artificial.

Compara con PLANTS: ¿El ganador de DiffDock es el mismo ligando (ej. cand24) que ganó en PLANTS?

Compara con Capsaicina: Recuerda correr DiffDock también para capsaicin_3d.sdf para tener tu punto de referencia.

#### Identificar al Ganador y el score de la Capsaicina

1. Si el ganador es, por ejemplo, cand24, busca ese archivo en tu carpeta de ligandos para saber qué droga es (usando obabel o abriéndolo):
```bash 
head library_sdfs/cand24.sdf
```
(Luego busca esa estructura o ID en el archivo original drugbank_library.sdf usando grep).

2. Encuentra el Score de la Capsaicina 
Estos resultados se guardaron en la carpeta `results_diffdock`. Diffdock crea una subcarpeta llamada `complex_0` y ahí pone los resultados del docking con la Capsaicina.
```bash 
cd results_diffdock_capsaicin/complex_0/
```

2.2 Busca el archivo `.sdf` que diga `rank1`
    - Tu Score de Capsaicina: Es el número que está en el nombre (ej. `rank1_confidence-0.28.sdf`).
