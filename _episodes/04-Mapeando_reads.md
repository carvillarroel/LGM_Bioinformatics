---
title: "Mapeando reads al genoma"
teaching: 60
exercises: 30
questions:
- "¿Como mapeo reads al genoma?"
objectives:
- "Crear indeces del genoma para los programas de mapeo"
- "Mapear reads de DNAseq y ATACseq al genoma con BWA"
- "Mapear reads de RNAseq a los genes anotados en el genoma"
keypoints:
- ""
---

## Mapeo de reads al genoma y transcriptoma

Nuestras reads son de distintos ensayos: DNAseq, ATACseq, y RNAseq. Una diferencia fundamental entre estos ensayos es el material biológico de donde provienen, que es ADN genómico (DNAseq y ATACseq) o ARN mensajero (RNAseq). Como bien sabemos la estructura del ARNm puede ser distinto al de su templado en el ADN genómico, en especial por la presencia de intrones en el ADN, y esta diferencia es importante en el momento de mapear reads a genomas de referencia. Por esto existen programas especializados en el mapeo de reads genomicas (como BWA), y programas para el mapeo de reads de transcriptoma (RNA STAR).

BWA y RNA STAR requiren realizar un index del genoma de referencia (que les permitirá realizar su trabajo de manera más eficiente), el cual lo tenemos en formato FASTA en la carpeta reference_genome


~~~
bwa index reference_genome/Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa
~~~
{: .language-bash}


~~~

[bwa_index] Pack FASTA... 0.23 sec
[bwa_index] Construct BWT for the packed sequence...
[bwa_index] 8.41 seconds elapse.
[bwa_index] Update BWT... 0.16 sec
[bwa_index] Pack forward-only FASTA... 0.11 sec
[bwa_index] Construct SA from BWT and Occ... 3.64 sec
[main] Version: 0.7.17-r1188
[main] CMD: bwa index reference_genome/sacCer3.fa
[main] Real time: 14.551 sec; CPU: 12.562 sec
~~~
{: ..output}

BWA INDEX generó archivos en la carpeta reference_genome
Ahora realizamos el index para analizar transcriptomas con RNA STAR. En este caso, también es necesario proveer de la anotación del genoma (en que posicion estan los genes codificantes) que esta en formato GTF.



~~~
mkdir star_saccer
STAR --runMode genomeGenerate --genomeDir star_saccer/ --genomeFastaFiles reference_genome/Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa --sjdbGTFfile reference_genome/Saccharomyces_cerevisiae.R64-1-1.57.gff3
~~~
{: .language-bash}

En este caso fue necesario primero crear una carpeta (star_saccer), donde se guardaron los archivos del index.

Ahora estamos listos para mapear nuestras reads (limpiadas) a los index de BWA y STAR.
Primero creamos un directorio donde dejar los archivos de mapeo (bam), y luego ejecutamos BWA para mapear las reads de DNAseq y ATACseq

~~~
mkdir bam
bwa mem -t 8 -o bam/WE_1_DNAseq_YPD_1.sam reference_genome/Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa DNAseq/cleaned_WE_1_DNAseq_YPD_1.fq.gz DNAseq/cleaned_WE_1_DNAseq_YPD_2.fq.gz
bwa mem -t 8 -o bam/WExNA_1_ATACseq_HIGH_N_1.sam reference_genome/Saccharomyces_cerevisiae.R64-1-1.dna.toplevel.fa ATACseq/cleaned_WExNA_1_ATACseq_HIGH_N_1.fq.gz ATACseq/cleaned_WExNA_1_ATACseq_HIGH_N_2.fq.gz
~~~
{: .language-bash}

Luego ejecutamos STAR para cada pareja de reads pareadas 
~~~
STAR --runThreadN 8 --readFilesIn RNAseq/cleaned_WExNA_1_RNAseq_HIGH_N_1.fq.gz RNAseq/cleaned_WExNA_1_RNAseq_HIGH_N_2.fq.gz  --genomeDir star_saccer/ --outFileNamePrefix bam/WENA_1_RNAseq_HIGH_N_  --outSAMtype BAM Unsorted --readFilesCommand zcat --outTmpDir ~/tmp/
STAR --runThreadN 8 --readFilesIn RNAseq/cleaned_WExNA_2_RNAseq_HIGH_N_1.fq.gz RNAseq/cleaned_WExNA_2_RNAseq_HIGH_N_2.fq.gz  --genomeDir star_saccer/ --outFileNamePrefix bam/WENA_2_RNAseq_HIGH_N_  --outSAMtype BAM Unsorted --readFilesCommand zcat --outTmpDir ~/tmp/
STAR --runThreadN 8 --readFilesIn RNAseq/cleaned_WExNA_3_RNAseq_HIGH_N_1.fq.gz RNAseq/cleaned_WExNA_3_RNAseq_HIGH_N_2.fq.gz  --genomeDir star_saccer/ --outFileNamePrefix bam/WENA_3_RNAseq_HIGH_N_  --outSAMtype BAM Unsorted --readFilesCommand zcat --outTmpDir ~/tmp/
STAR --runThreadN 8 --readFilesIn RNAseq/cleaned_WExNA_1_LOW_N_MS60_1.fq.gz RNAseq/cleaned_WExNA_1_LOW_N_MS60_2.fq.gz  --genomeDir star_saccer/ --outFileNamePrefix bam/WENA_1_RNAseq_LOW_N_  --outSAMtype BAM Unsorted --readFilesCommand zcat --outTmpDir ~/tmp/
STAR --runThreadN 8 --readFilesIn RNAseq/cleaned_WExNA_2_LOW_N_MS60_1.fq.gz RNAseq/cleaned_WExNA_2_LOW_N_MS60_2.fq.gz  --genomeDir star_saccer/ --outFileNamePrefix bam/WENA_2_RNAseq_LOW_N_  --outSAMtype BAM Unsorted --readFilesCommand zcat --outTmpDir ~/tmp/
STAR --runThreadN 8 --readFilesIn RNAseq/cleaned_WExNA_3_LOW_Nq_MS60_1.fq.gz RNAseq/cleaned_WExNA_3_LOW_N_MS60_2.fq.gz  --genomeDir star_saccer/ --outFileNamePrefix bam/WENA_3_RNAseq_LOW_N_  --outSAMtype BAM Unsorted --readFilesCommand zcat --outTmpDir ~/tmp/


~~~
{: .language-bash}

Hemos generado archivos SAM y BAM, que son archivos de alineamiento de reads. Muchos programas que usaremos mas adelante ocupan estos archivos, por ejemplo para visualizar mapeos, encontrar SNPs, contar reads que mapean a ciertos genes, etc.
Los archivos SAM y BAM se pueden optimizar para las siguientes tareas, primero ordenandolos y luego haciendo un index. Esto se realiza con samtools
~~~
mkdir bam/sorted_bam
samtools sort -O bam -o bam/sorted_bam/WE_1_DNAseq_YPD_1.sorted.bam bam/WE_1_DNAseq_YPD_1.sam
samtools sort -O bam -o bam/sorted_bam/WExNA_1_ATACseq_HIGH_N_1.sorted.bam bam/WExNA_1_ATACseq_HIGH_N_1.sam
samtools sort -O bam -o bam/sorted_bam/WENA_1_RNAseq_HIGH_N_Aligned.out.sorted.bam bam/WENA_1_RNAseq_HIGH_N_Aligned.out.bam
samtools sort -O bam -o bam/sorted_bam/WENA_2_RNAseq_HIGH_N_Aligned.out.sorted.bam bam/WENA_2_RNAseq_HIGH_N_Aligned.out.bam
samtools sort -O bam -o bam/sorted_bam/WENA_3_RNAseq_HIGH_N_Aligned.out.sorted.bam bam/WENA_3_RNAseq_HIGH_N_Aligned.out.bam
samtools sort -O bam -o bam/sorted_bam/WENA_1_RNAseq_LOW_N_Aligned.out.sorted.bam bam/WENA_1_RNAseq_LOW_N_Aligned.out.bam
samtools sort -O bam -o bam/sorted_bam/WENA_2_RNAseq_LOW_N_ligned.out.sorted.bam bam/WENA_2_RNAseq_LOW_N_Aligned.out.bam
samtools sort -O bam -o bam/sorted_bam/WENA_3_RNAseq_LOW_N_Aligned.out.sorted.bam bam/WENA_3_RNAseq_LOW_N_Aligned.out.bam
~~~
{: .language-bash}  

Ahora aprovechando que todos los archivos que generamos terminan en .sorted.bam, podemos correr samtools index usando un for loop:

~~~
for FILE in bam/sorted_bam/*.sorted.bam;do samtools index $FILE;done
~~~
{: .language-bash}  

Estos archivos nos servirán en la próxima sección para visualizar y realizar informes de calidad de mapeo.

{: .language-bash}
{% include links.md %}
