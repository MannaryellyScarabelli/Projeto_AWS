# Projeto_AWS
Este projeto se desenvolve em um ambiente AWS oferecido pelo programa de estágio da Compass.UOL. Seu objetivo é rodar Wordpress em nuvem em uma instância EC2 utilizando Docker e o banco de dados MySQL.

## Etapas
**1.Criar uma VPC✅**

**2.Criar um banco de dados RDS✅**

**3.Criar um script user_data.sh✅** 

**4.Cria uma EC2✅** 

**5.Conectar a EC2 com o banco de dados✅**

**6.Criar um EFS(Elastic Fyle System)**

**7.Criar um Load Balancer**



## Pré-requisitos
**Ter uma conta AWS com as permissês necessárias**

**Ter acesso a um terminal SSH**


## Criação da VPC

1- Clicar em "criar VPC"

2- Selecionar "criar VPC Ee muito mais", isso permite criar e vizualuizar a tabela de rotas de sub-redes

3- Dê um nome a sua VPC

4- Selecione "em 1 AZ" em Gateways NAT (USD)

![image](https://github.com/user-attachments/assets/a240f985-ddfd-4425-89f7-70113c9c79ae)


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
#!/bin/bash
#user_data.sh - Script de automação para configurar Docker e implantar Wordpress no Ubuntu

#Atualizar o sistema
sudo apt update -y && sudo apt upgrade -y

#Instalar dependências
sudo apt install -y docker.io git curl

#Iniciar e habilitar Docker
sudo systemctl start docker
sudo systemctl enable docker

#Adicionar o usuário atual ao grupo docker (opcional, requer logout/login para aplicar)
sudo usermod -aG docker $USER

#Instalar Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep 'tag_name' | cut -d '"' -f 4)/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

#Criar estrutura de pastas
mkdir -p ~/wordpress-efs
cd ~/wordpress-efs

#Criar o arquivo docker-compose.yml
cat > docker-compose.yml <<EOL
version: '3.7'
services:
  wordpress:
    image: wordpress:latest
    ports:
      - "80:80"
    environment:
      WORDPRESS_DB_HOST: colocar_endpoint
      WORDPRESS_DB_USER: user_rds
      WORDPRESS_DB_PASSWORD: password_rds
      WORDPRESS_DB_NAME: nome_database
    volumes:
      - wp-data:/var/www/html

volumes:
  wp-data:
EOL
#Alterar a propriedade do arquivo docker-compose.yml (caso necessário)
sudo chown $USER:$USER ~/wordpress-efs/docker-compose.yml

#Subir os containers do Docker Compose
cd ~/wordpress-efs && docker-compose up -d
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


## Conectar a instância com o banco de dados

1- Selecione a instãncia criada 
2- Vá em ações, redes e clique em "conectar banco de dados do RDS"
3- Selecione o que mostra na imagem a seguir:

![image](https://github.com/user-attachments/assets/32abefc9-5c23-43fa-b98f-c604a2c7ca35)


