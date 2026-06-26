# =========================================================
# ETAPA 1: Compilación de la aplicación (Build Stage)
# =========================================================
# Usamos una imagen de Maven con Java 17 sobre Alpine (Ultra ligera)
FROM maven:3.9.6-eclipse-temurin-17-alpine AS builder

WORKDIR /app

# Copiamos el archivo de configuración de dependencias (POM)
COPY pom.xml .

# Descargamos las dependencias necesarias sin compilar el código (Aprovecha la caché)
RUN mvn dependency:go-offline -B

# Copiamos todo el código fuente del proyecto
COPY src ./src

# Compilamos el proyecto saltándonos los tests para ahorrar tiempo en el despliegue
RUN mvn clean package -DskipTests

# =========================================================
# ETAPA 2: Entorno de Ejecución Seguro (Production Stage)
# =========================================================
# Para correr el archivo final (.jar) solo necesitamos el entorno de ejecución de Java (JRE)
FROM eclipse-temurin:17-jre-alpine AS runner

WORKDIR /app

# Requisito de Pauta: Usuario NO-ROOT por seguridad laboral/industrial
# Creamos un grupo y usuario limitado llamado 'devopsuser'
RUN addgroup -S devopsgroup && adduser -S devopsuser -G devopsgroup

# Copiamos el archivo ejecutable (.jar) compilado en la Etapa 1
# Nota: Usamos un comodín (*) porque el nombre exacto depende de la versión del pom.xml
COPY --from=builder /app/target/*.jar app.jar

# Cambiamos los privilegios para que el usuario no-root sea dueño del archivo
RUN chown -R devopsuser:devopsgroup /app

# Activamos el usuario no-root
USER devopsuser

# Puerto por defecto de las aplicaciones Spring Boot (Comúnmente 8080, confírmame si usan este)
EXPOSE 8081

# Comando para ejecutar nuestra API de Java
CMD ["java", "-jar", "app.jar"]