## Requisitos
### Paqueteria necesaria para este flujo de trabajo:
- Vina 
- Open Babel

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
IMAGEEEEEEEN
Estos son las 9 poses 3D que Vina Calculó

**Esto significa que el mejor "score" o afinidad de unión que Vina encontró para la Capsaicina en la proteína NorA es de -5.624 kcal/mol.**
