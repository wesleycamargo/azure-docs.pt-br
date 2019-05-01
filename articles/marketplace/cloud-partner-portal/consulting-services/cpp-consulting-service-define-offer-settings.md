---
title: Definir configurações de oferta para uma oferta de serviço de consultoria | O Azure Marketplace
description: Defina configurações da oferta em um Azure ou Dynamics 365 consultoria oferta de serviço no Portal de parceiros de nuvem para o Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 601ad62bddd1373742b0cab5a388a55cfd52f4bc
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64942784"
---
# <a name="offer-settings-tab"></a>Guia Configurações da Oferta

Na tela **Nova Oferta**, a primeira etapa é criar a identidade da oferta. A identidade da oferta consiste em três partes: **ID da oferta**, **ID do Editor** e **Nome**. Cada um desses blocos é abordado nas seções a seguir.

![Criar uma nova oferta de serviço de consultoria – guia Configurações da oferta](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>ID de oferta *

Esse identificador é um nome exclusivo que você cria quando envia a oferta pela primeira vez. Deve conter apenas caracteres alfanuméricos minúsculos, traços ou sublinhados. A **ID da oferta** será visível na URL e afeta os resultados do mecanismo de pesquisa. Um exemplo é *yourcompanyname_exampleservice*.

Conforme mostrado no exemplo, a **ID da oferta** é anexada à ID do editor para criar um identificador exclusivo. O identificador exclusivo é exposto como um link permanente que pode ser registrado e é indexado pelos mecanismos de pesquisa.

>[!Note]
>Depois que uma oferta estiver ativa, o identificador não poderá ser atualizado.


### <a name="publisher-id"></a>Publicador ID *

Esse identificador está relacionado à sua conta. Depois de se conectar com sua conta organizacional, a **ID do editor** será exibida no menu suspenso.


### <a name="name"></a>Nome*

Essa cadeia de caracteres será exibida como nome da oferta no AppSource ou no Azure Marketplace. O campo **Nome** é limitado a 50 caracteres. O revisor talvez precise editar o título para acrescentar a duração e o tipo de oferta ao nome da oferta.

O exemplo a seguir mostra como o nome da oferta é montado. 

![Criar uma nova oferta de serviços de consultoria](media/cppsampleconsultingoffer.png)

O nome da oferta é composto de quatro partes:

-   **Duração:** definida na guia **Detalhes da vitrine** do editor. A duração pode ser expressa em horas, dias ou semanas.
-   **Tipo de serviço:** definida na guia **Detalhes da vitrine** do editor. Os tipos de serviço são `Assessment`, `Briefing`, `Implementation`, `Proof of concept` e `Workshop`.
-   **Preposição:** inserida pelo revisor.
-   **Nome:** definida na página **Configurações da oferta**.

>[!Note]
>O campo **Nome** é limitado a 50 caracteres. O revisor talvez precise editar o título para acrescentar a duração e o tipo de oferta ao nome da oferta.

A lista a seguir fornece vários nomes de oferta adequados:

-   Conceitos básicos para Serviços profissionais: resumo de uma hora
-   Plataforma de Migração na Nuvem: resumo de uma hora
-   PowerApps e Microsoft Flow: workshop de um dia
-   Serviços do Azure Machine Learning: prova de conceito de três semanas
-   Solução de varejo Brick and Click: resumo de uma hora
-   Traga seus próprios dados: workshop de uma semana
-   Análise de nuvem: workshop de três dias
-   Treinamento em Power BI: workshop de três dias
-   Solução de gerenciamento de vendas: implementação de uma semana
-   Início Rápido para CRM: workshop de um dia
-   Dynamics 365 for Sales: avaliação de dois dias

Depois de preencher a guia **Configurações da oferta**, salve seu envio. Agora, o nome da oferta é exibido acima do editor e você pode encontrá-lo em **Todas as ofertas**.

## <a name="next-steps"></a>Próximas etapas

Agora, você pode inserir [detalhes da vitrine e determinar se deseja publicar no Azure Marketplace ou no AppSource](./cpp-consulting-service-storefront-details.md).
