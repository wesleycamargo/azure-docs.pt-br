---
author: ramonarguelles
ms.service: spatial-anchors
ms.topic: include
ms.date: 1/30/2019
ms.author: rgarcia
ms.openlocfilehash: 3ee9aca1ee86b04998e357875b98b14782b3e56d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56963357"
---
### <a name="launch-the-publish-wizard"></a>Iniciar assistente de publicação

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **SharingService** e selecione **Publicar**.

O assistente de publicação é iniciado automaticamente. Selecione **Serviço de Aplicativo** > **Publicar** para abrir a caixa de diálogo **Criar Serviço de Aplicativo**.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Na caixa de diálogo **Criar Serviço de Aplicativo**, clique em **Adicionar uma conta** e faça logon em sua assinatura do Azure. Se você já estiver conectado, selecione a conta que deseja na lista suspensa.

> [!NOTE]
> Se você já estiver conectado, não selecione **Criar** ainda.
>

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](resource-group.md)]

Ao lado de **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos **myResourceGroup** e selecione **Ok**.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](app-service-plan.md)]

Ao lado de **Plano de Hospedagem**, selecione **Novo**.

Na caixa de diálogo **Configurar Plano de Hospedagem**, use as configurações na tabela.

| Configuração | Valor sugerido | DESCRIÇÃO |
|-|-|-|
|Plano do Serviço de Aplicativo| MySharingServicePlan | O nome do plano do Serviço de Aplicativo. |
| Local padrão | Oeste dos EUA | O datacenter onde o aplicativo Web está hospedado. |
| Tamanho | Grátis | O [Tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) determina os recursos de hospedagem. |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Publicar e publicar o aplicativo Web

Em **Nome do Aplicativo**, digite um nome exclusivo do aplicativo (os caracteres válidos são `a-z`, `0-9` e `-`) ou aceite o nome exclusivo gerado automaticamente. A URL do aplicativo Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome do aplicativo.

Clique em **Criar** para começar a criar os recursos do Azure.

Após a conclusão do assistente, ele publicará o aplicativo Web ASP.NET Core no Azure e, em seguida, iniciará o aplicativo no navegador padrão.

![Aplicativo Web ASP.NET publicado no Azure](./media/spatial-anchors-azure/web-app-running-live.png)

O nome do aplicativo especificado na [etapa para criar e publicar](#create-and-publish-the-web-app) é usado como o prefixo de URL no formato `https://<app_name>.azurewebsites.net`. Anote essa URL, pois ela será usada posteriormente.
