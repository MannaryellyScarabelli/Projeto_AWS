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




## Instância EC2
