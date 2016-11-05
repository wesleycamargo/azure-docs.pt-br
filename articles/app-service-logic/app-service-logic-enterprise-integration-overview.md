---
title: Visão geral do Enterprise Integration | Microsoft Docs
description: Usar os recursos do Enterprise Integration para permitir cenários de processos e integração corporativa usando Aplicativos lógicos
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: cgronlun

ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: deonhe

---
# Visão geral do Enterprise Integration Pack
## O que é o Enterprise Integration Pack?
O Enterprise Integration Pack é a solução baseada em nuvem da Microsoft que permite uma comunicação perfeita entre empresas (B2B). O pacote usa protocolos padrão da indústria, incluindo [AS2](app-service-logic-enterprise-integration-as2.md), [X12](app-service-logic-enterprise-integration-x12.md) e [EDIFACT](app-service-logic-enterprise-integration-edifact.md) para trocar mensagens entre parceiros comerciais. Como opção, as mensagens podem ser protegidas usando criptografia e assinaturas digitais.

O pacote permite que as organizações que usam protocolos e formatos diferentes troquem mensagens eletronicamente, transformando os diferentes formatos em um formato que os sistemas de ambas as organizações possam interpretar e, assim, executar ações.

Se você estiver familiarizado com o BizTalk Server ou os Serviços BizTalk do Microsoft Azure, achará fácil usar os recursos do Enterprise Integration, pois a maioria dos conceitos é semelhante. A principal diferença é que o Enterprise Integration usa contas de integração para simplificar o armazenamento e o gerenciamento de artefatos usados em comunicações B2B.

Em termos de arquitetura, o Enterprise Integration Pack é baseado em **contas de integração** que armazenam todos os artefatos que podem ser usados para criar, implantar e manter seus aplicativos B2B. Uma conta de integração é basicamente um contêiner baseado em nuvem onde você armazena artefatos como esquemas, parceiros, certificados, mapas e contratos. Esses artefatos podem ser usados em Aplicativos lógicos para criar fluxos de trabalho B2B. Para poder usar os artefatos em um Aplicativo lógico, é preciso vincular sua conta de integração ao seu Aplicativo lógico. Depois de vinculá-los, seu Aplicativo lógico terá acesso aos artefatos da conta de integração.

## Por que você deve usar a integração corporativa?
* Com a integração corporativa, você é capaz de armazenar todos os artefatos em um único local, que é a sua conta de integração.
* Você pode aproveitar o mecanismo dos Aplicativos lógicos e todos os seus conectores para criar fluxos de trabalho B2B e realizar a integração com aplicativos SaaS de terceiros, aplicativos locais, bem como aplicativos personalizados
* Você também pode aproveitar as funções do Azure

## Como começar a usar a integração corporativa?
Você pode criar e gerenciar aplicativos B2B usando o Enterprise Integration Pack por meio do designer de Aplicativos lógicos no **Portal do Azure**.

Você também pode usar o [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Tópicos do PowerShell para aplicativos lógicos") para gerenciar seus Aplicativos lógicos.

Confira uma visão geral das etapas que necessárias para criar aplicativos no Portal do Azure: ![imagem da visão geral](./media/app-service-logic-enterprise-integration-overview/overview-0.png)

## Quais são os cenários comuns?
O Enterprise Integration oferece suporte a estes padrões do setor:

* EDI - Intercâmbio Eletrônico de Dados
* EAI - Integração de Aplicativos Empresariais

## Para começar, você precisa do seguinte
* Uma assinatura do Azure com uma conta de integração
* Visual Studio 2015 para criar esquemas e mapas
* [Microsoft Azure Logic Apps Enterprise Integration Tools for Visual Studio 2015 2.0 (Ferramentas de integração corporativa do Aplicativo Lógico do Microsoft Azure para Visual Studio 2015)](https://aka.ms/vsmapsandschemas)

## Experimente
[Teste-o agora mesmo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) para implantar um exemplo de aplicativo lógico de envio e recebimento AS2 totalmente operacional que usa os recursos B2B dos Aplicativos Lógicos.

## Saiba mais sobre:
* [Contratos](app-service-logic-enterprise-integration-agreements.md "Saiba mais sobre contratos de integração corporativa")
* [Cenários B2B (Entre empresas)](app-service-logic-enterprise-integration-b2b.md "Aprenda a criar Aplicativos lógicos com recursos de B2B")
* [Certificados](app-service-logic-enterprise-integration-certificates.md "Saiba mais sobre certificados da integração corporativa")
* [Codificação/decodificação de arquivo simples](app-service-logic-enterprise-integration-flatfile.md "Aprenda a codificar e decodificar o conteúdo de arquivo simples")
* [Contas de integração](app-service-logic-enterprise-integration-accounts.md "Saiba mais sobre contas de integração")
* [Mapas](app-service-logic-enterprise-integration-maps.md "Saiba mais sobre mapas da integração corporativa")
* [Parceiros](app-service-logic-enterprise-integration-partners.md "Saiba mais sobre parceiros da integração corporativa")
* [Esquemas](app-service-logic-enterprise-integration-schemas.md "Saiba mais sobre esquemas de integração corporativa")
* [Validação de mensagem XML](app-service-logic-enterprise-integration-xml.md "Aprenda a validar mensagens XML com Aplicativos lógicos")
* [Transformação de XML](app-service-logic-enterprise-integration-transform.md "Saiba mais sobre mapas da integração corporativa")
* [Conectores de integração Enterprise](../connectors/apis-list.md "Saiba mais sobre os conectores do enterprise integration pack")

<!---HONumber=AcomDC_0921_2016-->