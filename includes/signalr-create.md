---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: signalr
author: wesmc7777
ms.service: signalr
ms.topic: include
ms.date: 04/17/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 57407606214d8d3a305476cfbfdabca9eee937e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60690266"
---
1. Para criar um novo recurso de serviço do Azure SignalR, primeiro entre no [Portal do Azure](https://portal.azure.com). No canto superior esquerdo da página, clique em **+ Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, digite **SignalR Service** e pressione **enter**.

2. Clique em **SignalR Service** nos resultados e clique em **Criar**.

3. Na nova página de configurações do **SignalR**, adicione as seguintes configurações ao novo recurso do SignalR:

    | Nome | Valor recomendado | Descrição |
    | ---- | ----------------- | ----------- |
    | Nome do Recurso | *testsignalr* | Insira um nome exclusivo do recurso a ser usado para o recurso do SignalR. O nome deve ser uma cadeia de caracteres com 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome não pode começar nem terminar com o caractere `-` e os caracteres `-` consecutivos não são válidos.|
    | Assinatura | Escolha sua assinatura |  Selecione a assinatura do Azure que você deseja usar para testar o SignalR. Se sua conta tiver apenas uma assinatura, ela será automaticamente selecionada e o menu suspenso **Assinatura** não será exibido.|
    | Grupo de recursos | Criar um novo grupo de recursos chamado *SignalRTestResources*| Selecione ou crie um grupo de recursos para seu recurso do SignalR. Esse grupo é útil para organizar vários recursos que você talvez queira excluir ao mesmo tempo, excluindo o grupo de recursos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](../articles/azure-resource-manager/resource-group-overview.md). |
    | Local padrão | *Leste dos EUA* | Use **Local** para especificar a localização geográfica em que o recurso do SignalR está hospedado. Para obter o melhor desempenho, recomendamos que você crie o recurso na mesma região que outros componentes do seu aplicativo. |
    | Tipo de preço | *Gratuito* | Atualmente, as opções **Gratuito** e **Standard** estão disponíveis. |
    | Fixar no painel | ✔ | Marque esta caixa para fixar o recurso no painel facilitando a localização. |

4. Clique em **Criar**. A implantação pode levar alguns minutos para ser concluída.

5. Após a conclusão da implantação, clique em **Chaves** em **CONFIGURAÇÕES**. Copie a cadeia de conexão da chave primária. Você usará isso posteriormente para configurar seu aplicativo para usar o recurso de Serviço do Azure SignalR.

    A cadeia de conexão terá o seguinte formato:
    
        Endpoint=<service_endpoint>;AccessKey=<access_key>;
