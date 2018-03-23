---
title: Criar, vincular, excluir ou mover uma conta de integração no Aplicativo Lógico do Azure | Microsoft Docs
description: Como criar uma conta de integração e vinculá-la aos seus aplicativos lógicos
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: ''
ms.assetid: d3ad9e99-a9ee-477b-81bf-0881e11e632f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: LADocs; divswa
ms.openlocfilehash: fb1d0ceb26c5ed792f22051e2af10a7572200bdc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2018
---
# <a name="what-is-an-integration-account"></a>O que é uma conta de integração?

Uma conta de integração fornece uma maneira para seus aplicativos de integração empresarial, especificamente aplicativos lógicos, acessar e gerenciar artefatos B2B, por exemplo, parceiros comerciais, contratos, mapas, esquemas, certificados, e assim por diante. Para fornecer esse acesso, vincule a conta de integração ao aplicativo lógico, após certificar-se de que a conta de integração e o aplicativo lógico têm um *mesmo local do Azure*.

## <a name="create-an-integration-account"></a>Criar uma conta de integração

1. Entre no [portal do Azure](http://portal.azure.com "portal do Azure"). 

2. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira "integração" e selecione **Contas de integração**.

   ![Criar conta de integração](./media/logic-apps-enterprise-integration-accounts/account-1.png)

3. Na parte superior da página, escolha **Adicionar**.

   ![Escolha Adicionar](./media/logic-apps-enterprise-integration-accounts/account-3.png)

4. Nomeie sua conta de integração e selecione a assinatura do Azure que você deseja usar. Você pode criar um novo **grupo de recursos** ou escolher um grupo de recursos existente. Selecione um **Local** para hospedar a conta de integração e um **Tipo de Preço**. Quando você estiver pronto, escolha **Criar**.

   ![Forneça detalhes para sua conta de integração](./media/logic-apps-enterprise-integration-accounts/account-4.png)

   O Azure fornece a conta de integração no local selecionado, que deve ser concluído dentro de um minuto.

5. Atualize a página. Você verá a nova conta de integração listada.

   ![A nova conta de integração é exibida](./media/logic-apps-enterprise-integration-accounts/account-5.png) 

Em seguida, vincule a conta de integração criada ao seu aplicativo lógico. 

## <a name="link-an-integration-account-to-a-logic-app"></a>Vincular uma conta de integração a um aplicativo lógico

Para que os aplicativos lógicos acessem artefatos B2B, como parceiros comerciais, contratos, mapas e esquemas em sua conta de integração, vincule a conta de integração ao aplicativo lógico. 

1. No portal do Azure, selecione seu aplicativo lógico e verifique o local dele.

   ![Selecionar seu aplicativo lógico, verificar o local](./media/logic-apps-enterprise-integration-accounts/linkaccount-1.png)

2. Em **Configurações**, selecione **Conta de Integração**.

   ![Selecionar “Conta de Integração”](./media/logic-apps-enterprise-integration-accounts/linkaccount-2.png)

3. Na lista **Selecione a conta de integração**, selecione a conta de integração que você deseja vincular ao aplicativo lógico. Para concluir a vinculação, escolha **Salvar**.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-accounts/linkaccount-3.png)

   Você verá uma notificação mostrando que sua conta de integração foi vinculada ao seu aplicativo lógico, e que todos os artefatos de sua conta de integração estão disponíveis para seu aplicativo lógico.

   ![Seu aplicativo lógico é vinculado à sua conta de integração](./media/logic-apps-enterprise-integration-accounts/linkaccount-5.png)

Agora que a conta de integração está vinculada ao aplicativo lógico, você poderá usar os conectores B2B no aplicativo lógico. Alguns conectores B2B comuns incluem a validação de XML e codificação/decodificação de arquivo simples.  

## <a name="delete-your-integration-account"></a>Excluir sua conta de integração

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira "integração" e selecione **Contas de integração**.

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Selecione a conta de integração que você deseja excluir.

    ![Selecionar conta de integração a ser excluída](./media/logic-apps-enterprise-integration-accounts/account-5.png)

3. No menu, escolha **Excluir**.

    ![Clique em “Excluir”](./media/logic-apps-enterprise-integration-accounts/delete.png)

4. Confirme sua escolha para excluir a conta de integração.

## <a name="move-your-integration-account"></a>Selecionar sua conta de integração

Para mover uma conta de integração para outro grupo de recursos ou de assinatura do Azure, siga estas etapas. Após mover a conta de integração, certifique-se de atualizar todos os scripts para usar as novas IDs do recurso.

1. No menu principal do Azure, selecione **Todos os serviços**. Na caixa de pesquisa, insira "integração" e selecione **Contas de integração**.

   ![Localizar sua conta de integração](./media/logic-apps-enterprise-integration-accounts/account-1.png)

2. Selecione a conta de integração que você deseja mover. Em **Configurações**, escolha **Propriedades**.

   ![Selecione a conta de integração a ser movida. Em Configurações, escolha Propriedades](./media/logic-apps-enterprise-integration-accounts/move.png)

3. Altere o grupo de recursos ou a assinatura do Azure associada à sua conta de integração.

   ![Escolher Alterar grupo de recursos ou Alterar assinatura](./media/logic-apps-enterprise-integration-accounts/move-2.png)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Saiba mais sobre os contratos de integração corporativa")  

