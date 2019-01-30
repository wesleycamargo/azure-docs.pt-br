---
title: Preparação da solução do Dynamics 365 | Microsoft Docs
description: Estrutura para empacotamento, a instalação e desinstalação de componentes
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricarod.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ea62a826b9f99e264cb1b2242f8d8108453d8e7
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54448754"
---
# <a name="dynamics-365-solution-preparation"></a>Preparação da solução do Dynamics 365

O sistema de solução do Dynamics 365 é uma estrutura para empacotar, instalar e desinstalar componentes que oferecem funcionalidade empresarial específica. As soluções são usadas por ISVs e outros parceiros do Microsoft Dynamics 365 para distribuir as extensões que elas criam.

Se você é um ISV existente do Dynamics 365 (xRM), você muito provavelmente já criou uma solução gerenciada e tem um arquivo .zip da solução. Em sua solução, certifique-se de que os campos "Nome de exibição" e "Descrição" refletem o que você deseja que os clientes vejam. Eles são exibidos no Centro de Administração Online do CRM.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Observação:** no pacote de exemplo a seguir, suporemos que o nome da solução é "SampleSolution.zip"_

Se você for um novo ISV, será possível obter mais detalhes sobre como criar uma solução aqui: [https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Se a solução exigir dados de suporte:

* Crie os dados de exemplo no seu ambiente de teste
* Use a Ferramenta de Migração de Configuração para criar um esquema com regras de comparação para seus dados.
* Salve o esquema de configuração com seus arquivos de projeto. Você precisará dele futuramente se atualizar seus dados de configuração.
* Exporte os dados de configuração. Dê um nome que seja significativo para você ao arquivo de exportação.
