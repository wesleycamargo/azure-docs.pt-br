---
title: Gerenciar metadados de artefato de contas de integração – Aplicativo Lógico do Azure | Microsoft Docs
description: Adicionar ou recuperar metadados de artefato de contas de integração dos Aplicativo Lógico do Azure
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e7ef6aef9bc1062ae0f76adfbaf086961fcaa94
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298358"
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Gerenciar metadados de artefato nas contas de integração de aplicativos lógicos

É possível definir metadados personalizados para artefatos em contas de integração e recuperar esses metadados durante o tempo de execução do aplicativo lógico. Por exemplo, é possível especificar metadados para artefatos, como parceiros, contratos, esquemas e mapas – todos armazenam metadados usando pares chave-valor. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Adicionar metadados a artefatos em contas de integração

1. No portal do Azure, crie uma [conta de integração](logic-apps-enterprise-integration-create-integration-account.md).

2. Adicione um artefato à conta de integração, por exemplo, um [parceiro](logic-apps-enterprise-integration-partners.md), [contrato](logic-apps-enterprise-integration-agreements.md) ou [esquema](logic-apps-enterprise-integration-schemas.md).

3. Selecione o artefato, escolha **Editar** e insira os detalhes dos metadados.

   ![Inserir metadados](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Recuperar metadados de artefatos de aplicativos lógicos

1. No Portal do Azure, crie um [aplicativo lógico](quickstart-create-first-logic-app-workflow.md).

2. Crie um [link do aplicativo lógico para a conta de integração](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. No Designer de Aplicativos Lógicos, adicione um gatilho como **Solicitação** ou **HTTP** ao aplicativo lógico.

4. No gatilho, escolha **Nova etapa** > **Adicionar uma ação**. Pesquise “conta de integração” para poder localizá-la e, em seguida, selecione esta ação: **Conta de Integração – Pesquisa de Artefato da Conta de Integração**

   ![Selecionar Pesquisa de Artefato da Conta de Integração](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Selecione o **Tipo de Artefato** e forneça o **Nome do Artefato**. Por exemplo: 

   ![Selecionar tipo de artefato e especificar um nome de artefato](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Exemplo: Recuperar metadados do parceiro

Suponha que esse parceiro tenha metadados com `routingUrl` detalhes:

![Encontrar metadados de “routingURL” do parceiro](media/logic-apps-enterprise-integration-metadata/image6.png)

1. No aplicativo lógico, adicione o gatilho, uma ação **Conta de Integração – Pesquisa de Artefato da Conta de Integração** para o parceiro e uma ação **HTTP**, por exemplo:

   ![Adicionar gatilho, pesquisa de artefato e ação HTTP para o aplicativo lógico](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Para recuperar o URI, na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Visualização de Código** para seu aplicativo lógico. A definição do aplicativo lógico deve ser parecida com este exemplo:

   ![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre contratos](logic-apps-enterprise-integration-agreements.md)
