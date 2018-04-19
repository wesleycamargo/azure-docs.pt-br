---
title: Terraform com slot de implantação de provedor do Azure
description: Tutorial do Terraform com slot de implantação de provedor do Azure
keywords: terraform, devops, máquina virtual, Azure, slots de implantação
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Usando Terraform para provisionar infraestrutura com slots de implantação do Azure

Os [slots de implantação do Azure](/azure/app-service/web-sites-staged-publishing) permitem alternar entre versões diferentes do seu aplicativo - como produção e preparo - para minimizar o impacto das implantações quebradas. Este artigo ilustra um exemplo de uso de slots de implantação ao guiá-lo pela implantação de dois aplicativos por meio de GitHub e Azure. Um aplicativo é hospedado em um "slot de produção", enquanto o segundo aplicativo é hospedado em um slot de "preparação". (Os nomes "produção" e "preparação" são arbitrários e podem ser qualquer coisa desejada que represente seu cenário.) Uma vez configurados seus slots de implantação, você pode usar o Terraform para alternar entre os dois slots conforme necessário.

## <a name="prerequisites"></a>pré-requisitos

- **Assinatura do Azure**: caso você não tenha uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Conta do GitHub** - Uma conta do [GitHub](http://www.github.com) é necessária para criar fork e usar o repositório GitHub de teste.

## <a name="create-and-apply-the-terraform-plan"></a>Criar e aplicar o plano do Terraform

1. Navegue até o [Portal do Azure](http://portal.azure.com)

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview) e selecione **Bash** como seu ambiente caso não tenha feito isso anteriormente.

    ![Aviso do Cloud Shell](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `deploy`.

    ```bash
    mkdir deploy
    ```

1. Crie um diretório com o nome `swap`.

    ```bash
    mkdir swap
    ```

1. Verifique se ambos os diretórios foram criados com êxito usando o comando bash `ls`.

    ![Cloud Shell após a criação de diretórios](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Altere os diretórios para o diretório `deploy`.

    ```bash
    cd deploy
    ```

1. Usando o [vi editor](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), crie um arquivo chamado `deploy.tf`, que conterá a [configuração do Terraform](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Entre no modo de inserção pressionando a tecla da letra `i`.

1. Cole o código a seguir no editor:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Pressione a tecla **&lt;Esc>** para sair do modo de inserção.

1. Salve o arquivo e saia do vi editor inserindo o seguinte comando, seguido do pressionamento de **&lt;Enter>**:

    ```bash
    :wq
    ```

1. Quando o arquivo tiver sido criado, você poderá verificar seu conteúdo.

    ```bash
    cat deploy.tf
    ```

1. Inicialize o Terraform.

    ```bash
    terraform init
    ```

1. Crie o plano do Terraform.

    ```bash
    terraform plan
    ```

1. Provisione os recursos definidos no arquivo de configuração `deploy.tf`. (Confirme a ação inserindo `yes` no prompt.)

    ```bash
    terraform apply
    ```

1. Feche a janela do Cloud Shell.

1. No menu principal do Portal do Azure, clique em **Grupos de recursos**.

    ![Grupos de recursos do Portal do Azure](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Na guia **Grupos de Recursos**, selecione **slotDemoResourceGroup**.

    ![Grupo de recursos criado pelo Terraform](./media/terraform-slot-walkthru/resource-group.png)

Quando terminar, você verá todos os recursos criados pelo Terraform.

![Recursos criados pelo Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Criar fork do projeto de teste

Antes de testar a criação e a troca dentro e fora dos slots de implantação, você precisa criar fork do projeto de teste do GitHub.

1. Navegue até o [repositório awesome-terraform no GitHub](https://github.com/Azure/awesome-terraform).

1. Crie fork do **repositório awesome-terraform**.

    ![Crie fork do repositório awesome-terraform do GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Siga todos os avisos para criar fork para seu ambiente.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Implantar do GitHub para seus slots de implantação

Assim que criar fork do repositório do projeto de teste, configure os slots de implantação com as seguintes etapas:

1. No menu principal do Portal do Azure, clique em **Grupos de recursos**.

1. Selecione **slotDemoResourceGroup**.

1. Selecione **slotAppService**.

1. Selecione **Opções de implantação**.

    ![Opções de implantação para um recurso de Serviço de Aplicativo](./media/terraform-slot-walkthru/deployment-options.png)

1. Na guia **Opção de implantação**, selecione **Escolher Fonte** e, em seguida, selecione **GitHub**.

    ![Selecionar fonte de implantação](./media/terraform-slot-walkthru/select-source.png)

1. Depois que o Azure faz a conexão e exibe todas as opções, selecione **Autorização**.

1. Na guia **Autorização**, selecione **Autorizar** e forneça as credenciais necessárias para o Azure acessar sua conta do GitHub. 

1. Depois que o Azure validar suas credenciais do GitHub, uma mensagem será exibida indicando que o processo de autorização foi concluído. Selecione **OK** para fechar a guia **Autorização**.

1. Selecione **Escolha sua organização** e selecione sua organização.

1. Selecione **Escolher projeto**.

1. Na guia **Escolher projeto**, selecione o projeto **awesome-terraform**.

    ![Escolher o projeto awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Selecione **Escolher branch**.

1. Na guia **Escolher branch**, selecione **mestre**.

    ![Escolher o branch mestre](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Na guia **Opção de implantação**, selecione **OK**.

Neste ponto, você implantou o slot de produção. Para implantar o slot de preparo, execute todas as etapas anteriores nesta seção apenas com as modificações a seguir:

- Na etapa 3, recurso **slotAppServiceSlotOne**.

- Na etapa 13, selecione o branch de "trabalho" em vez do branch mestre.

    ![Escolher branch de trabalho](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testar as implantações de aplicativo

Nas seções anteriores, você configura dois slots - **slotAppService** e **slotAppServiceSlotOne** - para implantar a partir de branches diferentes no GitHub. Vamos visualizar os aplicativos Web para validar que foram implantados com êxito.

Execute as etapas a seguir duas vezes, em que, na etapa 3, você seleciona **slotAppService** na primeira vez e, em seguida, seleciona **slotAppServiceSlotOne** na segunda vez:

1. No menu principal do Portal do Azure, clique em **Grupos de recursos**.

1. Selecione **slotDemoResourceGroup**.

1. Selecione **slotAppService** ou **slotAppServiceSlotOne**.

1. Na página de visão geral, selecione **URL**.

    ![Selecione a URL na guia de visão para renderizar o aplicativo](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Pode levar vários minutos para o Azure compilar e implantar o site a partir do GitHub.
>
>

Para o aplicativo Web **slotAppService**, você vê uma página azul com um título de página de **Slot Demo App 1**. Para o aplicativo Web **slotAppServiceSlotOne**, você vê uma página verde com um título de página de **Slot Demo App 2**.

![Visualizar os aplicativos para testar se foram implantados corretamente](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Trocar os dois slots de implantação

Para testar a troca dos dois slots de implantação, execute as seguintes etapas:
 
1. Alternar para a guia do navegador executando **slotAppService** (o aplicativo com a página azul). 

1. Retorne ao Portal do Azure em uma guia separada.

1. Abra o Cloud Shell.

1. Altere os diretórios para o diretório **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Usando o vi editor, crie um arquivo chamado `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Entre no modo de inserção pressionando a tecla da letra `i`.

1. Cole o código a seguir no editor:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Pressione a tecla **&lt;Esc>** para sair do modo de inserção.

1. Salve o arquivo e saia do vi editor inserindo o seguinte comando, seguido do pressionamento de **&lt;Enter>**:

    ```bash
    :wq
    ```

1. Inicialize o Terraform.

    ```bash
    terraform init
    ```

1. Crie o plano do Terraform.

    ```bash
    terraform plan
    ```

1. Provisione os recursos definidos no arquivo de configuração `swap.tf`. (Confirme a ação inserindo `yes` no prompt.)

    ```bash
    terraform apply
    ```

1. Quando o Terraform tiver finalizado a troca dos slots, volte para o navegador que está renderizando o aplicativo Web **slotAppService** e atualize a página. 

O aplicativo web no slot de preparo **slotAppServiceSlotOne** foi trocado pelo slot de produção e agora renderiza verde. 

![Os slots de implantação foram trocados](./media/terraform-slot-walkthru/slots-swapped.png)

Para retornar para a versão de produção original do aplicativo, aplique novamente o plano do Terraform criado a partir do arquivo de configuração `swap.tf`.

```bash
terraform apply
```

Quando estiverem trocados, você poderá ver a configuração original.