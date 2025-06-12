## Criação do repositorio no github

- Primeiro vamos começar criando um repositorio no github. 
- Siga as etapas abaixo:
    - Acesse https://github.com, e faça login na sua conta(caso não tenha uma conta, é preciso que crie para continuar com pratica desse repositorio).
    - Clique em <b>new</b> como na imagem para criar um novo repositorio.
    
        ![alt text](../../assets/et1/new.png)

    - Adicione um nome ao seu repositorio 
    - Recomendo deixa-lo vazio inicialmente para evitar erros.
    - Também deixe o repo público, caso não queira ter que configurar credenciais de segurança, quando for deployar sua aplicação.

        ![alt text](<../../assets/et1/criação repo.png>)  

    - Se tudo correr deve ver algo no como nas imagens abaixo:

        ![alt text](../../assets/et1/repocriado.png)  
        ![alt text](../../assets/et1/repocriado2.png)

## Criação da conta no Docker Hub

- Acesse hub.docker.com

- Clique em Sign up e se cadastre

    ![alt text](../../assets/et1/dockerhub.png)

- Siga as etapas indicadas pelo site e terá sua conta criada.


## Verificar acesso ao cluster Kubernetes local

- Antes de realizar essa etapa é necessario que tenha o kubernetes no seu pc
    - Recomendo que instale o rancher desktop, mas também pode ser o docker hub, minikube ou semelhante.

- Em seu terminal, execute os comandos abaixo para confirmar que há um cluster kubernetes localmente.
```powershell
    kubeclt cluster-info \\ 1º Comando - Info do Cluster
    kubectl --version \\ 2º Comando - Versão do Kubernetes
    kubectl get pods \\ 3º Comando - Verificando os pods
```
- Se funcionar as saidas dos comandos devem ser como as minhas:

    ![alt text](../../assets/et1/saidacomandos.png)

- Tudo certo, cluster verificado!

## Validar execução local com uvicorn

- Primeiro clone o repositorio https://github.com/gasparotto-l/aplicacao-CICD.git 
- e Acese a pasta backend

```powershell
    git clone https://github.com/gasparotto-l/aplicacao-CICD.git
    cd backend
```

- Verifique se o python está instalado na sua maquina, se não instale-o antes de prossegir.
- Nesse projeto utilizei o python 3.13, mas podem realizar utilizando outra versão.

```powershell
    python --version
    python3 --version
```
- Saída do comando:

   ![alt text](../../assets/et1/python.png)

- Agora siga os comandos abaixo pra criar um ambiente virtual:
```powershell
    # Criando o Ambiente
    python3 -m venv venv
    # Ativar(Powershell)
    .\venv\Scripts\Activate.ps1
```
- Instale as depencias usando o comando abaixo:
```powershell
    pip install -r requirements.txt
```
- Execução da Aplicação 
```powershell
    python -m uvicorn main:app --reload
```

- Teste a API acessando http://localhost:8000/docs

    ![alt text](../../assets/et1/backendocs.png)

- Se conseguiu acessar, então tudo certo!

---

<p align="center">
  <a href="https://github.com/gasparotto-l/CICD-Projeto-Final/tree/main/Etapas/2-Conteirnização-com-DockerHub#readme
  ">
    <img src="https://raw.githubusercontent.com/gasparotto-l/CICD-Projeto-Final/refs/heads/main/elementosgraficos/proxima.jpg" alt="Próxima Etapa" height="40">
  </a>
</p>

