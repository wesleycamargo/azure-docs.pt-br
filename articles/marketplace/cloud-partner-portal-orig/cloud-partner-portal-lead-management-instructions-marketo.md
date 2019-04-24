---
title: Marketo | Microsoft Docs
description: Configure o gerenciamento de cliente potencial para Marketo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 482d7a9662a79503bb2b197d5a6c63c9fa3c1c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398431"
---
# <a name="configure-lead-management-in-marketo"></a>Configurar o gerenciamento de cliente potencial no Marketo

Este artigo descreve como configurar o Marketo para lidar com clientes potenciais de vendas da Microsoft.

1. Entre no Marketo.
2. Selecione **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Selecione **Novo Formulário**.
    ![Novo formulário do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Preencha os campos obrigatórios no Novo formulário e selecione **Criar**.
    ![Criar novo formulário do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Em Detalhes do Campo, selecione **Concluir**.
    ![Formulário de conclusão do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Aprovar e fechar.

6.  Na guia MarketplaceLeadBacked, selecione **	Código de Inserção**.
    ![Opção de código de inserção do Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  O Código de Inserção do Marketo exibe um código semelhante ao exemplo a seguir.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copie os valores mostrados no Código de Inserção para poder configurar a **ID do Servidor**, **ID do Munchkin** e **ID do Formulário** nos campos do Marketo no Cloud Partner Portal.

Use o próximo exemplo como guia para obter as IDs que você precisa no exemplo do Código de Inserção.

- ID do servidor = **ys12**
- ID do Munchkin = **123-PQR-789**
- ID do Formulário = **1179**\
