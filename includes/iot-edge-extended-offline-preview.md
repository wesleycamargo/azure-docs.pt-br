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
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004672"
---
## <a name="enabling-extended-offline-operation-preview"></a>Habilitando a operação offline estendida (versão prévia)
Começando pela [versão v1.0.2](https://aka.ms/edge102) do Tempo de execução do Edge, o dispositivo Edge e os dispositivos downstream que se conectam a ele podem ser configurados para operação offline estendida. 

Com essa funcionalidade, os módulos locais ou dispositivos downstream podem autenticar-se novamente com o dispositivo Edge conforme necessário e comunicar-se uns com os outros usando mensagens e métodos mesmo quando desconectados do Hub IoT. Confira esta [postagem no blog](https://aka.ms/iot-edge-offline) e o [artigo de conceito](../articles/iot-edge/offline-capabilities.md) para obter mais detalhes e o escopo dessa funcionalidade.

Para habilitar a operação offline estendida em um cenário de gateway, estabeleça uma relação pai-filho entre dispositivo de borda e dispositivos downstream que se conectarão a ela.

1. Na folha de detalhes do dispositivo Edge no portal do Hub IoT, clique no botão **Gerenciar dispositivos filho (versão prévia)** na barra de comandos superior.

1. Clique no botão **+ Adicionar**.

1. Na lista de dispositivos, selecione os dispositivos filho e use a seta para a direita para escolher as opções para adicionar como filhos.

1. Clique em **OK** para confirmar.

1. Na tela **Definir módulos** nos detalhes do dispositivo Edge, clique em **Definir configurações avançadas do Tempo de Execução do Edge** e, nas variáveis de ambiente do **Hub do Edge**, adicione uma entrada **UpstreamProtocol** com o valor **MQTT**. Adicione a mesma variável de ambiente e o mesmo valor para o **Agente do Edge** também. 

1. Clique em **Salvar** e certifique-se de **Enviar** as alterações após clicar em **Avançar** duas vezes.

Agora o dispositivo Edge e seus dispositivos filho estão habilitados para operação offline estendida.  