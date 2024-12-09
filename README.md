# Projeto de Redes 

## Objetivo do Trabalho
Entrega de um projeto de redes que utilize AWS ou VirtualBox para criar uma infraestrutura de rede robusta e escalável.

## Requisitos do Projeto

### 1. Arquitetura da Rede
   - **Desenho da Topologia**: Esboçar a topologia da rede, incluindo todas as máquinas, conexões e fluxos de dados envolvidos.
   - **Detalhamento de Componentes**:
     - Load Balancer
     - Proxy Reverso
     - Banco de Dados

### 2. Configuração do Load Balancer
   - **Implementação**: Utilizar o Nginx como load balancer.
   - **Balanceamento de Carga**: Configurar o load balancer para distribuir o tráfego entre pelo menos três máquinas que fornecerão conteúdo web.

### 3. Proxy Reverso
   - **Configuração**: Designar uma máquina para atuar como proxy reverso usando Nginx.
   - **Gerenciamento de Requisições**: Explicar o funcionamento do proxy reverso na gestão e redirecionamento de requisições para as máquinas corretas.

### 4. Banco de Dados
   - **Configuração do Banco de Dados**:
     - Criar uma máquina para o banco de dados usando Docker ou AWS RDS.
     - Escolher um banco de dados relacional ou não relacional (ex.: MySQL, PostgreSQL, MongoDB).
   - **Segurança**: Descrever as medidas de segurança adotadas para proteção dos dados armazenados.

### 5. VPN
   - **Implementação da VPN**:
     - Configurar uma VPN (ex.: OpenVPN) para garantir acesso seguro à rede da empresa XPTO.
     - Demonstrar o processo de configuração e a integração da VPN com o restante da infraestrutura.

### 6. Docker
   - **Containers**:
     - Utilizar Docker para criação e gerenciamento dos containers dos servidores web e do banco de dados.
   - **Comunicação entre Containers**: Explicar como os containers são configurados para se comunicarem entre si.
   - **Escalabilidade e Migração**: Demonstrar como os containers podem ser escalados ou migrados para outros ambientes.


## Topologia

<img src="/img/02_topologia_rede.png"/>


## Configurações das máquinas



A entrega foi feita utilizando AWS. 

Todas as máquinas foram configuradas com o protocolo SSH e HTTP, e um par de chaves foi utilizado para acessar as instâncias. Para simplificação e fins acadêmicos, o mesmo par de chaves pode ser usado em todas as máquinas. Porém, para uma configuração mais segura, é recomendável gerar uma chave individual para cada máquina.

### Portas Liberadas

| Serviço | Porta | Protocolo | Descrição                  |
|---------|-------|-----------|----------------------------|
| SSH     | 22    | TCP       | Acesso remoto às VMs       |
| HTTP    | 80    | TCP       | Tráfego web não seguro     |
| HTTPS   | 443   | TCP       | Tráfego web seguro         |

### Criando uma instância

Ao criar sua MV escolha a opção de chave .pem

[Vídeo tutorial](https://www.youtube.com/watch?v=YfnHh-jb2eo)



### Acesso as máquinas

O acesso as MV da AWS pode ser feito via terminal, caso seu sistema operacional seja linux. No caso do Windows o acesso pode ser feito pelo Bitvise.

### Bitvise

Baixe e instale o [Bitvise SSH Client](https://bitvise.com/ssh-client-download).

-No campo *host* coloque o ip da máquina irá acessar

-Em *Username* coloque ubuntu

-Importe a chave que foi baixada na criação da MV em *Client Key manager*

-Selecione a chave da MV em *Client Key*

<video width="320" height="240" controls>
  <source src="img/1209.mp4" type="video/mp4">
  Seu navegador não suporta o elemento de vídeo.
</video>


## Configuração da Máquina Frontend

 Instalação dos Serviços

Para configurar o ambiente, instale os seguintes serviços:

- **nodejs**
- **git**
- **npm**
- **apache2**

-Execute os comandos abaixo:

```
sudo apt update
 ```

```
sudo apt install git
 ```

  ```
sudo apt install npm
 ```

  ```
sudo apt install nodejs
 ```
  ```
sudo apt install apache2
 ```

-Clone o projeto:

```
git clone [link do projeto]
 ```

-Entre no arquivo api.tsx e certifique-se de colocar o ip público da máquina:

```
sudo nano Projeto-de-Sistemas-Operacionais/frontend/src/services/api.tsx
 ```

```
import axios from "axios";

const api = axios.create({baseURL : 'http://IPv4-público-da-instância:3000/'});

export default api

```

-Logo em seguida instale as dependências do frontend.

```
cd Projeto-de-Sistemas-Operacionais/frontend
npm install

```

-No arquivo :

nano node_modules/jwt-decode/build/esm/index.d.ts

adicione o seguinte campo:


```
export interface JwtPayload {
    iss?: string;
    sub?: string;
    aud?: string[] | string;
    exp?: number;
    nbf?: number;
    iat?: number;
    jti?: string;
    role?: any;
}
```
-Libere o módulo de reescrita:

```
a2enmod rewrite
```

-Reestart o apache2:

```
systemctl restart apache2

```

-Após isso realize o build:

```
npm run build

```

 -Configure o apache retornar a página do frontend:

```
 sudo mv Projeto-de-Sistemas-Operacionais/frontend/dist /var/www/
 ```

```
sudo nano /etc/apache2/sites-available/000-default.conf
```
-Edite o arquivo abaixo:

```
    <VirtualHost *:80>
      ServerAdmin webmaster@localhost
      DocumentRoot /var/www/html/seu-app-react/build

      <Directory /var/www/dist>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted

        # Ativando a reescrita
        RewriteEngine On

        # Se o arquivo solicitado não existir, redireciona para o index.html
        RewriteCond %{REQUEST_FILENAME} !-f
        RewriteCond %{REQUEST_FILENAME} !-d
        RewriteRule ^ /index.html [L]
    </Directory>
  </VirtualHost>

```

-Criar o arquivo .htaccess na pasta /var/www/dist
```
    <IfModule mod_rewrite.c>
    RewriteEngine On
    RewriteBase /

    # Se o arquivo solicitado não existir, redireciona para o index.html
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteRule ^ /index.html [L]
</IfModule>
```

-Reinicie o Apache para aplicar as configurações:

```
    sudo systemctl restart apache2
```


## Configuração da Máquina Backend

Instalação dos Serviços

Para configurar o ambiente, instale os seguintes serviços:

- **nodejs**
- **git**
- **npm**
- **NestJs**

-Execute os comandos abaixo:

```
sudo apt update
 ```

```
sudo apt install git
 ```

  ```
sudo apt install npm
 ```

  ```
sudo apt install nodejs
 ```
  ```
npm i -g @nestjs/cli

 ```

-Servidor de Banco de Dados – MySQL/MariaDB:

  ```
sudo apt install default-mysql-server
 ```

-Após a instalar, acesse o MySQL:

  ```
sudo mysql -u root
 ```

-Adicione uma senha para o usuário root e crie um banco de dados:
 ```
 ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'senha-de-root';

 CREATE DATABASE nome-do-banco;

 exit;
 ```


-Instale as dependências do backEnd:

```cmd
cd Projeto-de-Sistemas-Operacionais/backend
npm install
npm install --save @nestjs/typeorm typeorm mysql2

```

-Crie e configure o arquivo .env:

```cmd
sudo touch .env
```
-Conteúdo:

```cmd
sudo nano .env
```

```cmd
DB_HOST=127.0.0.1
DB_PORT=3306
DB_USER=root
DB_PASSWORD=sua-senha
DATABASE=nome-do-banco-de-dados
TOKEN_JWT=coloque-qualquer-coisa
```

-Rodar o backEnd:

```cmd
nest start
```

## Configuração da Máquina Load Balancer e Proxy Reverso

-Libere a porta 81 na AWS

-Instale o NGINX:
  ```cmd
  sudo apt install nginx
  ```
-Crie o arquivo do Load Balance e Proxy Reverso na pasta /etc/nginx/conf.d/loadbalancer.conf

```cmd
  sudo nano /etc/nginx/conf.d/loadbalancer.conf
```

-Configure o arquivo do Load Balance e Proxy Reverso:

```cmd
upstream loadbalancer{
	server ip_publico1:80;
	server ip_publico2:80;
	server ip_publico3:80;
}

server {
	listen 81; 


	location / {
                  proxy_pass http://loadbalancer;
      }

            location ~* \.(css|js|png|jpg|jpeg) {
                        proxy_pass http://loadbalancer;
            }
}

```

-Reinicie o NGINX para aplicar as configurações:

  ```cmd
  sudo systemctl restart nginx
  ```

##  Configuração do Docker.

### Instale o Docker e Docker Compose

```cmd
  sudo apt-get update
  sudo apt install docker.io -y
  sudo apt install docker-compose
```

### Crie o arquivo docker compose

-Criar o arquivo `docker-compose.yml`

```cmd
  sudo vim docker-compose.yml
```

-Insira as seguintes informações no arquivo `docker-compose.yml`

```cmd
  services:
    mysql:
        image: mysql:8
        container_name: "mysql-2"
        environment:
            MYSQL_ROOT_PASSWORD: ${BD_ROOT_PASSWORD}
            MYSQL_DATABASE: ${DATABASE}
        ports:
            - ${BD_PORT}
        restart: always
```

-Rode o docker-compose

```cmd
  sudo docker-compose up -d
```

##  Configuração da VPN

Neste trabalho a configuração da VPN foi feita na mesma máquina que o Loadbalancer e o Proxy Reverso.

Será necessário que a porta *1194* seja liberada na instância AWS.

Também baixe e instale o [OpenVPN GUI](https://openvpn.net/community-downloads/).


No terminal da instância da AWS reazlize os seguintes passos:

-Instalação da OpenVPN

```
  sudo docker-compose up -d
```
```
  sudo apt install openvpn
```
-Dentro da pasta openvpn exclua as pastas padrões client e server:

```
  cd /etc/openvpn
```
```
  rm -r client
```
```
  rm -r server
```
-Crie um arquivo de configuração para o servidor:
```
  sudo vim servidor.conf
```

-Adiciona as seguintes configurações no arquivo:
```
  dev tun
ifconfig 10.0.0.1 (servidor) 10.0.0.2 (cliente)
remote 54.197.91.67
secret C://chavevpn
port 1194
proto udp
keepalive 10 120
persist-tun
persist-key
verb 4
float
comp-lzo
cipher AES256
```
-Crie um arquivo de configuração para o cliente:

```
  sudo vim cliente.conf
```

-Adiciona as seguintes configurações no arquivo:
```
  dev tun
ifconfig 10.0.0.2 (cliente) 10.0.0.1 (servidor)
remote 54.197.91.67
secret C://chavevpn (local da chave no Windows)
port 1194
proto udp
keepalive 10 120
persist-tun
persist-key
verb 4
float
comp-lzo
cipher AES256
```

-Crie uma chave criptografada:
```
  openvpn --genkey --secret chave
```

-Entre em /etc/nginx/sites-available e certifique-se de:

```
 cd /etc/nginx/sites-available
```
```
 server{
   listen 80 default_server;
   listen [::]:80 default_server;

 }
 ```
 ```
 server_name 10.0.0.1;

      location / {
                  proxy_pass http://loadbalancer;
      }

            location ~* \.(css|js|png|jpg|jpeg) {
                        proxy_pass http://loadbalancer;
            }


```

-reinicie o nginx:
```
sudo systemctl restart nginx
```


-Copie tanto a chave como o arquivo de configuração do cliente para a home do ubuntu, iremos passa-lo para o computador, pois terminaremos a configuração da vpn no OpenVPN Gui.

```
  cp chave /home/ubuntu
```
```
  cp cliente.conf /home/ubuntu
```

-Mude a permissões da chave e do arquivo do cliente:

```
  chown ubuntu:ubuntu chave 
```
```
  chown ubuntu:ubuntu cliente.conf
```

-Através do Bitvise copie para o windows a chave e o arquivo de configuração client.conf


-Com os arquivos em seu computador, coloque a chave dentro do Disco Local (C:), do seu computador.

-Renomeie o arquivo client.conf para a extenção ovpn, para que ele seja reconhecido pelo Windows, coloque-o dentro da pasta confg do OpenVPN, para isso percorra o seguinte caminho: C:\Program Files\OpenVPN\config

-No terminal da instância AWS inicie o servidor e o cliente:

```
    openvpn --config servidor.conf
```
```
    openvpn --config cliente.conf
```

-No seu computador conecte o cliente através do OpenVPN, clique com o botão direito no ícone e clique em conectar, quando o ícone estiver verde a conexão está completa. No navegador coloque o ip do servidor *10.0.0.1:81* para acessar o site.

<img src="/img/OpenVpnConectar.png"/>






