# Projeto_AWS
Este projeto se desenvolve em um ambiente AWS oferecido pelo programa de estágio da Compass.UOL. Seu objetivo é rodar Wordpress em nuvem em uma instância EC2 utilizando Docker e o banco de dados MySQL.

## Etapas
**1.Criar uma VPC✅**

**2.Grupos de segurança✅**

**3.Criar um banco de dados RDS✅**

**4.Criar um EFS(Elastic Fyle System)✅**

**5.Criar um script user_data.sh✅** 

**6.Cria uma EC2✅** 

**7.Criar um Load Balancer✅**

**8.Criar grupo de Auto Scaling✅**


## Pré-requisitos
**Ter uma conta AWS com as permissês necessárias**

**Ter acesso a um terminal SSH**


## Criação da VPC

1- Clicar em "criar VPC"

2- Selecionar "criar VPC Ee muito mais", isso permite criar e vizualuizar a tabela de rotas de sub-redes

3- Dê um nome a sua VPC

4- Selecione "em 1 AZ" em Gateways NAT (USD)

![image](https://github.com/user-attachments/assets/a240f985-ddfd-4425-89f7-70113c9c79ae)

### Grupos de segurança (Regras de acesso)
Para configurar as regras de acesso é necessário:

### Para grupo público, as entradas permitidas são:

HTTP: (porta 80) de qualquer origem (0.0.0.0/0)

HTTPS: (porta 443) de qualquer origem (0.0.0.0/0)

SSH: (porta 22) de qualquer origem (0.0.0.0/0)

### Saídas permitidas, são:

Todo o tráfego, sem restrição de portas ou protocolos

### Para grupo privado, as entradas permitidas são:

MySQL: (porta 3306) de qualquer origem.

HTTP: (porta 80) e HTTPS (porta 443) apenas do grupo público.

SSH: (porta 22) de qualquer origem.

NFS: (porta 2049) de qualquer origem

### Saídas permitidas, são:

Todo o tráfego liberado.


## Criação da RDS

1- Criação padrão

2- MySQL

3- Nível gratuito

4- Nomeie a instância

5- Nomeie o nome do usuário principal

6- Crie uma senha

7- Selecione "db.t3.micro"

8- Crie um grupo de segurança 

9- Selecione a zona de disponibilidade "us-east-1a"

10- Em configuração adicional dê o nome ao banco de dados

![image](https://github.com/user-attachments/assets/3734e8d3-c3ae-4e9a-a2de-72385a295f82)



## Script de automação


```
!/bin/bash 
 
sudo yum update -y 
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker ec2-user
newgrp docker
sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
sudo mkdir /home/ec2-user/wordpress
cat <<EOF > /home/ec2-user/wordpress/docker-compose.yml
services:
 
  wordpress:
    image: wordpress
    restart: always
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_HOST: endpoint
      WORDPRESS_DB_USER: user_nome
      WORDPRESS_DB_PASSWORD: senha
      WORDPRESS_DB_NAME: database_nome
    volumes:
      - /mnt/efs:/var/www/html
EOF
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-075721db6bcd55dce.efs.us-east-1.amazonaws.com:/ /mnt/efs
docker-compose -f /home/ec2-user/wordpress/docker-compose.yml up -d
````


## Instância EC2

1- Clicar em "executar instância"

2- Colocar as tags necessárias, observe a seguir:

![image](https://github.com/user-attachments/assets/11ea68e8-d364-4e83-9813-914a4f702e40)

3- Selecione Ubuntu

4- Crie um par de chave

5- Selecione a VPC criada e a sub-rede

6- Crie um grupo de segurança para a EC2

7- Coloque as regras de entrada como mostra a imagem a seguir:

![image](https://github.com/user-attachments/assets/f6ef1a00-f42f-4282-bed4-ae77e45e558f)

8- Em detalhes avançados adicone o script e execute a instância


## Criação Load Balancer

Nesta etapa será onde 
