---
title: "Usar nvblas en linux (nvidia BLAS CUDA)"
date: 2018-06-30
tags: [prueba,latex]
excerpt: "Data Science, System-Config"
mathjax: "true"
---

### Configuracion

Instalar cuda toolkit

NVIDIA distributes NVBLAS as part of their CUDA Toolkit.

checar que exista la libreria nvblas

```bash
adrianrdzv@adrianrdzv-Lenovo-ideapad-Y700-17ISK:~$ locate nvblas.so
/usr/local/cuda-9.1/targets/x86_64-linux/lib/libnvblas.so
/usr/local/cuda-9.1/targets/x86_64-linux/lib/libnvblas.so.9.1
/usr/local/cuda-9.1/targets/x86_64-linux/lib/libnvblas.so.9.1.181
/usr/local/cuda-9.1/targets/x86_64-linux/lib/libnvblas.so.9.1.85

```

si existe tendremo que crear o modificar un archivo conf **nvblas.conf**

```bash
nano /etc/nvblas.conf
#copiar algo similar a esto
NVBLAS_LOGFILE nvblas.log
NVBLAS_CPU_BLAS_LIB /usr/lib/libopenblas.so
NVBLAS_GPU_LIST ALL
```

La configuracion anterior sirve para multigpu que es lo que hace la utlima linea

Para correr R usando nvblas despues de tener todo configurado se hace desde la consola de la manera siguiente

### Pruebas

LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libnvblas.so NVBLAS_CONFIG_FILE=/etc/nvblas.conf R

```R
> A<-matrix(rnorm(9000000),nrow=3000,ncol=3000,byrow=T)
> B<-matrix(rnorm(9000000),nrow=3000,ncol=3000,byrow=T)
> system.time(C<- A %*%B)
   user  system elapsed 
  1.070   0.364   1.433 
  
> A<-matrix(rnorm(6000*6000),nrow=6000,ncol=6000,byrow=T)
> B<-matrix(rnorm(6000*6000),nrow=6000,ncol=6000,byrow=T)
> system.time(C<- A %*%B)
   user  system elapsed 
  8.062   3.188  11.245 
```

prueba usando R con libopenblas(Multicore no gpu)

```R
> A<-matrix(rnorm(9000000),nrow=3000,ncol=3000,byrow=T)
> B<-matrix(rnorm(9000000),nrow=3000,ncol=3000,byrow=T)
> system.time(C<- A %*%B)
   user  system elapsed 
  3.777   0.342   0.561 
  
> A<-matrix(rnorm(6000*6000),nrow=6000,ncol=6000,byrow=T)
> B<-matrix(rnorm(6000*6000),nrow=6000,ncol=6000,byrow=T)
> system.time(C<- A %*%B)
   user  system elapsed 
 28.116   2.386   3.971 

#Usando flotante simple
> A<-fl(A)
> B<-fl(B)
> system.time(C<- A %*%B)
   user  system elapsed 
 23.335   1.926   3.250 

```

Parece que CPU multicore le gana a GPU en mi laptop

![https://i.imgur.com/dqei09e.png](https://i.imgur.com/dqei09e.png)

![](https://i.imgur.com/SPPoaAe.png)

---

Al probar usando los floatantes de doble precision en algunas tarjetas graficas como la mía se notara que incluso es mas rapido usando multicore que usando la gpu y esto es debido que ciertas gpu's solo estan opitmizadas a trabajar rapido usando flotantes de precision simple, Si para nuestro caso nos basta con usar precision simple podremos hacer uso de la libreria float y asi convertir nuestros flotantes de doble a simple precision

```R
> library("float")
> A<-matrix(rnorm(9000000),nrow=3000,ncol=3000,byrow=T)
> B<-matrix(rnorm(9000000),nrow=3000,ncol=3000,byrow=T)
> object.size(A)
72000200 bytes
> print(object.size(A),units="Mb")
68.7 Mb
> A<-fl(A)
> B<-fl(B)
> print(object.size(A),units="Mb")
34.3 Mb
> system.time(C<- A %*%B)
   user  system elapsed 
  0.084   0.032   0.113 
> A<-matrix(rnorm(6000*6000),nrow=6000,ncol=6000,byrow=T)
> B<-matrix(rnorm(6000*6000),nrow=6000,ncol=6000,byrow=T)
> print(object.size(A),units="Mb")
274.7 Mb
> print(object.size(B),units="Mb")
274.7 Mb
> A<-fl(A)
> B<-fl(B)
> print(object.size(A),units="Mb")
137.3 Mb
> print(object.size(B),units="Mb")
137.3 Mb
> system.time(C<- A %*%B)
   user  system elapsed 
  0.553   0.160   0.709 

```

Aqui vemos como la gpu usando flotante simple es 4x-5x veces mas rapido que usando multicore con flotantes simples.



### Referencias:

* https://clint.id.au/?p=1900
* https://github.com/fommil/netlib-java/wiki/NVBLAS
* https://github.com/BVLC/caffe/issues/194

