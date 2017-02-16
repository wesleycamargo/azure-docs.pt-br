---
title: "Metadados da conta de integração do Aplicativo Lógico do Azure | Microsoft Docs"
description: "Visão geral dos metadados da conta de integração"
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e5d567800d00b41ec0782216b442f437a2500928
ms.openlocfilehash: 41edd713d85790793341e100f77300f999ac8c73


---
# <a name="azure-logic-apps-integration-account-metadata"></a>Metadados da conta de integração do Aplicativo Lógico do Azure

## <a name="overview"></a>Visão geral

Parceiros, contratos, esquemas, mapas adicionados a uma conta de integração armazenam metadados com pares de chave/valor. Você pode definir metadados personalizados que podem ser recuperados durante o tempo de execução.  Neste momento, os artefatos não têm a capacidade de criar metadados na interface de usuário; você pode usar as API REST para criá-los.  Parceiros, contratos e esquemas têm **EDITAR como JSON** e permitem a criação de chave de informações de metadados.  Em um aplicativo lógico, a **Pesquisa de Artefato da Conta de Integração** ajuda na recuperação das informações de metadados.

## <a name="how-to-store-metadata"></a>Como armazenar metadados

1. Crie uma [Conta de Integração](logic-apps-enterprise-integration-create-integration-account.md)   

2. Adicione um [parceiro](logic-apps-enterprise-integration-partners.md#how-to-create-a-partner), ou um [contrato](logic-apps-enterprise-integration-agreements.md#how-to-create-agreements), ou um [esquema](logic-apps-enterprise-integration-schemas.md) na conta de integração

3. Selecione um parceiro, ou um contrato, ou um esquema. Selecione **Editar como JSON** e insira os detalhes de metadados    
![Inserir metadados](media/logic-apps-enterprise-integration-metadata/image1.png)  

## <a name="call-integration-account-artifact-lookup-from-a-logic-app"></a>Chamar **Pesquisa de Artefato da Conta de Integração** de um aplicativo lógico

1. Crie um [Aplicativo Lógico](logic-apps-create-a-logic-app.md)

2. [Vincule](logic-apps-enterprise-integration-create-integration-account.md#link-an-integration-account-to-a-logic-app) o Aplicativo Lógico a uma Conta de Integração    

3. Crie um gatilho, por exemplo, usando *Solicitação* ou *HTTP* antes de procurar **Pesquisa de Artefato da Conta de Integração**.  Pesquise **integração** para procurar **Pesquisa de Artefato da Conta de Integração**  
![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image2.png)

3. Selecione **Pesquisa de Artefato da Conta de Integração**  

4. Selecione **Tipo de Artefato** e forneça **Nome do Artefato**  
![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="an-example-to-retrieve-partner-metadata"></a>Um exemplo para recuperar metadados do parceiro

1. Os metadados do parceiro tem detalhes da URL do roteamento    
![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image6.png)

2. Em um aplicativo lógico, configure **Pesquisa de Artefato da Conta de Integração** e **HTTP**   
![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image4.png)

3. Para recuperar o URI, o modo de exibição de código deve se parecer com    
![Procurar pesquisa](media/logic-apps-enterprise-integration-metadata/image5.png)


## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos](logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  



<!--HONumber=Feb17_HO1-->


