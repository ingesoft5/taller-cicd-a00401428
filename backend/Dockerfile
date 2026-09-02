## Configurar la imagen
# ==========================================
# Etapa 1: Construcción y Pruebas (Builder)
# ==========================================
FROM maven:3.9.6-eclipse-temurin-17 AS builder

WORKDIR /app

# 1. Copiar primero el descriptor del proyecto
COPY pom.xml .

# 2. Descargar las dependencias fuera de línea
# Esto aprovecha el caché de capas de Docker. Si el pom.xml no cambia, 
# Docker no volverá a descargar todo internet en las siguientes construcciones.
RUN mvn dependency:go-offline -B

# 3. Copiar el código fuente de la aplicación (y las pruebas)
COPY src ./src

# 4. Compilar el proyecto, ejecutar pruebas unitarias y empaquetar
# Al usar H2 en memoria, las pruebas del repositorio y servicios se ejecutarán 
# sin necesidad de levantar bases de datos externas en este paso.
RUN mvn clean package -B

# ==========================================
# Etapa 2: Entorno de Ejecución (Runtime)
# ==========================================
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

# Por seguridad, creamos un usuario sin privilegios de root para ejecutar la app
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Copiar el archivo .jar generado en la etapa 'builder'
# Se usa un comodín (*.jar) para no depender del nombre o versión exacta en el pom.xml
COPY --from=builder /app/target/*.jar app.jar

# Exponer el puerto en el que escucha Spring Boot por defecto
EXPOSE 8080

# Punto de entrada para iniciar la aplicación
ENTRYPOINT ["java", "-jar", "/app/app.jar"]
