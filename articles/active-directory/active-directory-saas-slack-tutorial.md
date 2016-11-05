---
title: 'Tutorial: Integração do Active Directory do Azure ao Slack | Microsoft Docs'
description: Saiba como usar o Slack com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2016
ms.author: jeedes

---
# Tutorial: Integração do Active Directory do Azure com o Slack
O objetivo deste tutorial é mostrar a integração do Azure com o Slack. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Slack

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Slack poderão fazer logon único no aplicativo em seu site de empresa do Slack (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Slack
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-slack-tutorial/IC794980.png "Cenário")

## Habilitando a integração de aplicativos para o Slack
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Slack.

### Para habilitar a integração de aplicativos para o Slack, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-slack-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-slack-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-slack-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-slack-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Slack**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-slack-tutorial/IC794981.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Slack** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Cenário](./media/active-directory-saas-slack-tutorial/IC796925.png "Cenário")

## Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Slack com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### Para configurar o logon único, execute as seguintes etapas:
1. No Portal clássico do Azure, na página de integração do aplicativo **Slack**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/IC794982.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no Slack**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/IC794983.png "Configurar o logon único")
3. Na página **Configurar URL do Aplicativo**, a caixa de texto **URL de Entrada do Slack**, digite a URL de locatário do Slack (por exemplo: "*https://azuread.slack.com*") e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-slack-tutorial/IC794984.png "Configurar a URL do Aplicativo")
4. Na página **Configurar logon único no Slack**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/IC794985.png "Configurar o logon único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa Slack como um administrador.
6. Vá para **AD do Microsoft Azure > Configurações de Equipe**.
   
   ![Configurações da Equipe](./media/active-directory-saas-slack-tutorial/IC794986.png "Configurações da Equipe")
7. Na seção **Configurações de Equipe**, clique na guia **Autenticação** e em **Alterar Configurações**.
   
   ![Configurações da Equipe](./media/active-directory-saas-slack-tutorial/IC794987.png "Configurações da Equipe")
8. No diálogo **Configurações de Autenticação SAML**, realize as seguintes etapas:
   
   ![Configurações do SAML](./media/active-directory-saas-slack-tutorial/IC794988.png "Configurações do SAML")
   
   1. No Portal clássico do Azure, na página de diálogo **Configurar logon único no Slack**, copie o valor da **URL de SSO de SAML** e cole-o na caixa de texto **Ponto de Extremidade SAML 2.0 (HTTP)**.
   2. No Portal clássico do Azure, na página de diálogo **Configurar logon único no Slack**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor do Provedor de Identidade**.
   3. Crie um arquivo **codificado em Base 64** por meio do certificado baixado.
      
      > [!TIP]
      > Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado Público**.
   5. Desmarque **Permitir que os usuários alterem seu endereço de email**.
   6. Selecione **Permitir que os usuários escolham seu próprio nome de usuário**.
   7. Para **A autenticação de sua equipe deve ser usada por**, selecione **É opcional**.
   8. Clique em **Salvar Configuração**.
9. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-slack-tutorial/IC794989.png "Configurar o logon único")

## Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no Slack, eles deverão ser provisionados no Slack.

Não há nenhum item de ação para a configuração de provisionamento de usuário para o Slack. Quando um usuário atribuído tentar fazer logon no Slack, uma conta do Slack será automaticamente criada, se necessário.

## Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### Para atribuir usuários ao Slack, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Slack**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-slack-tutorial/IC794990.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-slack-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0921_2016-->