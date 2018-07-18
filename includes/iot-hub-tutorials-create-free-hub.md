---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e3110e4018e214e7e7aa591b811246369c029ecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34667096"
---
Para criar um Hub IoT usando o Portal do Azure:

1. Entre no [Portal do Azure](http://portal.azure.com).

1. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

    ![Selecione para instalar o Hub IoT](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Para criar o hub IoT gratuito, use os valores na tabela abaixo:

    | Configuração | Valor |
    | ------- | ----- |
    | Assinatura | Na lista suspensa, selecione sua assinatura do Azure. |
    | Grupo de recursos | Criar novo. Este tutorial usa o nome **tutoriais-iot hub rg**. |
    | Região | Este tutorial usa **Oeste dos EUA**. Escolha a região mais próxima a você. |
    | NOME | A captura de tela a seguir usa o nome **tutorials-iot-hub**. Você precisa escolher seu próprio nome exclusivo ao criar seu hub. |

    ![Configurações de hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo e escala de preço | F1 Gratuito. Você só pode ter um hub de camada gratuita em uma assinatura. |
    | Unidades do Hub IoT | 1 |

    ![Configurações de hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Clique em **Criar**. Pode demorar alguns minutos para que o hub seja criado.

    ![Configurações de hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Anote o nome do hub IoT escolhido. Você usará esse valor posteriormente no tutorial.