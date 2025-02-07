# Workshop: Instalación y Configuración de StreamSets Data Collector 3.22.2 con Docker

## Objetivo
Este workshop tiene como propósito guiar a los estudiantes en la instalación y configuración de **StreamSets Data Collector (SDC) versión 3.22.2** utilizando **Docker**. Este enfoque permite una implementación rápida y sencilla de un entorno de procesamiento de datos.

---

## Prerrequisitos
Antes de iniciar, es necesario contar con los siguientes requisitos en el sistema:
- **Docker** instalado. Se puede obtener siguiendo las instrucciones en [la documentación oficial de Docker](https://docs.docker.com/get-docker/)
- **Acceso a una terminal** en Linux, macOS o WSL en Windows
- **Conexión a Internet** para descargar la imagen base y los paquetes necesarios

---

## Paso 1: Creación del Dockerfile
Docker utiliza un archivo denominado `Dockerfile` que contiene las instrucciones necesarias para construir la imagen de **StreamSets 3.22.2**.

1. Abrir una terminal y crear una nueva carpeta de trabajo:
   ```sh
   mkdir streamsets_workshop && cd streamsets_workshop
   ```
2. Crear un archivo `Dockerfile` dentro de la carpeta:
   ```sh
   touch Dockerfile
   ```
3. Abrir el archivo con un editor de texto como `nano`, `vim` o VS Code. Por ejemplo:
   ```sh
   nano Dockerfile
   ```

4. Agregar el siguiente contenido en el `Dockerfile`:

   ```Dockerfile
   # Usar la imagen base de Ubuntu 20.04
   FROM ubuntu:20.04

   # Establecer variables de entorno para evitar la interacción manual en la instalación
   ENV DEBIAN_FRONTEND=noninteractive

   # Instalar dependencias necesarias
   RUN apt-get update && apt-get install -y \
       openjdk-11-jdk \
       wget \
       curl \
       unzip \
       && rm -rf /var/lib/apt/lists/*

   # Crear el usuario para ejecutar StreamSets
   RUN useradd -m -s /bin/bash streamsets

   # Descargar y configurar StreamSets Data Collector 3.22.2
   RUN wget -q https://archives.streamsets.com/datacollector/3.22.2/tarball/streamsets-datacollector-core-3.22.2.tgz -O /tmp/sdc.tgz \
       && mkdir -p /opt/streamsets \
       && tar -xzf /tmp/sdc.tgz -C /opt/streamsets --strip-components=1 \
       && rm /tmp/sdc.tgz

   # Asignar permisos al usuario streamsets
   RUN chown -R streamsets:streamsets /opt/streamsets

   # Definir el usuario de ejecución
   USER streamsets

   # Definir el directorio de trabajo
   WORKDIR /opt/streamsets

   # Exponer el puerto 18630 para la interfaz web
   EXPOSE 18630

   # Comando para iniciar StreamSets Data Collector
   CMD ["/opt/streamsets/bin/streamsets", "dc"]
   ```

5. Guardar los cambios y salir del editor.

---

## Paso 2: Construcción de la imagen Docker
Con el `Dockerfile` configurado, es posible construir la imagen de StreamSets.

Ejecutar el siguiente comando en la terminal dentro del directorio `streamsets_workshop`:

```sh
docker build -t streamsets_3.22.2 .
```

El proceso puede tardar algunos minutos, dependiendo de la velocidad de la conexión a Internet.

---

## Paso 3: Ejecución del contenedor
Una vez finalizada la construcción de la imagen, es posible ejecutar el contenedor con el siguiente comando:

```sh
docker run -d -p 18630:18630 --name streamsets_container streamsets_3.22.2
```

Este comando ejecuta el contenedor en segundo plano, asignándole el nombre `streamsets_container` y exponiendo el puerto 18630.

---

## Paso 4: Acceso a la interfaz web
Para acceder a StreamSets Data Collector, abrir un navegador y dirigirse a:

```
http://localhost:18630
```

Aparecerá la pantalla de inicio de StreamSets, donde se podrá iniciar sesión y comenzar a configurar pipelines.

---

## Paso 5: Verificación del estado del contenedor
Para verificar que el contenedor se encuentra en ejecución, usar el siguiente comando:

```sh
docker ps
```

Si en algún momento es necesario detener el contenedor, se puede ejecutar:

```sh
docker stop streamsets_container
```

Para reiniciar el contenedor:

```sh
docker start streamsets_container
```

Para eliminarlo definitivamente:

```sh
docker rm -f streamsets_container
```

---

## Conclusión
Al finalizar este workshop, los estudiantes habrán configurado **StreamSets Data Collector 3.22.2** dentro de un contenedor Docker y aprendido a gestionar su ejecución. Esta metodología permite experimentar con el procesamiento de datos sin afectar la configuración del sistema operativo principal.


