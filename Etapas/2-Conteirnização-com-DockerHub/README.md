## Criação do Dockerfile

- Acesse a sua pasta backend como feito na etapa anterior
- Crie o arquivo Dockerfile, e cole essas configurações:

```bash
FROM python:3.13-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .
EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

- Agora crie também um docker-compose.yaml

```bash
version: "3.8"  
    services:
    backend:
        build:
        context: ./backend
        dockerfile: Dockerfile
        ports:
        - "8000:8000"
        container_name: meu-backend
        restart: unless-stopped
        networks:
        - app-network

    networks:
    app-network:
        driver: bridge
```
## Construindo a Imagem

- Construa a imagem utilizando o docker compose:

```bash
    docker-compose build
```

- Verifique se as imagens foram criadas 
```bash
    docker images
```
- Saida do comando:

Imagem docker images

## Executando a Imagem

- Faça a execução utilizando o docker compose e verifique os conteiners rodando.

```bash
    # Suba a aplicação
    docker-compose up -d
    # Verifique os conteineres
    docker ps
```

- Acesse no navegador http://localhost:8000/docs

Imagem backend navegador 8000

## Publicando a imagem no Docker Hub
- Faça o login na sua conta do docker usando o comando abaixo:

```bash
    docker login -u <username>
```

- Adicione tags e faça o push para o docker 

```bash
    docker tag aplicacao-CICD-backend gasparottoluo/meu-backend:latest
    docker push gasparottoluo/meu-backend:latest
```
Imagem docker no site

- Se conseguiu realizar isso, então tudo certo!


