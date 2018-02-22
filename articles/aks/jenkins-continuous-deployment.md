---
title: "Implantação contínua do Jenkins com Kubernetes no Serviço de Contêiner do Azure"
description: "Como automatizar um processo de implantação contínua com Jenkins para implantar e atualizar um aplicativo em recipientes no Kubernetes no Serviço de Contêiner do Azure"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 1293fda45602203570a0f7f75481f67bdcb6edf3
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="continuous-deployment-with-jenkins-and-azure-container-service"></a>Implantação contínua com o Jenkins e o Serviço de Contêiner do Azure

Este documento demonstra como configurar um fluxo de trabalho de implantação contínua básico entre o Jenkins e um cluster do AKS (Serviço de Contêiner do Azure). 

O fluxo de trabalho de exemplo inclui as seguintes etapas:

> [!div class="checklist"]
> * Implante o aplicativo de voto do Azure para seu cluster Kubernetes.
> * Atualize o código do aplicativo do Azure voto e envie-o por push para um repositório GitHub, que inicia o processo de implantação contínua.
> * O Jenkins clona o repositório e compila uma nova imagem de contêiner com o código atualizado.
> * Essa imagem é enviada por push para um ACR (Registro de Contêiner do Azure).
> * O aplicativo em execução no cluster do AKS é atualizado com a nova imagem de contêiner.

## <a name="prerequisites"></a>pré-requisitos

Para concluir as etapas neste artigo, você precisa dos itens a seguir.

- Noções básicas sobre Kubernetes, Git, CI/CD e ACR (Registro de Contêiner do Azure).
- Um [cluster do AKS (Serviço de Contêiner do Azure)][aks-quickstart] e [credenciais do AKS configuradas][aks-credentials] em seu sistema de desenvolvimento.
- Um [ACR (Registro de Contêiner do Azure)][acr-quickstart], o nome do servidor de logon do ACR e [credenciais do ACR][acr-authentication] com acesso via push e pull.
- CLI do Azure instalada no sistema de desenvolvimento.
- Docker instalado no sistema de desenvolvimento.
- Conta do GitHub, [token de acesso pessoal do GitHub][git-access-token] e cliente Git instalados no sistema de desenvolvimento.

## <a name="prepare-application"></a>Preparar o aplicativo

O aplicativo de voto do Azure usado em todo este documento contém uma interface da Web hospedada em um ou mais pods, além de um segundo pod hospedando o Redis para armazenamento de dados temporário. 

Antes de criar a integração entre o Jenkins e o AKS, prepare e implante o aplicativo de voto do Azure no cluster do AKS. Pense nisso como a primeira versão do aplicativo.

Crie o fork do repositório do GitHub a seguir.

```
https://github.com/Azure-Samples/azure-voting-app-redis
```

Quando o fork tiver sido criado, clone-o no sistema de desenvolvimento. Verifique se você está usando a URL do seu fork ao clonar este repositório.

```bash
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Altere os diretórios pois você está trabalhando no diretório clonado.

```bash
cd azure-voting-app-redis
```

Execute o arquivo `docker-compose.yaml` para criar a imagem de contêiner `azure-vote-front` e inicie o aplicativo.

```bash
docker-compose up -d
```

Quando concluído, use o comando [docker images][docker-images] para ver a imagem criada.

Observe que três imagens foram baixadas ou criadas. A imagem `azure-vote-front` contém o aplicativo e usa a imagem `nginx-flask` como base. A imagem `redis` é usada para iniciar uma instância do Redis.

```console
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Obtenha o servidor de logon do ACR com o comando [az acr list][az-acr-list]. Verifique se você atualizou o nome do grupo de recursos com o grupo de recursos que hospeda o registro do ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use o comando [docker tag][docker-tag] para marcar a imagem com o nome do servidor de logon e um número de versão `v1`.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Atualize o valor do servidor de logon do ACR com o nome do servidor de logon do ACR e envie a imagem `azure-vote-front` por push para o registro. 

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-application-to-kubernetes"></a>Implantar o aplicativo no Kubernetes

Um arquivo de manifesto do Kubernetes pode ser encontrado na raiz do repositório de voto do Azure e pode ser usado para implantar o aplicativo no cluster do Kubernetes.

Primeiro, atualize o arquivo de manifesto **azure-vote-all-in-one-redis.yaml** com o local do registro de ACR. Abra o arquivo com qualquer editor de texto e substitua `microsoft` pelo nome do servidor de logon do ACR. Esse valor pode ser encontrado na linha **47** do arquivo de manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Em seguida, use o comando [kubectl create][kubectl-create] para executar o aplicativo. Esse comando analisa o arquivo de manifesto e cria objetos Kubernetes definidos.

```bash
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Um [serviço Kubernetes][kubernetes-service] é criado para expor o aplicativo à Internet. Esse processo pode levar alguns minutos. 

Para monitorar o andamento, use o comando [kubectl get service][kubectl-get] com o argumento `--watch`.

```bash
kubectl get service azure-vote-front --watch
```

Inicialmente o *EXTERNAL -IP* para o serviço *azure-vote-front* aparece como *pendente*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Depois que o endereço *EXTERNAL-IP* for alterado de *pendente* para um *endereço IP*, use `control+c` para interromper o processo kubectl watch. 

```
azure-vote-front   10.0.34.242   13.90.150.118   80:30676/TCP   2m
```

Para consultar o aplicativo, navegue até o endereço IP externo.

![Imagem do cluster Kubernetes no Azure](media/aks-jenkins/azure-vote-safari.png)

## <a name="deploy-jenkins-to-vm"></a>Implantar o Jenkins na VM

Um script foi criado previamente para implantar uma máquina virtual, configurar o acesso à rede e concluir uma instalação básica do Jenkins. Além disso, o script copia o arquivo de configuração do Kubernetes do seu sistema de desenvolvimento para o sistema do Jenkins. Esse arquivo é usado para autenticação entre o Jenkins e o cluster do AKS.

Execute os comandos a seguir para baixar e executar o script. A URL abaixo também pode ser usada para examinar o conteúdo do script.

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Quando o script for concluído, ele produzirá como saída um endereço para o servidor Jenkins, além de uma chave para desbloquear o Jenkins. Navegue até a URL, insira a chave e siga as instruções na tela para concluir a configuração do Jenkins.

```console
Open a browser to http://52.166.118.64:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

## <a name="jenkins-environment-variables"></a>Variáveis de ambiente Jenkins

Uma variável de ambiente Jenkins é usada para armazenar o nome do servidor de logon do ACR (Registro de Contêiner do Azure). Essa variável é referenciada durante o trabalho de implantação contínua do Jenkins.

No portal de administração do Jenkins, clique em **Gerenciar Jenkins** > **Configurar Sistema**.

Em **Propriedades Globais**, selecione **Variáveis de ambiente** e adicione uma variável com o nome `ACR_LOGINSERVER` e um valor de seu servidor de logon do ACR.

![Variáveis de ambiente Jenkins](media/aks-jenkins/env-variables.png)

Ao concluir, clique em **Salvar** na página de configuração do Jenkins.

## <a name="jenkins-credentials"></a>Credenciais do Jenkins

Agora armazene suas credenciais do ACR em um objeto de credencial do Jenkins. Essas credenciais são referenciadas durante o trabalho de build do Jenkins.

De volta ao portal de administração do Jenkins, clique em **Credenciais** > **Jenkins** > **Credenciais globais (irrestritas)** > **Adicionar Credenciais**.

Verifique se o tipo de credencial é **Nome de usuário com senha** e insira os seguintes itens:

- **Nome de usuário** – ID da entidade de serviço usada para autenticação com o registro de ACR.
- **Senha** – segredo do cliente da entidade de serviço usada para autenticação com o registro de ACR.
- **ID** – identificador de credencial, tal como `acr-credentials`.

Ao concluir, o formulário de credenciais deve ser semelhante a esta imagem:

![Credenciais de ACR](media/aks-jenkins/acr-credentials.png)

Clique em **OK** e retorne ao portal de administração do Jenkins.

## <a name="create-jenkins-project"></a>Criar projeto do Jenkins

No portal de administração do Jenkins, clique em **Novo Item**.

Dê um nome ao projeto, por exemplo `azure-vote`, selecione **Projeto de Estilo Livre** e clique em **OK**. 

![Projeto do Jenkins](media/aks-jenkins/jenkins-project.png)

Em **Geral**, selecione **Projeto do GitHub** e digite a URL para seu fork do projeto GitHub do voto do Azure.

![Projeto do GitHub](media/aks-jenkins/github-project.png)

Em **Gerenciamento de Código-fonte**, selecione **Git** e insira a URL para seu fork do repositório do GitHub do voto do Azure. 

Para as credenciais, clique em **Adicionar** > **Jenkins**. Em **Tipo**, selecione **Texto secreto** e insira seu [Token de acesso pessoal do GitHub][git-access-token] como o segredo. 

Selecione **Adicionar** quando terminar.

![Credenciais do GitHub](media/aks-jenkins/github-creds.png)

Em **Gatilhos de Build**, selecione **Gatilho de webhook do GitHub para sondagem de GITScm**.

![Gatilhos de build do Jenkins](media/aks-jenkins/build-triggers.png)

Em **Ambiente de Build**, selecione **Usar arquivos ou textos secretos**.

![Ambiente de build do Jenkins](media/aks-jenkins/build-environment.png)

Em **Associações**, selecione **Adicionar** > **Nome de usuário e senha (separados)**. 

Digite `ACR_ID` para a **Variável de Nome de Usuário** e `ACR_PASSWORD` para a **Variável de Senha**.

![Associações do Jenkins](media/aks-jenkins/bindings.png)

Adicione uma **Etapa de Build** do tipo **Executar shell** e use o texto a seguir. Esse script compila uma nova imagem de contêiner e envia-a por push para o registro de ACR.

```bash
# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME
```

Adicione outra **Etapa de Build** do tipo **Executar shell** e use o texto a seguir. Esse script atualiza a implantação de Kubernetes.

```bash
# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
```

Depois de concluído, clique em **Salvar**.

## <a name="test-the-jenkins-build"></a>Testar o build do Jenkins

Antes de prosseguir, teste o build do Jenkins. Isso valida que o trabalho de build foi configurado corretamente, o arquivo de autenticação do Kubernetes adequado está em vigor e que as credenciais do ACR adequadas foram fornecidas.

Clique em **Compilar Agora** no menu à esquerda do projeto. 

![Build de teste do Jenkins](media/aks-jenkins/test-build.png)

Durante este processo, o repositório do GitHub é clonado para o servidor de build do Jenkins. Uma nova imagem de contêiner é compilada e enviada por push para o registro do ACR. Por fim, o aplicativo de voto do Azure em execução no cluster do AKS é atualizado para usar a nova imagem. Já que nenhuma alteração foi feita ao código do aplicativo, o aplicativo não é alterado.

Quando o processo é concluído, você pode clicar em **Build 1** no histórico de builds e selecionar **Saída do Console** para ver toda a saída do processo de build. A linha final deve indicar um build bem-sucedido. 

## <a name="create-github-webhook"></a>Criar um webhook do GitHub

Em seguida, conecte o repositório de aplicativo ao servidor de build do Jenkins para que, no evento de qualquer confirmação, um novo build seja disparado.

1. Navegue até o repositório do GitHub em que um fork foi criado.
2. Selecione **Configurações** e, em seguida, **Integrações e serviços** no lado esquerdo.
3. Escolha **Adicionar Serviço**, digite `Jenkins (GitHub plugin)` na caixa de filtro e selecione o plug-in.
4. Para a URL de conexão do Jenkins, digite `http://<publicIp:8080>/github-webhook/`, em que `publicIp` é o endereço IP do servidor Jenkins. Não deixe de incluir a barra (/) à direita.
5. Selecione Adicionar serviço.
  
![Webhook do GitHub](media/aks-jenkins/webhook.png)

## <a name="test-cicd-process-end-to-end"></a>Testar o processo de CI/CD de ponta a ponta

No computador de desenvolvimento, abra o aplicativo clonado com um editor de código. 

No diretório **/azure-vote/azure-vote**, você pode encontrar um arquivo chamado **config_file.cfg**. Atualize os valores de voto neste arquivo para algo diferente de gatos e cachorros. 

O exemplo a seguir mostra um arquivo **config_file.cfg** atualizado.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Ao concluir, salve o arquivo, confirme as alterações e envie-o por push para o fork do repositório do GitHub. Após a conclusão da confirmação, o webhook do GitHub dispara um novo build do Jenkins, que atualiza a imagem de contêiner e a implantação do AKS. Monitore o processo de build no console de administrador do Jenkins. 

Após a conclusão do build, procure novamente o ponto de extremidade do aplicativo para observar as alterações.

![Voto do Azure atualizado](media/aks-jenkins/azure-vote-updated-safari.png)

Neste ponto, um processo de implantação contínua simples foi concluído. As etapas e configurações mostradas neste exemplo podem ser usadas para criar uma automação de build contínua mais robusta e pronta para produção.

<!-- LINKS - external -->
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[git-access-token]: https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[az-acr-list]: /cli/azure/acr#az_acr_list
[acr-authentication]: ../container-registry/container-registry-auth-aks.md
[acr-quickstart]: ../container-registry/container-registry-get-started-azure-cli.md
[aks-credentials]: /cli/azure/aks#az_aks_get_credentials
[aks-quickstart]: kubernetes-walkthrough.md
[azure-cli-install]: /cli/azure/install-azure-cli