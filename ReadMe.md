# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo service docker start
sudo docker run hello-world

# Install portainer
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.21.3


sudo apt install docker-compose
mkdir ~/odoo
cd ~/odoo
# Paste contents of docker-compose.yml and .env here

# Run the docker-compose.yml in the current directory
docker-compose up -d






# OPTIONAL  OPTIONAL OPTIONAL OPTIONAL
#  Installing and Configuring Nginx to be used as reverse proxy

sudo apt update
sudo apt install nginx

# Add conf file for the odoo site

sudo vi /etc/nginx/sites-available/odoo.conf

server {
    listen       80;
    listen       [::]:80;
    server_name  your_domain_here;

    access_log  /var/log/nginx/odoo.access.log;
    error_log   /var/log/nginx/odoo.error.log;

    location / {
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Proto https;
      proxy_pass http://localhost:8069;
  }
}

# replace your domain in the server_name section.

sudo ln -s /etc/nginx/sites-available/odoo.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx