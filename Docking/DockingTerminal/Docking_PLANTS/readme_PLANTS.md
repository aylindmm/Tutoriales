
# Bienvenido al Tutorial 
# Acoplamiento molecular paralelo utilizando el software PLANTS
PLANTS (Protein ligand ANT System) es un software de acoplamiento molecular, utilizado en el potencia descubrimiento y diseño de fármacos asistido por computadora.

Introducción a PLANTS
PLANTS se basa en un algoritmo de optimización estocástica conocido como **optimización por colonia de hormigas** (ant colony optimization o ACO), una técnica de inteligencia de enjambre inspirada en el comportamiento de las hormigas reales al buscar alimentos. Este algoritmo ayuda a explorar eficazmente el vasto espacio conformacional para encontrar las poses (orientaciones y conformaciones) de unión más favorables energéticamente.

Utiliza funciones de puntuación empíricas, como PLP, PLP95 y ChemPLP, para evaluar y clasificar las diferentes poses generadas durante el proceso de acoplamiento. ChemPLP es la función de puntuación predeterminada y tiene en cuenta las interacciones de van der Waals, los términos repulsivos y los puentes de hidrógeno dependientes del ángulo.

A diferencia de algunos métodos que tratan el receptor como una estructura rígida, PLANTS permite cierto grado de flexibilidad, incluyendo la de las cadenas laterales de los aminoácidos y la inclusión de moléculas de agua esenciales en el sitio de unión, lo que resulta en predicciones más realistas.

**Aplicaciones**: Es una herramienta versátil utilizada para el cribado virtual de grandes bibliotecas de compuestos, la optimización de compuestos líderes (leads) y la elucidación de los mecanismos de interacción molecular. Puede manejar miles o incluso miles de millones de compuestos para identificar candidatos potenciales a fármacos de manera eficiente.


## Requisitos
### Paqueteria necesaria para este flujo de trabajo:
- PLANTS 
- Open Babel
- Python (con librería RDKit)

## Procedimiento 
## Data
### 📂 Entorno de trabajo
Para empezar este tutorial, primero tenemos que preparar el entorno de trabajo
1. Asegurate de estar en tu entorno de trabajo, en donde, además de la herramienta, también tengas instalado la herramienta de `obabel` 
```bash 
conda activate plants
```
2. Crea una carpeta para tu proyecto:

```bash 
mkdir docking_capsaicin

cd docking_capsaicin
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

## ⚙️ Preparar los Archivos (Formato Mol2)
Necesitamos convertir nuestros tres archivos de trabajo (`protein_NorA.pdb`, `ligand_Fab36.pdb` y `capsaicin_3d.sdf`) al formato .mol2 que PLANTS entiende.

### 1. Prepara la Proteína (Receptor): 
```bash 
obabel protein_NorA.pdb -O protein_NorA.mol2 -p 7.4
```
Al finalizar el proceso, deberas ver el mensaje "1 molecule converted". Esto crea ``protein_NorA.mol2`` (la cadena Z) con los hidrógenos correctos.

### 2. Prepara el Ligando (Capsaicina):
```bash 
obabel capsaicin_3d.sdf -O ligand_capsaicin.mol2 -p 7.4 --gen3d
```
Al finalizar el proceso, deberas ver el mensaje "1 molecule converted". Esto crea `ligand_capsaicin.mol2` (nuestro ligando de prueba).

### 3. Prepara el Ligando de Referencia (Fab36): (Este lo usaremos para definir la caja, tal como hicimos en Vina).
```bash 
obabel ligand_Fab36.pdb -O ligand_Fab36.mol2 -p 7.4
```
Al finalizar el proceso, deberas ver el mensaje "1 molecule converted". Esto crea ligand_Fab36.mol2 (cadenas H y L).
_____________________________________

## 📦 Definir la Caja de Docking (El método de PLANTS)
Este paso es muy diferente a otras herramientas de Docking, por ejemplo a Vina. En lugar de calcular coordenadas X/Y/Z con `awk`, usaremos la función `--mode bind de PLANTS`.

Este comando lo que hace, es leer la proteína (`protein_NorA.mol2`) y el ligando de referencia (`ligand_Fab36.mol2`) y crea un archivo de definición de sitio de unión (`bindingsite.def`) basado en el centro del ligando de referencia, con un radio de 10 Å (como lo recomienda el tutorial de `parallel-PLANTS` https://github.com/discoverdata/parallel-PLANTS).

1. Ejecuta el siguiente comando:
```bash 
PLANTS1.2 --mode bind ligand_Fab36.mol2 10 protein_NorA.mol2
```
Al finalizar el proceso, verás un nuevo archivo llamado `bindingsite.def` en tu carpeta de trabajo.


## ✍️ Crear el Archivo de Configuración de PLANTS
Ahora, crearemos un archivo de configuración para PLANTS.

1. Usaremos `nano` para crear el archivo:
```bash 
nano plants.conf
```

2. Copia y pega el siguiente texto completo dentro de `nano.` Esto le dice a PLANTS dónde está todo:
```bash 
# --- Archivos de entrada ---
protein_file protein_NorA.mol2
ligand_file ligand_capsaicin.mol2
bindingsite_file bindingsite.def

# --- Directorio de salida ---
output_dir results_plants

# --- Precisión del Docking ---
# (Opciones comunes: speed1, speed2, speed4)
search_speed speed1
```
Guarda y cierra `nano.`

## 🏁 Ejecutar el Docking con PLANTS
1. Ahora, le pasamos nuestro archivo de configuración a PLANTS.
```bash 
PLANTS1.2 --mode screen plants.conf
```
PLANTS comenzará a trabajar y creará una nueva carpeta llamada `results_plants`. Dentro de esta carpeta, se guardará las poses y los scores.

## 📊 Revisar los Resultados 
Los resultados de PLANTS son claros y faciles de visualizar. 

1. Dirigete a la carpeta de resultados generada por PLANTS:
```bash 
cd results_plants
```

2. Mira el ranking: PLANTS crea un archivo de ranking. Para ver el mejor score, usa cat en el archivo que empieza con `ranking`...
```bash 
cat ranking.csv
```
Esto te mostrará una tabla CSV. La columna más importante es TOTAL_SCORE. Al igual que en Vina, el número más negativo es el mejor.

2.2 El archivo `bestranking.csv` contendrá la línea con el mejor score.

2.3 El archivo `docked_ligands.mol2` contiene las coordenadas 3D de todas las poses que PLANTS calculó. La "Pose 1" dentro de este archivo es la estructura "ganadora". Descargala y visualizala en PyMOL. 

2.4 El archivo `protein_NorA.mol2` contiene la proteína NorA. Descargala, ya que, es necesaria para poder visualizar `docked_ligands.mol2` en PyMOL


# Virtual Screening DrugBank - NorA
En los pasos anteriores, evaluamos el acoplamiento molecular de la **Capsaicina** frente a la bomba de expulsión **NorA**. Ahora, llevaremos el estudio un paso más allá realizando un **Cribado Virtual (Virtual Screening)**.

Utilizaremos la base de datos **DrugBank**, ⚠disponible localmente⚠, que contiene miles de fármacos aprobados y experimentales, para buscar compuestos que cumplan dos criterios:
1.  Sean estructuralmente similares a la Capsaicina (Tanimoto > 0.4).
2.  Cumplan con la Regla de 5 de Lipinski (propiedades farmacológicas favorables).
3.  Tengan una mejor afinidad de unión teórica que la Capsaicina en el sitio activo de NorA.

---

## Data
### Archivos necesarios
Para este tutorial, continuaremos trabajando en el directorio donde realizaste el docking individual. Necesitamos los siguientes archivos generados previamente:

* `protein_NorA.mol2` (El receptor preparado)
* `bindingsite.def` (La definición de la caja/sitio de unión)
* `capsaicin_3d.sdf` (Nuestra referencia para la búsqueda)
* **Base de Datos DrugBank:** Ubicada en el servidor en `/DISCO1/Aylin/Data_Bases/Drugbank/3D structures.sdf`

---

## Procedimiento

### 📂 Preparación del Entorno
1. Asegúrate de tener activado el entorno donde se encuentran tus herramientas (Open Babel y RDKit). Si seguiste la configuración recomendada:

```bash
conda activate plants
```
2. Instala RDKit (necesario para filtrar la base de datos química) si aún no lo has hecho:
```bash 
conda install -c conda-forge rdkit
```

3. Ubícate en tu carpeta de trabajo: `docking_capsaicin``

### Filtrado de la Base de Datos (DrugBank)
La base de datos completa es demasiado grande para procesarla en su totalidad. Por lo que, utilizaremos un script de Python para seleccionar únicamente los compuestos que sean estructuralmente similares a la Capsaicina (Tanimoto > 0.4) y cumplan con la Regla de 5 de **Lipinski**.

1. Crea el script de filtrado:
```bash 
nano filter_drugbank_plants.py
```

2. Copia y pega el siguiente código. Este script leerá la base de datos local y va a generar un archivo con los candidatos seleccionados.

```Python 
import os
from rdkit import Chem
from rdkit.Chem import DataStructs, Descriptors

# --- CONFIGURACIÓN ---
# Ruta a la base de datos DrugBank en el servidor
DB_PATH = "/DISCO1/Aylin/Data_Bases/Drugbank/3D structures.sdf"
# Ruta a nuestra Capsaicina de referencia
REF_PATH = "capsaicin_3d.sdf"
# Archivo de salida
OUT_PATH = "drugbank_library.sdf"

# --- PARÁMETROS ---
TANIMOTO_CUTOFF = 0.4  # Similitud > 40%

print("--- Iniciando Filtrado de DrugBank para PLANTS ---")

# 1. Cargar la referencia (Capsaicina)
ref_suppl = Chem.SDMolSupplier(REF_PATH)
ref_mol = next(ref_suppl)
if ref_mol is None:
    print("Error: No se pudo leer capsaicin_3d.sdf")
    exit()

ref_fp = Chem.RDKFingerprint(ref_mol)
print(f"Referencia cargada: Capsaicina")

# 2. Preparar el archivo de salida
writer = Chem.SDWriter(OUT_PATH)
count_total = 0
count_hits = 0

# 3. Leer DrugBank y filtrar
print(f"Leyendo base de datos desde: {DB_PATH}")

try:
    suppl = Chem.SDMolSupplier(DB_PATH)
    for mol in suppl:
        if mol is None: continue
        count_total += 1
        
        try:
            # A. Filtro de Lipinski
            mw = Descriptors.MolWt(mol)
            logp = Descriptors.MolLogP(mol)
            donors = Descriptors.NumHDonors(mol)
            acceptors = Descriptors.NumHAcceptors(mol)
            
            if mw > 500 or logp > 5 or donors > 5 or acceptors > 10:
                continue 

            # B. Filtro de Similitud
            target_fp = Chem.RDKFingerprint(mol)
            similarity = DataStructs.TanimotoSimilarity(ref_fp, target_fp)
            
            if similarity >= TANIMOTO_CUTOFF:
                # Usar el nombre del producto o un ID genérico
                name = mol.GetProp("GENERAL_INFORMATION_PRODUCT_PRODUCT_NAME") if mol.HasProp("GENERAL_INFORMATION_PRODUCT_PRODUCT_NAME") else f"DB_{count_total}"
                mol.SetProp("_Name", name)
                writer.write(mol)
                count_hits += 1
                if count_hits % 10 == 0: print(f"Encontrados {count_hits} compuestos...")
                    
        except: continue

except OSError:
    print(f"Error: No se encontró la base de datos")
    exit()

writer.close()
print(f"Guardados: {count_hits} compuestos en '{OUT_PATH}'")
```

3. Ejecuta el script.
```bash 
python filter_drugbank_plants.py
```
Al finalizar el proceso, tendrás un archivo `drugbank_library.sdf` con tus candidatos.


### Preparar la Librería (Formato Multi-Mol2)
A diferencia de otras herramientas que requieren un archivo por ligando, PLANTS tiene la ventaja de procesar un solo archivo que contenga múltiples moléculas.

Usaremos Open Babel para convertir nuestra librería filtrada (.sdf) a un único archivo .mol2 que contenga todos los ligandos.

1. Ejecuta la conversiónl
```bash 
obabel drugbank_library.sdf -O ligand_library.mol2 -p 7.4 --gen3d
```
**Nota**: Al no usar la bandera `-m`, Open Babel apilará todas las moléculas en un solo archivo `ligand_library.mol2`.

### Configuración del Screening
Ahora, necesitamos crear un nuevo archivo de configuración para nuestra nueva librería de ligandos.

1. Crea el archivo de configuración:
```bash 
nano plants_screening.conf
```

2. Pega el siguiente contenido:

```bash 
# --- Archivos de entrada ---
protein_file protein_NorA.mol2
ligand_file ligand_library.mol2

# --- Definición del Sitio de Unión (Directamente aquí) ---
bindingsite_center 138.174 139.541 106.735
bindingsite_radius 18

# --- Directorio de salida ---
output_dir results_screening_plants

# --- Configuración de Escritura ---
write_multi_mol2 1

# --- Precisión del Docking ---
search_speed speed1
```

### Ejecutar el Screening
PLANTS detectará automáticamente que el archivo de entrada contiene múltiples moléculas y ejecutará el docking secuencialmente para cada una.

1. Ejecuta PLANTS
```bash 
PLANTS1.2 --mode screen plants_screening.conf
```
El proceso comenzará y verás el progreso molécula por molécula en la terminal.

## 📊 Resultados y Análisis
Una vez finalizado el proceso, PLANTS generará la carpeta `results_screening_plants`.

**Identificar a los mejores candidatos**: PLANTS genera un archivo llamado `ranking.csv` que contiene una lista ordenada de todos los resultados.

1. Entra a la carpeta de resultados:
```bash 
cd results_screening_plants
```

2. Para ver los 10 mejores compuestos (los scores más negativos en la columna TOTAL_SCORE), utiliza el siguiente comando para ordenar y visualizar:
```bash 
sort -t ',' -k2 -n ranking.csv | head -n 10
```
**Nota**: El comando sort ordena numéricamente por la segunda columna, que corresponde al Score).

## Visualización
- El archivo `docked_ligands.mol2` contendrá todas las poses generadas.

- La primera molécula en este archivo corresponde al mejor resultado del ranking.

- Puedes descargar este archivo y visualizarlo en PyMOL junto con tu proteína `protein_NorA.mol2`.


## Identificar al Fármaco Ganador
El ranking te dará un código (por ejemplo, DB_7336). Para saber el nombre real del fármaco, buscaremos ese código dentro de nuestra librería original:

```bash 
grep -A 100 "DB_7336" drugbank_library.sdf
```
Para validar si haz encontrado mejores candidatos, compara los resultados del screening con el score obtenido para la Capsaicina en los pasos anteriores.
