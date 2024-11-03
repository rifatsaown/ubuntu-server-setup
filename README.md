# Setup of Linax(Ubuntu) for first time Essential Tools

## 1. Update and Upgrade
```bash
sudo apt update
sudo apt upgrade
```

## 2. Install Essential Tools
```bash
sudo apt install -y git curl wget nano unzip # Install Essential Tools
```

## 3. Install Node.js
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash # Install NVM (Node Version Manager)
nvm install node # Install Latest Node.js
# or
nvm install --lts # Install Latest LTS Node.js
# or
nvm install 14.17.6 # Install Specific Node.js
```

## 4. Install Yarn
```bash
npm install -g yarn
```

## 5. Install Zsh
```bash
sudo apt install -y zsh
chsh -s $(which zsh) # Change Shell to Zsh
# Logout and Login to Apply Changes
```

## 6. Install Oh My Zsh
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```


## 7. Install Docker Details in [Docker-Setup.md](Docker-Setup.md)
```bash
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER # Add User to Docker Group (Reboot Required) 
```

## 8. Install Docker Compose
```bash
sudo apt install -y docker-compose
```

## 9. Install Nginx Details in [Nginx-Setup.md](Nginx-Setup.md)
```bash
sudo apt install -y nginx # Install Nginx
sudo systemctl start nginx # Start Nginx
sudo systemctl enable nginx # Enable Nginx to Start on Boot
```

## 10. Install Python
```bash
sudo apt install -y python3
```