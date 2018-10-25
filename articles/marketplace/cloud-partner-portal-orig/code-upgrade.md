---
title: Atualizando o código para a plataforma mais recente | Microsoft Docs
description: Este tópico explica como atualizar sua versão de plataforma do Microsoft Dynamics 365 for Operations para a versão de plataforma mais recente
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 787d9ef509bcafeb6a21d4b0ec952ea1eb4fd52b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805139"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Atualizando o código para a plataforma mais recente

Este artigo explica como atualizar sua versão de plataforma do Microsoft Dynamics 365 for Operations para a versão de plataforma mais recente.

## <a name="overview"></a>Visão geral

A plataforma do Microsoft Dynamics 365 for Operations é composta pelos componentes a seguir:

Os binários da plataforma do Dynamics 365 for Operations como AOS (Servidor de Objeto de Aplicativo), a estrutura de gerenciamento de dados, a estrutura de relatórios e BI (business intelligence), as ferramenta de desenvolvimento e os serviços de análise. Os pacotes AOT (Árvore de Objeto de Aplicativo) a seguir:

1. Plataforma de aplicativo
2. Base do aplicativo
3. Conceitos básicos de teste

**Importante**: para migrar para a plataforma mais recente do Dynamics 365 for Operations, sua implementação do Dynamics 365 for Operations não pode ter nenhuma personalização (sobreposição de camadas) de qualquer um dos pacotes AOT que pertencem à plataforma. Essa restrição foi introduzida na atualização de plataforma 3 para que as atualizações contínuas pudessem ser feitas para a plataforma. Se você estiver executando em uma plataforma mais antiga do que a atualização de plataforma 3, confira o Atualizando para a atualização de plataforma 3 de uma seção de build mais antigo no final deste artigo.

Para saber mais sobre Atualização de código, confira [aqui](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).