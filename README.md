# Zoom virtual background on Ubuntu 20.04-LTS (focal)
Making zoom virtual background on Linux working is very difficult indeed, because zoom requires a **Physical green screen** which is not something we always have. 
The following are some quick commands to build a virtual webcam so you can instantly hook into Zoom. 

For more information please go here: [https://leftsidemonitor.com/ubuntu-zoom-virtual-background-without-physical-green-screen/](https://leftsidemonitor.com/ubuntu-zoom-virtual-background-without-physical-green-screen/)

## Install docker and run everything inside docker
Run the following command to install docker if you don't have one on your local machine yet: 

```bash 
sudo apt-get remove docker docker-engine docker.io containerd runc
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER
docker run hello-world
sudo systemctl enable docker
sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

## Clone this repo, change setting, and run

```bash 
# Install some required packages
sudo apt install v4l2loopback-dkms;
sudo modprobe -r v4l2loopback;
sudo modprobe v4l2loopback devices=1 video_nr=20 card_label="v4l2loopback" exclusive_caps=1;

# Clone
git clone https://github.com/leftsidemonitor/ubuntu-zoom-virtual-background.git ~/ubuntu-zoom-virtual-background
cd ~/ubuntu-zoom-virtual-background
cp docker_defaults.env .env

docker-compose up &;
```

That's it! now in Zoom you should be able to find `v4l2loopback` as the camera name and start using it. 

## Stop the fake camera
``` 
cd ~/ubuntu-zoom-virtual-background; 
docker-compose down &;
```

## Make some alias in your ~/.bashrc so you can start and stop this even faster
The above command can be long to type, let's make some alias so we can run this a bit faster next time by adding the following line to your `.bashrc` file 

```bash 
# add to ~/.bashrc or ~/.zshrc

alias fakecam='sudo modprobe -r v4l2loopback;sudo modprobe v4l2loopback devices=1 video_nr=20 card_label="v4l2loopback" exclusive_caps=1;cd ~/ubuntu-zoom-virtual-background; docker-compose up &;'
alias stopcam='cd ~/ubuntu-zoom-virtual-background; docker-compose down &;'
```
