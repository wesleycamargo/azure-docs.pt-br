---
title: Usar o portal do Azure para configurar o carregamento do arquivo | Microsoft Docs
description: Como usar o Portal do Azure para configurar o Hub IoT a fim de habilitar transferências de arquivos de dispositivos conectados. Inclui informações sobre como configurar a conta de armazenamento do Azure de destino.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 0100cbe4bbc66d0c4ef940cc40f4fa3441176a1a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633199"
---
# <a name="configure-iot-hub-file-uploads-using-the-azure-portal"></a>Configurar uploads de arquivo do Hub IoT usando o Portal do Azure

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

## <a name="file-upload"></a>Upload de arquivos

Para usar a [funcionalidade de upload de arquivo no Hub IoT][lnk-upload], primeiro você deve associar uma conta de Armazenamento do Azure ao hub. Selecione **Upload de arquivo** para exibir uma lista de propriedades de upload de arquivo para o hub IoT que está sendo modificado.

![Exibir configurações de upload de arquivo do Hub IoT no portal][13]

**Contêiner de armazenamento**: use o Portal do Azure para selecionar um contêiner de blobs em uma conta de Armazenamento do Azure na assinatura atual do Azure para associar ao Hub IoT. Se for necessário, você pode criar uma conta de Armazenamento do Azure na folha **Contas de armazenamento** e um contêiner de blob na folha **Contêineres**. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

![Exibir contêineres de armazenamento para upload de arquivos no portal][14]

**Receber notificações para os arquivos carregados**: habilitar ou desabilitar notificações de upload de arquivo por meio de um botão de opção.

**TTL de SAS**: essa configuração é a vida útil dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Definido para uma hora por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

**TTL de configurações de notificação de arquivo padrão**: a vida útil de uma notificação de upload de arquivo antes de sua expiração. Definido para um dia por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

**Contagem de entrega máxima de notificação de arquivo**: o número de vezes que o Hub IoT tenta entregar uma notificação de carregamento de arquivo. Definido como 10 por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

![Configurar o upload de arquivos do Hub IoT no portal][15]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de upload de arquivo do Hub IoT, consulte [Carregar arquivos de um dispositivo][lnk-upload] no guia do desenvolvedor do Hub IoT.

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT][lnk-bulk]
* [Métricas do Hub IoT][lnk-metrics]
* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Implantação do IA em dispositivos de borda com o Azure IoT Edge][lnk-iotedge]
* [Proteger sua solução de IoT desde o início][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-securing]: iot-hub-security-ground-up.md
