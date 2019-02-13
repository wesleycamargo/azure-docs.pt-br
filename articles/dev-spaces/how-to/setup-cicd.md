---
title: Usar CI/CD com Azure Dev Spaces | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: article
manager: yuvalm
description: Desenvolvimento rápido de Kubernetes com contêineres e microsserviços no Azure
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Contêiner do Azure, contêineres
ms.openlocfilehash: 0abe2902248c8203046cfe891d136ca7d5d0a75b
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665967"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Usar CI/CD com o Azure Dev Spaces

Este artigo orienta você pela configuração da integração contínua/implantação contínua (CI/CD) ao Serviço de Kubernetes do Azure (AKS) com o Dev Spaces habilitado. A CI/CD para o AKS permite que as atualizações de aplicativo sejam implantadas automaticamente sempre que o código confirmado é enviado para seu repositório de origem. Usar a CI/CD em conjunto com um cluster habilitado para o Dev Spaces é útil porque pode manter uma linha de base do aplicativo atualizada para o trabalho da equipe.

![Exemplo de diagrama CI/CD](../media/common/ci-cd-simple.png)

Embora este artigo oriente você com o Azure DevOps, os mesmos conceitos se aplicariam aos sistemas de CI/CD, como Jenkins, TeamCity, etc.

## <a name="prerequisites"></a>Pré-requisitos
* [Cluster do Serviço de Kubernetes do Azure (AKS) com o Azure Dev Spaces habilitado](../get-started-netcore.md)
* [CLI do Azure Dev Spaces instalado](upgrade-tools.md)
* [Organização do Azure DevOps com um projeto](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Registro de Contêiner do Azure (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Detalhes disponíveis da [conta do administrador](../../container-registry/container-registry-authentication.md#admin-account) do Registro de Contêiner do Azure
* [Autorizar o cluster do AKS para efetuar pull de seu Registro de Contêiner do Azure](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>Baixar código de exemplo
Por uma questão de tempo, vamos criar uma bifurcação do nosso repositório GitHub de código de amostra. Acesse https://github.com/Azure/dev-spaces e selecione **Bifurcação**. Quando o processo de bifurcação estiver completo, **Clone** sua versão com bifurcação do repositório localmente. Por padrão o branch _mestre_ será verificado, mas incluímos algumas alterações de economia de tempo no branch _azds_updates_, que também deve ter sido transferido durante o processo de bifurcação. O branch _azds_updates_ contém atualizações que pedimos que você faça manualmente nas seções do tutorial do Dev Spaces, bem como alguns arquivos YAML e JSON pré-criados para simplificar a implantação do sistema CI/CD. Você pode usar um comando como `git checkout -b azds_updates origin/azds_updates` para verificar o branch _azds_updates_ em seu repositório local.

## <a name="dev-spaces-setup"></a>Configuração do Dev Spaces
Crie um novo espaço chamado _dev_ usando o comando `azds space select`. O espaço _dev_ será usado por seu pipeline de CI/CD para enviar suas alterações de código. Ele também será usado para criar _espaços filhos_ baseados no _dev_.

```cmd
azds space select -n dev
```

Quando solicitado a selecionar um espaço de desenvolvimento pai, selecione _\<none\>_.

O espaço _dev_ sempre conterá o estado mais recente do repositório, uma linha de base, para que os desenvolvedores possam criar _espaços filho_ a partir do _dev_ para testar suas alterações isoladas dentro do contexto do aplicativo maior. Esse conceito é discutido com mais detalhes nos tutoriais do Dev Spaces.

## <a name="creating-the-build-definition"></a>Criar a definição de build
Abra seu projeto de equipe do Azure DevOps em um navegador da Web e navegue até a seção _Pipelines_. Primeiro, clique na sua foto de perfil no canto superior direito do site do Azure DevOps, abra o painel de versões prévias do recurso e desative a opção _Nova experiência de criação de pipeline YAML_:

![Abertura do painel de versões prévias do recurso](../media/common/preview-feature-open.png)

A opção para desabilitar:

![Opção Nova experiência de criação de pipeline YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> A versão prévia do recurso do Azure DevOps _Nova experiência de criação de pipeline YAML_ entra em conflito com a criação de pipelines de build pré-definidos no momento. É preciso desabilitá-lo por enquanto para implantar nosso pipeline de build predefinido.

No branch _azds_updates_, incluímos um simples [Azure Pipeline YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) que define as etapas de compilação necessárias para *mywebapi* e *webfrontend*.

Dependendo da linguagem escolhida, o pipeline YAML foi registrado em um caminho semelhante a: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Para criar um pipeline desse arquivo:
1. Na página principal do projeto DevOps, navegue até Pipelines > Builds
1. Selecione a opção para criar um pipeline de build **Novo**
1. Selecione **GitHub** como a origem, autorize com sua conta do GitHub, se necessário, e selecione o branch _azds_updates_ da sua versão bifurcada do repositório sampleapp dev-spaces
1. Selecione **Configuração como código**, ou **YAML**, como o seu modelo
1. Você agora verá uma página de configuração para o pipeline de build. Como mencionado acima, insira o caminho específico da linguagem para o **c**. Por exemplo, `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`
1. Vá para a guia Variáveis
1. Adicione manualmente _dockerId_ como uma variável, que é o nome de usuário de sua [conta de administrador do Registro de Contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). (Mencionado nos pré-requisitos do artigo)
1. Adicione manualmente _dockerPassword_ como uma variável, que é a senha da sua conta do administrador do [Registro de Contêiner do Azure](../../container-registry/container-registry-authentication.md#admin-account). Certifique-se de especificar _dockerPassword_ como um Segredo (selecionando o ícone de bloqueio) para fins de segurança.
1. Selecione **Salvar & Fila**

Agora você tem uma solução de IC que irá criar automaticamente os itens *mywebapi* e *webfrontend* para qualquer atualização enviada para o branch _azds_updates_ para a sua bifurcação do GitHub. Você pode verificar se as imagens do Docker foram enviadas navegando até o portal do Azure, selecionando seu Registro de Contêiner do Azure e navegando na guia _Repositórios_:

![Repositórios de Registro de Contêiner do Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Criar a definição da versão

1. Na página principal do projeto DevOps, navegue até Pipelines > Versões
1. Se você estiver trabalhando em um projeto DevOps totalmente novo que ainda não contenha uma definição de versão, primeiro será necessário criar uma definição de versão vazia antes de continuar. A opção Importar não é exibida na interface do usuário até que você tenha uma definição de versão existente.
1. À esquerda, clique no botão **+ Novo** e, em seguida, clique em **Importar um pipeline**
1. Selecione o arquivo .json em `samples/release.json`
1. Clique em OK. Observe que o painel Pipeline foi carregado com a página de edição da definição de versão. Observe também que há alguns ícones de aviso vermelhos indicando detalhes específicos do cluster que ainda precisam ser configurados.
1. À esquerda do painel Pipeline, clique na bolha **Adicionar um artefato**.
1. Na lista suspensa **Origem**, selecione o pipeline de build que criamos anteriormente neste documento.
1. Para a **Versão Padrão**, recomendamos escolher **Últimas do branch padrão do pipeline de build**. Não é preciso especificar quaisquer tags.
1. Defina o **Alias de Origem** como `drop`. As tarefas de versão predefinidas usam **Alias de Origem**, portanto, a opção deve ser selecionada.
1. Clique em **Adicionar**.
1. Agora clique no ícone de raio na origem de artefatos `drop` recém-criada, conforme mostrado abaixo:

    ![Configuração da implantação contínua de artefato de versão](../media/common/release-artifact-cd-setup.png)
1. Habilite o **Gatilho de Implantação Contínua**
1. Agora vá para o painel Tarefas. O estágio _dev_ deve ser selecionado, e você deve receber três controles suspensos vermelhos como abaixo:

    ![Configuração da definição de versão](../media/common/release-setup-tasks.png)
1. Especifique a assinatura do Azure, o grupo de recursos e o cluster que você está usando com o Azure Dev Spaces.
1. Deve haver apenas mais uma seção em vermelho neste ponto; a seção **Trabalho do Agente**. Acesse e especifique **Hosted Ubuntu 1604** como o pool de agentes para este estágio.
1. Passe o mouse sobre o Seletor de Tarefas na parte superior, selecione _prod_.
1. Especifique a assinatura do Azure, o grupo de recursos e o cluster que você está usando com o Azure Dev Spaces.
1. Em **Trabalho do Agente**, configure **Hosted Ubuntu 1604** como o pool de agente.
1. Clique em **Salvar** no canto superior direito e em **OK**.
1. Clique em **+ Versão** (ao lado do botão Salvar) e em **Criar uma versão**.
1. Verifique se o build mais recente de seu pipeline de build está selecionado na seção Artefatos e clique em **Criar**.

Agora, um processo de versão automatizado será iniciado, implantando os gráficos *mywebapi* e *webfrontend* em seu cluster do Kubernetes no espaço de nível superior _dev_. Você pode monitorar o progresso de sua versão no portal da Web do Azure DevOps.

> [!TIP]
> Se a sua versão falhar com uma mensagem de erro como *FALHA NO UPGRADE: expirou aguardando a condição*, tente inspecionar os pods em seu cluster [usando o painel do Kubernetes](../../aks/kubernetes-dashboard.md). Se você observar a falha de início dos pods, com mensagens de erro como *Falha ao efetuar pull da imagem "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon: Obter https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: não autorizado: autenticação necessária*, pode ser que seu cluster não tenha sido autorizado a efetuar pull do seu Registro de Contêiner do Azure. Certifique-se de ter concluído o pré-requisito [Autorizar o cluster do AKS a receber do seu Registro de Contêiner do Azure](../../container-registry/container-registry-auth-aks.md).

Agora você tem um pipeline de CI/CD totalmente automatizado para sua bifurcação do GitHub dos aplicativos de amostra do Dev Spaces. Sempre que você confirmar e enviar um código, o pipeline de build criará e enviará as imagens *mywebapi* e *webfrontend* para sua instância de ACR personalizada. Em seguida, o pipeline de versão implantará o gráfico de Helm para cada aplicativo no espaço _dev_ em seu cluster habilitado para Dev Spaces.

## <a name="accessing-your-dev-services"></a>Acessar seus serviços _dev_
Após a implantação, a versão _dev_ do *webfrontend* pode ser acessada com uma URL pública como: `http://dev.webfrontend.<hash>.<region>.aksapp.io`.

Você pode encontrar essa URL usando a CLI do *kubectl*:
```cmd
kubectl get ingress -n dev webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="deploying-to-production"></a>Implantação em Produção
Clique em **Editar** em sua definição de versão e observe que há um estágio _prod_ definido:

![Estágio de Produção](../media/common/prod-env.png)

Para promover manualmente uma versão específica para _prod_ usando o sistema CI/CD criado neste tutorial:
1. Abra uma versão anteriormente bem-sucedida no portal DevOps
1. Passe o mouse sobre o estágio 'prod'
1. Selecione Implantar

![Promova para Produção](../media/common/prod-promote.png)

Nosso exemplo de pipeline de CI/CD faz uso de variáveis para alterar o prefixo DNS para *webfrontend* dependendo de qual ambiente está sendo implantado. Então, para acessar seus serviços 'prod', você pode usar uma URL como: `http://prod.webfrontend.<hash>.<region>.aksapp.io`.

Após a implantação, você pode encontrar esta URL usando a CLI *kubectl*: <!-- TODO update below to use list-uris when the product has been updated to list non-azds ingresses #769297 -->

```cmd
kubectl get ingress -n prod webfrontend -o=jsonpath="{.spec.rules[0].host}"
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentação do Dev Spaces na produção
Embora a instrumentação do Dev Spaces tenha sido projetada para _não_ atrapalhar a operação normal de seu aplicativo, recomendamos executar suas cargas de trabalho de produção em um namespace do Kubernetes que não esteja habilitado com Dev Spaces. Usar esse tipo de namespace do Kubernetes significa que você deve criar seu namespace de produção usando a CLI do `kubectl` ou permitir que seu sistema de CI/CD o crie durante a primeira implantação de Helm. _Selecionar_ ou criar um espaço usando a ferramenta Dev Spaces adicionará instrumentação do Dev Spaces a esse namespace.

Aqui está um exemplo de estrutura de namespace que dá suporte ao desenvolvimento de recursos, o ambiente 'dev', _e_ produção, tudo em um único cluster do Kubernetes:

![Exemplo de estrutura de namespace](../media/common/cicd-namespaces.png)

> [!Tip]
> Se você já tiver criado um espaço `prod` e simplesmente gostaria de excluí-lo da instrumentação do Dev Spaces (sem excluí-lo!), poderá fazê-lo com o seguinte comando da CLI do Dev Spaces:
>
> `azds space remove -n prod --no-delete`
>
> Talvez seja necessário excluir todos os pods no namespace `prod` depois de fazer isso, para que possam ser recriados sem a instrumentação do Dev Spaces.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o desenvolvimento da equipe usando o Azure Dev Spaces](../team-development-netcore.md)