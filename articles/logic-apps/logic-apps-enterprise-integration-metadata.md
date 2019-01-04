---
title: Gerenciar metadados de artefato de contas de integração – Aplicativos Lógicos do Azure | Microsoft Docs
description: Adicionar ou recuperar metadados de artefato de contas de integração nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128796"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Gerenciar metadados de artefato de contas de integração nos Aplicativos Lógicos do Azure com o Enterprise Integration Pack

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
