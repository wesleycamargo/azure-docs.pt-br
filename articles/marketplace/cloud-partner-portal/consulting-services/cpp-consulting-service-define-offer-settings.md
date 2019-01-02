---
title: Oferta de serviço de consultoria do Azure e do Dynamics 365 – definir configurações da oferta | Microsoft Docs
description: Guia para definir as configurações da oferta em uma oferta de serviço de consultoria do Dynamics 365 ou do Azure no Portal do Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 6ce51dc9bbcdc7b46593a4800c4fce07a42f3171
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344799"
---
# <a name="offer-settings-tab"></a>Guia Configurações da Oferta

Na tela **Nova Oferta**, a primeira etapa é criar a identidade da oferta. A identidade da oferta consiste em três partes: **ID da oferta**, **ID do Editor** e **Nome**. Cada um desses blocos é abordado nas seções a seguir.

![Criando uma oferta de serviço de consultoria – guia de configurações da oferta](media/consultingoffer-settings-tab.png)

*ID da oferta*

Esse identificador é um nome exclusivo que você cria quando envia a oferta pela primeira vez. Deve conter apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. A **ID da oferta** será visível na URL e afeta os resultados do mecanismo de pesquisa. Por exemplo, *yourcompanyname_exampleservice*

Conforme mostrado no exemplo, a **ID da oferta** é anexada à ID do editor para criar um identificador exclusivo. Ele é exposto como um link permanente que pode ser registrado e é indexado pelos mecanismos de pesquisa.

*Depois que uma oferta estiver ativa, o identificador não poderá ser atualizado*

*ID do Editor*

Esse identificador está relacionado à sua conta. Quando você estiver conectado com sua conta organizacional, a ID do **Editor** será exibida no menu suspenso.

*Nome*

É essa cadeia de caracteres que será exibida como nome da oferta no AppSource ou no Azure Marketplace. O campo *nome* é limitado a 50 caracteres.  O revisor pode precisar editar o título para permitir o acréscimo do nome da oferta com a duração e o tipo de oferta.

>[!Note]
>Importante: Insira aqui apenas o nome do serviço real. Não inclua a duração e o tipo de serviço.

O exemplo a seguir da Edgewater Fullscope mostra como o nome da oferta é montado. O nome da oferta aparece como:

![Criação de uma nova oferta de serviços de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** - definida na guia **Detalhes da vitrine** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** - definido na guia **Detalhes da vitrine** do editor. Os tipos de serviço são `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposição:** - inserida pelo revisor
-   **Nome:** - definida na página **Configurações da oferta**.

>[!Note]
>O campo nome é limitado a 50 caracteres. O nome que você enviar talvez precise ser editado pelo revisor para permitir que a duração e o tipo de oferta sejam acrescentados ao nome.

A lista a seguir fornece vários nomes de oferta adequados:

-   Conceitos básicos para Serviços Profissionais: Resumo de 1 hora
-   Plataforma de Migração na Nuvem: Resumo de 1 hora
-   PowerApps e Microsoft Flow: Workshop de um dia
-   Serviços do Azure Machine Learning: Prova de conceito de 3 semanas
-   Solução de varejo Brick and Click: Resumo de 1 hora
-   Traga seus próprios Dados: Workshop de uma semana
-   Análise de Nuvem: Workshop de três dias
-   Treinamento em Power BI: Workshop de três dias
-   Solução de Gerenciamento de Alertas: Implementação de 1 semana
-   Início rápido do CRM: Workshop de um dia
-   Dynamics 365 for Sales: Avaliação de dois dias

Depois de concluir a guia **Configurações da oferta**, é possível salvar seu envio. Agora, o nome da oferta será exibido acima do editor e você poderá encontrá-lo em Todas as ofertas.

**Próximas etapas**

Agora você pode inserir [Detalhes da vitrine e determinar se deseja publicar no Azure Marketplace ou no AppSource](./cpp-consulting-service-storefront-details.md).