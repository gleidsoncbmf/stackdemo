<h1 align='center'>stackdemo</h1>  
<h6>Grupo: Gleidson e Mateus</h6>
Execute agora os seguintes comandos, vale lembrar que esses comandos devem ser executados individualmente:


    # Iniciando um cluster do Docker Swarm
    docker swarm init
    
    # Criando um serviço chamado "registry" que publica a porta 5000 e utiliza a imagem "registry:2" do Docker Hub
    docker service create --name registry --publish published=5000,target=5000 registry:2
    
    # Listando os serviços em execução no cluster Swarm
    docker service ls
    
    # Fazendo uma solicitação HTTP GET para o serviço de registro
    curl http://localhost:5000/v2/
      
	  

------------


Nestes próximos passos iremos montar os arquivos antes de subir a aplicação, monte o **Dockerfile**  . 
```
# Imagem base do Python 3.4 no Alpine Linux
FROM python:3.4-alpine    

# Copia o conteúdo atual para /code no contêiner
ADD . /code               

# Define /code como o diretório de trabalho
WORKDIR /code             

# Instala as dependências do arquivo requirements.txt
RUN pip install -r requirements.txt    

# Define o comando padrão para executar app.py
CMD ["python", "app.py"]  

```

------------

Agora vamos montar o **docker-compose.yml**
```
services:
  web:
    image: 127.0.0.1:5000/stackdemo
    build: .
    ports:
      - "8000:8000"
  redis:
    image: redis:alpine
```

------------

Agora vamos montar o **requirements.txt**
```
flask
redis
```
------------

E por fim, vamos montar o **app.py**  
```
from flask import Flask
from redis import Redis

app = Flask(__name__)
redis = Redis(host='redis', port=6379)

@app.route('/')
def hello():
    count = redis.incr('hits')
    return 'Hello World! I have been seen {} times.\n'.format(count)

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=8000, debug=True)
```
Este link leva a montagem dessa mesma aplicação com mais detalhes [Deploy a stack to a swarm](https://docs.docker.com/engine/swarm/stack-deploy/?authuser=0 "Deploy a stack to a swarm")  

------------



Execute agora os seguintes comandos, vale lembrar que esses comandos devem ser executados **individualmente:**  

    # Inicia os serviços definidos no arquivo docker-compose.yml em segundo plano
    docker-compose up -d  
    
    # Lista os serviços definidos no arquivo
    docker-compose ps  
    
    # Obtém o token de junção para adicionar um nó ao cluster Docker Swarm como gerente
    docker swarm join-token manager  
    
	

------------


Para se conectar ao manager é necessário fazer essa conexão via tocken que é gerado, ao fazer isso, a conexão será feita no manager.
    
