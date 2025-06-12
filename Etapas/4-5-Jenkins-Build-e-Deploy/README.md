## Criando Pipeline no Jenkins

- Para realizar essa etapa, é necessario ter o jenkins instalado localmente, siga o guia de instalação do site oficial https://www.jenkins.io/doc/book/installing/ e depois continue com a realização da pratica.
- Caso já tenha o jenkins instalado basta seguir com a patrica.

- Com jenkins instalado e configurado, instale os seguintes plugins para garantir sucesso na execução.
    - Docker 
    - Docker Pipeline
    - Kubernetes CLI
    - Chuck Norris(Extra) <- Opcional

- Não esqueça de selecionar para que jenkins reinicie pos-instalação, ou reinicie manualmente.

## Configurando a pipeline
- Agora crie um novo projeto no jenkins e selecione o tipo pipeline.

- Escolha para o projeto e siga com as configurações.
- ### 1. Em pipeline: definition
    - selecione Pipeline Script from SCM
    - escolha o git como SCM
    - cole o URL do repositorio com a aplicação. No meu caso https://github.com/gasparotto-l/aplicacao-CICD.git
    - altere a branch de `*/master` para `*/main`
    - Salve a pipeline, e agora vamos configurar os arquivos.

## Configuração de Credenciais no Jenkins

Para configurar corretamente as credenciais no Jenkins, siga os passos abaixo:

### Credenciais do DockerHub

1. Acesse: `Gerenciar Jenkins > Credenciais`.
2. Clique em: `Sistema > Global credentials (global)`.
3. Adicione uma credencial do tipo **Username with password**:
   - **ID:** `docker-creds`
   - **Username:** `seu_username`
   - **Password:** *Sua senha do DockerHub*

---

### Credenciais do GitHub

1. Acesse: `Gerenciar Jenkins > Credenciais`.
2. Clique em: `Sistema > Global credentials (global)`.
3. Adicione uma credencial do tipo **Username with password**:
   - **ID:** `github-creds`
   - **Username:** `seu_usarname`
   - **Password:** *Sua senha do GitHub*

---

### Credenciais do Kubernetes (kubeconfig)

1. Copie o arquivo `kubeconfig` do seu cluster Kubernetes.
2. Acesse: `Gerenciar Jenkins > Credenciais`.
3. Clique em: `Sistema > Global credentials (global)`.
4. Adicione uma credencial do tipo **Secret file**:
   - **ID:** `rancher-kubeconfig` ou `kubeconfig`
   - **Arquivo:** selecione o seu arquivo `kubeconfig`


## Arquivo Jenkinsfile

- ### Agora crie configure um arquivo Jenkins, como usado abaixo:
- Nesse arquivo voce irá configurar todo deploy da pipeline
    - Tenha muita atenção ao configurar as credenciais, e acessos que a pipeline irá executar para não ter problemas.
- Neste Jenkinsfile eu configurei o plugin do chuck norris      
    - porém, isso é opcional, caso não vá usar basta remover do arquivo. 
- Para evitar erros, meu jenkinsfile está mais robusto e realizando teste de execução durante o deploy, mas caso queira fazer mais simplificado, acredito que não terá problemas.

#### Jenkinsfile:
```groovy
pipeline {
    agent any

    triggers {
        pollSCM('* * * * *') 
    }

    environment {
        DOCKERHUB_REPO = "gasparottoluo"
        BUILD_TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Build Backend Docker Image') {
            steps {
                script {
                    def backendapp = docker.build("${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}", "-f ./backend/Dockerfile ./backend")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    def backendapp = docker.image("${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-creds') {
                        backendapp.push('latest')
                        backendapp.push("${BUILD_TAG}")
                    }
                }
            }
        }

        stage('Deploy no Kubernetes') {
            environment {
                tag_version = "${BUILD_TAG}"
            }
            steps {
                withKubeConfig([credentialsId: 'rancher-kubeconfig']) {
                    script {
                        try {
                            echo "🔍 Testando conectividade com o cluster..."
                            bat 'kubectl cluster-info'
                            
                            echo "🔄 Atualizando tag da imagem no deployment..."
                            
                            bat """
                                powershell -Command "(Get-Content ./k8s/deployment.yaml) -replace 'gasparottoluo/meu-backend:v1.0.0', '${DOCKERHUB_REPO}/meu-backend:${tag_version}' | Set-Content ./k8s/deployment.yaml"
                            """
                            
                            echo "📋 Visualizando o deployment atualizado..."
                            bat 'type k8s\\deployment.yaml'
                            
                            echo "🚀 Aplicando deployment no Kubernetes..."
                            
                            try {
                                bat 'kubectl apply -f k8s/deployment.yaml'
                            } catch (Exception e) {
                                echo "⚠️ Falha na validação, tentando sem validação..."
                                bat 'kubectl apply -f k8s/deployment.yaml --validate=false'
                            }
                            
                            echo "⏳ Aguardando rollout do deployment..."
                            bat 'kubectl rollout status deployment/backend-app --timeout=300s'
                            
                        } catch (Exception e) {
                            echo "❌ Erro durante o deploy: ${e.getMessage()}"
                            
                            echo "🔍 Informações de debug:"
                            bat 'kubectl config current-context || echo "Erro ao obter contexto"'
                            bat 'kubectl get nodes || echo "Erro ao listar nodes"'
                            bat 'kubectl get namespaces || echo "Erro ao listar namespaces"'
                            
                            throw e
                        }
                    }
                }
            }
        }

        stage('Verificar Deploy') {
            steps {
                withKubeConfig([credentialsId: 'rancher-kubeconfig']) {
                    script {
                        echo "🔍 Verificando status dos pods e serviços..."
                        
                        bat 'kubectl get pods -l app=backend-app -o wide'
                        bat 'kubectl get services'
                        
                        try {
                            bat 'kubectl get pods -l app=backend-app --no-headers'
                        } catch (Exception e) {
                            echo "⚠️ Erro ao verificar status dos pods: ${e.getMessage()}"
                        }
                        
                        echo "📋 Logs recentes do Backend:"  
                        bat 'kubectl logs -l app=backend-app --tail=10 || echo "Sem logs disponíveis"'
                    }
                }
            }
        }
    }

    post {
        always {
            chuckNorris()
            
            script {
                try {
                    bat """
                        powershell -Command "(Get-Content ./k8s/deployment.yaml) -replace '${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}', 'gasparottoluo/meu-backend:v1.0.0' | Set-Content ./k8s/deployment.yaml"
                    """
                    echo "🧹 Deployment.yaml restaurado para o estado original"
                } catch (Exception e) {
                    echo "⚠️ Não foi possível restaurar o deployment.yaml: ${e.getMessage()}"
                }
            }
        }
        success {
            echo '🎉 Deploy realizado com sucesso!'
            echo "✅ Backend: ${DOCKERHUB_REPO}/meu-backend:${BUILD_TAG}"
            echo '🔧 Backend disponível em: http://localhost:30081'
            
            script {
                try {
                    bat 'kubectl get pods -l app=backend-app'
                } catch (Exception e) {
                    echo "ℹ️ Status final dos pods não disponível"
                }
            }
        }
        failure {
            echo '❌ Build falhou!'
            
            script {
                try {
                    echo "🔍 Informações de debug da falha:"
                    bat 'kubectl describe pods -l app=backend-app || echo "Erro ao descrever pods backend"'
                    bat 'powershell -Command "kubectl get events --sort-by=.metadata.creationTimestamp | Select-Object -Last 10" || echo "Erro ao obter eventos"'
                } catch (Exception e) {
                    echo "⚠️ Não foi possível obter informações de debug: ${e.getMessage()}"
                }
            }
        }
        unstable {
            echo '⚠️ Build instável!'
        }
    }
}

```

## Teste de execução

- Agora acesse sua pipeline
- Clique em <b>Build Now</b> e pronto! Tudo deve correr como esperado.

#### Imagens da execução e Acesso

- Execução:

- Acesso:

