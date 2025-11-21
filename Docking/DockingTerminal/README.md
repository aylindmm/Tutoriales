## Requisitos
### Paqueteria necesaria para este flujo de trabajo:
- **Vina** 
- **Open Babel**
- **RDKit** 
- **Python**

#

## Procedimiento 
## Data
### Entorno de trabajo
Para empezar este tutorial, primero tenemos que preparar el entorno de trabajo
```bash 
conda activate Vina
```
#

### Crear el espacio de trabajo
1. Ve a tu carpeta principal y crea un directorio para tu proyecto
```bash 
mkdir docking_capsaicin
cd docking_capsaicin
```
#
### Descarga la Proteína (NorA + Fab36)
```bash 
wget https://files.rcsb.org/download/7LO8.pdb
```
#
### Descarga el Ligando (Capsaicina)
```bash 
wget "https://pubchem.ncbi.nlm.nih.gov/rest/pug/compound/cid/1548943/SDF?record_type=3d" -O capsaicin_3d.sdf
```
#

## Preparar los archivos
El siguiente paso es prepara los archivos que descargamos, debido a que Vina no puede leer archivos de tipo "PDB" o "SDF" directamente. Para lo que, necesitamos un formato llamado PDBQT, el cual incluye hidrógenos polares y cargas parciales a la molecula. 

#
### Preparar la Proteína (El receptor)
1. **Conoce tu archivo:** el archivo `7L08.pdb` contiene la bomba NorA, las cadenas del anticuerpo Fab36, moléculas de agua, etc. El archivo se organiza en 3 cadenas de proteínas llamadas H, L y Z, que contienen el numero de átomos para formar los aminoácidos de estas cadenas:

- 1734 `H` (Cadena Heavy o Pesada del anticuerpo)

- 1636 `L` (Cadena Light o Ligera del anticuerpo)

- 2835 `Z` (Es la proteína NorA)

`Nota`: si quieres saber exactamente qué cadenas de proteínas hay en un archivo de este tipo y cuantos átomos tiene, puedes utilizar la estructura del siguiente comando aplicado a nuestro caso:  
```bash 
grep "^ATOM " 7LO8.pdb | awk '{print $5}' | sort | uniq -c
```

2. **Limpiar el archivo PDB:** por el momento, nos interesa únicamente  la bomba NorA que corresponde a las cadenas Alfa y Beta contenidas en la `cadena Z` del archivo, para lo que, tenemos que limpiar la molecular, utilizando el siguiente comando: 

```bash 
awk '$1 == "ATOM" && $5 == "Z"' 7LO8.pdb > protein_NorA.pdb
```

Esto crea un nuevo archivo, `protein_NorA.pdb` con la proteína de interes. 

3. **Verifica**: antes de continuar, verifica que el arhcivo se haya creado con éxito.
```bash 
ls -l protein_NorA.pdb
```
Deberías de ver un tamaño de archivo de varios KBs.

4. **Convertir a PDBQT:** ahora tenemnos que convertir el archivo `protein_NorA.pdb` a formato PDBQT, añandiendo hidrógenos para un pH de 7.4.
```bash 
obabel protein_NorA.pdb -O receptor.pdbqt -xr -p 7.4
```
Debe decir al final `1 molecule converted`

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

#

### Preparar el Ligando (Capsaicina): 

1. **Convertimos el archivo SDF de la capsaicina al formato PDBQT:**
```bash 
obabel capsaicin_3d.sdf -O ligand.pdbqt -p 7.4 --gen3d
```
Debe decir al final `1 molecule converted`

#

## Verificación
Hasta este punto deberas tener los siguientes archivos en el directorio de tu proyecto:
```
7LO8.pdb          ligand_Fab36.pdb  protein_NorA.pdb
capsaicin_3d.sdf  ligand.pdbqt      receptor.pdbqt
```

## Definir la caja de Docking
Ahora vamos a calcular el centro y el tamaño de la caja basándonos en el archivo `ligand_Fab36.pdb` que acabamos de crear.
### Calcular los parámetros de la caja
1. Copia y pega el siguiente comando, el cual es un script awk de una sola línea que lee el archivo `ligand_Fab36.pdb`.
```bash 
awk 'BEGIN { \
    min_x=99999; max_x=-99999; \
    min_y=99999; max_y=-99999; \
    min_z=99999; max_z=-99999 \
} \
/^ATOM/ { \
    x=$7; y=$8; z=$9; \
    if (x < min_x) min_x = x; \
    if (x > max_x) max_x = x; \
    if (y < min_y) min_y = y; \
    if (y > max_y) max_y = y; \
    if (z < min_z) min_z = z; \
    if (z > max_z) max_z = z \
} \
END { \
    printf "center_x = %.3f\n", (min_x + max_x) / 2; \
    printf "center_y = %.3f\n", (min_y + max_y) / 2; \
    printf "center_z = %.3f\n", (min_z + max_z) / 2; \
    printf "size_x = %.3f\n", (max_x - min_x) + 10; \
    printf "size_y = %.3f\n", (max_y - min_y) + 10; \
    printf "size_z = %.3f\n", (max_z - min_z) + 10 \
}' ligand_Fab36.pdb
```
Este comando debera imprimir 6 líneas con valores `size` **positivos**.

2. Las 6 líneas deberan ser iguales o parecidas a las siguientes:

center_x = 137.580

center_y = 140.468

center_z = 110.404

size_x = 56.372

size_y = 60.447

size_z = 99.953

**⚠ Si tu input no es exactamente el mismo, no te preocupes, en el siguiente paso podras ajustarlo a tu resultado. ⚠**

### Crear el archivo de configuración
AutoDock Vina necesita un archivo de texto simple que le diga dónde están las piezas del Docking. Por lo que, vamos a crear un archivo llamado `config.txt` que contenga esta información.

1. Ejecuta el siguiente comando, el cual es un `echo` largo que escribe las 8 líneas que necesitamos (2 rutas de archivo + los 6 números). 

**⚠ Si por alguna razón, las 6 líneas no fueron exactamente iguales a las del paso anterior, aqui es donde deberas remplazar los valores de los 3 `center` y 3 `size` a los que te dieron a ti. ⚠**

```bash 
echo "receptor = receptor.pdbqt
ligand = ligand.pdbqt

center_x = 137.580
center_y = 140.468
center_z = 110.404

size_x = 56.372
size_y = 60.447
size_z = 99.953" > config.txt
```
2. **Verificación:** Esto creara el archivo `config.txt`, para verificar que se creo con éxito, puedes ver el contenido del archivo que acabas de crear con:
```bash 
cat config.txt
```
Deberas ver las 8 líneas del `echo`. 

## Ejecutar el Docking con Autodock Vina
(Recuerda que tienes que tener activado el entorno `conda` de `Vina`)

1. Para ejecutar el docking utiliza el siguiente comando:
```bash 
vina --config config.txt --out results.pdbqt &> log.txt
```

## Revisar los Scores del docking
Cuando la herramienta termine, puedes revisar los scores con el comando: 
```bash 
cat log.txt
```
Al final deberas ver un recuadro como el siguiente:
<img width="397" height="237" alt="scores 1" src="https://github.com/user-attachments/assets/de4213bb-e65c-43eb-8830-0ccd38092f0d" />


Estos son las 9 poses 3D que Vina Calculó

**Esto significa que el mejor "score" o afinidad de unión que Vina encontró para la Capsaicina en la proteína NorA es de -5.624 kcal/mol.**
#


# Virtual Screening DrugBank - NorA
En los pasos anteriores, evaluamos el acoplamiento molecular de la **Capsaicina** frente a la bomba de expulsión **NorA**. Ahora, llevaremos el estudio un paso más allá realizando un **Cribado Virtual (Virtual Screening)**.

Para lo que, utilizaremos la base de datos **DrugBank**, que contiene miles de fármacos aprobados y experimentales, para buscar compuestos que cumplan dos criterios:
1.  Sean estructuralmente similares a la Capsaicina (Tanimoto > 0.4).
2.  Cumplan con la Regla de 5 de Lipinski (propiedades farmacológicas favorables).
3.  Tengan una mejor afinidad de unión teórica que la Capsaicina en el sitio activo de NorA.

---

## Data
### Archivos necesarios
Para este tutorial, se asume que ya cuentas con los archivos generados en los pasos anteriores de este tutorial de Vina  dentro de tu carpeta `docking_capsaicin`:

* `receptor.pdbqt` (La proteína NorA preparada)
* `capsaicin_3d.sdf` (Nuestra referencia)
* `config.txt` (El archivo de configuración de la caja)

* **Base de Datos DrugBank:** Ubicada en el servidor en `/DISCO1/Aylin/Data_Bases/Drugbank/3D structures.sdf`

---

## Procedimiento

### Preparación del Entorno
Primero, necesitamos instalar la librería **RDKit** en nuestro entorno de Conda para poder manipular la base de datos química.

1. Asegurate de estar en el entorno de Vina:
```bash
conda activate Vina
```

2. Instala RDKIT
```bash 
conda install -c conda-forge rdkit
```

### Filtrado de la Base de Datos (DrugBank)
Como no podemos dockear todos los fármacos de DrugBank porque tomaría demasiado tiempo. Crearemos un script de Python para filtrar y quedarnos solo con los "parecidos" a la Capsaicina.

1. Ubícate en tu carpeta de trabajo: docking_capsaicin

2. Crea el script de filtrado:

```bash 
nano filter_drugbank.py
```

3. Copia y pega el siguiente código dentro de `nano`. Este script lee la base de datos local, aplica los filtros y guarda los resultados.

```Python 
import os
from rdkit import Chem
from rdkit.Chem import DataStructs, Descriptors

# --- CONFIGURACIÓN ---
# Ruta a la base de datos DrugBank (La que me indicaste)
DB_PATH = "/DISCO1/Aylin/Data_Bases/Drugbank/3D structures.sdf"
# Ruta a nuestra Capsaicina de referencia
REF_PATH = "capsaicin_3d.sdf"
# Archivo de salida
OUT_PATH = "drugbank_library.sdf"

# --- PARÁMETROS DE BÚSQUEDA ---
TANIMOTO_CUTOFF = 0.4  # Similitud > 40% (Igual que en Galaxy)

print("--- Iniciando Filtrado de DrugBank ---")

# 1. Cargar la referencia (Capsaicina)
ref_suppl = Chem.SDMolSupplier(REF_PATH)
ref_mol = next(ref_suppl)
if ref_mol is None:
    print("Error: No se pudo leer capsaicin_3d.sdf")
    exit()

# Generar huella digital (Fingerprint) de la capsaicina
ref_fp = Chem.RDKFingerprint(ref_mol)
print(f"Referencia cargada: Capsaicina")

# 2. Preparar el archivo de salida
writer = Chem.SDWriter(OUT_PATH)
count_total = 0
count_hits = 0

# 3. Leer DrugBank y filtrar
print(f"Leyendo base de datos desde: {DB_PATH}")
print("Esto puede tardar unos minutos dependiendo del tamaño del archivo...")

try:
    suppl = Chem.SDMolSupplier(DB_PATH)
    for mol in suppl:
        if mol is None: continue
        count_total += 1
        
        try:
            # A. Filtro de Lipinski (Regla de 5)
            mw = Descriptors.MolWt(mol)
            logp = Descriptors.MolLogP(mol)
            h_donors = Descriptors.NumHDonors(mol)
            h_acceptors = Descriptors.NumHAcceptors(mol)
            
            if mw > 500 or logp > 5 or h_donors > 5 or h_acceptors > 10:
                continue # No cumple Lipinski, saltar

            # B. Filtro de Similitud (Tanimoto)
            target_fp = Chem.RDKFingerprint(mol)
            similarity = DataStructs.TanimotoSimilarity(ref_fp, target_fp)
            
            if similarity >= TANIMOTO_CUTOFF:
                # ¡Es un hit! Guardar en la librería
                mol.SetProp("_Name", mol.GetProp("GENERAL_INFORMATION_PRODUCT_PRODUCT_NAME") if mol.HasProp("GENERAL_INFORMATION_PRODUCT_PRODUCT_NAME") else f"DrugBank_{count_total}")
                writer.write(mol)
                count_hits += 1
                if count_hits % 10 == 0:
                    print(f"Encontrados {count_hits} compuestos similares...")
                    
        except:
            continue # Si hay error con una molécula, saltar

except OSError:
    print(f"Error: No se encontró el archivo en {DB_PATH}")
    exit()

writer.close()
print("-" * 30)
print(f"Procesados: {count_total} moléculas.")
print(f"Guardados:  {count_hits} moléculas similares en '{OUT_PATH}'")
```


4. Ejecuta el script
```bash 
python filter_drugbank.py
```
Al finalizar, tendrás un archivo llamado `drugbank_library.sdf` con tus mejores candidatos.

---

### Preparar los Ligandos (PDBQT)
Como Vina necesita archivos individuales para cada ligando, usaremos Open Babel para separar nuestra librería filtrada.

1. Crea un directorio para los ligandos:
```bash 
mkdir ligands_pdbqt
```

2. Convierte y separa los compuestos:
```bash 
obabel drugbank_library.sdf -O ligands_pdbqt/lig.pdbqt --gen3d -p 7.4 -m
```
**Nota**: El comando `-m` separa las moléculas en archivos secuenciales (`lig1.pdbqt`, `lig2.pdbqt`, etc.).

### Ejecutar el Screening (Script de Bash)
Ahora automatizaremos Vina para que analice todos los archivos dentro de la carpeta `ligands_pdbqt`.

1. Crea una plantilla de configuración (quitando la línea del ligando original):
```bash 
grep -v "ligand =" config.txt > config_template.txt
```

2. Crea el script de ejecución:
```bash 
nano run_screening.sh
```
3. Pega el siguiente código en `nano`:
```bash 
#!/bin/bash

# --- Configuración ---
CONFIG_FILE="config_template.txt"
LIGAND_DIR="ligands_pdbqt"
RESULTS_CSV="screening_results.csv"

# --- Crear directorios ---
mkdir -p vina_results_poses
mkdir -p vina_results_logs

# --- Encabezado del CSV ---
echo "Ligand_File,Affinity_kcal_mol" > "$RESULTS_CSV"

echo "=== INICIANDO SCREENING VIRTUAL (DrugBank) ==="

# --- Bucle Principal ---
for LIGAND_FILE in "$LIGAND_DIR"/*.pdbqt; do

    BASENAME=$(basename "$LIGAND_FILE" .pdbqt)
    OUT_FILE="vina_results_poses/${BASENAME}_out.pdbqt"
    LOG_FILE="vina_results_logs/${BASENAME}_log.txt"
    
    echo "Procesando: $BASENAME"
    
    # Correr Vina
    vina --config "$CONFIG_FILE" --ligand "$LIGAND_FILE" --out "$OUT_FILE" &> "$LOG_FILE"
    
    # Extraer Score (toma el primer valor de afinidad)
    SCORE=$(awk '/^ *1 / {print $2}' "$LOG_FILE")
    
    if [ ! -z "$SCORE" ]; then
        echo "$BASENAME,$SCORE" >> "$RESULTS_CSV"
    else
        echo "$BASENAME,ERROR" >> "$RESULTS_CSV"
    fi
    
done

echo "=== SCREENING COMPLETADO ==="
```

4. Da permisos de ejecución y corre el script:
```bash 
chmod +x run_screening.sh
```
```bash 
./run_screening.sh
``` 
(Este proceso puede tardar varios minutos dependiendo de cuántos compuestos hayas encontrado).

## Resultados y Análisis
Al finalizar el proceso, obtendrás un archivo llamado `screening_results.csv`, este contiene una tabla con el nombre del archivo del ligando y su afinidad de unión.

**Identificar a los mejores candidatos**
Para ver los 10 mejores compuestos (los que tienen la afinidad más negativa), utiliza el siguiente comando:
```bash 
sort -t ',' -k2 -n screening_results.csv | head -n 10
```

**Interpretación**: Compara estos valores con el score obtenido por la Capsaicina pura (aprox -5.6 kcal/mol). Cualquier compuesto con un valor más negativo (ej. -7.0, -8.0) es un candidato potencial para inhibir NorA mejor que la capsaicina.


# Vizualisación de la mejor pose
Una vez identificada el archivo con el mejor candidato, descargalo en tu computadora local y sigue los siguientes pasos:
Abre PyMOL.

1. Carga tu proteína: `protein_NorA.pdb` (o receptor.pdbqt).

2. Carga la pose de la capsaicina (para comparar): `results.pdbqt`.

3. Carga la pose de tu nuevo ganador: `lig27_out.pdbqt`(en mi caso fue este archivo).

# Resultado
<img width="1200" height="1000" alt="result" src="https://github.com/user-attachments/assets/67709b1e-43eb-4147-9640-972cb27ff9a8" />

**Visualización del sitio de unión de la bomba de expulsión NorA de S. aureus (PDB: 7LO8)**

Se muestra la superposición de los modos de unión predichos mediante AutoDock Vina para la **Capsaicina** (carbonos en cian) y el compuesto candidato `lig27` (carbonos en verde). La proteína se representa en formato de cintas (gris) y los residuos del sitio activo interactuante se muestran en líneas finas. Las líneas punteadas amarillas indican posibles interacciones de puentes de hidrógeno.
