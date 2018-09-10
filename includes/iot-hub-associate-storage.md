---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 8aa4695ea1175fe9d558e02bae661c9610123299
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43086400"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Associar uma conta do Armazenamento do Azure ao Hub IoT

Como o aplicativo do dispositivo simulado carrega um arquivo em um blob, você deve ter uma conta de [Armazenamento do Azure](../articles/storage/common/storage-quickstart-create-account.md) associada ao Hub IoT. Quando você associa uma conta de armazenamento do Azure a um hub IoT, o hub IoT gera um URI de SAS. Um dispositivo pode usar esse URI de SAS para carregar um arquivo com segurança em um contêiner de blob. O serviço de Hub IoT e os SDKs do dispositivo coordenam o processo que gera o URI de SAS e o torna disponível para um dispositivo a ser usado para carregar um arquivo.

Siga as instruções em [Configurar carregamentos de arquivo usando o portal do Azure](../articles/iot-hub/iot-hub-configure-file-upload.md) para associar uma conta de Armazenamento do Azure ao seu Hub IoT. Certifique-se de que um contêiner de blob é associado ao hub IoT e que as notificações de arquivo estão habilitadas.

![Habilitar Notificações de Arquivo no portal](./media/iot-hub-associate-storage/enable-file-notifications.png)