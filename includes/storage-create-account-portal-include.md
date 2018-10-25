---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/18/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f21710354572c9b54dbf0e08d02809bb68a8e120
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49805047"
---
Para criar uma conta de armazenamento de uso geral v2 no portal do Azure, siga estas etapas:

1. No portal do Azure, expanda o menu à esquerda para abrir o menu de serviços e escolha **Todos os serviços**. Em seguida, role para baixo até **Armazenamento** e escolha **Contas de armazenamento**. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, clique em **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

    ![Captura de tela mostrando como criar um grupo de recursos no portal](./media/storage-create-account-portal-include/create-resource-group.png)

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deverá ser único no Azure, deverá ter entre 3 e 24 caracteres e poderá conter somente números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Deixe esses campos definidos como seus valores padrão:
    - O campo **Modelo de implantação** é definido como **Resource Manager** por padrão.
    - O campo **Desempenho** é definido como **Standard** por padrão.
    - O campo **Tipo de conta** é definido como **StorageV2 (general-purpose v2)** por padrão.
    - O campo **Replicação** definido como **LRS (Armazenamento com redundância local)** por padrão.
    - A **Camada de acesso** é definido como **Frequente** por padrão.

1. Clique em **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Clique em **Criar**.

Para obter mais informações sobre tipos de contas de armazenamento e outras configurações da conta de armazenamento, confira [Visão geral da conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-account-overview). Para saber mais sobre grupos de recursos, confira [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). 
