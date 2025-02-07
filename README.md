# Workshop: Instalación y Configuración de StreamSets Data Collector en Docker

## Introducción
StreamSets Data Collector (SDC) es una herramienta diseñada para la integración y procesamiento de datos en tiempo real. En este workshop, aprenderás a instalar y configurar StreamSets Data Collector versión 3.22.2 en Docker, asegurando que tu entorno esté listo para construir pipelines de datos eficientes.

---

## Requisitos Previos
Antes de comenzar, asegúrate de contar con los siguientes requisitos:
- Docker instalado en tu máquina.
- Conexión a internet estable.
- Conocimientos básicos de línea de comandos.

---

## Paso 1: Crear el Dockerfile
Para comenzar, necesitamos un Dockerfile que nos permita construir una imagen personalizada de StreamSets con Java 17.

Crea un archivo llamado `Dockerfile` y copia el siguiente contenido:

```dockerfile
FROM streamsets/datacollector:3.22.2

# Instalar OpenJDK 17 y configurarlo como predeterminado
USER root
RUN dnf install -y java-17-openjdk \
    && alternatives --install /usr/bin/java java /usr/lib/jvm/java-17-openjdk/bin/java 1 \
    && alternatives --set java /usr/lib/jvm/java-17-openjdk/bin/java \
    && java -version

# Crear usuario streamsets si no existe
RUN id -u streamsets &>/dev/null || useradd -m streamsets

# Asegurar permisos en el directorio de StreamSets
RUN chown -R streamsets:streamsets /opt/streamsets-datacollector

# Cambiar al usuario streamsets
USER streamsets

# Exponer el puerto predeterminado de StreamSets
EXPOSE 18630

# Comando por defecto para iniciar StreamSets
CMD ["dc"]
```

---

## Paso 2: Construcción de la Imagen Docker
Ejecuta el siguiente comando en la terminal dentro del directorio donde guardaste el `Dockerfile`:

```sh
docker build -t my_streamsets .
```

Este proceso puede tardar unos minutos mientras se descargan y configuran los paquetes necesarios.

---

## Paso 3: Ejecutar StreamSets Data Collector
Una vez que la imagen se haya construido correctamente, ejecuta el siguiente comando para iniciar un contenedor basado en ella:

```sh
docker run -p 18630:18630 my_streamsets
```

Esto iniciará StreamSets Data Collector y lo expondrá en el puerto 18630 de tu máquina.

Para acceder a la interfaz gráfica de StreamSets, abre un navegador y ve a:

```
http://localhost:18630
```

---

## Challenge: Creando un Pipeline de Streaming con StreamSets, Kafka y Hadoop

Ahora que tienes StreamSets instalado y en funcionamiento, es hora de aplicar lo aprendido en un desafío práctico.

### Objetivo
El objetivo del challenge es construir un pipeline de streaming utilizando StreamSets, Kafka y Hadoop. Este pipeline debe:
1. Leer archivos JSON con estructuras anidadas y mapas desde un directorio local.
2. Publicar cada evento en un tópico de Kafka.
3. Tener un segundo StreamSets como consumidor de Kafka, el cual:
   - Deserializa y aplana los eventos JSON.
   - Convierte los datos en formato CSV.
   - Almacena los archivos CSV en Hadoop.

### Pasos
1. **Levantar Kafka y Hadoop:**
   - Asegúrate de tener una instancia de Kafka y Hadoop en funcionamiento.
   - Puedes utilizar Docker o un clúster local para estos servicios.

2. **Pipeline 1: Productor de Kafka en StreamSets:**
   - Agrega un origen *Directory* en StreamSets para leer archivos JSON de un directorio específico.
   - Configura un *Kafka Producer* como destino para enviar los eventos JSON al tópico correspondiente en Kafka.

3. **Pipeline 2: Consumidor de Kafka en StreamSets:**
   - Configura un *Kafka Consumer* en StreamSets para leer los eventos del tópico de Kafka.
   - Usa un *Field Flattener* para aplanar los datos JSON anidados.
   - Agrega un *Local FS* como destino para almacenar los eventos en formato CSV en Hadoop.

4. **Validar el Flujo de Datos:**
   - Envía un archivo JSON de prueba al directorio de origen.
   - Verifica que los eventos lleguen al tópico de Kafka.
   - Confirma que los datos sean convertidos en CSV y almacenados en Hadoop correctamente.

Este desafío te permitirá entender mejor cómo integrar StreamSets con tecnologías de procesamiento de datos en tiempo real y almacenamiento distribuido.

Diagrama de arquitectura para referencia

https://github.com/jcarriolaa/Big-Data-Workshop-Streamsets/blob/9f1f625dc88388dbf083d49b83437ed063534729/Images/streamsetskafka.png

