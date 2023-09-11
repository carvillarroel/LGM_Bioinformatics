---
title: Setup
---
Para este curso es necesario contar con un sistema UNIX, por ejemplo Mac OSX, Ubuntu, o Ubuntu para Windows.
También es tener una instalación de MAMBA (conda), que nos facilitará la instalación de herramientas bioinformaticas (ver más abajo)
Por último, necesitaremos el explorador de genomas (genome browser) JBROWSE, que puede ser ejecutado en Windows, Linux, o MacOs. 


Instalaremos programas dentro de este ambiente con el comando conda install

~~~

mamba create -n mapping -c bioconda fastp fastqc bwa star multiqc salmon

~~~
{: .language-bash}

Este comando buscará en el repositorio "bioconda" las instrucciones para instalar fastp, fastqc, bwa, star, y samtools en nuestro ambiente BASE.
Samtools necesitamos instalarlo con la opcion adicional openssl=1.0 para que funcione con nuestra version de ubuntu.
Si la instalación es exitosa ya podemos utilizar todos estos programas.


{% include links.md %}
