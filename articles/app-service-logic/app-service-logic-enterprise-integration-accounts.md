---
title: "Visão geral das contas de integração e do Enterprise Integration Pack | Microsoft Docs"
description: "Saiba tudo sobre contas de integração, o Enterprise Integration Pack e Aplicativos lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: dec91134-8c21-4d86-92f2-da6c131d2ccf
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6f1a3f38bacd8358a1b57591500c3f37da2ea84


---
# <a name="overview-of-integration-accounts"></a>Visão geral sobre contas de integração
## <a name="what-is-an-integration-account"></a>O que é uma conta de integração?
Uma conta de integração é uma conta do Azure que permite aos aplicativos do Enterprise Integration gerenciar artefatos, incluindo esquemas, mapas, certificados, parceiros e contratos. Qualquer aplicativo de integração que você criar precisará usar uma conta de integração para acessar um esquema, mapa ou certificado, por exemplo.

## <a name="create-an-integration-account"></a>Criar uma conta de integração
1. Selecione **Procurar**   
   ![](./media/app-service-logic-enterprise-integration-accounts/account-1.png)  
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados     
   ![](./media/app-service-logic-enterprise-integration-accounts/account-2.png)  
3. Selecione o botão *Adicionar* no menu na parte superior da página      
   ![](./media/app-service-logic-enterprise-integration-accounts/account-3.png)  
4. Insira o **Nome**, selecione a **Assinatura** que você deseja usar, crie um novo **Grupo de recursos** ou selecione um grupo de recursos existente, selecione um **Local** onde sua conta de integração será hospedada, selecione um **Tipo de preços**, em seguida, selecione o botão **Criar**.   
   
   Neste ponto, a conta de integração será provisionada no local selecionado. Isso deve ser concluído dentro de 1 minuto.    
   ![](./media/app-service-logic-enterprise-integration-accounts/account-4.png)  
5. Atualize a página. Você verá a nova conta de integração listada. Parabéns!  
   ![](./media/app-service-logic-enterprise-integration-accounts/account-5.png) 

## <a name="how-to-link-an-integration-account-to-a-logic-app"></a>Como vincular uma conta de integração a um Aplicativo lógico
Para que seus Aplicativos lógicos acessem mapas, esquemas, contratos e outros artefatos localizados em sua conta de integração, primeiro você deve vincular a conta de integração ao seu Aplicativo lógico.

### <a name="here-are-the-steps-to-link-an-integration-account-to-a-logic-app"></a>Estas são as etapas para vincular uma conta de integração a um Aplicativo lógico
#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta de integração
* Um Aplicativo lógico

> [!NOTE]
> Certifique-se de que sua conta de integração e o Aplicativo lógico estejam no **mesmo local do Azure** antes de começar
> 
> 

1. Selecione o link **Configurações** no menu do seu Aplicativo lógico  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-1.png)   
2. Selecione o item **Conta de Integração** na folha Configurações  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-2.png)   
3. Selecione a conta de integração que você deseja vincular ao seu Aplicativo lógico na caixa de lista suspensa **Selecionar uma Conta de integração**  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-3.png)   
4. Salve seu trabalho   
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-4.png)   
5. Você verá uma notificação indicando que sua conta de integração foi vinculada ao seu Aplicativo lógico, e que todos os artefatos de sua conta de integração estão disponíveis para seu Aplicativo lógico.  
   ![](./media/app-service-logic-enterprise-integration-accounts/linkaccount-5.png)   

Agora que sua conta de integração está vinculada ao seu Aplicativo lógico, você pode acessar seu Aplicativo lógico e usar conectores B2B, como a Validação de XML, codificação/decodificação de arquivo simples ou Transformação para criar aplicativos com recursos de B2B.  

## <a name="how-to-delete-an-integration-account"></a>Como excluir uma conta de integração?
1. Selecione **Procurar**  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta integração** que você deseja excluir  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Selecione o link **Excluir** está localizado no menu   
   ![](./media/app-service-logic-enterprise-integration-accounts/delete.png)  
5. Confirme suas opções    

## <a name="how-to-move-an-integration-account"></a>Como mover uma conta de integração?
Você pode mover facilmente uma conta de integração para uma nova assinatura e um novo grupo de recursos. Se você precisar mover sua conta de integração, execute estas etapas:

> [!IMPORTANT]
> Será necessário atualizar todos os scripts a fim de usar as IDs do novo recurso depois de mover uma conta de integração.
> 
> 

1. Selecione **Procurar**  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados     
   ![](./media/app-service-logic-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta integração** que você deseja excluir  
   ![](./media/app-service-logic-enterprise-integration-overview/overview-3.png)  
4. Selecione o link **Mover** localizado no menu   
   ![](./media/app-service-logic-enterprise-integration-accounts/move.png)  
5. Confirme suas opções    

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o Enterprise Integration Pack")  
* [Saiba mais sobre os contratos](app-service-logic-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  




<!--HONumber=Nov16_HO3-->


