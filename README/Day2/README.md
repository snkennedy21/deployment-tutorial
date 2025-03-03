# Dockerizing Nginx for Deployment on DigitalOcean

This guide walks through the steps required to containerize an **Nginx application** using Docker and deploy it on a **DigitalOcean Droplet**.

---

## **Step 1: Create a `Dockerfile`**

1. Inside your project folder (`nginx-deploy`), create a `Dockerfile`:
   ```sh
   touch Dockerfile
   nano Dockerfile
   ```
2. Add the following content:

   ```dockerfile
   # Use the official Nginx image
   FROM nginx:latest

   # Copy custom Nginx config
   COPY nginx/nginx.conf /etc/nginx/conf.d/default.conf

   # Copy static files
   COPY public /usr/share/nginx/html

   # Expose port 80
   EXPOSE 80

   # Start Nginx
   CMD ["nginx", "-g", "daemon off;"]
   ```

3. Save and exit (`CTRL + X`, then `Y`, then `Enter`).

---

## **Step 2: Create a `docker-compose.yml` File**

1. Inside your project folder, create a `docker-compose.yml` file:
   ```sh
   nano docker-compose.yml
   ```
2. Add the following content:

   ```yaml
   version: "3.8"

   services:
     nginx:
       build: .
       container_name: nginx_server
       ports:
         - "80:80"
       restart: always
   ```

3. Save and exit (`CTRL + X`, then `Y`, then `Enter`).

---

## **Step 3: Add an `nginx` Folder with a Custom `nginx.conf`**

1. Create an Nginx folder:
   ```sh
   mkdir nginx
   ```
2. Inside the folder, create `nginx.conf`:
   ```sh
   nano nginx/nginx.conf
   ```
3. Add the following content:

   ```nginx
   server {
       listen 80;

       root /usr/share/nginx/html;
       index index.html;

       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```

4. Save and exit (`CTRL + X`, then `Y`, then `Enter`).

---

## **Step 4: Uninstall Nginx from the DigitalOcean Server**

Since we will now manage Nginx inside Docker, we must remove the existing installation.

1. Stop and disable Nginx:
   ```sh
   sudo systemctl stop nginx
   sudo systemctl disable nginx
   ```
2. Uninstall Nginx:
   ```sh
   sudo apt remove --purge nginx nginx-common -y
   sudo apt autoremove -y
   ```
3. Remove Nginx-related files (optional):
   ```sh
   sudo rm -rf /etc/nginx /var/log/nginx /var/www/html
   ```
4. Verify Nginx is uninstalled:
   ```sh
   nginx -v
   ```
   If it’s uninstalled, you should see:
   ```
   Command 'nginx' not found
   ```

---

## **Step 5: Install Docker and Docker Compose on the Server**

1. Update package lists:
   ```sh
   sudo apt update
   ```
2. Install Docker:
   ```sh
   sudo apt install docker.io -y
   ```
3. Install Docker Compose:
   ```sh
   sudo apt install docker-compose -y
   ```
4. Enable and start Docker:
   ```sh
   sudo systemctl enable docker
   sudo systemctl start docker
   ```
5. Verify Docker installation:
   ```sh
   docker --version
   docker-compose --version
   ```

---

## **Step 6: Start the Application in Docker**

1. Navigate to the project folder:
   ```sh
   cd nginx-deploy
   ```
2. Build and start the Docker container:
   ```sh
   docker-compose up --build -d
   ```
3. Verify the container is running:

   ```sh
   docker ps
   ```

   You should see `nginx_server` running.

4. Access your application by visiting your **Droplet’s public IP**:
   ```
   http://your-droplet-ip
   ```
   You should see:
   ```
   Welcome to My Web App
   ```

---

## **Next Steps**

1. **Handling TLS in Docker** – Set up Let's Encrypt for HTTPS inside Docker.
2. **Adding a Backend & Database** – Expand to include an Express.js backend and PostgreSQL.
3. **Automating Deployment** – Use GitHub Actions to automatically redeploy on updates.

---

### 🎉 **Your Nginx application is now fully Dockerized and running on DigitalOcean!** 🚀
