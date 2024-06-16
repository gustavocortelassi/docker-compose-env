# Docker Compose Project

Desenvolvimento de um ambiente Docker com os seguintes requisitos:
- Aplicação web (Wordpress)
- Banco de dados (MySQL)
- Banco de cache (Redis)
- Ferramenta de monitoramento (Prometheus)

Utilizando docker-compose.

## Pré-requisitos para rodar o projeto

1. Docker Desktop.
2. WSL.

## Como executar esse repositório

1. Clone o repositório para sua máquina:
```
git clone https://github.com/gustavocortelassi/docker-compose-env
```
3. Execute o Docker Compose:
```
docker-compose up -d
```
5. Verifique os containers em execução:
```
docker ps
```
6. Verifique o serviço Wordpress:
Acesse http://localhost:8080 no navegador.

7. Verifique o serviço MySQL:
```
docker exec -it dockercompose-db-1 mysql -uwordpress -p
```
senha: wordpress.

8. Verifique o serviço Redis:
```
docker exec -it dockercompose-redis-1 redis-cli
```
comando: ```ping```

9. Verifique o serviço Prometheus:
Acesse http://localhost:9090 no navegador
Status > Target

# Criando o Projeto

1. Criação de uma pasta da área de trabalho, com o nome de 'docker compose'.
2. Criação de um arquivo, dentro da pasta, com o nome de 'docker-compose.yaml'.
3. Criação de um arquivo, dentro da pasta, com o nome de 'prometheus.yml'.

## Configurações do arquivo docker-compose.yaml.
Nesta seção, será feita a configuração do arquivo docker-compose.yaml. Siga os passos abaixo:

1. Definir a versão do docker compose que será utilizado.
```
version: '3.8'
```
2. Iniciar a seção de serviços que será utilizado - lembre-se que o arquivo .yaml tem hierarquia de espaços, ou seja, ele utiliza espaços para estruturar seu conteúdo de forma hierárquica e aninhada.
```
services:
       wordpress:
       db:
       redis:
       prometheus:
```
3. Iniciar a seção de volumes que será criado.
```
volumes:
       wordpress_data:
       db_data:
       redis_data:
       prometheus_data:
```
4. Configuração do serviço wordpress:
Especifica que o container utilizará a imagem mais recende do Wordpress.
```
- image: wordpress:latest
```
Indica que o contêiner deve ser reiniciado sempre que parar.
```
- restart: always
```
Realiza o mapeamento para a porta que será executado(porta 80 do container wordpress para a porta 8080 do host).
```
- ports: 
       - "8080:80"
```
Configura variáveis de ambiente necessário para rodar o wordpress.
```
- environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
```
Define os volumes que serão utilizados e onde serão mapeados.
```
- volumes:
      - wordpress_data:/var/www/html
```
Seu arquivo final deve se parecer com isso:
```
wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress_data:/var/www/html
```
5. Configuração do serviço db:
Especifica que o container utilizará a imagem mais recente do MySQL.
```
- image: mysql:latest
```
Indica que o contêiner deve ser reiniciado sempre que parar.
```
- restart: always
```
Configura variáveis de ambiente necessário para rodar o db.
```
- environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_ROOT_PASSWORD: rootpassword
```
Define os volumes que serão utilizados e onde serão mapeados.
```
- volumes:
      - db_data:/var/lib/mysql
```
Seu arquivo final deve se parecer com isso:
```
db:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db_data:/var/lib/mysql
```
6. Configuração do serviço redis:
Especifica que o container utilizará a imagem mais recente do Redis.
```
- image: redis:latest
```
Indica que o contêiner deve ser reiniciado sempre que parar.
```
- restart: always
```
Realiza o mapeamento para a porta que será executado(porta 6379 do container wordpress para a porta 6379 do host).
```
- ports:
      - "6379:6379"
```
Define os volumes que serão utilizados e onde serão mapeados.
```
- volumes:
      - redis_data:/data
```
Seu arquivo final deve se parecer com isso:
```
redis:
    image: redis:latest
    restart: always
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
```
7. Configuração do serviço prometheus:
Especifica que o container utilizará a imagem mais recente do Prometheus.
```
- image: prom/prometheus:latest
```
Indica que o contêiner deve ser reiniciado sempre que parar.
```
- restart: always
```
Realiza o mapeamento para a porta que será executado(porta 9090 do container wordpress para a porta 9090 do host).
```
- ports:
      - "9090:9090"
```
Define os volumes que serão utilizados e onde serão mapeados.
```
- volumes:
      - prometheus_data:/prometheus
```
Define o comando que será executado quando iniciar o container(indica onde o prometheus vai achar suas configurações).
```
command:
      - '--config.file=/etc/prometheus/prometheus.yml'
```
Seu arquivo final deve se parecer com isso:
```
prometheus:
    image: prom/prometheus:latest
    restart: always
    ports:
      - "9090:9090"
    volumes:
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
```
8. Configuração dos volumes:
Volumes criados para persistir dados entre os containers
```
- volumes:
         wordpress_data:
         db_data:
         redis_data:
         prometheus_data:
```

9. Arquivo final:
```
version: '3.8'

services:
  wordpress:
    image: wordpress:latest
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress_data:/var/www/html

  db:
    image: mysql:latest
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - db_data:/var/lib/mysql

  redis:
    image: redis:latest
    restart: always
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  prometheus:
    image: prom/prometheus:latest
    restart: always
    ports:
      - "9090:9090"
    volumes:
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'

volumes:
  wordpress_data:
  db_data:
  redis_data:
  prometheus_data:

```
## Configurações do arquivo prometheus.yml.
Nesta seção, será feita a configuração do arquivo prometheus.yml. Siga os passos abaixo:
Configuração de scrape: Define globalmente, o intervalo em que o prometheus irá coletar metricas para os targets que serão definidos
```
global:
  scrape_interval: 15s
```
Configuração de scrape: Define as configuracoes individuais de cada job (coleta de metrica)
```
scrape_configs:
```
Define o job prometheus, que irá realizar coleta do próprio servidor do prometheus
```
- job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
```
Define o job wordpress, que irá realizar coleta do servico wordpress, que está rodando na porta 8080.
```
- job_name: 'wordpress'
    metrics_path: /metrics
    static_configs:
      - targets: ['wordpress:80']
```
Define o job mysql, que irá realizar coleta do servico mysql, que está rodando na porta 9104.
```
- job_name: 'mysql'
    metrics_path: /metrics
    static_configs:
      - targets: ['db:9104']
```
Define o job redis, que irá realizar coleta do servico redis, que está rodando na porta 6379.
```
- job_name: 'redis'
    static_configs:
      - targets: ['redis:6379']
```
Arquivo final:
```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
      
  - job_name: 'wordpress'
    metrics_path: /metrics
    static_configs:
      - targets: ['wordpress:80']
      
  - job_name: 'mysql'
    metrics_path: /metrics
    static_configs:
      - targets: ['db:9104'] 

  - job_name: 'redis'
    static_configs:
      - targets: ['redis:6379']
```
