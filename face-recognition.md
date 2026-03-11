# Minimum System Requirements
***
- Ubuntu Server (22.04, 2vCPUs, 8GB RAM, 50GB storage), *ideally fresh install*
- Python (at least version 3.10)
- Nginx
- MongoDB

# Prerequisites
***
- Ubuntu server with firewall properly configured, allowing `http` and `https` traffic
- A user with `sudo` privileges or `root` access
- DNS `"A"` record resolving to the public IP address of the server

# Setup Guide
***

### Clone repository
- Clone this repository.
  e.g. `git clone git@github.com:DOST6/face-recognition.git`

### Setup **MongoDB**

Reference: https://www.mongodb.com/docs/manual/tutorial/install-mongodb-on-ubuntu/

1. Install prerequisites
    `sudo apt install software-properties-common gnupg apt-transport-https ca-certificates curl -y`
2. Import the public key for MongoDB.
    ```
    curl -fsSL https://www.mongodb.org/static/pgp/server-8.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg \
   --dearmor
   ```
3. Add MongoDB 8.0 APT repository to the `/etc/apt/sources.list.d` directory.
    `echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list`
4. Reload the local package index.
    `sudo apt update`
5. Install MongoDB.
    `sudo apt install mongodb-org -y`
6. Verify installation by checking MongoDB version.
    `mongod --version`
7. Start MongoDB service.
    `sudo systemctl start mongod`
    - To stop the service, run `sudo systemctl stop mongod`
    - To restart the service, run `sudo systemctl restart mongod`
8. Check MongoDB service status.
    `sudo systemctl status mongod`
9. Enable MongoDB to start on boot.
    `sudo systemctl enable mongod`
10. Set the MongoDB connection string, by creating a `.env` file inside the project directory.
    `nano .env`
11. Copy  the connection string and paste to the `.env` file.
    *By default, MongoDB binds to the `localhost` network interface, accepting connections from clients on the same machine only.*
    `MONGO_CONNECTION_STRING=mongodb://127.0.0.1:27017/users`

### Install and configure **Redis** for rate limiting

Reference: https://www.digitalocean.com/community/tutorials/how-to-install-and-secure-redis-on-ubuntu-22-04

1. Update local `apt` package cache.
    `sudo apt update`
2. Install Redis.
    `sudo apt install redis-server`
3. Generate a strong password for `redis`.
    `openssl rand 60 | openssl base64 -A`
4. Copy the output of the command, and add to `.env` file using key `redis_password`.
    `nano .env`
    `redis_password=<your-strong-password>`
4. Configure Redis.
    `sudo nano /etc/redis/redis.conf`
    1. Use `systemd` to manage `redis`
    `supervised systemd`
    2. Require password by uncommenting the `requirepass` directive and paste the strong password previously generated.
    `requirepass <your-strong-password>`
    3. Rename dangerous redis commands.
        ```
        rename-command FLUSHDB ""
        rename-command FLUSHALL ""
        rename-command DEBUG ""
        rename-command SHUTDOWN SHUTDOWN_MENOT
        rename-command CONFIG ASC12_CONFIG
        ```
5. Restart Redis.
    `sudo systemctl restart redis.service`

### Setup python environment
1. Install `pip`.
    `sudo apt install -y python3-pip`
2. Install other useful packages.
    `sudo apt install -y build-essential libssl-dev libffi-dev python3-dev`
3. Install OpenCV.
    `sudo apt install libopencv-dev python3-opencv`
4. Install virtual environment support for Python. Here, `venv` is used, but other virtual enviroment implementation may be used.
    `sudo apt install -y python3-venv`
5. Create virtual environment named `myenv` (or other name you prefer) inside project directory.
    `cd face-recognition`
    `python3 -m venv myenv`
6. Activate the virtual environment.
    `source myenv/bin/activate`
7. With virtual environment active, inside the project directory:
    `pip install -r requirements.txt`
    - In case errors are encountered when installing `torch` packages, comment out these three lines in `requirements.txt`:
        ```
        # torch==2.2.1+cpu
        # torchaudio==2.2.1+cpu
        # torchvision==0.17.1+cpu
        ```
        and manually install the packages:
        ```
        pip install torch
        pip install torchaudio
        pip install torchvision
        ```
8. Install dependencies for spoof detector.
    `cd Silent-Face-Anti-Spoofing/`
    `pip install -r requirements.txt`

### Test Run the face-recognition API
Inside the project directory, with virtual environment active:
`uvicorn api:app --reload`
By default, the API will be accessible through http on `localhost` port `8000`.

### Create **system service** to automatically run the face recognition at system boot
1. Create the service configuration.
    `nano /etc/systemd/system/face-recognition.service`
2. Refer to the sample service definition, for user named `user`, and project files located at `/home/user/face/recognition`
    ```
    [Unit]
    
    Description=Uvicorn service for Face Recognition API
    After=network.target
    
    [Service]
    User=user
    Group=www-data
    
    WorkingDirectory=/home/user/face-recognition
    ExecStart=/home/user/face-recognition/recognizer/myenv/bin/uvicorn api:app --reload
    
    [Install]
    WantedBy=multi-user.target
    ```
3. Enable the service to start on system boot.
    `sudo enable face-recognition.service`

### Setup **nginx**
1. Install `nginx`
    `sudo apt install nginx`
2. Create nginx server block for project. Nginx will proxy the requests to the local `face-recognition.service`.
    `sudo nano /etc/nginx/sites-available/api.smarthr.ph`
3. Refer to the sample nginx server block. *Note: The recommended server name is **api.smarthr.ph**, but other name can be used, as applicable.*
    ```
    server {
       server_name api.smarthr.ph;
       location / {
           include proxy_params;
           proxy_pass http://127.0.0.1:8000;
       }
    }
    ```
4. Activate the server block.
    `sudo ln -s /etc/nginx/sites-available/api.smarthr.ph /etc/nginx/sites-enabled`
5. Test configuration and restart `nginx` if no error.
    `sudo nginx -t`
    `sudo systemctl restart nginx.service`

### Setup **certbot**

1. Install `certbot` using **snap**.
    ```
    sudo snap install core; sudo snap refresh core
    sudo apt remove certbot
    sudo snap install --classic certbot
    sudo ln -s /snap/bin/certbot /usr/bin/certbot
    ```
2. Obtain certificate for the subdomain
    `sudo certbot --nginx -d api.smarthr.ph`
3. Once certificate is successfully obtained, check the API docs at https://api.smarthr.ph/docs.

