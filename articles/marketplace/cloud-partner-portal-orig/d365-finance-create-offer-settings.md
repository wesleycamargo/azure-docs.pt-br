---
title: Como preencher o formulário de Configuração da Oferta | Microsoft Docs
description: Explica os vários campos que exigem valores no formulário de Configurações de Oferta para um novo aplicativo do Dynamics 365 Business Central.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: a050a8cfdd67f662786fc0b9b7ed5451c88dccb1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805312"
---
<a name="how-to-fill-out-the-offer-settings-form"></a>Como preencher o formulário de Configuração da oferta
=======================================

O formulário de configurações da oferta é um formulário básico para especificar as configurações da oferta.
Os campos obrigatórios estão descritos abaixo.

### <a name="offer-id"></a>ID da oferta

`OfferId` é um identificador exclusivo para a oferta em um perfil de editor.
Essa ID estará visível em URLs do produto. Ele só pode ser composto de caracteres alfanuméricos minúsculos ou traços (-). A ID não pode terminar com um traço e pode ter um máximo de 50 caracteres. Esse campo é bloqueado quando uma oferta é ativada.

Se, por exemplo, o parceiro "Contoso" criar uma ID de oferta chamada "sample-Web App", ela será exibida no AppSource como:

&emsp; `https://appsource.microsoft.com/marketplace/apps/contoso.sample-Web App?tab=Overview`


### <a name="publisher-id"></a>ID do fornecedor

Este menu suspenso permite que você escolha o perfil de fornecedor em que deseja publicar essa oferta. Esse campo é bloqueado quando uma oferta é ativada.


### <a name="name"></a>NOME

Este é o nome de exibição para seu aplicativo/oferta, que será exibido no Microsoft [AppSource](https://appsource.microsoft.com/). Ele pode ter um máximo de 50 caracteres.

> [!NOTE]
> O nome curto deve ser o mesmo que o nome do editor especificado no manifesto do aplicativo.

Clique em **Salvar** para salvar o progresso. A próxima etapa seria adicionar informações técnicas à oferta.
