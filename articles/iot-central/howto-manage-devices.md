---
title: Gerenciar os dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como gerenciar dispositivos no aplicativo Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: b882c31f40f177b81fc84eb7071f396bddeaa0dd
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55658107"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerenciar dispositivos no aplicativo Azure IoT Central

Este artigo descreve como, como operador, gerenciar dispositivos no seu Microsoft IoT Central. Como um operador, você pode:

- Use a página **Device Explorer** para exibir, adicionar e excluir dispositivos conectados ao seu Microsoft IoT Central.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do dispositivo atualizados, alterando os valores armazenados nas propriedades de dispositivo.
- Controle o comportamento dos dispositivos, atualizando uma configuração em um dispositivo específico na página **Configurações**.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>Exibir dispositivos

Para exibir um dispositivo individual:

1. Escolha **Device Explorer** no menu de navegação à esquerda. Aqui você visualiza uma lista dos [modelos de dispositivos](howto-set-up-template.md).

1. Escolha um modelo de dispositivo em Modelos no painel esquerdo.

1. No painel direito da página do Device Explorer, você vê uma lista de dispositivos criados a partir desse modelo de dispositivo, conforme mostrado abaixo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para o dispositivo:

    [![Página Detalhes do Dispositivo](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo ao aplicativo Azure IoT Central:

1. Escolha **Device Explorer** no menu de navegação à esquerda.

1. Escolha o modelo de dispositivo, a partir do qual você deseja criar um dispositivo.

1. Escolha + **Novo**.

1. Escolha **Real** ou **Simulado**. Um dispositivo real é para um dispositivo físico que você conecta ao aplicativo Azure IoT Central. Um dispositivo simulado tem dados de exemplo gerados a você pelo Azure IoT Central. Este exemplo usa um dispositivo real. Escolha **Real** para navegar até a página **Detalhes do Dispositivo** do seu novo dispositivo.


## <a name="import-devices"></a>Importar dispositivos

Para conectar um grande número de dispositivos ao aplicativo, o Azure IoT Central oferece dispositivos de importação em massa por meio de um arquivo CSV. O arquivo CSV deve ter as seguintes colunas (e cabeçalhos)
1.  IOTC_DeviceID **<span style="color:Red">(deve estar em minúsculas)</span>**
1.  IOTC_DeviceName (opcional)


Para dispositivos de registro em massa no aplicativo:

1. Escolha **Device Explorer** no menu de navegação à esquerda.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja criar os dispositivos em massa.

 >   [!NOTE] 
    Se você ainda não tiver um modelo de dispositivo, poderá importar dispositivos em **Dispositivos não associados** e registrá-los sem nenhum modelo. Depois que os dispositivos forem importados, você poderá associá-los a um modelo como uma etapa subsequente.

1. Clique em **Importar**.

    [![Ação Importar](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Selecione o arquivo CSV que contém a lista de IDs dos Dispositivos a serem importados.

1. A importação de dispositivo inicia assim que o arquivo é carregado. É possível acompanhar o status de importação na parte superior da grade do dispositivo.

1. Depois que a importação estiver concluída, uma mensagem de êxito será exibida na grade do dispositivo.

    [![Importação com êxito](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Se a operação de importação do dispositivo falhar, uma mensagem de erro será exibida na grade do dispositivo. Um arquivo de log que captura todos os erros é gerado e pode ser baixado clicando na mensagem de erro.


**Associando dispositivos a um modelo**

Se você registrar dispositivos iniciando a importação em **Dispositivos não associados**, os dispositivos serão criados sem associação de modelos de dispositivo. O dispositivo deve estar associado a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estas etapas para associar dispositivos a um modelo:
1. Escolha **Device Explorer** no menu de navegação à esquerda.
1. No painel esquerdo, escolha **Dispositivos não associados**.
    [![Dispositivos não Associados](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Selecione os dispositivos que você deseja associar a um modelo.
1. Clique na opção **Associar**.
    [![Associar Dispositivos](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Escolha o modelo na lista de modelos disponíveis e clique no botão **Associar** .
1. Os dispositivos selecionados serão movidos sob o respectivo modelo de dispositivo.

 >   [!NOTE] 
    Depois que um dispositivo tiver sido associado a um modelo, não poderá ser desassociado ou associado a um modelo diferente.

## <a name="export-devices"></a>Exportar dispositivos

Para provisionar dispositivos para se conectar à IoT Central, você precisará de uma cadeia de conexão do dispositivo gerada pela IoT Central. Você pode usar o recurso Exportar para obter as cadeias de conexão e outras propriedades dos dispositivos em massa do seu aplicativo. A exportação cria um arquivo CSV com a identidade do dispositivo, o nome do dispositivo e a cadeia de conexão principal de todos os dispositivos selecionados.

Para exportar em massa os dispositivos do seu aplicativo:
1. Escolha **Device Explorer** no menu de navegação à esquerda.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja exportar os dispositivos.

1. Selecione os dispositivos que você deseja exportar e, em seguida, clique em **Exportar**.

    [![Exportar](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. O processo de exportação será iniciado e você poderá acompanhar o status na parte superior da grade. 

1. Quando a exportação for concluída, será mostrada uma mensagem com um link para baixar o arquivo gerado.

1. Clique na **mensagem** para baixar o arquivo para uma pasta local no disco.

    [![Exportação bem-sucedida](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. O arquivo CSV exportado terá as seguintes informações de colunas: **ID do dispositivo, Nome do dispositivo, Chaves primárias/secundárias do dispositivo e Impressões digitais do certificado primário/secundário**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

## <a name="delete-a-device"></a>Excluir um dispositivo

Para excluir um dispositivo real ou simulado do aplicativo Azure IoT Central:

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo que você deseja excluir.

1. Marque a caixa próxima ao dispositivo a ser excluído.

1. Clique em **Excluir**.

## <a name="change-a-device-setting"></a>Alterar uma configuração de dispositivo

As configurações controlam o comportamento de um dispositivo. Em outras palavras, elas permitem que você forneça entradas ao dispositivo. É possível exibir e atualizar as configurações do dispositivo na página **Detalhes do Dispositivo**.

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas configurações você quer alterar.

1. Escolha a guia **Configurações** . Nesta guia são exibidas todas as configurações do dispositivo e os respectivos valores atuais. Para cada configuração é possível ver se o dispositivo ainda está sendo sincronizado.

1. Modifique as configurações para os valores desejados. É possível modificar várias configurações de uma vez e atualizá-las todas ao mesmo tempo.

1. Escolha **Atualizar**. Os valores são enviados ao dispositivo. Quando o dispositivo confirmar a alteração da configuração, o status da configuração retornará para **sincronizado**.

## <a name="change-a-property"></a>Alterar uma propriedade

Propriedades são os metadados do dispositivo associados ao dispositivo, como cidade e número de série. É possível exibir e atualizar as propriedades na página **Detalhes do Dispositivo**.

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades você quer alterar.

1. Escolha a guia **Propriedades**, na qual todas as propriedades são exibidas.

1. Modificar as propriedades do aplicativo para seus valores desejados. É possível modificar várias propriedades de uma vez e atualizá-las todas ao mesmo tempo. Escolha **Atualizar**.

> [!NOTE]
> Não é possível alterar o valor das _propriedades do dispositivo_. As propriedades do dispositivo são definidas pelo dispositivo e são somente leitura no aplicativo Azure IoT Central.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar dispositivos no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como usar conjuntos de dispositivos](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
