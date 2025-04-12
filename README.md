- # De cero a Docker Compose
  
**Fecha:**12/04/2025  
---
- ## 🐳 Taller de Iniciación a Docker
  
  **Objetivo**: Aprender a usar Docker con ejemplos reales, desde lo más básico hasta crear tus propios servicios con Dockerfile y Docker Compose.  
  
---
- ### 📦 ¿Qué es Docker?
	- Es una forma de empaquetar software con todas sus dependencias en una imagen.
	- Ideal para desarrollo, despliegue y pruebas rápidas.
	- Usaremos contenedores, no máquinas virtuales: corren en el mismo kernel pero están separados del sistema y solo tienen acceso a lo que se les habilita.
	- #### 🥘 Analogía: el robot de cocina
		- En lugar de cocinar manualmente, creamos recetas que el robot reproduce.
		- ¿Cómo se cocina un WordPress?
			- Instalar una base de datos.
			- Inicializarla con usuarios y tablas.
			- Instalar y configurar un servidor web.
			- Descargar WordPress.
			- Descomprimirlo en el directorio raíz.
			- Configurar las variables de conexión a la base de datos.
- #### ⚙️ ¿De qué está compuesta una aplicación?
	- **Binarios**: sistema operativo, base de datos, servidor web.
	- **Configuración estática**: archivos `.conf`, variables de entorno.
	- **Estado**: ficheros persistentes como bases de datos o contenidos subidos.
- #### 🧱 ¿De dónde sale todo esto?
	- Imágenes creadas localmente (`Dockerfile`).
	- Repositorios públicos como [Docker Hub](https://hub.docker.com/).
	    
---
- ### ❤️ ¿Qué me gusta de Docker?
- #### Como operador/usuario:
	- Probar casi cualquier cosa desde GitHub en segundos.
	- Fácil de gestionar:
		- Toda la configuración y datos en una carpeta.
		- Ideal para migraciones.
- #### Como desarrollador:
	- Crear entornos de desarrollo reproducibles.
	  <!-- Sugerencia: Podrías añadir aquí un ejemplo real de stack dev reproducible (por ejemplo Flask + Postgres + Redis) -->
	    
---
- ### 🧪 Instalar Docker
	- #### Opción 1: Desde repositorios
		-
		  ```bash
		  apt install docker docker-compose
		  ```
	- ### Opción 2: Script oficial
		-
		  ```bash
		  curl -fsSL https://get.docker.com -o get-docker.sh
		  sh get-docker.sh
		  ```
-
- ## 🚀 Primer paso: ¡Tu primer contenedor!
	-
	  ```
	  docker run hello-world
	  ```
	- ¿Qué hace esto?
		- Busca la imagen hello-world en local
		- La descarga de internet
		- ejecuta la imagen con docker
- ### 🧰 Herramientas básicas de Docker
- Ejecutar un servicio
	-
	  ```bash
	  docker run -it --rm -d -p 8080:80 --name mi-web nginx
	  ```
- Diagnóstico
	-
	  ```bash
	  docker ps
	  docker logs mi-web
	  docker exec -it mi-web bash
	  ```
- Parar y limpiar
	-
	  ```bash
	  docker stop miapp
	  docker rm miapp
	  docker system prune
	  ```
-
-
-
---
- ### 📂 Docker Compose
	- ¿Qué es y por qué usarlo?
	- Estructura básica del archivo `docker-compose.yml`
		-
		  ```yaml
		  services:
		    web:  			#Nombre del servicio
		      image: nginx	#Imagen que va a usar
		      ports:			#Puertos accesibles
		        - "8080:80"
		  ```
	- más opciones:
		- Volúmenes
		- Carpetas
		- Variables de entorno
		- Redes
		- Permisos
		- Dispositivos
	- ## Cheatsheet
	-
	  ```bash
	  docker compose up -d
	  docker compose down
	  
	  docker compose start
	  docker compose stop
	  
	  docker compose logs
	  docker compose ps
	  ```
- ### 👨‍🔧 Manos a la obra: Ejemplos prácticos
	- **Levantamos un wordpress rapido con un docker compose**
	    
	  ```yaml
	  services:
	    db:
	      image: mariadb:lts
	      restart: always
	      environment:
	        MYSQL_ROOT_PASSWORD: MySQLRootPassword
	        MYSQL_DATABASE: MySQLDatabaseName
	        MYSQL_USER: MySQLUsername
	        MYSQL_PASSWORD: MySQLUserPassword
	      volumes:
	        - ./db:/var/lib/mysql
	        
	    wordpress:
	      depends_on:
	        - db
	      image: wordpress:latest
	      restart: always
	      ports:
	        - "80:80"
	      environment:
	        WORDPRESS_DB_HOST: db:3306
	        WORDPRESS_DB_USER: MySQLUsername
	        WORDPRESS_DB_PASSWORD: MySQLUserPassword
	        WORDPRESS_DB_NAME: MySQLDatabaseName
	      volumes:
	        - "./html :/var/www/html"
	  ```
-
	- Levantamos un Home assistant desde una imagen de linux server
	-
	  ```yaml
	  services:
	    homeassistant:
	      image: lscr.io/linuxserver/homeassistant:latest
	      container_name: homeassistant
	      network_mode: host
	      environment:
	        - PUID=1000
	        - PGID=1000
	        - TZ=Europe/Madrid
	      volumes:
	        - ./data:/config
	  #    ports:
	  #      - 8123:8123 #optional
	  #    devices:
	  #      - /path/to/device:/path/to/device #optional
	      restart: unless-stopped
	  ```
- Bola extra:
	- Mover las variables de entorno a un .env
-
-
- ### **Crea tu propio servicio con Dockerfile**
	- Servicio web simple(por ejemplo, Flask o Node.js).
		- ### Estructura:
		-
		  ```
		  hola-mundo-docker/
		  ├── app.py
		  ├── requirements.txt
		  ├── Dockerfile
		  └── docker-compose.yml
		  ```
		-
		- ### `app.py`
		  ```python
		  from flask import Flask
		  
		  app = Flask(__name__)
		  
		  @app.route('/')
		  def hello():
		      return "¡Hola Mundo desde Docker + Python!"
		  
		  if __name__ == "__main__":
		      app.run(host="0.0.0.0", port=5000)
		  ```
		-
		- ### `requirements.txt`
		    
		  ```
		  flask
		  ```
		-
		- ### `Dockerfile`
		    
		  ```dockerfile
		  FROM python:3.12-slim
		  
		  WORKDIR /app
		  EXPOSE 5000
		  
		  COPY requirements.txt requirements.txt
		  RUN pip install --no-cache-dir -r requirements.txt
		  
		  COPY app.py app.py
		  
		  CMD ["python", "app.py"]
		  ```
		-
		- ### `docker-compose.yml`
		    
		  ```yaml
		  services:
		    web:
		      build: .
		      ports:
		        - "5000:5000"
		  ```
	-
	-
	-
