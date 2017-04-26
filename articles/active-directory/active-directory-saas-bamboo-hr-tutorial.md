---
title: "Tutorial: Integração do Azure Active Directory ao BambooHR | Microsoft Docs"
description: "Saiba como usar o BambooHR com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e190cfdf6d7a9dee86ed7b9072dc51ca8c993d32
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bamboo-hr"></a>Tutorial: Integração do Active Directory do Azure ao Bamboo HR
O objetivo deste tutorial é mostrar a integração do Azure ao BambooHR.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para SSO (logon único) do BambooHR

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao BambooHR poderão fazer logon único no aplicativo em seu site de empresa do BambooHR (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

* Habilitando a integração de aplicativos para o BambooHR
* Configuração do SSO (logon único)
* Configurando o provisionamento de usuários
* Atribuindo usuários

![Cenário](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")

## <a name="enable-the-application-integration-for-bamboohr"></a>Habilitar a integração de aplicativos para o BambooHR
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o BambooHR.

**Para habilitar a integração de aplicativos para o BambooHR, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bamboo-hr-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-bamboo-hr-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-bamboo-hr-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-bamboo-hr-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **BambooHR**.
   
   ![Galeria de aplicativos](./media/active-directory-saas-bamboo-hr-tutorial/IC796686.png "Galeria de aplicativos")
7. No painel de resultados, selecione **BambooHR** e clique em **Concluir** para adicionar o aplicativo.
   
   ![BambooHR](./media/active-directory-saas-bamboo-hr-tutorial/IC796687.png "BambooHR")
   
## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no BambooHR com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.  

Como parte deste procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração do aplicativo **BambooHR**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Cenário](./media/active-directory-saas-bamboo-hr-tutorial/IC796685.png "Cenário")
2. Na página **Como você deseja que os usuários façam logon no BambooHR**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar logon único](./media/active-directory-saas-bamboo-hr-tutorial/IC796688.png "Configurar logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do BambooHR**, digite a URL usada pelos usuários para fazer logon em seu aplicativo BambooHR (por exemplo: https://company.bamboohr.com) e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-bamboo-hr-tutorial/IC796689.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no BambooHR**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar logon único](./media/active-directory-saas-bamboo-hr-tutorial/IC796690.png "Configurar logon único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa BambooHR como um administrador.
6. Na página inicial, execute as seguintes etapas:
   
   ![Logon Único](./media/active-directory-saas-bamboo-hr-tutorial/IC796691.png "Logon Único")   
   1. Clique em **Aplicativos**.
   2. No menu de aplicativos à esquerda, clique em **Logon Único**.
   3. Clique em **Logon Único do SAML**.
7. Na seção **Logon Único do SAML** , realize as seguintes etapas:
   
   ![Logon Único do SAML](./media/active-directory-saas-bamboo-hr-tutorial/IC796692.png "Logon Único do SAML")
   
   1. No Portal Clássico do Azure, na página da caixa de diálogo **Configurar logon único no BambooHR**, copie o valor da **URL do Serviço de Logon Único** e cole-o na caixa de texto **URL de Logon de SSO **.
   2. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
   
      >[!TIP]
      >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o). 
      > 
   3. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**
   4. Clique em **Salvar**.
8. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar logon único](./media/active-directory-saas-bamboo-hr-tutorial/IC796693.png "Configurar logon único")
   
## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário

Para permitir que os usuários do AD do Azure façam logon no BambooHR, eles deverão ser provisionados no BambooHR.  

* No caso do BambooHR, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu site de empresa do **BambooHR** como administrador.
2. Na barra de ferramentas na parte superior, clique em **Configurações**.
   
   ![Configuração](./media/active-directory-saas-bamboo-hr-tutorial/IC796694.png "Configuração")
3. Clique em **Visão Geral**.
4. No painel de navegação à esquerda, vá para **Segurança \> Usuários**.
5. Digite o nome de usuário, a senha e o endereço de email de uma conta válida do AAD que deseja provisionar nas caixas de texto relacionadas.
6. Clique em **Salvar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do BambooHR ou as APIs fornecidas pelo BambooHR para provisionar as contas de usuário do AAD. 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao BambooHR, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **BambooHR**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-bamboo-hr-tutorial/IC796695.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-bamboo-hr-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


