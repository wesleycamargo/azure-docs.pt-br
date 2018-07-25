---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: iot-edge
author: wesmc7777
ms.service: iot-edge
ms.topic: include
ms.date: 06/26/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 657006360105ac26091c54fe2e0deb523d1e6dea
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39008494"
---
Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos. 

> [!IMPORTANT]
> A exclusão de recursos do Azure e dos grupos de recursos é irreversível. Quando os itens são excluídos, o grupo de recursos e todos os recursos contidos nele são excluídos permanentemente. Não exclua acidentalmente grupo de recursos ou recursos incorretos. Caso tenha criado o hub IoT dentro de um grupo de recursos existente com recursos que você deseja manter, exclua o próprio recurso hub IoT em vez de excluir o grupo de recursos.
>

Para excluir apenas o hub IoT, execute o comando a seguir. Substitua \<YourIoTHub > pelo nome do hub IoT e \<TestResources > pelo nome do seu grupo de recursos:

```azurecli-interactive
az iot hub delete --name <YourIoTHub> --resource-group <TestResources>
```


Para excluir o grupo de recursos inteiro por nome:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Na caixa de texto **Filtrar por nome**, insira o nome do grupo de recursos que contém seu Hub IoT. 

3. À direita do seu grupo de recursos na lista de resultados, selecione as reticências (**...**) e selecione **Excluir grupo de recursos**.

    ![Excluir grupo de recursos](./media/iot-edge-quickstarts-clean-up-resources/iot-edge-delete-resource-group.png)

4. Você receberá uma solicitação para confirmar a exclusão do grupo de recursos. Insira novamente o nome do grupo de recursos para confirmar e selecione **Excluir**. Após alguns instantes, o grupo de recursos, e todos os recursos contidos nele, serão excluídos.






