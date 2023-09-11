---
title: "Ordenando los datos de secuenciacion masiva"
teaching: 60
exercises: 30
questions:
- "¿Como proceso los archivos obtenidos de secuenciación masiva?"
objectives:
- "Aprender a renombrar y mover archivos usando funciones de bash"
keypoints:
- "Usar archivos de textos como metadata para ordenar experimentos bioinformaticos"
---

## Explorar y ordenar los archivos del curso

Primero revisamos si estamos en la carpeta Taller_NGS
~~~
pwd
~~~
{: .language-bash}

~~~
/mnt/c/ubuntu/Taller_NGS
~~~
{: ..output}


Para ver si hemos descomprimido bien todos los archivos revisamos con ls. Añadimos la opción -R para visualizar también el contenido incluido en la carpeta reference_genome

~~~
ls -R
~~~
{: .language-bash}

~~~
.:

metadata.txt
ATAC_1.fastq.gz
ATAC_2.fastq.gz
3A_Sc_DBVPG6765_1.fastq.gz
3A_Sc_DBVPG6765_2.fastq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAABRAAPEI-P20B8_1.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAABRAAPEI-P20B8_2.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAHRABPEI-P92H8_1.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAHRABPEI-P92H8_2.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAANRABPEI-P69F9_1.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAANRABPEI-P69F9_2.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAORABPEI-P81G9_1.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAORABPEI-P81G9_2.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAURABPEI-P58E10_1.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAURABPEI-P58E10_2.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAYRABPEI-P11A11_1.fq.gz
FCHTNGHCCXY_L8_HKRDYEAwgtTAAYRABPEI-P11A11_2.fq.gz

./reference_genome:
sacCer3.ensGene.gtf
sacCer3.fa
~~~
{: ..output}



El archivo metadata.txt contiene información sobre el origen de cada archivo de secuenciación, indicando la cepa de levadura, la réplica biológica, el experimento (MS300,MS60,YPD), y el tipo de ensayo que se obtuvo de ese experimento (RNAseq, DNAseq, ATACseq).

Para revisarlo en pantalla (y aprovechando que es un archivo tabulado relativamente pequeño) usaremos cat


~~~
cat metadata.txt
~~~
{: .language-bash}


~~~
WExNA	1	RNAseq	MS300	FCHTNGHCCXY_L8_HKRDYEAwgtTAAHRABPEI-P92H8_1.fq.gz	FCHTNGHCCXY_L8_HKRDYEAwgtTAAHRABPEI-P92H8_2.fq.gz
WExNA	2	RNAseq	MS300	FCHTNGHCCXY_L8_HKRDYEAwgtTAANRABPEI-P69F9_1.fq.gz	FCHTNGHCCXY_L8_HKRDYEAwgtTAANRABPEI-P69F9_2.fq.gz
WExNA	3	RNAseq	MS300	FCHTNGHCCXY_L8_HKRDYEAwgtTAAURABPEI-P58E10_1.fq.gz	FCHTNGHCCXY_L8_HKRDYEAwgtTAAURABPEI-P58E10_2.fq.gz
WExNA	1	RNAseq	MS60	FCHTNGHCCXY_L8_HKRDYEAwgtTAABRAAPEI-P20B8_1.fq.gz	FCHTNGHCCXY_L8_HKRDYEAwgtTAABRAAPEI-P20B8_2.fq.gz
WExNA	2	RNAseq	MS60	FCHTNGHCCXY_L8_HKRDYEAwgtTAAORABPEI-P81G9_1.fq.gz	FCHTNGHCCXY_L8_HKRDYEAwgtTAAORABPEI-P81G9_2.fq.gz
WExNA	3	RNAseq	MS60	FCHTNGHCCXY_L8_HKRDYEAwgtTAAYRABPEI-P11A11_1.fq.gz	FCHTNGHCCXY_L8_HKRDYEAwgtTAAYRABPEI-P11A11_2.fq.gz
WE	1	DNAseq	YPD	3A_Sc_DBVPG6765_1.fastq.gz	3A_Sc_DBVPG6765_2.fastq.gz
WExNA	1	ATACseq	MS300	ATAC_1.fastq.gz	ATAC_2.fastq.gz
~~~
{: ..output}



Aprovecharemos los datos de metadata para renombrar los archivos de secuenciación. Para lograr esto de forma automática correremos un loop que renombrará cada archivo registrado en metadata (columnas $5 y $6) con el nombre cepa ($1), replicado ($2), tipo de ensayo($3), y experimento ($4), a lo que le incluiremos la extension ".fq.gz".
Este script lo correremos dos veces, uno para cada archivo de las reads pareadas ("_1" o "_2")


~~~
cat metadata.txt | while read line;do name1=$(echo "$line"|awk '{print $5}');name2=$(echo "$line"|awk '{print $1"_"$2"_"$3"_"$4}');mv "$name1" "${name2}_1.fq.gz";done
~~~
{: .language-bash}


~~~
cat metadata.txt | while read line;do name1=$(echo "$line"|awk '{print $6}');name2=$(echo "$line"|awk '{print $1"_"$2"_"$3"_"$4}');mv "$name1" "${name2}_2.fq.gz";done
~~~
{: .language-bash}

> ## Cuidado!
> Para que este script sea exitoso, todos los archivos deben estar en la carpeta del script, y no deberian haber espacios en ninguno de los nombres a utilizar (lo que en general es buena práctica cuando se trabaja en unix).
> Como funciona este script? 
> Este script es un poco complejo, combina los programas CAT, AWK, y MV, y además incluye un pipe ("|") y un loop "WHILE READ". El loop "while read" nos permite generar una variable ("line") que será una fila entera del archivo metadata.txt (que lo alimentamos con cat y el pipe |)
> El hecho de que sea un loop significa que hará su tarea fila por fila hasta que llegue al final del archivo metadata.txt. Lo que hay entre "do" y "done" son los comandos necesarios para encontrar los archivos originales y renombrarlos, siempre usando la información obtenida por el "while read" desde metadata.txt   
{: .callout}

Revisemos los archivos renombrados

~~~
ls
~~~
{: .language-bash}

~~~

WE_1_DNAseq_YPD_1.fq.gz
WE_1_DNAseq_YPD_2.fq.gz
WExNA_1_ATACseq_MS300_1.fq.gz
WExNA_1_ATACseq_MS300_2.fq.gz
WExNA_1_RNAseq_MS300_1.fq.gz
WExNA_1_RNAseq_MS300_2.fq.gz
WExNA_1_RNAseq_MS60_1.fq.gz
WExNA_1_RNAseq_MS60_2.fq.gz
WExNA_2_RNAseq_MS300_1.fq.gz
WExNA_2_RNAseq_MS300_2.fq.gz
WExNA_2_RNAseq_MS60_1.fq.gz
WExNA_2_RNAseq_MS60_2.fq.gz
WExNA_3_RNAseq_MS300_1.fq.gz
WExNA_3_RNAseq_MS300_2.fq.gz
WExNA_3_RNAseq_MS60_1.fq.gz
WExNA_3_RNAseq_MS60_2.fq.gz
metadata.txt
reference_genome
~~~
{: ..output}

Ahora es buen momento para ordenar nuestros archivos en distintas carpetas, una para cada tipo de ensayo. Para hacerlo de forma automatica en bash usaremos en conjunto las herramientas "find" y "mv"

Primero tendremos que crear los directorios 

~~~
mkdir RNAseq
mkdir ATACseq
mkdir DNAseq
~~~
{: .language-bash}


El comando para mover los archivos a sus respectivas carpetas (aprovechando que incluimos el tipo de ensayo al renombrar los archivos) es el siguiente



~~~
find *_RNAseq* -exec mv -t RNAseq/ {} +
find *_ATACseq* -exec mv -t ATACseq/ {} +
find *_DNAseq* -exec mv -t DNAseq/ {} +
~~~
{: .language-bash}








{% include links.md %}
