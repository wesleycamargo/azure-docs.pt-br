---
title: Não consigo entrar para gerenciar minha assinatura do Azure | Microsoft Docs
description: Descreve as informações de solução de problemas para alguns problemas comuns de logon de assinatura do Azure
services: ''
documentationcenter: ''
author: genlin
manager: msmbaldwin
editor: ''
tags: billing

ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/09/2016
ms.author: genli

---
# Não consigo entrar para gerenciar minha assinatura do Azure
Este artigo explica alguns dos métodos mais comuns para resolver problemas de logon.

> [!NOTE]
> Se você precisar de mais ajuda a qualquer momento neste artigo, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
> 
> 

## A página para de responder no status de carregamento
Esse problema pode causar um problema que afeta o seu navegador da Internet.

Para resolver esse problema, tente os seguintes métodos, na ordem fornecida. Depois de executar todos os métodos, tente reconectar-se à página de entrada no portal.

* Atualize a página.
* Use um navegador de Internet diferente.
* Se você estiver usando o Microsoft Internet Explorer, navegue até o portal do Azure usando o modo de Navegação InPrivate. Para fazer isso, siga estas etapas:
  
  R. Clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Segurança** > **Navegação do InPrivate**.
  
  B. Navegue até o [portal do Azure](https://portal.azure.com) e entre no portal.

## Mensagem de erro "Nenhuma assinatura encontrada"
Esse problema poderá ocorrer se a conta não tiver direitos de usuário suficientes. Um administrador da conta pode acessar apenas o [Centro de Contas](https://account.windowsazure.com/), enquanto os SA (administradores de serviço) e CA (coadministradores) têm acesso apenas ao [Portal do Azure](https://portal.azure.com).

**Cenário 1: a mensagem de erro é recebida no [Portal do Azure](https://portal.azure.com)**

Para resolver esse problema, [adicione a função de administrador colegas ou proprietário](billing-add-change-azure-subscription-administrator.md) para a conta.

**Cenário 2: a mensagem de erro é recebida no [Centro de Contas do Azure](https://account.windowsazure.com/Subscriptions)**

Verifique se a conta usada é o administrador da conta. Para verificar quem é o administrador da conta, siga estas etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. No menu Hub, selecione **Assinatura**.
3. Selecione a assinatura que você deseja verificar e, em seguida, selecione **Configurações**.
4. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta**.

## Você está automaticamente conectado como um usuário diferente
Esse problema poderá ocorrer se você estiver usando mais de uma conta de usuário em um navegador da Internet.

Para resolver o problema, tente usar um dos seguintes métodos:

* Saia do portal e entre novamente com a conta que você deseja usar.
* Limpe o cache e exclua cookies da Internet. Para fazer isso no Internet Explorer, clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Opções da Internet** > **Excluir**, certifique-se de que as caixas de seleção para arquivos temporários, cookies, senha e o histórico de navegação estejam selecionadas e, em seguida, clique em Excluir.
* Redefina as configurações do Internet Explorer para reverter todas as configurações pessoais que você tenha feito. Para fazer isso, clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png)> **Opções da Internet** > **Avançado** > selecione a caixa **Excluir configurações pessoais** > **Redefinir**.
* Navegue até o portal do Azure no modo de Navegação InPrivate. Para fazer isso, clique em **Ferramentas** ![botão ferramentas](./media/billing-cannot-login-subscription/Toolsbutton.png) > **Segurança** > **Navegação do InPrivate**.

## Entre usando uma conta organizacional
Sua conta da Microsoft é o endereço de e-mail usado, juntamente com sua senha para entrar em qualquer programa do Windows Live ou serviço, como o Outlook, Hotmail, MSN ou OneDrive. Você pode configurar uma conta da Microsoft usando qualquer endereço de e-mail que pertence a você, incluindo o e-mail da sua empresa. Consulte [www.microsoft.com/account](http://www.microsoft.com/account) para obter mais detalhes.

A página de entrada padrão do Portal do Azure destina-se à conta da Microsoft. Se sua conta estiver associada uma Conta institucional, selecione a opção de logon correta conforme mostrado abaixo. Para obter mais informações sobre como usar uma Conta institucional, consulte [Inscreva-se no Azure como uma instituição](active-directory/sign-up-organization.md):

![página de entrada](./media/billing-cannot-login-subscription/signin.png)

> [!NOTE]
> Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
> 
> 

<!---HONumber=AcomDC_0928_2016-->