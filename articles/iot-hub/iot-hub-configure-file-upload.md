---
title: Usar o portal do Azure para configurar o carregamento do arquivo | Microsoft Docs
description: "Uma visão geral de como configurar o carregamento do arquivo usando o Portal do Azure"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 915f1597-272d-4fd4-8c5b-a0ccb1df0d91
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e3ac3e8cee2724b76f51423d1a6757382cca04f0


---
# <a name="configure-file-uploads-using-the-azure-portal"></a>Configurar carregamentos de arquivo usando o Portal do Azure
## <a name="file-upload"></a>Upload de arquivos
Para usar a [funcionalidade de carregamento de arquivo no Hub IoT][lnk-upload], primeiro você deve associar uma conta de Armazenamento do Azure ao hub. Selecione as configurações de **Upload de arquivo** para exibir uma lista de propriedades de upload de arquivo para o hub IoT que está sendo modificado.

![][13]

**Contêiner de armazenamento**: use o Portal do Azure para selecionar um contêiner de blobs em uma conta de Armazenamento do Azure na assinatura atual do Azure para associar ao Hub IoT. Se for necessário, você pode criar uma conta de Armazenamento do Azure na folha **Contas de armazenamento** e um contêiner de blob na folha **Contêineres**. O Hub IoT gera automaticamente os URIs de SAS com permissões de gravação para esse contêiner de blob para dispositivos a serem usados ao carregar arquivos.

![][14]

**Receber notificações para os arquivos carregados**: habilitar ou desabilitar notificações de upload de arquivo por meio de um botão de opção.

**TTL de SAS**: essa configuração é a vida útil dos URIs de SAS retornados para o dispositivo pelo Hub IoT. Definido para uma hora por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

**TTL de configurações de notificação de arquivo padrão**: a vida útil de uma notificação de upload de arquivo antes de sua expiração. Definido para um dia por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

**Contagem de entrega máxima de notificação de arquivo**: o número de vezes que o Hub IoT tenta entregar uma notificação de carregamento de arquivo. Definido como 10 por padrão, mas pode ser personalizado para outros valores usando o controle deslizante.

![][15]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os recursos de carregamento de arquivo do Hub IoT, consulte [Carregar arquivos de um dispositivo][lnk-upload] no guia do desenvolvedor.

Para saber mais sobre o gerenciamento do Hub IoT do Azure, siga estes links:

* [Gerenciamento em massa de dispositivos IoT][lnk-bulk]
* [Métricas de uso][lnk-metrics]
* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedor][lnk-devguide]
* [Simulando um dispositivo com o SDK de Gateway do IoT][lnk-gateway]
* [Proteger sua solução de IoT desde o início][lnk-securing]

[13]: ./media/iot-hub-configure-file-upload/file-upload-settings.png
[14]: ./media/iot-hub-configure-file-upload/file-upload-container-selection.png
[15]: ./media/iot-hub-configure-file-upload/file-upload-selected-container.png

[lnk-upload]: iot-hub-devguide-file-upload.md

[lnk-bulk]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-securing]: iot-hub-security-ground-up.md



<!--HONumber=Nov16_HO3-->


