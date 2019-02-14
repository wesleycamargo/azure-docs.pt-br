---
title: Configurações de oferta de um Aplicativo do Power BI - Azure Marketplace | Microsoft Docs
description: Defina as configurações de uma oferta de Aplicativo do Power BI para o Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
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
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665807"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Guia Configurações de Oferta de Aplicativos do Power BI

A página **Nova oferta** para aplicativos de serviço é aberta na primeira guia chamada **Configurações de Oferta**.  Você fornecerá os identificadores primários e um nome para a oferta nessa guia.  Um asterisco anexado (*) no nome do campo indica que isso é necessário.

![Guia Configurações da Oferta](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Campos de configurações da oferta 

Na guia **Configurações da oferta**, você deve fornecer os seguintes campos obrigatórios.

|  Campo        |  DESCRIÇÃO                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID da oferta**  | Um identificador exclusivo (dentro de um perfil de editor) para a oferta. Esse identificador ficará visível em URLs de produtos, modelos do Resource Manager e relatórios de faturamento. Ele tem um comprimento máximo de 50 caracteres, só pode ser composto de caracteres alfanuméricos minúsculos e traços (-), mas não pode terminar em um traço. Este campo não pode ser alterado depois que uma oferta entra no ar. Por exemplo, se a Contoso publica uma oferta com a ID de oferta `sample-SvcApp`, ela é atribuída à URL do AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publicador** | O identificador exclusivo de sua organização no [AppSource](https://appsource.microsoft.com). Todas as suas ofertas devem ser associadas à sua ID do publicador. Este valor não pode ser modificado quando a oferta é salva.                         |
| **Nome**      | Nome de exibição da sua oferta. Esse nome será exibido no AppSource e no Portal do Cloud Partner. Ele pode ter um máximo de 50 caracteres. A orientação aqui é incluir um nome de marca reconhecível para o seu produto. Não inclua o nome de sua organização aqui, a menos que o aplicativo esteja sendo comercializado assim. Se você estiver promovendo essa oferta em outros sites e publicações, verifique se o nome é o mesmo em todas as publicações.    <br/>Se você lançar uma oferta durante o período de versão prévia dos Aplicativos do Power BI, modo de versão prévia, acrescente a cadeia de caracteres `(Preview)` ao nome do aplicativo, por exemplo, `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Próximas etapas

Na próxima guia, você especificará [Informações técnicas](./cpp-technical-info-tab.md) da oferta.
