---
title: Saiba mais sobre parceiros e o Enterprise Integration Pack | Microsoft Docs
description: "Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: 549dd716df6ee64892ef33ffe4d42751d8662ccb


---
# <a name="learn-about-partners-and-enterprise-integration-pack"></a>Saiba mais sobre parceiros e o Enterprise Integration Pack
## <a name="overview"></a>Visão geral
Antes de criar um parceiro, você e a organização com a qual você pretende fazer negócios devem compartilhar informações que ajudarão vocês a identificar e a validar as mensagens trocadas. Após essas discussões, e quando você estiver pronto para começar sua relação corporativa, crie um *parceiro* em sua conta de integração.

## <a name="what-is-a-partner"></a>O que é um parceiro?
Os parceiros são as entidades que participam de mensagens e transações B2B (entre empresas). 

## <a name="how-are-partners-used"></a>Como os parceiros são usados?
Os parceiros são usados para criar contratos. Um contrato define os detalhes sobre as mensagens que serão trocadas entre os parceiros. 

Antes de criar um contrato, você precisa adicionar pelo menos dois parceiros à sua conta de integração. Um dos parceiros de um contrato deve ser a sua organização. O parceiro que representa a sua organização é conhecido como o **parceiro host**. O segundo parceiro representaria a outra organização com a qual sua organização troca mensagens. O segundo parceiro é conhecido como o **parceiro convidado**. O parceiro convidado pode ser outra empresa, ou até mesmo um departamento dentro da sua organização.  

Depois de adicionar os parceiros, você usa esses parceiros para criar um contrato. 

As configurações de Recebimento e Envio são orientadas do ponto de vista do Parceiro host. Por exemplo, as configurações de recebimento em um contrato determinam como o parceiro host recebe as mensagens enviadas de um parceiro convidado. Da mesma forma, as configurações de envio no contrato indicam como o parceiro host envia mensagens ao parceiro convidado.

## <a name="how-to-create-a-partner"></a>Como criar um parceiro?
No Portal do Azure:  

1. Selecione **Procurar**  
   ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)    
2. Digite **integração** na caixa de pesquisa do filtro e selecione **Contas de Integração** na lista de resultados     
   ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)  
3. Selecione a **conta integração** à qual você adicionará os parceiros  
   ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)  
4. Escolha o bloco **Parceiros**  
   ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)  
5. Selecione o botão **Adicionar** na folha Parceiros exibida  
   ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)  
6. Insira um **Nome** para seu parceiro e escolha o **Qualificador **, finalmente, insira um **Valor**. O valor é usado para ajudar a identificar documentos que entram em seus aplicativos.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)  
7. Escolha o ícone de notificação de *sino* para ver o progresso do processo de criação de parceiro.  
   ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)  
8. Escolha o bloco **Parceiros** . Isso atualiza o bloco e você poderá ver o número de parceiros aumentar, refletindo que o novo parceiro foi adicionado com êxito.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)  
9. Depois de selecionar o bloco Parceiros, você também verá o parceiro recém-adicionado exibido na folha Parceiros.    
   ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)  

## <a name="how-to-edit-a-partner"></a>Como editar um parceiro
Execute estas etapas para editar um parceiro que já existe em sua conta de integração:  

1. Escolha o bloco **Parceiros**  
2. Selecione o parceiro que você deseja editar quando a folha Parceiros for aberta  
3. No bloco **Atualizar Parceiro** , faça as alterações necessárias  
4. Se você estiver satisfeito com suas alterações, selecione o link **Salvar**, caso contrário, selecione o link **Descartar** para descartar suas alterações.  
   ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)  

## <a name="how-to-delete-a-partner"></a>Como excluir um parceiro
1. Escolha o bloco **Parceiros**  
2. Selecione o parceiro que você deseja editar quando a folha Parceiros for aberta  
3. Selecione o link **Excluir**    
   ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)   

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  




<!--HONumber=Jan17_HO3-->


