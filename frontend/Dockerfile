# Completar
# ==========================================
# Etapa 1: Construcción (Builder)
# ==========================================
FROM node:20-alpine AS builder

WORKDIR /app

# 1. Copiar manifiestos de dependencias primero para optimizar la caché
COPY package*.json ./

# 2. Instalar dependencias usando clean install (reproducible y rápido)
RUN npm ci

# 3. Copiar el resto del código fuente del proyecto
COPY . .

# 4. Compilar la aplicación TypeScript a estáticos optimizados
# En Vite, esto genera por defecto la carpeta /app/dist
RUN npm run build

# ==========================================
# Etapa 2: Servidor Web de Producción (Runtime)
# ==========================================
FROM nginx:alpine

# 1. Eliminar la configuración predeterminada de Nginx
RUN rm -rf /etc/nginx/conf.d/default.conf

# 2. Copiar nuestra configuración personalizada para la SPA
COPY nginx.conf /etc/nginx/conf.d/default.conf

# 3. Copiar los archivos estáticos generados por Vite en la etapa builder
COPY --from=builder /app/dist /usr/share/nginx/html

# Exponer el puerto estándar HTTP
EXPOSE 80

# Iniciar Nginx en primer plano para mantener el contenedor activo
CMD ["nginx", "-g", "daemon off;"]
