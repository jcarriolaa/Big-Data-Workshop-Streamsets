# Usa una imagen base de Ubuntu
FROM ubuntu:20.04

# Configurar variables de entorno
ENV DEBIAN_FRONTEND=noninteractive

# Instalar dependencias necesarias
RUN apt-get update && apt-get install -y \
    openjdk-11-jdk \
    wget \
    curl \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# Definir el JAVA_HOME para OpenJDK 11
ENV JAVA_HOME=/usr/lib/jvm/java-11-openjdk-arm64
ENV PATH="${JAVA_HOME}/bin:${PATH}"

# Descargar e instalar StreamSets Data Collector 3.22.2
RUN wget -q https://archives.streamsets.com/datacollector/3.22.2/tarball/streamsets-datacollector-core-3.22.2.tgz -O /tmp/sdc.tgz \
    && mkdir -p /opt/streamsets \
    && tar -xzf /tmp/sdc.tgz -C /opt/streamsets --strip-components=1 \
    && rm /tmp/sdc.tgz

# Exponer el puerto web de StreamSets
EXPOSE 18630

# Definir el directorio de trabajo
WORKDIR /opt/streamsets

# Comando de inicio
CMD ["/opt/streamsets/bin/streamsets", "dc"]
