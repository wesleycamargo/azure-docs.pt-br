---
title: Tutorial - Criar um cluster do Red Hat OpenShift no Azure|Microsoft Docs
description: Saiba como criar um cluster do Red Hat OpenShift no Microsoft Azure usando a CLI do Azure
services: container-service
author: TylerMSFT
ms.author: twhitney
manager: jeconnoc
ms.topic: tutorial
ms.service: openshift
ms.date: 05/06/2019
ms.openlocfilehash: 5bc71a2d0f29fed163fb5c93ebd27df7f66a1325
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079471"
---
# <a name="tutorial-create-an-azure-red-hat-openshift-cluster"></a>Tutorial: Criar um cluster do Red Hat OpenShift no Azure

Este tutorial é a primeira parte de uma série. Você aprenderá a criar um cluster do Red Hat OpenShift no Microsoft Azure usando a CLI do Azure, dimensioná-lo e, em seguida, excluí-lo para limpar os recursos.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar um cluster do Red Hat OpenShift no Azure

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * Criar um cluster do Red Hat OpenShift no Azure
> * [Dimensionar um cluster do Red Hat OpenShift no Azure](tutorial-scale-cluster.md)
> * [Excluir um cluster do Red Hat OpenShift no Azure](tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

Certifique-se de [configurar o ambiente de desenvolvimento](howto-setup-environment.md), o que inclui:
- Instalar a CLI mais recente
- Criar um locatário
- Criar um objeto do Aplicativo Azure
- Criar um usuário do Active Directory usado para entrar em aplicativos em execução no cluster.

## <a name="step-1-sign-in-to-azure"></a>Etapa 1: Entrar no Azure

Se você estiver executando a CLI do Azure localmente, abra um shell de comando de Bash e execute `az login` para entrar no Azure.

```bash
az login
```

 Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="step-2-create-an-azure-red-hat-openshift-cluster"></a>Etapa 2: Criar um cluster do Red Hat OpenShift no Azure

Na janela de comando de Bash, defina as variáveis a seguir:

> [!IMPORTANT]
> O nome do cluster deve estar todo em letras minúsculas, senão a criação do cluster falhará.

```bash
CLUSTER_NAME=<cluster name in lowercase>
```

 Use o mesmo nome para o cluster escolhido na etapa 6 em [Criar um novo registro do aplicativo](howto-aad-app-configuration.md#create-a-new-app-registration).

```bash
LOCATION=<location>
```

Escolha uma localização para criar o cluster. Para obter uma lista de regiões do Azure que dão suporte ao OpenShift no Azure, consulte [Regiões com suporte](supported-resources.md#azure-regions). Por exemplo: `LOCATION=eastus`.

Defina `FQDN` como o nome totalmente qualificado do cluster. Esse nome é composto do nome do cluster, a localização e `.cloudapp.azure.com` acrescentado ao final. É o mesmo que a URL de Logon criada na etapa 6 em [Criar um novo registro do aplicativo](howto-aad-app-configuration.md#create-a-new-app-registration). Por exemplo:   

```bash
FQDN=$CLUSTER_NAME.$LOCATION.cloudapp.azure.com
```

Defina `APPID` para o valor que você salvou na etapa 9 em [Criar um novo registro do aplicativo](howto-aad-app-configuration.md#create-a-new-app-registration).  

```bash
APPID=<app ID value>
```

Defina `SECRET` para o valor que você salvou na etapa 6 em [Criar um segredo do cliente](howto-aad-app-configuration.md#create-a-client-secret).  

```bash
SECRET=<secret value>
```

Defina `TENANT` para o valor da ID de locatário que você salvou na etapa 7 em [Criar um movo locatário](howto-create-tenant.md#create-a-new-azure-ad-tenant)  

```bash
TENANT=<tenant ID>
```

Crie um grupo de recursos para o cluster. Execute o seguinte comando no shell de Bash que você usou para definir as variáveis acima:

```bash
az group create --name $CLUSTER_NAME --location $LOCATION
```

### <a name="optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network"></a>Opcional: Conectar a rede virtual do cluster a uma rede virtual existente

Se não for necessário conectar a VNET (rede virtual) do cluster criado a uma rede virtual existente, ignore esta etapa.

Primeiro, obtenha o identificador da VNET existente. O identificador será do formato: `/subscriptions/{subscription id}/resourceGroups/{resource group of VNET}/providers/Microsoft.Network/virtualNetworks/{VNET name}`.

Se você não souber o nome da rede ou o grupo de recursos ao qual pertence a rede virtual existente, vá para a [Folha redes virtuais](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Network%2FvirtualNetworks) e clique na sua rede virtual. A página da Rede virtual será exibida e listará o nome da rede e o grupo de recursos que ela pertence.

Defina uma variável VNET_ID usando o seguinte comando da CLI em um shell de BASH:

```bash
VNET_ID=$(az network vnet show -n {VNET name} -g {VNET resource group} --query id -o tsv)
```

Por exemplo: `VNET_ID=$(az network vnet show -n MyVirtualNetwork -g MyResourceGroup --query id -o tsv`

### <a name="create-the-cluster"></a>Criar o cluster

Agora você está pronto para criar um cluster.

 Se você não estiver conectando a rede virtual do cluster a uma rede virtual existente, omita o parâmetro `--vnet-peer-id $VNET_ID` à direita no exemplo a seguir.

```bash
az openshift create --resource-group $CLUSTER_NAME --name $CLUSTER_NAME -l $LOCATION --fqdn $FQDN --aad-client-app-id $APPID --aad-client-app-secret $SECRET --aad-tenant-id $TENANT --vnet-peer-id $VNET_ID
```

Após alguns minutos, `az openshift create` é concluído com êxito e retorna uma resposta de JSON contendo os detalhes do cluster.

> [!NOTE]
> Caso você receba um erro que o nome do host não está disponível, talvez seja porque o nome do cluster não é exclusivo. Tente excluir o registro do aplicativo original e refazer as etapas em [Criar um novo registro do aplicativo] (howto-aad-app-configuration.md#create-a-new-app-registration) (omitindo a etapa final de criação de um novo usuário, pois o mesmo já foi criado) com um nome de cluster diferente.

## <a name="step-3-sign-in-to-the-openshift-console"></a>Etapa 3: Entrar no console do OpenShift

Agora você está pronto para entrar no console do OpenShift com o novo cluster. O [Console Web OpenShift ](https://docs.openshift.com/dedicated/architecture/infrastructure_components/web_console.html) possibilita visualizar, procurar e gerenciar o conteúdo dos projetos do OpenShift.

Vamos entrar como o [novo usuário do Azure AD](howto-aad-app-configuration.md#create-a-new-active-directory-user) criado por você para testar. Para fazer isso, será necessária uma nova instância do navegador que ainda não armazenou em cache a identidade que você normalmente usa para entrar no portal do Azure.

1. Abra uma janela *anônimo* (Chrome) ou janela *InPrivate* (Microsoft Edge).
2. Navegue até a URL de Logon criada na etapa 6 em [Criar um novo registro do aplicativo](howto-aad-app-configuration.md#create-a-new-app-registration). Por exemplo, https://constoso.eastus.cloudapp.azure.com

> [!NOTE]
> O console do OpenShift usa um certificado autoassinado.
> Quando solicitado no seu navegador, ignore o aviso e aceite o certificado "não confiável".

Entre com o usuário e a senha criados em [Criar um novo usuário do Active Directory](howto-aad-app-configuration.md#create-a-new-active-directory-user) quando a caixa de diálogo **Permissões solicitadas** for exibida, selecione **Consentir em nome de sua organização**  e, em seguida, **Aceitar**.

Agora você está conectado ao console do cluster.

[Captura de tela do console do cluster do OpenShift](./media/aro-console.png)

 É possível saber mais sobre [Usar o console do OpenShift](https://docs.openshift.com/dedicated/getting_started/developers_console.html) para criar e compilar imagens na documentação do [Red Hat OpenShift](https://docs.openshift.com/dedicated/welcome/index.html).

## <a name="step-4-install-the-openshift-cli"></a>Etapa 4: Instalar a CLI do OpenShift

A [CLI do OpenShift](https://docs.openshift.com/dedicated/cli_reference/get_started_cli.html) (ou *Ferramentas de OC*) fornecem comandos para gerenciar os aplicativos e utilitários de nível inferior de modo a interagir com os diversos componentes do cluster do OpenShift.

No console do OpenShift, clique no ponto de interrogação no canto superior direito perto do seu nome de entrada e selecione **Ferramentas da Linha de Comando**.  Siga o link **Versão Mais Recente** para baixar e instalar a CLI de oc com suporte para Linux, MacOS ou Windows.

> [!NOTE]
> Se você não visualizar o ícone de ponto de interrogação no canto superior direito, selecione *Catálogo de Serviços* ou *Console do Aplicativo* na lista suspensa no canto superior esquerdo.
>
> Como alternativa, é possível [baixar a CLI de oc](https://www.okd.io/download.html) diretamente.

A página **Ferramentas da Linha de Comando** fornece um comando do formato `oc login https://<your cluster name>.<azure region>.cloudapp.azure.com --token=<token value>`.  Clique no botão *Copiar para área de transferência* para copiar esse comando.  Em uma janela de terminal, [defina o caminho](https://docs.okd.io/latest/cli_reference/get_started_cli.html#installing-the-cli) para incluir a instalação local das ferramentas de oc. Em seguida, entre no cluster usando o comando CLI de oc copiado.

Se não foi possível obter o valor do token usando as etapas acima, obtenha o valor do token de: `https://<your cluster name>.<azure region>.cloudapp.azure.com/oauth/token/request`.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um cluster do Red Hat OpenShift no Azure

Prosseguir para o próximo tutorial:
> [!div class="nextstepaction"]
> [Dimensionar um cluster do Red Hat OpenShift no Azure](tutorial-scale-cluster.md)