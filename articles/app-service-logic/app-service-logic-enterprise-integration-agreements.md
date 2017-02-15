---
title: "Visão geral dos parceiros e o Enterprise Integration Pack | Microsoft Docs"
description: "Saiba como usar parceiros com o Enterprise Integration Pack e aplicativos Lógicos"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun
ms.assetid: 447ffb8e-3e91-4403-872b-2f496495899d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 40e90cf5430cf070332ddff6083c6dbf16728e75


---
# <a name="learn-about-agreements-and-enterprise-integration-pack"></a>Saiba mais sobre contratos e o Enterprise Integration Pack
## <a name="overview"></a>Visão geral
Os contratos são a base das comunicações de B2B (business-to-business), permitindo que as entidades de negócios se comuniquem diretamente usando protocolos padrão da indústria.  

## <a name="what-is-an-agreement"></a>O que é um contrato?
Um contrato, no que diz respeito ao Enterprise Integration Pack, é um acordo de comunicação entre parceiros comerciais B2B. Um contrato se baseia na comunicação que os dois parceiros desejam atingir e é específico com relação a transporte ou protocolo.

A integração corporativa dá suporte a três padrões de protocolo/transporte:  

* [AS2](app-service-logic-enterprise-integration-as2.md)
* [X12](app-service-logic-enterprise-integration-x12.md)
* [EDIFACT](app-service-logic-enterprise-integration-edifact.md)

## <a name="why-use-agreements"></a>Por que usar contratos
Alguns dos benefícios comuns do uso de contratos são:

* Permite que diferentes organizações e empresas troquem informações em um formato conhecido.  
* Melhora a eficiência ao conduzir transações B2B  
* Fácil de criar, gerenciar e usar durante a criação de aplicativos de integração corporativa  

## <a name="how-to-create-agreements"></a>Como criar contratos
* [Criar um contrato AS2](app-service-logic-enterprise-integration-as2.md)   
* [Criar um contrato X12](app-service-logic-enterprise-integration-x12.md)   

## <a name="how-to-use-an-agreement"></a>Como usar um contrato
Depois de criar um contrato, você pode usá-lo por meio do Portal do Azure para criar [Aplicativos lógicos](app-service-logic-what-are-logic-apps.md "Saiba mais Aplicativos lógicos") com recursos de B2B.

## <a name="how-to-edit-an-agreement"></a>Como editar um contrato
Você pode editar qualquer contrato seguindo essas etapas:  

1. Selecione a conta de Integração que contém o contrato que você deseja modificar.  
2. Selecione o bloco **Contratos**  
3. Selecione o contrato que você deseja modificar na folha **Contratos**  
4. Selecione **Editar** no menu acima   
5. No menu Editar que é aberto, faça suas alterações e selecione o botão **OK** para salvá-las  

## <a name="how-to-delete-an-agreement"></a>Como excluir um contrato
Você pode excluir qualquer contrato seguindo estas etapas de dentro da conta de integração que contém o contrato que você deseja excluir:   

1. Selecione o bloco **Contratos**  
2. Selecione o contrato que você deseja excluir na folha **Contratos**  
3. Selecione **Excluir** no menu acima  
4. Confirme que você realmente deseja excluir o contrato  
5. Observe que o contrato não está mais listado na folha Contratos  

## <a name="next-steps"></a>Próximas etapas
* [Criar um contrato AS2](app-service-logic-enterprise-integration-as2.md)  




<!--HONumber=Nov16_HO3-->


