---
title: Criar pipeline do Azure de integração contínua - Processo de Ciência de Dados de Equipe
description: 'DevOps para aplicativos de IA (Inteligência Artificial): Criar pipeline de integração contínua no Azure usando Docker e Kubernetes'
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/22/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=jainr, previous-ms.author=jainr
ms.openlocfilehash: d99149f8112c19a07208523a1ee26ba1c36e5362
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474217"
---
# <a name="creating-continuous-integration-pipeline-on-azure-using-docker-kubernetes-and-python-flask-application"></a>Criar pipeline de integração contínua no Azure usando Docker, Kubernetes e aplicativo Python Flask
Para um aplicativo de IA, há geralmente dois fluxos de trabalho: cientistas de dados criando modelos de aprendizado de máquina e desenvolvedores de aplicativos compilando o aplicativo e o expondo ao consumo de usuários finais. Neste artigo, demonstraremos como implementar um pipeline de CI (integração contínua)/CD (entrega contínua) para um aplicativo de IA. O aplicativo de IA é uma combinação de código do aplicativo incorporado com um modelo de ML (aprendizado de máquina) pré-treinado. Neste artigo, estamos buscando um modelo pré-treinado de uma conta de armazenamento de blobs do Azure privada, que pode ser uma conta AWS S3 também. Usaremos um aplicativo Web python flask simples no artigo.

> [!NOTE]
> Esta é uma das várias maneiras que a CI/CD pode ser executada. Existem alternativas para as ferramentas e outros pré-requisitos mencionados abaixo. À medida que desenvolvermos conteúdo adicional, nós o publicaremos.
>
>

## <a name="github-repository-with-document-and-code"></a>Repositório GitHub com documentos e código
Você pode baixar o código-fonte do [GitHub](https://github.com/Azure/DevOps-For-AI-Apps). Um [tutorial detalhado](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) também está disponível.

## <a name="pre-requisites"></a>Pré-requisitos
Estes são os pré-requisitos para o pipeline de CI/CD descrito abaixo:
* [Organização do Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Cluster AKS (Serviço de Contêiner do Azure) executando Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-tutorial-kubernetes-deploy-cluster)
* [Conta do Azure Container Registry (ACR)](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal)
* [Instale Kubectl para executar comandos no cluster Kubernetes.](https://kubernetes.io/docs/tasks/tools/install-kubectl/) Precisaremos disso para buscar a configuração do cluster do ACS. 
* Bifurque o repositório para sua conta do GitHub.

## <a name="description-of-the-cicd-pipeline"></a>Descrição do pipeline de CI/CD
O pipeline inicia cada nova confirmação, executa o conjunto de testes, se o teste for aprovado, obtém o build mais recente, o empacota em um contêiner do Docker. Em seguida, o contêiner é implantado usando o Azure Container Service (ACS) e as imagens são armazenadas com segurança no Azure Container Registry (ACR). O ACS está executando Kubernetes para gerenciamento de cluster de contêiner, mas você pode escolher o Docker Swarm ou o Mesos.

O aplicativo envia por pull com segurança o modelo mais recente de uma conta do Armazenamento do Azure e pacotes como parte do aplicativo. O aplicativo implantado tem o código de aplicativo e o modelo de ML empacotados como um único contêiner. Isso separa os desenvolvedores de aplicativos e os cientistas de dados, para garantir que o aplicativo de produção sempre execute o código mais recente com o modelo de ML mais recente.

A arquitetura de pipeline é fornecida abaixo. 

![Arquitetura](./media/ci-cd-flask/Architecture.PNG?raw=true)

## <a name="steps-of-the-cicd-pipeline"></a>Etapas do pipeline de CI/CD
1. Os desenvolvedores trabalham no IDE escolhido no código do aplicativo.
2. Eles confirmam o código de controle do código-fonte de escolha (o Azure DevOps tem bom suporte para vários controles do código-fonte)
3. Separadamente, o cientista de dados trabalha no desenvolvimento do modelo.
4. Quando satisfeitos, eles publicam o modelo em um repositório de modelos; neste caso, estamos usando uma conta de armazenamento de blobs. 
5. Um build é iniciado no Azure DevOps com base em confirmação no GitHub.
6. O pipeline de build do Azure DevOps recebe o modelo mais recente do contêiner de Blob e cria um contêiner.
7. O Azure DevOps envia a imagem por push para o repositório de imagens privado no Registro de Contêiner do Azure
8. Em um agendamento definido (noturno), o pipeline de lançamento é iniciado.
9. A imagem mais recente do ACR é extraída e implantada em cluster Kubernetes no ACS.
10. Solicitação de usuários para o aplicativo passa pelo servidor DNS.
11. O servidor DNS passa a solicitação ao balanceador de carga e envia a resposta ao usuário.

## <a name="next-steps"></a>Próximas etapas
* Confira o [tutorial](https://github.com/Azure/DevOps-For-AI-Apps/blob/master/Tutorial.md) para seguir os detalhes e implementar seu próprio pipeline de CI/CD para o seu aplicativo.

## <a name="references"></a>Referências
* [TDSP (Processo de Ciência de dados de Equipe)](https://aka.ms/tdsp)
* [AML (Azure Machine Learning)](https://docs.microsoft.com/azure/machine-learning/service/)
* [Azure DevOps](https://www.visualstudio.com/vso/)
* [AKS (Serviços do Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/intro-kubernetes)
