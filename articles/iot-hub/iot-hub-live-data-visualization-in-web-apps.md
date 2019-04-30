---
title: Visualização de dados em tempo real de dados de sensor do seu Hub IoT do Azure – aplicativos Web | Microsoft Docs
description: Use a funcionalidade do aplicativo Web do Serviço de Aplicativo do Microsoft Azure para visualizar dados de temperatura e umidade que são coletados do sensor e enviados para o seu Hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 070f37a969411cfc4caf5f2d2b089ccfae759ca2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125705"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualizar dados do sensor em tempo real de seu Hub IoT do Azure usando a funcionalidade de aplicativos Web do serviço de aplicativo do Azure

![Diagrama de ponta a ponta](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Neste tutorial, você aprenderá como visualizar dados de sensor em tempo real que o seu Hub IoT recebe por meio da execução de um aplicativo Web hospedado em um aplicativo Web. Se você quiser tentar visualizar os dados em seu Hub IoT usando o Power BI, consulte [Usar o Power BI para visualizar dados de sensor em tempo real do Hub IoT do Azure](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>O que fazer

* Criar um aplicativo Web no portal do Azure.
* Preparar seu Hub IoT para acesso a dados, adicionando um grupo de consumidores.
* Configurar o aplicativo Web para ler dados de sensor de seu Hub IoT.
* Carregar um aplicativo Web a ser hospedado pelo aplicativo Web.
* Abrir o aplicativo Web para ver os dados de temperatura e umidade em tempo real de seu Hub IoT.

## <a name="what-you-need"></a>O que você precisa

* Conclua o [simulador online Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial ou um dos tutoriais dispositivo; por exemplo, [Raspberry Pi com Node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Estes abrangem os seguintes requisitos:

  * Uma assinatura ativa do Azure
  * Um Hub IoT em sua assinatura
  * Um aplicativo cliente que envia mensagens para o seu Hub IoT

* [Baixar Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Criar um aplicativo Web

1. No [Portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** > **Web + Celular** > **Aplicativo Web**.

2. Insira um nome de trabalho exclusivo, verifique a assinatura, especifique um grupo de recursos e um local, selecione **Fixar no painel** e, em seguida, clique em **Criar**.

   Recomendamos que você selecione o mesmo local do seu grupo de recursos. 
   
   ![Criar um aplicativo Web](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configurar o aplicativo Web para ler dados de sensor de seu Hub IoT

1. Abra o aplicativo Web que você acabou de provisionar.

2. Clique em **Configurações do aplicativo** e, em seguida, adicione os seguintes pares chave/valor em **Configurações do aplicativo**:

   | Chave                                   | Value                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtido da CLI do Azure                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | O nome do grupo de consumidores que você adiciona ao seu Hub IoT  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![Adicionar configurações ao seu aplicativo Web com pares chave/valor](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. Clique em **Configurações do aplicativo**, em **Configurações gerais**, alternar a opção de **soquetes da Web** e, em seguida, clique em **Salvar**.

   ![Alternar a opção de soquetes da Web](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Carregar um aplicativo Web a ser hospedado pelo aplicativo Web

No GitHub, foi disponibilizado um aplicativo Web que exibe dados de sensor em tempo real obtidos do seu Hub IoT. Tudo que você precisa fazer é configurar o aplicativo Web para trabalhar com um repositório Git, baixar o aplicativo Web do GitHub e carregá-lo no Azure para ser hospedado pelo aplicativo Web.

1. No aplicativo Web, clique em **Opções de Implantação** > **Escolher Fonte** > **Repositório Git Local**, e, em seguida, clique em **OK**.

   ![Configurar a implantação do seu aplicativo Web para usar o repositório Git local](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Clique em **Credenciais de Implantação**, crie um nome de usuário e senha que serão usados para conexão ao repositório Git no Azure e, em seguida, clique em **Salvar**.

3. Clique em **Visão geral** e anote o valor de **URL de clone do Git**.

   ![Obter a URL de clone do Git de seu aplicativo Web](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. Abra uma janela de terminal ou de comando no computador local.

5. Baixe o aplicativo Web do GitHub e carregue-o no Azure para ser hospedado pelo aplicativo Web. Para fazer isso, execute os seguintes comandos:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > \<URL de clone do Git\> é a URL do repositório Git encontrado na página **Visão geral** do aplicativo Web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Abrir o aplicativo Web para ver os dados de temperatura e umidade em tempo real de seu Hub IoT

Na página **Visão geral** do seu aplicativo Web, clique na URL para abri-lo.

![Obter a URL do seu aplicativo web](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

Você deve ver os dados de temperatura e umidade em tempo real de seu Hub IoT.

![Página de aplicativo Web mostrando a umidade e a temperatura em tempo real](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Verifique se o aplicativo de exemplo está em execução em seu dispositivo. Se não, você receberá um gráfico em branco, você pode consultar os tutoriais em [configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Próximas etapas

Você usou com êxito seu aplicativo Web para visualizar dados do sensor em tempo real do seu Hub IoT.

Para obter um modo alternativo para visualizar os dados de Hub IoT do Azure, consulte [Usar o Power BI para visualizar dados de sensor em tempo real do seu Hub IoT](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]