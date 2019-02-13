---
title: Criar uma oferta de Aplicativo do Power BI – Azure Marketplace | Microsoft Docs
description: Como criar uma oferta de aplicativo do Power BI para o Microsoft AppSource Marketplace.
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
ms.openlocfilehash: e0e1311276b858f1ac16fe6f17ccad49dd9901c9
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665927"
---
# <a name="create-a-power-bi-application-offer"></a>Criar uma oferta de Aplicativo do Power BI

Esta seção lista as etapas necessárias para criar uma nova oferta de Aplicativo do Power BI para o [AppSource](https://appsource.microsoft.com). Cada oferta aparece como sua própria entidade no AppSource.  Quando você cria uma nova oferta no [Portal do Cloud Partner](https://cloudpartner.azure.com/), deve fornecer quatro grupos de ativos para sua oferta.

|   Grupo de ativos      | DESCRIÇÃO                                                                         |
| ----------------   | ----------------                                                                    |
| Configurações da oferta     | Identificações primárias e nome da oferta                                      |
| Informações técnicas     | A URL do instalador usada para instalar o aplicativo no workspace do Power BI do cliente. Para obter mais informações sobre como gerar essa URL, confira a [documentação do Aplicativo do Power BI](https://go.microsoft.com/fwlink/?linkid=2028636).   |
| Detalhes da vitrine | Contém ativos de marketing, jurídicos e de cliente potencial. Os ativos de marketing incluem a descrição e os logotipos da oferta.  Ativos jurídicos incluem uma política de privacidade, termos de uso e outras documentações legais.  A política de gerenciamento de cliente potencial permite que você especifique como manusear os clientes potenciais do portal do usuário final do AppSource. |
| Contatos           | Contém informações de contato e política de suporte                                     |
|    |     |


## <a name="new-offer-form"></a>Novo formulário de oferta

Ao fazer logon no Portal do Cloud Partner, clique no item **+ Nova Oferta** na barra de menus à esquerda.  No menu resultante, clique em **Aplicativos do Power BI** para exibir o formulário **Nova Oferta** e iniciar o processo de definição de ativos para uma nova oferta de aplicativo.

![Item do menu da oferta do Power BI](./media/new-offer-menu.png)

> [!WARNING] 
> Se a opção **Aplicativos do Power BI** não é exibida ou não está habilitada, sua conta não tem permissão para criar esse tipo de oferta. Verifique se você atende a todos os [Pré-requisitos](./cpp-prerequisites.md) para esse tipo de oferta, incluindo o registro para uma conta de desenvolvedor.


## <a name="next-steps"></a>Próximas etapas

Os artigos subsequentes nesta seção espelham as guias na página **Nova Oferta** (para um tipo de oferta do Aplicativo do Power BI). Cada artigo explica como usar a guia associada para definir os grupos de ativos e serviços de suporte para sua nova oferta de aplicativo.

-  [Guia Configurações da Oferta](./cpp-offer-settings-tab.md)
-  [Guia Informações técnicas](./cpp-technical-info-tab.md)
-  [Guia Detalhes da Vitrine](./cpp-storefront-details-tab.md)
-  [Contatos](./cpp-contacts-tab.md)
