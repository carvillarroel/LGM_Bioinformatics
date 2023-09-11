---
title: Setup
---
Para este curso es necesario contar con un sistema UNIX, por ejemplo Mac OSX, Ubuntu, o Ubuntu para Windows.
También es tener una instalación de MAMBA (conda), que nos facilitará la instalación de herramientas bioinformaticas
Por último, necesitaremos el explorador de genomas (genome browser) JBROWSE, que puede ser ejecutado directamente en Windows. 

Instalación de MAMBA : https://github.com/conda-forge/miniforge#mambaforge
Instalación de JBROWSE : https://jbrowse.org/jb2/

Instalaremos programas bioinformaticos en nuestro Linux con el comando mamba create

~~~
mamba create -n mapping -c bioconda fastp fastqc bwa star multiqc salmon qualimap
mamba create -n samtools -c bioconda samtools openssl=1.0.2m
~~~
{: .language-bash}

** Este comando buscará en el repositorio "bioconda" las instrucciones para instalar fastp, fastqc, bwa, star en el ambiente MAPPING. 
** También crearemos un ambiente para el programa SAMTOOLS donde instalaremos samtools, que también debe llevar openssl version 1.0.2m.

{% include links.md %}
