---
title: 'Tutorial: Integração do Active Directory do Azure ao Chromeriver | Microsoft Docs'
description: Saiba como usar o Chromeriver com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2016
ms.author: jeedes

---
# Tutorial: Integração do Active Directory do Azure ao Chromeriver
O objetivo deste tutorial é mostrar a integração do Azure ao Chromeriver. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Chromeriver

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Chromeriver poderão fazer logon único no aplicativo em seu site de empresa do Chromeriver (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Chromeriver
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-chromeriver-tutorial/IC802755.png "Cenário")

## Habilitando a integração de aplicativos para o Chromeriver
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Chromeriver.

### Para habilitar a integração de aplicativos para o Chromeriver, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-chromeriver-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-chromeriver-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-chromeriver-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-chromeriver-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Chromeriver**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-chromeriver-tutorial/IC802756.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Chromeriver** e clique em **Concluir** para adicionar o aplicativo.
   
   ## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Chromeriver com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML.

### Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Cherwell**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-chromeriver-tutorial/IC802757.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no Chromeriver**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-chromeriver-tutorial/IC802758.png "Configurar o logon único")
3. Na página **Definir Configurações do Aplicativo**, execute as seguintes etapas:
   
   ![Definir configurações de aplicativo](./media/active-directory-saas-chromeriver-tutorial/IC802759.png "Definir configurações de aplicativo")
   
   1. Na caixa de texto **URL de Resposta**, digite a URL de **AssertionConsumerService** do Chromeriver (por exemplo: *https://qa-app.chromeriver.com/login/sso/saml/consume?customerId=911*).
      
      > [!NOTE]
      > Você pode obter esse valor com a equipe de suporte do Chromeriver.
      > 
      > 
   2. Clique em **Próximo**.
4. Na página **Configurar logon único no Chromeriver**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de metadados no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-chromeriver-tutorial/IC802760.png "Configurar o logon único")
5. Envie o arquivo de metadados baixado para a equipe de suporte do Chromeriver.
   
   > [!NOTE]
   > A equipe de suporte do Chromeriver precisa fazer a configuração real do SSO. Você receberá uma notificação quando o SSO tiver sido habilitado para sua assinatura.
   > 
   > 
6. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-chromeriver-tutorial/IC802761.png "Configurar o logon único")
   
   ## Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Chromeriver, eles devem ser provisionados no Chromeriver. No caso do Chromeriver, as contas de usuário precisam ser criadas pela equipe de suporte do Chromeriver.

> [!NOTE]
> Você pode usar qualquer outra ferramenta de criação da conta de usuário do Chromeriver ou as APIs fornecidas pelo Chromeriver para provisionar as contas de usuário do Active Directory do Azure.
> 
> 

## Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### Para atribuir usuários ao Chromeriver, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Chromeriver**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-chromeriver-tutorial/IC802762.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-chromeriver-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->