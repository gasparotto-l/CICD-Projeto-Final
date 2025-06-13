# Considerações Finais

## Resumo do projeto

Este projeto teve como objetivo principal a construção de um pipeline de integração e entrega contínua (CI/CD) para uma API em Python, utilizando FastAPI. Foram abordadas as etapas desde a criação do repositório no GitHub, passando por Docker, Docker Hub, Kubernetes (com Rancher Desktop), até a automação completa via Jenkins. O fluxo inclui build de imagem, publicação no Docker Hub, deploy no Kubernetes e feedback automatizado via Discord.


## Erros comuns

- **Credenciais mal configuradas no Jenkins**: IDs incorretos ou ausência de credenciais causaram falhas frequentes nas etapas de push e deploy.
- **Caminhos e tags desatualizados**: Alterações na tag da imagem Docker não refletidas no `deployment.yaml` geraram inconsistência no deploy.
- **Problemas com `kubectl` no Windows**: Uso de comandos em PowerShell exigiu atenção especial com barras invertidas e permissões.
- **Ambiente local instável**: Configuração incorreta ou incompleta do cluster Kubernetes local foi responsável por falhas no rollout.


## Lições aprendidas

- A importância de pipelines bem documentadas e reproduzíveis: mesmo pequenos erros podem comprometer todo o fluxo.
- O uso de `Jenkinsfile` robusto, com tratamento de exceções e logs detalhados, facilita o diagnóstico e a correção de erros.
- Automatizar o máximo possível evita erros humanos, mas exige atenção redobrada na configuração inicial.
- A integração com o Discord mostrou-se útil para rastreamento em tempo real das execuções.


## Observações

- O projeto foi pensado para ser executado em ambiente local, mas pode ser facilmente adaptado para clusters em nuvem.
- A estrutura modular das etapas permite expansão futura, como testes automatizados, deploy em múltiplos ambientes e monitoramento contínuo.
- O uso do plugin Chuck Norris foi incluído como exemplo de personalização e motivação visual, mas não é essencial ao funcionamento.

---

## Agradecimentos

- Este projeto foi desenvolvido de forma individual como parte do estágio realizado na Compass UOL.  
- Agradeço a você que está lendo esta documentação por dedicar tempo e atenção.  
- Caso encontre algum erro ou ponto de melhoria, sinta-se à vontade para entrar em contato comigo.

---

<p align="center">
  <a href="https://github.com/gasparotto-l/CICD-Projeto-Final/tree/main/Etapas/6-Extras#readme
  ">
    <img src="https://raw.githubusercontent.com/gasparotto-l/CICD-Projeto-Final/refs/heads/main/elementosgraficos/anterior.jpg" alt="Etapa Anterior" height="8%">
  </a>
</p>
