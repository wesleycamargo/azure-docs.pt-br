---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 05/22/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6cbf24c56458ab8b3b6c7b44bedbd8b48d4677b3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739198"
---
1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

2. Selecione **Aplicativo de Funções do Azure**, escolha **Criar Novo** e selecione **Publicar**.

    ![Escolher um destino de publicação](./media/functions-vstools-publish/functions-vstools-create-new-function-app.png)

2. Se você já não conectou o Visual Studio à conta do Azure, selecione **Adicionar uma conta...** .

3. No diálogo **Criar Serviço de Aplicativo**, use as configurações de **Hospedagem** conforme especificadas na tabela abaixo da imagem:

    ![Criar caixa de diálogo do Serviço de Aplicativo](./media/functions-vstools-publish/functions-vstools-publish.png)

    | Configuração      | Valor sugerido  | DESCRIÇÃO                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome do aplicativo** | Nome globalmente exclusivo | Nome que identifica seu novo aplicativo de funções de forma exclusiva. |
    | **Assinatura** | Escolha sua assinatura | A assinatura do Azure a utilizar. |
    | **[Grupo de Recursos](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nome do grupo de recursos no qual criar o seu aplicativo de funções. Escolha **Novo** para criar um novo grupo de recursos.|
    | **[Plano do Serviço de Aplicativo](../articles/azure-functions/functions-scale.md)** | Plano de consumo | Escolha **Consumo** em **Tamanho** após clicar em **Novo** para poder criar um novo plano. Também, escolha um **Local** em uma [região](https://azure.microsoft.com/regions/) perto de você ou perto de outros serviços que suas funções acessam.  |
    | **[Conta de armazenamento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** | Conta de armazenamento de uso geral | Uma conta de armazenamento do Azure é necessária para o tempo de execução de funções. Clique em **Novo** para criar uma conta de armazenamento de uso geral ou use uma existente.  |

4. Clique em **Criar** para criar um aplicativo de função e recursos relacionados no Azure com essas configurações e implantar seu código de projeto de função. 

5. Após a conclusão da implantação, anote o valor da **URL do Site**, que é o endereço do seu aplicativo de funções no Azure.

    ![Publicar mensagem de êxito](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
