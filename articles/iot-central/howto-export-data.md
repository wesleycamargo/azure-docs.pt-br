---
title: Exportar dados no Azure IoT Central | Microsoft Docs
description: Como exportar dados do aplicativo do Azure IoT Central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 12/07/2018
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: cba0bad2e81ffddedfc4ca04e82e17e4286b389b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53312112"
---
# <a name="export-your-data-in-azure-iot-central"></a>Exportar dados no Azure IoT Central

*Este tópico aplica-se aos administradores.*

Este artigo descreve como usar o recurso de exportação contínua de dados no Azure IoT Central para exportar seus dados para suas próprias instâncias do **Armazenamento de Blobs do Azure**, dos **Hubs de Eventos do Azure** e do **Barramento de Serviço do Azure**. Exporte **medidas**, **dispositivos** e **modelos de dispositivo** para seu próprio destino para análise de caminhos warm e cold. Exporte os dados para o Armazenamento de Blobs para executar a análise de tendências de longo prazo no Microsoft Power BI ou exporte os dados para os Hubs de Eventos e o Barramento de Serviço para transformar e aumentar seus dados em tempo quase real com os Aplicativos Lógicos do Azure ou o Azure Functions.

> [!Note]
> Ao ativar a exportação contínua de dados, você obtém apenas os dados desse momento em diante. Atualmente, não é possível recuperar dados por um tempo quando a exportação contínua de dados for desativada. Para reter dados mais históricos, ative a exportação contínua de dados no início.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ser um administrador do aplicativo IoT Central

## <a name="export-to-blob-storage"></a>Exportar para o Armazenamento de Blobs

Os dados de medidas, dispositivos e modelos de dispositivo são exportados para sua conta de armazenamento uma vez por minuto, com cada arquivo contendo o lote de alterações desde o último arquivo exportado. Os dados exportados estão no formato [Apache AVRO](https://avro.apache.org/docs/current/index.html).

Saiba mais sobre [a exportação para o Armazenamento de Blobs](howto-export-data-blob-storage.md).

## <a name="export-to-event-hubs-and-service-bus"></a>Exportar para Hubs de Eventos e Barramento de Serviço

Os dados de medidas, dispositivos e modelos de dispositivo são exportados para o hub de eventos ou uma fila ou um tópico do Barramento de Serviço. Os dados de medidas exportados são recebidos em tempo quase real e contêm a totalidade da mensagem enviada pelos dispositivos para o IoT Central, não apenas os valores das medidas propriamente ditos. Os dados de dispositivos exportados são recebidos em lotes uma vez por minuto e contêm as alterações em propriedades e as configurações de todos os dispositivos. Os modelos de dispositivo exportados contêm as alterações em todos os modelos de dispositivo.


Saiba mais sobre [a exportação para os Hubs de Eventos e o Barramento de Serviço](howto-export-data-event-hubs-service-bus.md).

## <a name="set-up-export-destination"></a>Configurar o destino de exportação

Caso não tenha um Armazenamento/Hubs de Eventos/Barramento de Serviço para o qual exportar, siga estas etapas:

### <a name="create-storage-account"></a>Criar Conta de Armazenamento

1. Crie uma [conta de armazenamento no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Saiba mais na [documentação do Armazenamento do Azure](https://aka.ms/blobdocscreatestorageaccount).
2. Para o tipo de conta, escolha **Uso geral** ou **Armazenamento de Blobs**.
3. Escolha uma assinatura. 

    > [!Note] 
    > Agora você pode exportar dados para outras assinaturas que **não são a mesma** usada para o aplicativo IoT Central pago conforme o uso. Você se conectará usando uma cadeia de conexão, nesse caso.

4. Crie um contêiner em sua conta de armazenamento. Vá até sua conta de armazenamento. Em **Serviço Blob**, selecione **Procurar Blobs**. Selecione **+ Contêiner** na parte superior para criar um contêiner.

### <a name="create-event-hubs-namespace"></a>Criar um namespace dos Hubs de Eventos

1. Crie um [namespace dos Hubs de Eventos no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.EventHub). Saiba mais na [documentação dos Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora você pode exportar dados para outras assinaturas que **não são a mesma** usada para o aplicativo IoT Central pago conforme o uso. Você se conectará usando uma cadeia de conexão, nesse caso.
3. Crie um hub de eventos em seu namespace dos Hubs de Eventos. Acesse o namespace e selecione **+ Hub de Eventos** na parte superior para criar uma instância de hub de eventos.

### <a name="create-service-bus-namespace"></a>Criar um namespace do Barramento de Serviço

1. Crie um [namespace do Barramento de Serviço no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Saiba mais na [documentação do Barramento de Serviço do Azure](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-create-namespace-portal).
2. Escolha uma assinatura. 

    > [!Note] 
    > Agora você pode exportar dados para outras assinaturas que **não são a mesma** usada para o aplicativo IoT Central pago conforme o uso. Você se conectará usando uma cadeia de conexão, nesse caso.

3. Acesse o namespace do Barramento de Serviço e selecione **+ Fila** ou **+ Tópico** na parte superior para criar uma fila ou um tópico para o qual exportar.

## <a name="set-up-continuous-data-export"></a>Configuração da exportação contínua de dados

Agora que você tem um destino do Armazenamento/Hubs de Eventos/Barramento de Serviço para o qual exportar os dados, siga estas etapas para configurar a exportação contínua de dados. 

1. Entre no aplicativo IoT Central.

2. No menu à esquerda, clique em **Exportação Contínua de Dados**.

    > [!Note]
    > Se a Exportação Contínua de Dados não for exibida no menu à esquerda, isso indicará que você não é um administrador no aplicativo. Contate o administrador para configurar a exportação de dados.

    ![Criar novo Hub de Eventos cde](media/howto-export-data/export_menu.PNG)

3. Clique no botão **+ Novo** no canto superior direito. Escolha uma das opções **Armazenamento de Blobs do Azure**, **Hubs de Eventos do Azure** ou **Barramento de Serviço do Azure** como o destino da exportação. 

    > [!NOTE] 
    > O número máximo de exportações por aplicativo é cinco. 

    ![Criar exportação contínua de dados](media/howto-export-data/export_new.PNG)

4. Na caixa de listagem suspensa, selecione a **Conta de Armazenamento/namespace do Barramento de Serviço/namespace dos Hubs de Eventos**. Você também pode escolher a última opção da lista, **Inserir uma cadeia de conexão**. 

    > [!NOTE] 
    > Você verá apenas as Contas de Armazenamento/os namespace dos Hubs de Eventos/namespaces do Barramento de Serviço na **mesma assinatura do aplicativo IoT Central**. Caso deseje exportar para um destino fora dessa assinatura, escolha **Inserir uma cadeia de conexão** e veja a etapa 5.

    > [!NOTE] 
    > Para aplicativos de avaliação de 7 dias, a única maneira de configurar a exportação contínua de dados é por meio de uma cadeia de conexão. Isso ocorre porque os aplicativos de avaliação de 7 dias não têm uma assinatura do Azure associada.

    ![Criar novo Hub de Eventos cde](media/howto-export-data/export_create.PNG)

5. (Opcional) Se você escolher **Inserir uma cadeia de conexão**, uma nova caixa será exibida para que você cole a cadeia de conexão. Para obter a cadeia de conexão para:
    - Conta de armazenamento, acesse a Conta de armazenamento no portal do Azure.
        - Em **Configurações**, clique em **Chaves de acesso**
        - Copie a Cadeia de conexão de key1 ou a Cadeia de conexão de key2
    - Hubs de Eventos ou Barramento de Serviço, acesse o namespace no portal do Azure.
        - Em **Configurações**, clique em **Políticas de Acesso Compartilhado**
        - Escolha a **RootManageSharedAccessKey** padrão ou crie uma
        - Copie a cadeia de conexão primária ou secundária
 
6. Escolha um Contêiner/Hub de eventos/Fila ou Tópico na caixa de listagem suspensa.

7. Em **Dados a serem exportados**, especifique cada tipo de dados a serem exportados, definindo o tipo para **Ativar**.

6. Para ativar a exportação contínua de dados, verifique se a opção **Exportação de dados** está **Ativada**. Clique em **Salvar**.

  ![Configure a exportação contínua de dados](media/howto-export-data/export_list.PNG)

7. Após alguns minutos, seus dados serão exibidos no destino escolhido.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe como exportar seus dados, continue para a próxima etapa:

> [!div class="nextstepaction"]
> [Exportar dados para o Armazenamento de Blobs do Azure](howto-export-data-blob-storage.md)

> [!div class="nextstepaction"]
> [Exportar dados para os Hubs de Eventos do Azure e o Barramento de Serviço do Azure](howto-export-data-event-hubs-service-bus.md)

> [!div class="nextstepaction"]
> [Como visualizar os dados no Power BI](howto-connect-powerbi.md)
