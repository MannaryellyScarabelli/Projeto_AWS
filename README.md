# Projeto_AWS
Este projeto se desenvolve em um ambiente AWS oferecido pelo programa de estágio da Compass.UOL. Seu objetivo é rodar Wordpress em nuvem em uma instância EC2 utilizando Docker e o banco de dados MySQL.

## Etapas

**1.Criar uma VPC✅**

**2.Grupos de segurança✅**

**3.Criar um banco de dados RDS✅**

**4.Criar um EFS(Elastic Fyle System)✅**

**5.Cria uma EC2✅** 

**6.Criar um Load Balancer✅**

**7.Criar grupo de Auto Scaling✅**

**8.Verificação✅**


## Pré-requisitos
**Ter uma conta AWS com as permissês necessárias**

## Criação da VPC

1- Clicar em "criar VPC"

2- Selecionar "criar VPC e muito mais", isso permite criar e vizualuizar a tabela de rotas de sub-redes

3- Dê um nome a sua VPC

4- Selecione "em 1 AZ" em Gateways NAT (USD)

![image](https://github.com/user-attachments/assets/74c016aa-22fc-4f84-97bb-1ec4c1512cda)


## Grupos de segurança (Regras de acesso)

Nesta etapa é onde ocorre a criação dos grupos de segurança. No canto esquerdo na AWS selecione "grupo de segurança" e crie um com as seguintes especificações: 

>Para grupo público, as entradas permitidas são:

HTTP: (porta 80) de qualquer origem (0.0.0.0/0)

HTTPS: (porta 443) de qualquer origem (0.0.0.0/0)

SSH: (porta 22) de qualquer origem (0.0.0.0/0)

>Saídas permitidas, são:

Todo o tráfego, sem restrição de portas ou protocolos

>Para grupo privado, as entradas permitidas são:

MySQL: (porta 3306) de qualquer origem.

HTTP: (porta 80) e HTTPS (porta 443) apenas do grupo público.

SSH: (porta 22) de qualquer origem.

NFS: (porta 2049) de qualquer origem

>Saídas permitidas, são:

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

![image](https://github.com/user-attachments/assets/6b3fc1b6-9843-4b8c-9bdb-f4534d2efdc3)


## Criação do Elastic File System (EFS)

1- Vá em EFS

2- Nomeie seu EFS

3- Selecione as sub-redes privadas criadas na VPC

4- Finalize as configurações e verifique que está em um grupo de segurança privado

  ![image](https://github.com/user-attachments/assets/0e826417-c876-4ca9-be90-c8ad063f0f08)
- Vá em anexar e atualize seu script usando a motagem via cliente DNS, subtituindo o "efs" pelo caminho do diretório de montagem



## Instâncias EC2

1- Clicar em "executar instância"

2- Colocar as tags necessárias, observe a seguir:

![image](https://github.com/user-attachments/assets/8cc3c0f3-cacd-4ac9-9184-2ff54c9bfb53)


3- Selecione Amazon Linux 2023

4- Crie um par de chave

5- Selecione a VPC criada e a sub-rede privada

6- Crie um grupo de segurança para a EC2

7- Coloque as regras de entrada como mostra a imagem a seguir:

![image](https://github.com/user-attachments/assets/f6ef1a00-f42f-4282-bed4-ae77e45e558f)

8- Em detalhes avançados adicone o script e execute a instância
9- Repita os passos criando outra instância quase idêntica, alterando apenas a zona de disponibilidade da sub-rede privada (se criou na A, agora crie na B e vice-versa) 


````
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
      WORDPRESS_DB_HOST: endpoint:porta do bd
      WORDPRESS_DB_USER: user_nome
      WORDPRESS_DB_PASSWORD: senha
      WORDPRESS_DB_NAME: database_nome
    volumes:
      - /mnt/efs:/var/www/html
EOF
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-00b28bccaed41500a.efs.us-east-1.amazonaws.com:/ /mnt/efs
docker-compose -f /home/ec2-user/wordpress/docker-compose.yml up -d

````

## Criação do Load Balancer

1- Para iniciar escolha a opção "Classic Load Balancer"

2- Adicione a VPC e as sub-redes públicas

3- Adicione o grupo de segurança público

4- Adicione a instância EC2

5- Para o monitoramento de saúde coloque o protocolo HTTP

6- Porta: 80

7- Caminho de ping: /wp-admin/install.php


## Grupo Auto Scaling

Nesta etapa será onde realiza as configurações para replicar as instâncias EC2.

1- Selecione as mesmas configurações da EC2 no seu grupo de autoscaling (menos as sub-redes públicas, selecione as privadas)

3- Conecte ao Load Balancer

4- Realize a finalização 

## Verificação

Para verificar o funcionamento, copie o DNS do Load Balancer e coloque no navegador para acessar o Wordpress.

![image](https://github.com/user-attachments/assets/428940b6-5237-4972-97ba-92a5832f630d)

