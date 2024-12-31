# Keycloak Installation Process

This guide will walk you through the process of installing Keycloak. Ensure you have all the prerequisites met before proceeding with the installation.

## Prerequisites

- Java (OpenJDK 11 or later is recommended)
- A Linux-based server (this guide assumes Ubuntu 20.04 or later)
- Access to Nginx or other proxy if you want to run Keycloak behind a reverse proxy

## Step 1: Install OpenJDK

1. **Install OpenJDK**

   Keycloak requires Java to run. Install OpenJDK 11 using the following command:
   ```sh
   sudo apt update
   sudo apt install openjdk-11-jdk
   ```
   Verify the installation:
   ```sh
   java -version
   ```
   Ensure that the output shows OpenJDK 11 or later.

## Step 2: Download and Install Keycloak

1. **Download Keycloak**

   Download the version  of Keycloak from the official website or use the following command to download using `wget`:

   ```sh
   wget https://github.com/keycloak/keycloak/releases/download/<VERSION>/keycloak-<VERSION>.tar.gz
   ```

2. **Extract the Archive**

   Extract the downloaded Keycloak tarball:

   ```sh
   tar xvf keycloak-<VERSION>.tar.gz
   ```

3. **Remove the Archive**

   Remove the downloaded tarball to save space:

   ```sh
   rm -rf keycloak-<VERSION>.tar.gz
   ```

4. **Move to Installation Directory**

   Move the extracted folder to the desired installation directory:

   ```sh
   sudo mv keycloak-<VERSION> /opt/keycloak
   ```

5. **Create Keycloak User**

   Create a dedicated system user for running Keycloak:

   ```sh
   sudo useradd -r -d /opt/keycloak -s /bin/false keycloak
   sudo chown -R keycloak:keycloak /opt/keycloak
   ```

## Step 2: Configure Keycloak

1. **Create an Admin User**

   Create an admin user by bootstrapping the admin configuration:
   ```sh
   sudo -u keycloak /opt/keycloak/bin/kc.sh bootstrap-admin user --username admin
   ```
   It will take sometime then prompt you to set the password for the admin user.

## Step 3: Running Keycloak as a Service

1. **Create a Systemd Service File**

   Create a `keycloak.service` file in the `/etc/systemd/system/` directory with the following content:

   ```ini
   [Unit]
   Description=Keycloak Server
   After=network.target

   [Service]
   User=keycloak
   Group=keycloak
   Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
   WorkingDirectory=/opt/keycloak
   ExecStart=/opt/keycloak/bin/kc.sh start --http-enabled=true --hostname=DOMAIN_NAME --proxy-headers=xforwarded
   Restart=on-failure

   [Install]
   WantedBy=multi-user.target
   ```

   Make sure to adjust the `JAVA_HOME` variable according to your system's Java installation.

2. **Reload Systemd and Start Keycloak**

   ```sh
   sudo systemctl daemon-reload
   sudo systemctl enable keycloak
   sudo systemctl start keycloak
   sudo systemctl status keycloak
   ```

   Keycloak will now start automatically on system boot.

## Step 4: Configure Nginx as a Reverse Proxy (Optional)

1. **Stop Nginx and Obtain SSL Certificate**

   Before configuring Nginx, stop the service and obtain an SSL certificate using Certbot:

   ```sh
   sudo certbot certonly -d DOMAIN_NAME
   ```

2. **Install Nginx**

   If Nginx is not already installed, you can install it using the following command:

   ```sh
   sudo apt update
   sudo apt install nginx
   ```

3. **Configure Nginx**

   Create a new configuration file for Keycloak in `/etc/nginx/sites-available/keycloak`:

   ```nginx
   server {
       listen 80;
       server_name your_domain.com;

       location / {
           proxy_pass http://localhost:8080;
           proxy_set_header Host $host;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header X-Forwarded-Proto $scheme;
       }
   }
   ```

   Replace `your_domain.com` with your actual domain name.

4. **Enable the Configuration**

   Enable the configuration by creating a symbolic link to the `sites-enabled` directory:

   ```sh
   sudo ln -s /etc/nginx/sites-available/keycloak /etc/nginx/sites-enabled/
   ```

5. **Restart Nginx**

   Restart Nginx to apply the changes:

   ```sh
      sudo systemctl enable nginx
      sudo systemctl start nginx
      sudo systemctl status nginx
   ```

## Step 5: Access Keycloak

After completing the installation and configuration steps, you can access Keycloak by navigating to `http://your_domain.com` in your web browser. Log in using the admin credentials you created earlier.

## Conclusion

You have successfully installed and configured Keycloak on your server. Make sure to secure your Keycloak instance by enabling SSL/TLS and configuring appropriate firewall rules.

