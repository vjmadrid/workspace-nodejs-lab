
# Hello World API

Tratar de conseguir que las imagenes de las aplicaciones sean lo más pequeñas que sean posibles

* Reduce el tamaño de la imagen final
  * Usar aquello que se requiere
* Reduce el tiempo de descarga
* Reduce la carga en memoria
* Reduce el tiempo de construcción
* Reduce el nº de vulnerabilidades

Permite a los desarrolladores crear secuencias de construcción de aplicaciones
* Usar un conjunto propio de aplicaciones

Cada stage puede producer una imagen intermedia que puede formar paret de un subsecuencia de stages



## Copiar variables
Copiar variables entre los stage de build es un aspecto importante a tener en cuenta

### Usar ARG

Se puede usar variables ARG durante el proceso de build

```bash
# ----- Stage 1 -----
FROM alpine AS stage1
ARG MY_VAR
RUN echo MY_VAR value is $MY_VAR

# ----- Stage 2 -----
FROM alpine AS stage2
ARG MY_VAR
RUN echo MY_VAR is still set to: $MY_VAR
```

### Usar ENV

Se pueden establecer variables durante el proceso de build y persistir el resultado en un imagen

```bash
# ----- Stage 1 -----
FROM alpine:latest as base
ENV MY_ENV="my_env"

# ----- Stage 2 -----
FROM base
RUN echo ${MY_ENV}
```

### Usar ficvhero

```bash
# Stage 1 - Build
FROM alpine AS build

# Write the variable value to a file
RUN echo "hello" > /tmp/myvar

# Stage 2 - Run
FROM alpine AS run

# Copy the variable value from the previous stage using a file
COPY --from=build /tmp/myvar /tmp/myvar
# Read the variable value from the file
RUN MY_VAR=$(cat /tmp/myvar) && echo "Running the app with MY_VAR=$MY_VAR"
```







## Ejemplos de uso

* Traducional
* Multistage
* 

### Tradicional

Usan un single-stage para construir y empaquetar la aplicacion
Lo que significa que incluye los entornos de build y runtime

```bash
# Construir la imagen
docker build -t app_traditional -f Dockerfile.traditional .

# Desplegar el contendor
docker run -d --name app1 -p 8080:8080 -t app_traditional

# Probar la aplicación
curl http://localhost:8080/
```

```bash
docker build -t hello-world:dev . -f Dockerfile.dev
```



### Multistage

Permite crear imagenes más pequeñas y eficientes
Separacion entre entorno de build y runtime
Cada staege puede tener su propia imagen base con la que trabajar

```bash
docker build -t app_multi_stage -f Dockerfile.multistage .

docker run -d --name app2 -p 8080:8080 -t app_multi_stage


```

 docker build --target builder -txxx .



docker build --target build-env -t build-env1.0 -f Dockerfile.multi .

docker run -it build-env1.0 /bin/sh