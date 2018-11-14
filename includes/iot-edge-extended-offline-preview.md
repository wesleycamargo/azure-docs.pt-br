---
title: Arquivo de inclusão
description: iot edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264339"
---
## <a name="enabling-extended-offline-operation-preview"></a>Habilitando a operação offline estendida (versão prévia)
Com o [lançamento v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) do Edge Runtime, o dispositivo Edge e os dispositivos downstream conectados a ele podem ser configurados para operação off-line estendida. 

Com essa funcionalidade, os módulos locais ou dispositivos downstream podem autenticar-se novamente com o dispositivo Edge conforme necessário e comunicar-se uns com os outros usando mensagens e métodos mesmo quando desconectados do Hub IoT. Confira esta [postagem no blog](https://aka.ms/iot-edge-offline) e o [artigo de conceito](../articles/iot-edge/offline-capabilities.md) para obter mais detalhes e o escopo dessa funcionalidade.

Para habilitar a operação offline estendida em um cenário de gateway, estabeleça uma relação pai-filho entre dispositivo de borda e dispositivos downstream que se conectarão a ela.

1. Na folha de detalhes do dispositivo Edge no portal do Hub IoT, clique no botão **Gerenciar dispositivos filho (versão prévia)** na barra de comandos superior.

1. Clique no botão **+ Adicionar**.

1. Na lista de dispositivos, selecione os dispositivos filho e use a seta para a direita para escolher as opções para adicionar como filhos.

1. Clique em **OK** para confirmar.

Agora o dispositivo Edge e seus dispositivos filho estão habilitados para operação offline estendida.  