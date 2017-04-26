---
title: "Não é possível entrar na assinatura do Azure | Microsoft Docs"
description: Descreve como solucionar alguns problemas comuns de logon na assinatura do Azure.
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 05e23ad6bc07293f53e081b905c3a9ce1c91e6f9
ms.lasthandoff: 04/07/2017


---
# <a name="i-cant-sign-in-to-manage-my-azure-subscription"></a>Não consigo entrar para gerenciar minha assinatura do Azure
Este artigo explica alguns dos métodos mais comuns para resolver problemas de logon.

## <a name="page-hangs-in-the-loading-status"></a>A página para de responder no status de carregamento
Se a página do navegador da Internet parar, experimente cada uma das etapas a seguir até chegar ao [portal do Azure](https://portal.azure.com).

* Atualize a página.
* Use um navegador de Internet diferente.
* Se você estiver usando o Microsoft Internet Explorer, navegue até o portal do Azure usando o modo de Navegação InPrivate. 
  
  R. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Segurança** > **Navegação InPrivate**.
  
  B. Navegue até o [portal do Azure](https://portal.azure.com)e entre no portal.

## <a name="error-message-no-subscriptions-found"></a>Mensagem de erro "Nenhuma assinatura encontrada"
Se sua conta não tiver permissões suficientes, talvez você veja uma mensagem de erro **Nenhuma assinatura encontrada**. Lembre-se de fazer logon como o administrador correto. Um Administrador da Conta só pode acessar o [Centro de Contas](https://account.windowsazure.com/Subscriptions). Os SA (Administradores de Serviço) e CA (Coadministradores) têm acesso apenas ao [Portal do Azure](https://portal.azure.com) ou ao Portal Clássico do Azure.

**Cenário 1: a mensagem de erro é recebida no [portal do Azure](https://portal.azure.com)**

Para corrigir esse problema:

* Verifique se o diretório correto do Azure está selecionado clicando em sua conta no canto superior direito.

![Selecione o diretório na parte superior direita do portal do Azure](./media/billing-cannot-login-subscription/directory-switch.png)

* Se o diretório correto do Azure estiver selecionado, mas você ainda receber o erro [adicione sua conta como um Proprietário](billing-add-change-azure-subscription-administrator.md).

**Cenário 2: a mensagem de erro é recebida no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)**

Verifique se a conta usada é o administrador da conta. Para verificar quem é o administrador da conta, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Assinatura**.
3. Selecione a assinatura que você deseja verificar e, em seguida, selecione **Configurações**.
4. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .

## <a name="you-are-automatically-signed-in-as-a-different-user"></a>Você está automaticamente conectado como um usuário diferente
Esse problema poderá ocorrer se você estiver usando mais de uma conta de usuário em um navegador da Internet.

Para resolver o problema, tente usar um dos seguintes métodos:

* Limpe o cache e exclua cookies da Internet. No Internet Explorer, clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opções da Internet** > **Excluir**. Verifique se as caixas de seleção dos arquivos temporários, dos cookies, das senha e do histórico de navegação estão marcadas e clique em Excluir.
* Redefina as configurações do Internet Explorer para reverter todas as configurações pessoais que você tenha feito. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opções da Internet** > **Avançado** > selecione a caixa **Excluir configurações pessoais** > **Redefinir**.
* Navegue até o portal do Azure no modo de Navegação InPrivate. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Segurança** > **Navegação InPrivate**.

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409) para resolver seu problema rapidamente. 


