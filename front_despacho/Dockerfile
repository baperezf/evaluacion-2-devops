# =========================================================
# ETAPA 1: Compilación del Frontend (Build Stage)
# =========================================================
FROM node:20-alpine AS builder

WORKDIR /app

# Copiamos los descriptores de dependencias
COPY package*.json ./

# Instalamos las dependencias de manera limpia
RUN npm ci

# Copiamos todo el código fuente del frontend
COPY . .

# Compilamos la aplicación para producción
# (Esto creará una carpeta llamada 'dist' o 'build' con el código optimizado)
RUN npm run build

# =========================================================
# ETAPA 2: Servidor de Producción Seguro (Production Stage)
# =========================================================
FROM nginx:1.25-alpine AS runner

# Requisito de Seguridad: Crear un usuario no-root para Nginx
# Por defecto, Nginx corre como root. Modificamos la configuración para mitigar riesgos.
RUN touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid /var/cache/nginx /var/log/nginx /usr/share/nginx/html

# Copiamos los archivos estáticos compilados desde la Etapa 1
# NOTA: Si tu proyecto compila en la carpeta 'build' en vez de 'dist', cambia 'dist' por 'build'
COPY --from=builder /app/dist /usr/share/nginx/html

# Cambiamos al usuario limitado preexistente de nginx
USER nginx

# El puerto estándar donde Nginx sirve contenido de forma interna
EXPOSE 80

# Arrancamos Nginx en primer plano (obligatorio para que el contenedor no se apague)
CMD ["nginx", "-g", "daemon off;"]