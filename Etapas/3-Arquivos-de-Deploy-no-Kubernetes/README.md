## Criação do deployment.yaml

- Prepare sua imagem

```bash
    docker tag backend-app:latest meu-backend:v1.0.0
```

- Navegue até a pasta k8s

```bash
    cd k8s/
```

- Crie o dockerfile e configure como está abaixo:

```bash
# Backend Deployment
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    name: backend-app
    namespace: default
    spec:
    replicas: 2
    selector:
        matchLabels:
        app: backend-app
    template:
        metadata:
        labels:
            app: backend-app
        spec:
        containers:
        - name: backend-app
            image: gasparottoluo/meu-backend:v1.0.0  
            ports:
            - containerPort: 8000
            imagePullPolicy: Always
    ---
    apiVersion: v1
    kind: Service
    metadata:
    name: backend-service
    namespace: default
    spec:
    selector:
        app: backend-app
    ports:
    - port: 80
        targetPort: 8000
        nodePort: 30081
    type: NodePort
```

- Faça o Deploy no Kubernetes
```bash
    kubectl apply -f deployment.yaml

    kubectl get pods

    kubectl get svc
```

- Acesse o Kubernetes 
    - Backend API: http://localhost:3000
    - Documentação API: http://localhost:30001/docs

- Se tudo deu certo até aqui, então perfeito!