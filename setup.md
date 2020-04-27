---
title: Setup
---
Para este curso es necesario contar con un sistema UNIX, por ejemplo Mac OSX, Ubuntu, o Ubuntu para Windows.
También es tener una instalación de CONDA, que nos facilitará la instalación de herramientas bioinformaticas (ver más abajo)
Por último, necesitaremos el explorador de genomas (genome browser) IGV, que puede ser ejecutado en Windows, Linux, o MacOs. (https://software.broadinstitute.org/software/igv/download) 

A continuación mostraré como instalar Ubuntu para Windows y dejarlo listo para el taller

## Instalación de Ubuntu para Windows (WLS) 

Ubuntu puede correr dentro de Windows 10 luego de activar algunas opciones (también es necesario tener Windows 10 actualizado)
Hay varias versiones de Ubuntu (y otras distribuciones de Linux) disponibles en la Windows App store, pero nosotros instalaremos la version Ubuntu 20.04 

![img0](../fig/image0.png)

En esa misma página se indica como activar las opciones de Windows para la ejecución de Ubuntu.

Luego podemos abrir la Ubuntu app, que nos pedirá crear un nombre de usuario

![img1](../fig/image1.png)

También nos pedirá crear un password para este usuario (al teclear el password no se imprimirá en pantalla!). Es importante no olvidar este password

![img2](../fig/image2.png)

Podemos cambiar las preferencias de visualización de nuestra terminal, y además agregar opciones de copiado y pegado.

![img4](../fig/image4.png)
![img5](../fig/image5.png)

Ahora estamos listos para instalar CONDA

## Instalación de miniconda

Instalaremos la version de minicoda para python 3.7 (Ubuntu 20.04 trae un python >3.7 ya instalado). Primero lo descargamos con el comando wget 

~~~
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
~~~
{: .language-bash}

Este archivo quedará en el directorio actual (/home/NOMBREDEUSUARIO/)
Para ejecutarlo usaremos el comando "bash" (el nombre del archivo puede variar para ustedes)


~~~
bash Miniconda3-latest-Linux-x86_64.sh
~~~
{: .language-bash}

Esto iniciará la instalación de minicoda, en dónde pueden avanzar con la tecla Enter. Será necesario aceptar la licencia (Yes), y aceptar la direccion de instalación que nos dará. También al final es importante aceptar que conda se inicie con ubuntu.

![img11](../fig/image11.png)

La instalación esta completa pero es necesario reiniciar ubuntu, simplemente con cerrar y volver abrir la app.
Ahora nuestra terminal indica (base), que es el ambiente de conda que estamos usando.
Configuramos conda para que mire a los siguientes canales cuando busqué los programas a instalar:
~~~
conda config --add channels defaults
conda config --add channels bioconda
conda config --add channels conda-forge
~~~
{: .language-bash}


Instalaremos programas dentro de este ambiente con el comando conda install

~~~
conda install -c bioconda fastp fastqc bwa star multiqc
conda install -c bioconda samtools openssl=1.0 
~~~
{: .language-bash}

Este comando buscará en el repositorio "bioconda" las instrucciones para instalar fastp, fastqc, bwa, star, y samtools en nuestro ambiente BASE.
Samtools necesitamos instalarlo con la opcion adicional openssl=1.0 para que funcione con nuestra version de ubuntu.
Si la instalación es exitosa ya podemos utilizar todos estos programas.


## Accediendo al disco C de windows desde Ubuntu

Los directorios de ubuntu (por ejemplo el directorio actual /home/NOMBREDEUSUARIO) no son visibles desde el explorador de windows, pero desde Ubuntu si se puede ingresar al disco C desde la carpeta /mnt/
Aprovecharemos esto para crear una carpeta llamada "ubuntu" en el disco C de windows, y dejar ahi todos los materiales del curso

~~~
cd /mnt/c
mkdir ubuntu
cd ubuntu
~~~
{: .language-bash}



{% include links.md %}
