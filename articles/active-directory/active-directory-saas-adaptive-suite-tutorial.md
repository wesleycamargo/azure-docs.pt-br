---
title: 'Tutorial: Integração do Active Directory do Azure ao Adaptive Suite | Microsoft Docs'
description: Saiba como usar o Adaptive Suite com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
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
# Tutorial: Integração do Active Directory do Azure ao Adaptive Suite
O objetivo deste tutorial é mostrar a integração do Azure ao Adaptive Suite. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Adaptive Suite

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Adaptive Suite poderão fazer logon único no aplicativo em seu site de empresa do Adaptive Suite (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Adaptive Suite
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "Cenário")

## Habilitando a integração de aplicativos para o Adaptive Suite
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Adaptive Suite.

### Para habilitar a integração de aplicativos ao Adaptive Suite, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Adaptive Suite**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Adaptive Suite** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
   
   ## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Adaptive Suite com a respectiva conta do AD do Azure usando federação baseada em protocolo SAML. Configurar o logon único para o Adaptive Suite exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

### Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Adaptive Suite**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no Adaptive Suite**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "Configurar o logon único")
3. Na página **Definir Configurações do Aplicativo**, na caixa de texto **URL de Resposta**, digite a URL usando o padrão "*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*" e clique em **Avançar**.
   
   > [!NOTE]
   > Você pode obter esse valor na página **Configurações de SSO do SAML** do Adaptive Suite.
   > 
   > 
   
   ![Definir configurações de aplicativo](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "Definir configurações de aplicativo")
4. Na página **Configurar logon único no Adaptive Suite**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "Configurar o logon único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa Adaptive Suite como administrador.
6. Vá para **Administrador**.
   
   ![Administrador](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")
7. Na seção **Usuários e Funções**, clique em **Gerenciar Configurações de SSO do SAML**.
   
   ![Gerenciar Configurações de SSO do SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "Gerenciar Configurações de SSO do SAML")
8. Na página **Configurações de SSO do SAML**, realize as seguintes etapas:
   
   ![Configurações de SSO do SAML](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "Configurações de SSO do SAML")
   
   1. Na caixa de texto **Nome do provedor de identidade**, digite um nome para a sua configuração.
   2. No portal clássico do Azure, na página do diálogo **Configurar logon único no Adaptive Suite**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do provedor de identidade**.
   3. No portal clássico do Azure, na página do diálogo **Configurar logon único no Adaptive Suite**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL de SSO do provedor de identidade**.
   4. No portal clássico do Azure, na página do diálogo **Configurar logon único no Adaptive Suite**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URL de logout personalizado**.
   5. Para carregar seu certificado baixado, clique em **Escolher arquivo**.
   6. Para **ID de usuário do SAML**, selecione **Nome de usuário do Adaptive Insights do Usuário**.
   7. Para **Local da ID de usuário do SAML**, selecione **ID de usuário em NameID da Entidade**.
   8. Para **Formato de NameID do SAML**, selecione **Endereço de email**.
   9. Como **Habilitar SAML**, selecione **Permitir SSO do SAML e direcionar logon do Adaptive Insights**.
   10. Clique em **Salvar**.
9. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "Configurar o logon único")
   
   ## Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Adaptive Suite, eles devem ser provisionados no Adaptive Suite. No caso do Adaptive Suite, o provisionamento é uma tarefa manual.

### Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Adaptive Suite** como administrador.
2. Vá para **Administrador**.
   
   ![Administrador](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "Administrador")
3. Na seção **Usuários e Funções**, clique em **Adicionar Usuário**.
   
   ![Adicionar usuário](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "Adicionar usuário")
4. Na seção **Novo Usuário**, realize as seguintes etapas:
   
   ![Enviar](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "Enviar")
   
   1. Digite o **Nome**, **Logon**, **Email** e **Senha** de um usuário válido do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
   2. Selecione uma **Função**.
   3. Clique em **Enviar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Adaptive Suite ou as APIs fornecidas pelo Adaptive Suite para provisionar as contas de usuário do AAD.
> 
> 

## Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### Para atribuir usuários ao Adaptive Suite, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Adaptive Suite**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->