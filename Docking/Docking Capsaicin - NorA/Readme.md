# Técnicas Experimentales: Docking Capsaicin - NorA
### Luis Castillo, Aylin del Moral
---
La capsaicina actúa como un inhibidor del crecimiento bacteriano indirecto al interferir con uno de los mecanismos de resistencia más importantes de *Staphylococcus aureus*: la **bomba de expulsión NorA**. Esta bomba pertenece a la superfamilia de transportadores MFS (Major Facilitator Superfamily) y es responsable de expulsar antibióticos como las fluoroquinolonas (e.g., ciprofloxacina), disminuyendo su eficacia. Al bloquear esta bomba, la concentración intracelular de antibióticos aumenta, potenciando su efecto bactericida. En el estudio citado, la capsaicina mostró una concentración mínima efectiva (MEC) de 50 µg/mL en la cepa SA1199B de *S. aureus* (que sobreexpresa NorA), reduciendo significativamente la concentración inhibitoria mínima (MIC) de ciprofloxacina. El papel de la capsaicina no es directamente bactericida, sino que actúa como un inhibidor de la resistencia bacteriana, restaurando la eficacia de antibióticos mediante la inhibición de la bomba de eflujo NorA (Naaz et al., 2021).

A nivel molecular, los estudios de acoplamiento molecular (molecular docking) y dinámica molecular (MD) muestran que la bomba puede ser bloqueada por péptidos dentro de la cavidad de unión de la proteína NorA, específicamente interactuando con los residuos Glu222 y Arg310, mediante enlaces de hidrógeno (de 3.32 Å y 2.81 Å, respectivamente) y múltiples interacciones hidrofóbicas. Este tipo de interacción sugiere que el sitio de unión está dentro del canal transmembrana de NorA, impidiendo su función de expulsar compuestos como antibióticos o colorantes (como el bromuro de etidio), y facilitando su acumulación intracelular (Brawley et al., 2022) .

---
En esta práctica vamos a evaluar la capacidad de la capsaicina para unirse a NorA. Además, compararemos la afinidad de unión con la de otros compuestos similares.

Para realizar un protocolo de docking, es fundamental contar con la estructura de referencia de la proteína blanco, preferentemente determinada experimentalmente. Además, es ideal conocer con precisión el sitio de unión del ligando, información que puede obtenerse a partir de estructuras previamente resueltas de complejos proteína-ligando. En este caso, utilizaremos como referencia la estructura 7LO8 (https://www.rcsb.org/structure/7LO8) de Protein Data Bank, que corresponde a NorA en complejo con el anticuerpo Fab36. Esta estructura revela el bloqueo del canal de eflujo por parte de Fab36, y dicha interfaz de contacto servirá como punto de partida para modelar la interacción con la capsaicina.

El ejercicio está inspirado en el siguiente tutorial:
https://training.galaxyproject.org/training-material/topics/computational-chemistry/tutorials/cheminformatics/tutorial.html#protein-ligand-docking 
Les recomiendo revisar los tips que les dan para que se den una idea de cómo funciona la plataforma de Galaxy.

--- 

# Tutorial 
## Requisitos
•	Una cuenta en usegalaxy.com

•	Pymol → Es un software de visualización molecular. Se puede descargar aquí https://www.pymol.org/
# 

## Data
• `Ligand_Fab36.pdb` - Este archivo contiene el péptido de Fab36 que bloquea al canal NorA.
  
• `Protein_NorA.pdb` - Contiene la estructura de la proteína blanco.
  
• `CAPSAICIN.sdf` - Contiene la estructura de la capsaicina.

# 

## Procedimiento
### Carga de datos
1.	Descarga los archivos `.pdb` y `.sdf`
2.	En galaxy selecciona el botón `“Upload”` ubicado a la izquierda de la pantalla.
3.	Arrastra los 3 archivos al recuadro.
4.	Para evitar inconvenientes, a los archivos `.pdb`, en el menu desplegable de `“Auto-detect”`, seleccionar manualmente el formato `pdb`.
5.	Selecióna el boton `“Start”` encontrado en el menú de abajo.
<img width="1186" height="580" alt="Carga de datos" src="https://github.com/user-attachments/assets/e74439bd-f748-456e-b556-584dcfbc824b" />

# 

### Convertir ligando a formato mol
1.	Selecciona el boton `“Tools”` ubicado a la izquierda de la pantalla.
2.	En el buscador de herramientas, escribe `“Compound conversion”` que es la herramienta que utilizaremos y selecciona la primera ópcion.
3.	Ingresa los siguientes parametros:

  a.	**Molecular input file**: Ligand_Fab36.pdb

  b.	**Output format**: MDL MOL format (sdf, mol)

  c.	**Add hydrogens appropriate for pH**: 7.4

  d.	Los demás parametros puedes dejarlos con sus valores por defecto.
<img width="1150" height="500" alt="CC Ligand" src="https://github.com/user-attachments/assets/5992c0a8-30b1-4d78-b269-3bff047807f0" />
<img width="1153" height="100" alt="ph" src="https://github.com/user-attachments/assets/40d70b58-97a4-441d-a1cf-974be11dc3e6" />

4.	Al terminar el proceso, cambia el nombre del archivo a Ligand (MOL) y guarda los cambios.
<img width="1060" height="415" alt="name" src="https://github.com/user-attachments/assets/c79582e4-d9b5-4c77-ae99-6e4d7d158e9e" />

# 

### Convertir capsaicina a formato mol
1.	De la misma forma, con la herramienta `“Compound conversion”` ingresa los siguientes parametros.

a.	**Molecular input file**: CAPSAICIN.sdf

b.	**Output format**: MDL MOL format (sdf, mol)

c.	**Add hydrogens appropriate for pH**: 7.4

d.	Los demás parametros puedes dejarlos con sus valores por defecto.

2.	Al terminar el proceso, cambia el nombre del archivo a Capsaicin (MOL)

# 

### Convertir Capsaicina a smile
1.	De la misma forma, con la herramienta `“Compound conversion”` ingresa los siguientes parametros.
   
a.	**Molecular input file**: CAPSAICIN (MOL)

b.	**Output format**: SMILES format (SMI)

c.	Los demás parametros puedes dejarlos con sus valores por defecto.

2.	Al terminar el proceso, cambia el nombre del archivo a “Capsaicin (smi)”

# 

### Construir una librería de compuestos similares a la Capsaicina
1.	En el buscador de herramientas, escribe “ChEMBL database” que es la herramienta que utilizaremos y selecciona la primera ópcion.
2.	Ingresa los siguientes parámetros:

a.	**SMILES input type**: File

b.	**Input File**: Capsaicin (smi)

c.	**Search type**: Similarity

d.	**Tanimoto cutoff score**: 40

e.	**Filter for Lipinski´s Rule of Five**: Yes

f.	Los demás parametros dejarlos con sus valores por defecto

3.	Al terminar el proceso, cambia el nombre del archivo a `“CheMBL Similar Compounds”`.

<img width="1198" height="805" alt="ChEMBL" src="https://github.com/user-attachments/assets/caef9999-a54f-4c4e-a14d-8b3fdce71421" />

# 

### Concatenar los compuestos encontrados con la capsaicina
1.	En el buscador de herramientas, escribe `“Concatenate dataset”` que es la herramienta que utilizaremos y selecciona la primera ópcion.
2.	Ingresa los siguientes parámetros:
a.	**Datasets to concatenate**: Capsaicin (smi)
b.	Haz clic en **Insert Dataset** y, en el nuevo cuadro de selección que aparece, selecciona el archivo: CheMBL Similar Compounds
3.	Ejecuta el proceso
4.	Al terminar el proceso, cambia el nombre al conjunto de datos de salida como “Compound library”
<img width="758" height="352" alt="concatenar" src="https://github.com/user-attachments/assets/ca818969-91cd-4e31-9046-1029f20f626f" />

# 
___
# Preparar archivos para docking
Para el siguiente paso, es necesario aplicar un paso de procesamiento a la estructura de la proteína y a los candidatos de docking: cada una de las estructuras debe convertirse al formato PDBQT antes de utilizar la herramienta de docking `AutoDock Vina`.

Además, el docking requiere definir las coordenadas de un sitio de unión. En la práctica, esto define una “caja” en la que el software intentará encontrar el sitio de unión óptimo. En este caso, ya conocemos la ubicación del sitio de unión, ya que la estructura PDB descargada contiene un ligando unido. Existe una herramienta en Galaxy que permite crear automáticamente un archivo de configuración para docking cuando ya se conocen las coordenadas del ligando.

___
# Tutorial
## Generar archivos PDBQT y de configuración para docking
### Preparar el receptor
1. Busca la herramienta `Prepare receptor`
2. Selecciona el archivo PDB: `Protein_NorA.pdb`
3. Ejecuta el proceso: `Run Tool`
4. Al finalizar el proceso, renombra el archivo generado a `Protein_NorA PDBQT`

# 

### Conversión de compuestos
1. Busca la herramienta `Compound conversion` y ingresa los siguientes parámetros:

a. **Molecular input file**: Compound library

b. **Output format**: MDL MOL format (sdf, mol)

c. **Generate 3D coordinates**: Yes

d. **Add hydrogens appropriate for pH**: 7.4

e. Los demás parametros como default y ejecutar el proceso.

2. Al finalizar el proceso renombrar el archivo como `Prepared ligands`

# 

### Calculator la caja de búsqueda

1. Busca la herramienta `Calculate the box parameters using RDKit`y ingresa los siguientes parámetros:

a. **Input ligand or pocket**: Ligand (MOL)

b. **x-axis buffer**: 5

c. **y-axis buffer**: 5

d. **z-axis buffer**: 5

e. **Random seed**: 1

2. Al finalizar el proceso renombrar el archivo generado como `Box configuration RDKit`

# 

### Para evitar errores 
#### Por los ligandos defectuosos, converetiremos las moléculas a pdbqt
1. Busca la herramienta `Prepare ligands for docking` y ingresa los parametros como se ve en la siguiente imagen:
2. Al finalizar el proceso renombra el archivo generado como `Prepared ligands collection`
<img width="756" height="438" alt="Captura de pantalla 2025-10-16 a la(s) 5 02 41 p m" src="https://github.com/user-attachments/assets/4da66d8f-ca95-404d-a794-3f39e4cf522e" />

# 

### Correr el docking utilizando AutoDock Vina
1. Busca la herramienta `VINA Docking` y ingresa los siguientes parámetros:

a. **Receptor**: `Protein_NorA PDBQT`

b. **Ligands** (selecciona `Dataset colection` para que puedas seleccionar el archivo): `Prepared ligands collection` (Los archivos pdbqt que generamos en el paso anterior)
<img width="156" height="75" alt="Captura de pantalla 2025-10-16 a la(s) 5 48 51 p m" src="https://github.com/user-attachments/assets/0a5a61c5-6fbb-4c01-b702-0e9e974aca8a" />


c. **Specify pH value for ligand protonation**: 7.4

d. **Specify parameters**: Upload a config file to specify parameters

e. **Box configuration**: `Box configuration RDKit`

d. **Exhaustiveness**: dejar en blanco

2. Al finalizar el proceso renombra el archivo generado como `Docking Results` 

**Nota**: este paso les seguirá generando un error "⚠", pero pueden ignorarlo. <img width="284" height="74" alt="Captura de pantalla 2025-10-16 a la(s) 9 17 32 p m" src="https://github.com/user-attachments/assets/162c0468-bff0-43fd-8faf-7dee3c25229c" />

---
Si todo sale bien, deben obtener un directorio con los resultados para cada ligando, para generar una tabla que contenga cada ligando y el score obtenido, ejecutaremos los siguientes comandos.

---
#### 1. Busca la herramienta `Flatten collection` y ingresar los siguientes parámetros:
a. **Input Collection**: Docking Results

b. **Join collection identifiers using**: underscore (_) 

1.1. Al finalizar el proceso renombra el archivo generado como `Docking Data Flattened` 

# |

#### 2. Busca la herramienta `Extract values from an SD-file` y ingresar los siguientes parámetros:
a. **Input SD-file**: Docking Data Flattened (¡Recuerda seleccionar el ícono de ‘colección’!) <img width="45" height="40" alt="Captura de pantalla 2025-10-17 a la(s) 12 09 38 a m" src="https://github.com/user-attachments/assets/9a3f2393-edea-4f19-9862-e0ba7d1a1ee3" />

b. **Include the property name as header**: Yes

c. **Include SMILES as column in output**: Yes

d. **Include molecule name as column in output**: Yes

2.1 Dejar lo demás sin cambio
2.2 Al finalizar el proceso renombra el archivo generado como `Docking SD-File` 

# |

#### 3. Busca la herramienta `Collapse Collection` y ingresar los siguientes parámetros:
a. **Collection of files to collapse into single dataset**: Docking SD-File <img width="45" height="40" alt="Captura de pantalla 2025-10-17 a la(s) 12 09 38 a m" src="https://github.com/user-attachments/assets/9a3f2393-edea-4f19-9862-e0ba7d1a1ee3" />

b. **Keep one header line**: Yes

c. **Append File name**: No

3.1. Al finalizar el proceso renombra el archivo generado como `Collapse Collection-Docking Capsaicin` 


---
El archivo resultante de este paso, nombrado como `Collapse Collection-Docking Capsaicin` pueden visualizarlo en excel, de esta forma es fácil identificar el ligando con el mejor score, el cual es el que tiene el valor más negativo.

Una vez terminado, pueden descargar el archivo de `Docking Results` el cual contiene todos los ligandos generados, y abrirlo en Pymol. Para ver el conjunto Proteína - Ligando, deben cargar en la misma sesión de Pymol el archivo Protein_NorA.pdb.

Para su reporte, incluyan la estructura del complejo capsaicina - NorA y una gráfica que muestre los scores obtenidos por todos los ligando evaluados, destacando la posición de la Capsaicina.

---

# Resultado:
<img width="710" height="622" alt="Captura de pantalla 2025-10-17 a la(s) 1 39 58 a m" src="https://github.com/user-attachments/assets/aa4b9f3a-4629-42b9-ac2f-4489aa52dc23" />
<img width="898" height="688" alt="Captura de pantalla 2025-10-17 a la(s) 1 38 31 a m" src="https://github.com/user-attachments/assets/a254a093-fc3e-4c74-9665-ff2da779ef5c" />


# Referencias

  Brawley, D. N., Sauer, D. B., Li, J., Zheng, X., Koide, A., Jedhe, G. S., Suwatthee, T., Song, J., Liu, Z., Arora, P. S., Koide, S., Torres, V. J., Wang, D.-N., & Traaseth, N. J. (2022). Structural basis for inhibition of the drug efflux pump NorA from Staphylococcus aureus. Nature Chemical Biology, 18(7), 706–712.

  Naaz, F., Khan, A., Kumari, A., Ali, I., Ahmad, F., Ahmad Lone, B., Ahmad, N., Ali Khan, I., Rajput, V. S., Grover, A., & Shafi, S. (2021). 1,3,4-oxadiazole conjugates of capsaicin as potent NorA efflux pump inhibitors of Staphylococcus aureus. Bioorganic Chemistry, 113, 105031.
