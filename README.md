# Deploying a Static Web Page on DigitalOcean with Nginx

This guide walks through setting up a **DigitalOcean Droplet**, configuring **Nginx**, and serving a static HTML page.

---

## **Step 1: Set Up the Initial Repository**
1. Create a new directory for your project:
   ```sh
   mkdir nginx-deploy && cd nginx-deploy
   ```
2. Initialize a Git repository:
   ```sh
   git init
   ```
3. Create a basic HTML file:
   ```sh
   mkdir public
   echo "<h1>Welcome to My Web App</h1>" > public/index.html
   ```
4. Create an initial commit:
   ```sh
   git add .
   git commit -m "Initial commit"
   ```
5. Create a GitHub repository and push the code:
   ```sh
   git remote add origin git@github.com:your-username/nginx-deploy.git
   git push -u origin main
   ```

---

## **Step 2: Create a DigitalOcean Droplet**
1. Log in to **[DigitalOcean](https://cloud.digitalocean.com/)**.
2. Click **Create Droplet**.
3. Choose **Ubuntu 22.04** as the operating system.
4. Select the smallest available plan (**Basic, $6/month**).
5. Choose a data center region close to your users.
6. Under **Authentication**, select **SSH keys** and add your local public key (or create a password).
7. Click **Create Droplet** and copy the public IP address.

---

## **Step 3: Create an SSH Key for the Droplet**
If you didn’t add an SSH key while creating the Droplet, do the following on your local machine:

1. **Generate a key pair (if you don’t already have one):**
   ```sh
   ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
   ```
2. **Copy the public key:**
   ```sh
   cat ~/.ssh/id_rsa.pub
   ```
3. **Add the public key to your Droplet:**
   ```sh
   ssh root@your-droplet-ip "mkdir -p ~/.ssh && echo 'your-public-key' >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
   ```

---

## **Step 4: Access the Droplet**
1. SSH into the Droplet using the assigned IP:
   ```sh
   ssh root@your-droplet-ip
   ```
2. Update the system:
   ```sh
   sudo apt update && sudo apt upgrade -y
   ```

---

## **Step 5: Create an SSH Key on the Droplet for GitHub**
1. **Generate an SSH key on the Droplet:**
   ```sh
   ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
   ```
   Press **Enter** for all prompts.
2. **Copy the public key:**
   ```sh
   cat ~/.ssh/id_rsa.pub
   ```
3. **Add the key to GitHub (Settings → SSH Keys).**
4. **Test the connection:**
   ```sh
   ssh -T git@github.com
   ```
   You should see a confirmation message.

---

## **Step 6: Clone Your Repository**
Once the SSH key is added to GitHub, clone your repository:
```sh
git clone git@github.com:your-username/nginx-deploy.git
cd nginx-deploy
```

---

## **Step 7: Install and Start Nginx**
1. **Update packages and install Nginx:**
   ```sh
   sudo apt update && sudo apt install nginx -y
   ```
2. **Start and enable Nginx to run on boot:**
   ```sh
   sudo systemctl start nginx
   sudo systemctl enable nginx
   ```

---

## **Step 8: Modify the Nginx Configuration**
1. **Move the static files to Nginx’s web root:**
   ```sh
   sudo cp -r public/* /var/www/html/
   ```
2. **Update Nginx configuration:**
   ```sh
   sudo nano /etc/nginx/sites-available/default
   ```
   Replace the contents with:
   ```nginx
   server {
       listen 80;
       root /var/www/html;
       index index.html;
       location / {
           try_files $uri $uri/ =404;
       }
   }
   ```
   Save and exit (**CTRL + X**, then **Y**, then **Enter**).

---

## **Step 9: Restart Nginx and Allow Traffic**
1. Restart Nginx:
   ```sh
   sudo systemctl restart nginx
   ```
2. Allow web traffic through the firewall:
   ```sh
   sudo ufw allow 'Nginx Full'
   sudo ufw enable
   ```

---

## **Step 10: Access Your Website**
Open a browser and go to:
```
http://your-droplet-ip
```
You should see:
```
Welcome to My Web App
```

---

## **Next Steps**
1. **Automate Deployment** – Use GitHub Actions to automatically pull updates.
2. **Secure with SSL** – Use Let's Encrypt to enable HTTPS.
3. **Introduce Docker** – Containerize the Nginx setup.

---

### 🎉 **Your static web page is now live!** 🚀
