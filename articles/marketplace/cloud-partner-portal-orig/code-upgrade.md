---
title: Atualizar o código para a plataforma mais recente | O Azure Marketplace
description: Este tópico explica como atualizar sua versão de plataforma do Microsoft Dynamics 365 for Operations para a versão de plataforma mais recente
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64935277"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Atualizando o código para a plataforma mais recente

Este artigo explica como atualizar sua versão de plataforma do Microsoft Dynamics 365 for Operations para a versão de plataforma mais recente.

## <a name="overview"></a>Visão geral

A plataforma do Microsoft Dynamics 365 for Operations é composta pelos componentes a seguir:

Os binários da plataforma do Dynamics 365 for Operations como AOS (Servidor de Objeto de Aplicativo), a estrutura de gerenciamento de dados, a estrutura de relatórios e BI (business intelligence), as ferramenta de desenvolvimento e os serviços de análise. Os pacotes AOT (Árvore de Objeto de Aplicativo) a seguir:

1. Plataforma de aplicativo
2. Base do aplicativo
3. Conceitos básicos de teste

**Importante**: Para mover para o mais recente do Dynamics 365 para a plataforma de operações, o Dynamics 365 para implementação de operações não pode ter todas as personalizações (sobreposição) de qualquer um dos pacotes AOT que pertencem à plataforma. Essa restrição foi introduzida na atualização de plataforma 3 para que as atualizações contínuas pudessem ser feitas para a plataforma. Se você estiver executando em uma plataforma mais antiga do que a atualização de plataforma 3, confira o Atualizando para a atualização de plataforma 3 de uma seção de build mais antigo no final deste artigo.

Para saber mais sobre Atualização de código, confira [aqui](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).