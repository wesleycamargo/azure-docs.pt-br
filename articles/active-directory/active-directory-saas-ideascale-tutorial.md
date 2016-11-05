---
title: 'Tutorial: integração do Active Directory do Azure ao IdeaScale | Microsoft Docs'
description: Saiba como usar o IdeaScale com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/09/2016
ms.author: jeedes

---
# Tutorial: integração do Active Directory do Azure ao IdeaScale
O objetivo deste tutorial é mostrar a integração do Azure ao IdeaScale. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do IdeaScale

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao IdeaScale poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o IdeaScale
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-ideascale-tutorial/IC790838.png "Cenário")

## Habilitando a integração de aplicativos para o IdeaScale
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o IdeaScale.

### Para habilitar a integração de aplicativos para o IdeaScale, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-ideascale-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-ideascale-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-ideascale-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **IdeaScale**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-ideascale-tutorial/IC790841.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **IdeaScale** e clique em **Concluir** para adicionar o aplicativo.
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
   ## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no IdeaScale com a própria conta do Azure AD usando a federação baseada no protocolo SAML. Configurar o logon único para o IdeaScale exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com este procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

### Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **IdeaScale**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-ideascale-tutorial/IC790843.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no IdeaScale**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-ideascale-tutorial/IC790844.png "Configurar o logon único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Entrada do IdeaScale**, digite a URL usada pelos usuários para fazer logon no aplicativo IdeaScale (por exemplo: "*https://company.IdeaScale.com*") e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-ideascale-tutorial/IC790845.png "Configurar a URL do Aplicativo")
4. Na página **Configurar logon único no IdeaScale**, para baixar os metadados, clique em **Baixar metadados** e salve o arquivo de metadados localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-ideascale-tutorial/IC790846.png "Configurar o logon único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa do IdeaScale como administrador.
6. Vá para **Configurações da Comunidade**.
   
   ![Configurações da Comunidade](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configurações da Comunidade")
7. Vá para **Segurança > Configurações de Logon Único**.
   
   ![Configurações de Logon Único](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Configurações de Logon Único")
8. Para **Tipo de Logon Único**, selecione **SAML 2.0**.
   
   ![Tipo de Logon Único](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Tipo de Logon Único")
9. No diálogo **Configurações de Logon Único**, realize as seguintes etapas:
   
   ![Configurações de Logon Único](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Configurações de Logon Único")
   
   1. No portal clássico do Azure, na página de diálogo **Configurar logon único no IdeaScale**, copie o valor da **ID de Entidade** e cole-o na caixa de texto **ID de Entidade do IdP do SAML**.
   2. Copie o conteúdo do arquivo de metadados baixado e cole-o na caixa de texto **Metadados do IdP do SAML**.
   3. No portal clássico do Azure, na página de diálogo **Configurar logon único no IdeaScale**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Êxito de Logoff**.
   4. Clique em **Salvar Alterações**.
10. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o logon único](./media/active-directory-saas-ideascale-tutorial/IC790851.png "Configurar o logon único")
    
    ## Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no IdeaScale, eles deverão ser provisionados no IdeaScale. No caso do IdeaScale, o provisionamento é uma tarefa manual.

### Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **IdeaScale** como administrador.
2. Vá para **Configurações da Comunidade**.
   
   ![Configurações da Comunidade](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Configurações da Comunidade")
3. Vá para **Configurações Básicas > Gerenciamento de Membros**.
4. Clique em **Adicionar Membro**.
   
   ![Gerenciamento de Membros](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Gerenciamento de Membros")
5. Na seção Adicionar Novo Membro, execute as seguintes etapas:
   
   ![Adicionar Novo Membro](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Adicionar Novo Membro")
   
   1. Na caixa de texto **Endereços de Email**, digite o endereço de email de uma conta de AAD válida que você deseja provisionar.
   2. Clique em **Salvar Alterações**.
   
   > [!NOTE]
   > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.
   > 
   > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do IdeaScale ou as APIs fornecidas pelo IdeaScale para provisionar as contas de usuário do AAD.
> 
> 

## Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### Para atribuir usuários ao IdeaScale, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **IdeaScale**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-ideascale-tutorial/IC790854.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-ideascale-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->