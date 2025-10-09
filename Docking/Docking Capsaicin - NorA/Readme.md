<img width="442" height="53" alt="image" src="https://github.com/user-attachments/assets/e5d0107e-87f4-4669-a0a8-ac05740b243d" /># Técnicas Experimentales: Docking Capsaicin - NorA
### Aylin del Moral
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

## Data
• `Ligand_Fab36.pdb` - Este archivo contiene el péptido de Fab36 que bloquea al canal NorA.
  
• `Protein_NorA.pdb` - Contiene la estructura de la proteína blanco.
  
• `CAPSAICIN.sdf` - Contiene la estructura de la capsaicina.

## Procedimiento
### Carga de datos
1.	Descarga los archivos `.pdb` y `.sdf`
2.	En galaxy selecciona el botón `“Upload”` ubicado a la izquierda de la pantalla.
3.	Arrastra los 3 archivos al recuadro.
4.	Para evitar inconvenientes, a los archivos `.pdb`, en el menu desplegable de `“Auto-detect”`, seleccionar manualmente el formato `pdb`.
5.	Selecióna el boton `“Start”` encontrado en el menú de abajo.
<img width="1186" height="580" alt="Carga de datos" src="https://github.com/user-attachments/assets/e74439bd-f748-456e-b556-584dcfbc824b" />

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

### Convertir capsaicina a formato mol
1.	De la misma forma, con la herramienta `“Compound conversion”` ingresa los siguientes parametros.

a.	**Molecular input file**: CAPSAICIN.sdf

b.	**Output format**: MDL MOL format (sdf, mol)

c.	**Add hydrogens appropriate for pH**: 7.4

d.	Los demás parametros puedes dejarlos con sus valores por defecto.

2.	Al terminar el proceso, cambia el nombre del archivo a Capsaicin (MOL)

### Convertir Capsaicina a smile
1.	De la misma forma, con la herramienta `“Compound conversion”` ingresa los siguientes parametros.
   
a.	**Molecular input file**: CAPSAICIN (MOL)

b.	**Output format**: SMILES format (SMI)

c.	Los demás parametros puedes dejarlos con sus valores por defecto.

2.	Al terminar el proceso, cambia el nombre del archivo a “Capsaicin (smi)”

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

### Concatenar los compuestos encontrados con la capsaicina
1.	En el buscador de herramientas, escribe `“Concatenate dataset”` que es la herramienta que utilizaremos y selecciona la primera ópcion.
2.	Ingresa los siguientes parámetros:
a.	**Datasets to concatenate**: Capsaicin (smi)
b.	Haz clic en **Insert Dataset** y, en el nuevo cuadro de selección que aparece, selecciona el archivo: CheMBL Similar Compounds
3.	Ejecuta el proceso
4.	Al terminar el proceso, cambia el nombre al conjunto de datos de salida como “Compound library”
<img width="758" height="352" alt="concatenar" src="https://github.com/user-attachments/assets/ca818969-91cd-4e31-9046-1029f20f626f" />



