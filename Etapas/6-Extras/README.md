## Chuck Norris
- Primeiro instale o plugin do Chuck Norris no Jenkins
    ![alt text](<../../assets/et6/chuck norris plugin.png>)

- Inclua no seu Jenkinsfile a utilização chuck norris, como no exemplo abaixo(faça como achar melhor):

```groovy
    pipeline {
        agent any

        stages {
            stage('Build') {
                steps {
                    echo 'Fazendo build...'
                }
            }
            stage('Test') {
                steps {
                    echo 'Executando testes...'
                }
            }
        }
    }

```

## Trivy



## Webhook Discord

