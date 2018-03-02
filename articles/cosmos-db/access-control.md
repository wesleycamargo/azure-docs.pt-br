---
title: Configurar controle de acesso no Azure Cosmos DB | Microsoft Docs
description: Saiba como configurar o controle de acesso no Azure Cosmos DB.
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: fae3af3f-4d6e-46d8-9d9b-b80a4218add9
ms.service: cosmos-db
ms.topic: article
ms.date: 02/06/2018
ms.author: mimig
ms.openlocfilehash: 5ef4a12c8229d8801a5d9749514a69c2c1e19499
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="access-control-in-azure-cosmos-db"></a>Controle de acesso no Azure Cosmos DB

Para adicionar o acesso de leitor de conta do Azure Cosmos DB à sua conta de usuário, peça ao proprietário de uma assinatura que execute as seguintes etapas no portal do Azure.

1. Abra o portal do Azure e selecione sua conta do Azure Cosmos DB.
2. Clique na guia **Controle de acesso (IAM)** e, em seguida, clique em **+ Adicionar**.
3. No painel **Adicionar permissões**, na caixa **Função**, selecione **Função de leitor de conta do Cosmos DB**.
4. Na caixa **Atribuir acesso à caixa**, selecione **Usuário, grupo ou aplicativo do Microsoft Azure Active Directory**.
5. Selecione o usuário, o grupo ou o aplicativo no diretório ao qual você deseja conceder acesso.  Você pode pesquisar o diretório por nome para exibição, endereço de email ou identificadores de objeto.
    O usuário, grupo ou aplicativo selecionado aparece na lista de membros selecionados.
6. Clique em **Salvar**.

A entidade agora poderá ler recursos do Azure Cosmos DB.
