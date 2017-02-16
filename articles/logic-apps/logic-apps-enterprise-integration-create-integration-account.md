---
title: "Criar, vincular, excluir ou mover uma conta de integração no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Como criar uma conta de integração e vinculá-la aos seus aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: dca762854f22721de76f69cbc28c0377d563fe28
ms.openlocfilehash: e26271a1291fc76e3ea8f93a7aa9ab8c811ff604


---

# <a name="what-is-an-integration-account"></a>O que é uma conta de integração?
Uma conta de integração permite aos aplicativos de integração corporativa gerenciar artefatos, incluindo esquemas, mapas, certificados, parceiros e contratos. Qualquer aplicativo de integração que você criar usará uma conta de integração para acessar esses esquemas, mapas, certificados etc.

## <a name="create-an-integration-account"></a>Criar uma conta de integração
1. Selecione **Procurar**:   
   ![](./media/logic-apps-enterprise-integration-accounts/account-1.png)  
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados:     
   ![](./media/logic-apps-enterprise-integration-accounts/account-2.png)  
3. Selecione o botão *Adicionar* no menu na parte superior da página:      
   ![](./media/logic-apps-enterprise-integration-accounts/account-3.png)  
4. Insira o **Nome**, selecione a **Assinatura** que você deseja usar, crie um novo **Grupo de recursos** ou selecione um grupo de recursos existente, selecione um **Local** onde sua conta de integração será hospedada, selecione uma **Camada de preços**, em seguida, selecione o botão **Criar**.   
   
   Neste ponto, a conta de integração será provisionada no local selecionado. Isso deve ser concluído dentro de 1 minuto.    
   ![](./media/logic-apps-enterprise-integration-accounts/account-4.png)  
5. Atualize a página. Você verá a nova conta de integração listada:  
   ![](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Em seguida, vincule a conta de integração que você acabou de criar ao seu aplicativo lógico. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Vincular uma conta de integração a um Aplicativo lógico
Para que seus Aplicativos lógicos acessem mapas, esquemas, contratos e outros artefatos em sua conta de integração, vincule a conta de integração ao seu Aplicativo lógico.

#### <a name="prereqs"></a>Pré-requisitos
* Uma conta de integração
* Um aplicativo lógico

> [!NOTE] 
> Certifique-se de que sua conta de integração e o Aplicativo lógico estejam no **mesmo local do Azure** antes de começar.

1. Selecione o link **Configurações** no menu do seu Aplicativo lógico:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)   
2. Selecione o item **Conta de Integração** na folha Configurações:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)   
3. Selecione a conta de integração que você deseja vincular ao seu Aplicativo lógico na caixa de lista suspensa **Selecionar uma Conta de integração**:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)   
4. Salve seu trabalho:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-4.png)   
5. Você verá uma notificação indicando que sua conta de integração foi vinculada ao seu Aplicativo lógico, e que todos os artefatos de sua conta de integração estão disponíveis para seu Aplicativo lógico:  
   ![](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)   

Agora que sua conta de integração está vinculada ao seu aplicativo lógico, você pode usar os conectores B2B dentro dos aplicativos lógicos. Alguns conectores B2B comuns incluem a validação de XML, codificação/decodificação de arquivo simples.  

## <a name="how-to-delete-an-integration-account"></a>Como excluir uma conta de integração?
1. Selecione **Procurar**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta integração** que você deseja excluir:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selecione o link **Excluir** está localizado no menu:   
   ![](./media/logic-apps-enterprise-integration-accounts/delete.png)  
5. Confirme suas opções.    

## <a name="how-to-move-an-integration-account"></a>Como mover uma conta de integração?
Você pode mover facilmente uma conta de integração para uma nova assinatura e um novo grupo de recursos. Se você precisar mover sua conta de integração, execute estas etapas:

> [!IMPORTANT]
> É necessário atualizar todos os scripts a fim de usar as IDs do novo recurso depois de mover uma conta de integração.

1. Selecione **Procurar**:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados:     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta integração** que você deseja excluir:  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Selecione o link **Mover** localizado no menu:   
   ![](./media/logic-apps-enterprise-integration-accounts/move.png)  
5. Confirme suas opções.    

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  




<!--HONumber=Jan17_HO5-->


