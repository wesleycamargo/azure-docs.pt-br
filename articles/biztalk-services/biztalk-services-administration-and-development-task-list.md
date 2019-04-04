---
title: Lista de Tarefas de Desenvolvimento e Administração nos Serviços BizTalk | Microsoft Docs
description: Planejamento e trabalho de auxiliam na implantação dos Serviços BizTalk do Azure.
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916233"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Lista de Tarefas de Desenvolvimento e Administração nos Serviços BizTalk   

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Introdução
Ao trabalhar com Serviços BizTalk do Microsoft Azure, há vários componentes locais e baseados em nuvem a serem considerados. Para começar, considere o seguinte fluxo de processo:  

| Etapa | Quem é responsável | Tarefa | Links relacionados |
| --- | --- | --- | --- |
| 1. |Administrador |Criar a Assinatura do Microsoft Azure usando uma conta da Microsoft ou uma conta institucional |[Portal do Azure](https://portal.azure.com) |
| 2. |Administrador |Criar ou provisionar um Serviço BizTalk. |[Criar um Serviço do BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Administrador |Registrar você ou a implantação dos Serviços BizTalk da sua empresa |[Registrando e atualizando uma implantação do serviço BizTalk no Portal de serviços do BizTalk](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Administrador |Aplica-se se o aplicativo usa o Serviço do Adaptador do BizTalk para conectar um sistema LOB (Linha de Negócios) local ou usa uma Fila ou Destino de Tópico.  Criar o Namespace do Barramento de Serviço do Azure Atribua valores a esse namespace, ao Nome do Emissor do Barramento de Serviço e à Chave do Emissor do Barramento de Serviço para o desenvolvedor. |[Como: Criar ou modificar um Namespace de serviço de barramento de serviço](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) e [valores de obter o nome do emissor e chave do emissor](biztalk-issuer-name-issuer-key.md) |
| 5. |Desenvolvedores |Instalar o SDK e criar o projeto do Serviço BizTalk no Visual Studio. |[Instalar o SDK dos Serviços BizTalk do Azure](/previous-versions/azure/hh689760(v=azure.100)) e [Criar pontos de extremidade de mensagens avançadas no Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Desenvolvedores |Implante o projeto de Serviço BizTalk  no seu Serviço BizTalk hospedado no Azure. |[Implantando e atualizando o projeto de serviços do BizTalk](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Administrador |Aplica-se se estiver usando o Eclipse:  Você pode adicionar Parceiros e criar Acordos no Portal de Serviços BizTalk do Microsoft Azure. Quando você cria um Contrato, pode adicionar a ponte e/ou Transformações criadas pelo desenvolvedor às Configurações do Contrato. |[Configurando EDI, AS2 e EDIFACT no Portal de serviços do BizTalk](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Administrador |Usando [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), monitore a integridade do seu Serviço BizTalk, incluindo as métricas de desempenho. |[Serviços BizTalk: Guias painel, Monitor e escala](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Administrador |Usando o Portal dos Serviços BizTalk do Microsoft Azure, gerencie os artefatos usados pelos Serviços BizTalk e acompanhe as mensagens enquanto são processadas pelos arquivos da ponte. |[Usando o Portal dos Serviços BizTalk](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Administrador |Crie um plano de backup para fazer backup do Serviço BizTalk. |[Continuidade dos negócios e recuperação de desastres nos Serviços BizTalk](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Próximas etapas
[Tutoriais e Amostras](/previous-versions/azure/hh689895(v=azure.100))

[Criar um projeto no Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Instalar o SDK dos Serviços BizTalk do Azure](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Conceitos
[Criar um projeto no Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 e EDIFACT mensagens (entre empresas)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Outros recursos
[Adicionar origem, destino e ponte de pontos de extremidade de mensagens](/previous-versions/azure/hh689877(v=azure.100))  
[Aprender e criar mapas de mensagem e transformações](/previous-versions/azure/hh689905(v=azure.100))  
[Usando o serviço de adaptador do BizTalk (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Serviços BizTalk do Azure](https://go.microsoft.com/fwlink/p/?LinkID=303664)

