
# Manual Odoo Installation Guide for v16.0 & v17.0 & v18.0

This guide provides a manual setup process for installing Odoo on a fresh server without relying on an automated script. 

---

## Step 1: System Update
 Update your system to ensure all packages are up-to-date.

   ```bash
    sudo apt update && sudo apt upgrade -y
  ```

---

## Step 2: Install Required Packages
 Install essential packages, including Git, Python3, pip, and development tools.

   ```bash
    sudo apt install git python3 python3-pip build-essential wget -y
  ```

---

## Step 3: Install PostgreSQL
 Install PostgreSQL and set it to start automatically on boot.

 ```bash
  sudo apt install postgresql -y
  sudo systemctl enable postgresql
  sudo systemctl start postgresql
 ```

---

## Step 4: Configure PostgreSQL User
 Create a PostgreSQL user for Odoo.

   ```bash
    sudo su - postgres
    psql
    CREATE USER odoo WITH SUPERUSER PASSWORD 'your_secure_password';
    \q
    exit
  ```

---

## Step 5: Install Node.js and npm
  1. Install Node.js and npm, which Odoo requires for managing JavaScript assets.

   ```bash
    sudo apt install npm -y
    sudo ln -s /usr/bin/nodejs /usr/bin/node
 ```
  2. If you want to 

3. Update `less` using npm:

    ```bash
    sudo npm install -g less less-plugin-clean-css
   ```

---

## Step 6: Install wkhtmltopdf
 Install `wkhtmltopdf` for PDF generation in Odoo.

```bash
 sudo apt install wkhtmltopdf -y
```

2. Alternatively, for specific versions for v16.0 & later:

```bash
  sudo wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.bionic_amd64.deb
  sudo dpkg -i wkhtmltox_0.12.6-1.bionic_amd64.deb
  sudo apt install -f
  ```

---

## Step 7: Create an Odoo User
 Create a system user for running the Odoo service.

```bash
  sudo adduser --system --home=/opt/odoo --group odoo
 ```

---

## Step 8: Clone the Odoo Source Code
 Clone the Odoo source code from GitHub. Replace `16.0` with the desired version.

   ```bash
    sudo git clone --depth 1 --branch 16.0 https://www.github.com/odoo/odoo /opt/odoo
   ```

---

## Step 9: Install Python Dependencies
   1. Odoo 17.0 and higher version need python version 3.10. If ubuntu server don't come with Python version 3.10 follow below process to install python 3.10 into the server
      ```bash
      apt update && sudo apt upgrade -y 
      apt install software-properties-common -y 
      add-apt-repository ppa:deadsnakes/ppa 
      apt install python3.10
      python3.10 --version
      sudo apt install python3.10-distutils
      sudo apt install python3.10-venv
      ```
   2. Install Python libraries and dependencies needed for Odoo.

      ```bash
       sudo apt install python3-dev python3-venv python3-wheel libxslt-dev libzip-dev libldap2-dev libsasl2-dev python3-setuptools node-less libjpeg-dev -y
      ```
---

## Step 10: Create a Python Virtual Environment
 Set up a virtual environment to manage Odoo’s Python dependencies.

   ```bash
    cd /opt/odoo
    python3 -m venv venv # or python3.10 whatever the version.
    source venv/bin/activate
   ```

---

## Step 11: Install Python Dependencies in the Virtual Environment
 Install the Python dependencies listed in Odoo’s `requirements.txt` file.

   ```bash
    pip install -r requirements.txt
    deactivate
  ```

---

## Step 12: Configure Odoo
1. Create a configuration file for Odoo with necessary settings.

    ```bash
    sudo cp /opt/odoo/debian/odoo.conf /etc/odoo.conf
    sudo nano /etc/odoo.conf
    ```

2. Update the configuration file with the following settings:

    ```ini
    [options]
    ; This is the password for the Odoo database user
    admin_passwd = your_secure_password
    db_host = False
    db_port = False
    db_user = odoo
    db_password = False
    addons_path = /opt/odoo/addons
    logfile = /var/log/odoo/odoo.log
    ```

3. Save and exit the file.

4. Set the appropriate ownership and permissions for the configuration file:

    ```bash
    sudo chown odoo: /etc/odoo.conf
    sudo chmod 640 /etc/odoo.conf
    ```

---

## Step 13: Create a Systemd Service File for Odoo
 Create a service file to manage the Odoo server with `systemctl`.

  ```bash
   sudo nano /etc/systemd/system/odoo.service
  ```

2. Add the following content:

    ```ini
    [Unit]
    Description=Odoo
    Documentation=http://www.odoo.com
    [Service]
    # User and group
    User=odoo
    Group=odoo
    # Specify the service working directory
    WorkingDirectory=/opt/odoo
    # Set up the virtual environment and run the server
    ExecStart=/opt/odoo/venv/bin/python /opt/odoo/odoo-bin -c /etc/odoo.conf
    # Set restart policy and other options
    Restart=always
    [Install]
    WantedBy=multi-user.target
    ```

3. Save and close the file.

4. Reload the systemd manager configuration:

    ```bash
   sudo systemctl daemon-reload
    ```

---

## Step 14: Start and Enable the Odoo Service
 Start and enable Odoo to automatically start on boot.


   ```bash
    sudo systemctl start odoo
    sudo systemctl enable odoo
   ```


---

## Step 15: Access Odoo
 Once the service is running, you can access Odoo by going to:

   ```console
    http://<your_server_ip>:8069
   ```
   Replace `<your_server_ip>` with your server’s IP address.

---

## Step 16: Manage the Odoo Service
 Use the following commands to manage the Odoo service:

  ```bash
    sudo systemctl status odoo     # Check if Odoo is running
    sudo systemctl stop odoo       # Stop Odoo
    sudo systemctl restart odoo    # Restart Odoo
   ```

---
