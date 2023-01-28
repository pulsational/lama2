# Setup env

## Package to docker: terminal
### Setup environment on a fresh Ubuntu
```
sudo apt-get update
sudo apt-get install python3 python3-dev
sudo apt install python3-pip
sudo apt install python-pip
sudo apt  install curl
sudo apt install git-all
// Install notepadqq
//sudo apt-get install notepadqq
sudo snap install notepadqq
// Install oh-my-zsh or oh-my-bash
// oh-my-zsh
//sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
// oh-my-bash
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```
- Docker
```
// Docker install: https://docs.docker.com/engine/install/ubuntu/
// uninstall previous docker
sudo apt-get remove docker docker-engine docker.io containerd runc 
// Update the apt package index and install packages to allow apt to use a repository over HTTPS:
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
// Add Docker’s official GPG key:
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
// Use the following command to set up the repository:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
// To install the latest version, run:
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
// Verify that the Docker Engine installation is successful by running the hello-world image:
sudo docker run hello-world
```
Resolve permission, in case got error
Enter
```
sudo groupadd docker
sudo usermod -aG docker ${USER}
```
Log out and log in
- Git
```
// Generate ssh key for git
ssh-keygen -t ed25519 -C "jason.hou.ca@gmail.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```
Past it to the github SSH.

```
git clone git@github.com:pulsational/lama2.git
git config --global user.email "jason.hou.ca@gmail.com"
git config --global user.name "Pulsational"
```
- Install AWS cli
```
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```
- Setup AWS CLI
AWS user:
```
root: plusmon.graphy@gmail.com
iam: 746615178768, pulsational
// Create them in IAM/User/pulsational/Security Credentials/Access keys
Access key: AKIA23VN3QYIEVA5QWWW
Secret access key: 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
Region: us-west-2
Output format: json
```
Config, with a existing ECR repository
```
aws configure 
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy
```

### Check permission
- Make sure lama folder is owned by current user such as `jason` not `root`.
- Create the folder under the shared folder is always owned by `root`
- You need to clone to another folder.
- If no this step, when install running bash command later, you will have error
```
12:00:06 jason@jason-VirtualBox lama ±|main ✗|→ bash docker/2_predict.sh $(pwd)/big-lama $(pwd)/LaMa_test_images $(pwd)/output device=cpu
docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec /home/user/.local/bin/entrypoint.sh: permission denied: unknown.
```
### Build docker image
```
cd docker
./build.sh
docker images
```
Shows
```
11:46:50 jason@jason-VirtualBox lama ±|main ✗|→ docker images
REPOSITORY      TAG                        IMAGE ID       CREATED             SIZE
windj007/lama   latest                     5aedbe4e9346   55 seconds ago      8.59GB
```

### Install aws cli
- Generate a pair of access key and secret access key in IAM
- Fill region and output format as `json`
### Tag and upload to ecr
- Create a repository in ECR
  
- login
  
```
aws configure
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy
```
### Run prediction
```
cd lama
export TORCH_HOME=$(pwd) && export PYTHONPATH=$(pwd)
bash docker/2_predict.sh $(pwd)/big-lama $(pwd)/LaMa_test_images $(pwd)/output device=cpu

```

## Install python virtualenv
```
pip3 install virtualenv
```
## setup env(in git bash)
```
virtualenv inpenv --python=/c/Users/Renjie/AppData/Local/Programs/Python/Python39/python.exe
source inpenv/Scripts/activate // or in windows, .\inpenv\Scripts\activate
pip install torch==1.8.0 torchvision==0.9.0
pip install -r ./requirements_edited.txt // updated version of scikit-image==0.17.2 to scikit-image==0.19.0
export TORCH_HOME=$(pwd) && export PYTHONPATH=$(pwd)
pip3 install wldhx.yadisk-direct
```
## Download model
```
curl -L $(yadisk-direct https://disk.yandex.ru/d/ouP6l8VJ0HpMZg) -o big-lama.zip
unzip big-lama.zip
```
## Download test images
```
curl -L $(yadisk-direct https://disk.yandex.ru/d/xKQJZeVRk5vLlQ) -o LaMa_test_images.zip
unzip LaMa_test_images.zip
```

## predict
```
python bin/predict.py model.path=$(pwd)/big-lama indir=$(pwd)/LaMa_test_images outdir=$(pwd)/output
```



## Package a deployment zip
### Install the packages by pip
```
source venv/bin/activate
pip install <whatever package>
deactivate
```
### Pack to zip
You may install zip for git bash. Copy `zip/bzip2.dll` and `zip/zip.exe` to `C:\Program Files\Git\usr\bin`.
```
cd ./venv/Lib/site-packages/
zip -r ../../../lambda-package.zip .
cd ../../..
zip -g lambda-package.zip lambda_function.py
```
## Package to docker: git bash
In git bash
```
cd docker
./build.sh
docker images
```
Shows
```
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
windj007/lama   latest    06369b6cf45e   13 minutes ago   8.59GB
```
### Install aws cli
- Generate a pair of access key and secret access key in IAM
- Fill region and output format as `json`
### Tag and upload to ecr
- Create a repository in ECR
  
- login
  
```
aws configure
aws ecr get-login-password --region us-west-2 | docker login --username AWS --password-stdin 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy
```
- Push
Try
```
docker tag alpine/git:latest 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy.test:latest
docker push 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy.test:latest
```
Real
```
docker tag windj007/lama:latest 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy:latest
docker push 746615178768.dkr.ecr.us-west-2.amazonaws.com/plusmon.graphy:latest
```

## Trouble shooting
- Error
```
exec /home/user/.local/bin/entrypoint.sh: no such file or directory
```
Go to [this page](https://stackoverflow.com/questions/38905135/why-wont-my-docker-entrypoint-sh-execute),
Click on the bottom of IDE the `UTF-8` and change the encoding to `UTF-8`. Rebuild the image and solved. 
