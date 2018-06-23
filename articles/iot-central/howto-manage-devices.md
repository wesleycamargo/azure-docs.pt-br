---
title: Gerenciar os dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como gerenciar dispositivos no aplicativo Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/21/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8a1b88621feaaaff3f787cca8c4b4e45d4974931
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807468"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerenciar dispositivos no aplicativo Azure IoT Central

Este artigo descreve como um operador gerencia dispositivos no aplicativo Microsoft Azure IoT Central. Como um operador, você pode:

- Use a página **Explorer** para exibir, adicionar e excluir dispositivos conectados ao aplicativo Azure IoT Central.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do dispositivo atualizados, alterando os valores armazenados nas propriedades de dispositivo.
- Controle o comportamento dos dispositivos, atualizando uma configuração em um dispositivo específico na página **Configurações**.

## <a name="view-your-devices"></a>Exibir dispositivos

Para exibir um dispositivo individual:

1. Escolha **Explorer** no menu de navegação esquerdo. Aqui você visualiza uma lista dos [modelos de dispositivos](howto-set-up-template.md).

1. Escolha um **Modelo de Dispositivo** no painel esquerdo.

1. No painel direito, é exibida uma lista de dispositivos criados a partir desse modelo de dispositivo. Escolha um dispositivo individual para exibir a página **Detalhes do Dispositivo** desse dispositivo:

    [![Página Detalhes do Dispositivo](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo ao aplicativo Azure IoT Central:

1. Escolha **Explorer** no menu de navegação esquerdo.

1. Escolha o modelo de dispositivo, a partir do qual você deseja criar um dispositivo.

1. Escolha + **Novo**.

1. Escolha **Real** ou **Simulado**. Um dispositivo real é para um dispositivo físico que você conecta ao aplicativo Azure IoT Central. Um dispositivo simulado tem dados de exemplo gerados a você pelo Azure IoT Central. Este exemplo usa um dispositivo real. Escolha **Real** para navegar até a página **Detalhes do Dispositivo** do seu novo dispositivo.


## <a name="bulk-import-devices"></a>Dispositivos de importação em massa

Para conectar um grande número de dispositivos ao aplicativo, o Azure IoT Central oferece dispositivos de importação em massa por meio de um arquivo CSV. 

Requisitos de arquivo CSV:
1. O arquivo CSV deve ter apenas uma coluna com as IDs dos Dispositivos.

1. O arquivo não deve ter nenhum cabeçalho.


Para dispositivos de registro em massa no aplicativo:

1. Escolha **Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja criar os dispositivos em massa.

1. Escolha **Novo** e selecione **Importar em Massa**.

    [![Ação Importar em Massa](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Selecione o arquivo CSV que contém a lista de IDs dos Dispositivos a serem importados.

1. A importação de dispositivo inicia assim que o arquivo é carregado. É possível acompanhar o status de importação na parte superior da grade do dispositivo.

1. Depois que a importação estiver concluída, uma mensagem de êxito será exibida na grade do dispositivo.

    [![Importação em Massa com Êxito](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Se a operação de importação do dispositivo falhar, uma mensagem de erro será exibida na grade do dispositivo. Um arquivo de log que captura todos os erros é gerado e pode ser baixado clicando na mensagem de erro.

## <a name="export-devices"></a>Exportar dispositivos

Para provisionar dispositivos para se conectar à IoT Central, você precisará de uma cadeia de conexão do dispositivo gerada pela IoT Central. Você pode usar o recurso Exportar para obter as cadeias de conexão e outras propriedades dos dispositivos em massa do seu aplicativo. A exportação cria um arquivo CSV com a identidade do dispositivo, o nome do dispositivo e a cadeia de conexão principal de todos os dispositivos selecionados.

Para exportar em massa os dispositivos do seu aplicativo:
1. Escolha **Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja exportar os dispositivos.

1. Selecione os dispositivos que você deseja exportar e, em seguida, clique em **Exportar**.

    [![Exportar](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. O processo de exportação será iniciado e você poderá acompanhar o status na parte superior da grade. 

1. Quando a exportação for concluída, será mostrada uma mensagem com um link para baixar o arquivo gerado.

1. Clique na **mensagem** para baixar o arquivo para uma pasta local no disco.

    [![Exportação bem-sucedida](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. O arquivo CSV exportado terá as seguintes informações:
    1. NOME
    1. Id do Dispositivo
    1. Cadeia de conexão primária


## <a name="delete-a-device"></a>Excluir um dispositivo

Para excluir um dispositivo real ou simulado do aplicativo Azure IoT Central:

1. Escolha **Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo que você deseja excluir.

1. Marque a caixa próxima ao dispositivo a ser excluído.

1. Clique em **Excluir**.

## <a name="change-a-device-setting"></a>Alterar uma configuração de dispositivo

As configurações controlam o comportamento de um dispositivo. Em outras palavras, elas permitem que você forneça entradas ao dispositivo. É possível exibir e atualizar as configurações do dispositivo na página **Detalhes do Dispositivo**.

1. Escolha **Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas configurações você quer alterar.

1. Escolha a guia **Configurações** . Nesta guia são exibidas todas as configurações do dispositivo e os respectivos valores atuais. Para cada configuração é possível ver se o dispositivo ainda está sendo sincronizado.

1. Modifique as configurações para os valores desejados. É possível modificar várias configurações de uma vez e atualizá-las todas ao mesmo tempo.

1. Escolha **Atualizar**. Os valores são enviados ao dispositivo. Quando o dispositivo confirmar a alteração da configuração, o status da configuração retornará para **sincronizado**.

## <a name="change-a-property"></a>Alterar uma propriedade

Propriedades são os metadados do dispositivo associados ao dispositivo, como cidade e número de série. É possível exibir e atualizar as propriedades na página **Detalhes do Dispositivo**.

1. Escolha **Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades você quer alterar.

1. Escolha a guia **Propriedades**, na qual todas as propriedades são exibidas.

1. Modifique as propriedades para os valores desejados. É possível modificar várias propriedades de uma vez e atualizá-las todas ao mesmo tempo.

1. Escolha **Atualizar**.

> [!NOTE]
> Não é possível alterar o valor das _propriedades do dispositivo_. As propriedades do dispositivo são definidas pelo dispositivo e são somente leitura no aplicativo Azure IoT Central.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar dispositivos no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como usar conjuntos de dispositivos](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
