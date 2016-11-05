---
title: 'Tutorial: integração do Active Directory do Azure ao Coupa | Microsoft Docs'
description: Saiba como usar o Coupa com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
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
# Tutorial: integração do Active Directory do Azure ao Coupa
O objetivo deste tutorial é mostrar a integração do Azure ao Coupa. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do Coupa

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Coupa poderão fazer logon único no aplicativo usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para Coupa
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-coupa-tutorial/IC791897.png "Cenário")

## Habilitando a integração de aplicativos para Coupa
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Coupa.

### Para habilitar a integração de aplicativos para o Coupa, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-coupa-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-coupa-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-coupa-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Coupa**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-coupa-tutorial/IC791898.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Coupa** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
   ## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários autentiquem no Coupa com a própria conta no Azure AD usando federação baseada no protocolo SAML. Configurar o logon único para o Coupa exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

### Para configurar o logon único, execute as seguintes etapas:
1. Faça logon em seu site de empresa do Coupa como administrador.
2. Vá para **Configuração > Controle de Segurança**.
   
   ![Controles de Segurança](./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de Segurança")
3. Para baixar o arquivo de metadados do Coupa no computador, clique em **Baixar e importar metadados do SP**.
   
   ![Metadados de Coupa SP](./media/active-directory-saas-coupa-tutorial/IC791901.png "Metadados de Coupa SP")
4. Em uma janela de navegador diferente, faça logon no portal clássico do Azure.
5. Na página de integração do aplicativo do **Coupa**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-coupa-tutorial/IC791902.png "Configurar o logon único")
6. Na página **Como você deseja que os usuários façam logon no Coupa**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-coupa-tutorial/IC791903.png "Configurar o logon único")
7. Na página **Configurar URL do Aplicativo**, execute as seguintes etapas:
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-coupa-tutorial/IC791904.png "Configurar a URL do Aplicativo")
   
   1. Na caixa de texto **URL de Logon**, digite a URL usada pelos usuários para entrar no aplicativo Coupa (por exemplo: “*http://company.Coupa.com*”).
   2. Abra o arquivo de metadados do Coupa baixado e copie o **Índice/URL de AssertionConsumerService**.
   3. Na caixa de texto **URL de Resposta do Coupa**, cole o valor de **Índice/URL de AssertionConsumerService**.
   4. Clique em **Próximo**.
8. Na página **Configurar logon único no Coupa**, para baixar o arquivo de metadados, clique em **Baixar metadados** e salve o arquivo localmente no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-coupa-tutorial/IC791905.png "Configurar o logon único")
9. No site de empresa do Coupa, vá para **Configuração > Controle de Segurança**.
   
   ![Controles de Segurança](./media/active-directory-saas-coupa-tutorial/IC791900.png "Controles de Segurança")
10. Na seção **Fazer Logon usando as credenciais do Coupa**, realize as seguintes etapas:
    
    ![Fazer logon usando credenciais do Coupa](./media/active-directory-saas-coupa-tutorial/IC791906.png "Fazer logon usando credenciais do Coupa")
    
    1. Selecione **Fazer logon usando o SAML**.
    2. Clique em **Procurar** para carregar seu arquivo de metadados baixado do Active do Azure.
    3. Clique em **Salvar**.
11. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar o logon único](./media/active-directory-saas-coupa-tutorial/IC791907.png "Configurar o logon único")
    
    ## Configurando o provisionamento de usuários

Para permitir que os usuários do Azure AD façam logon no Coupa, eles devem ser provisionados no Coupa. No caso do Coupa, o provisionamento é uma tarefa manual.

### Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Coupa** como administrador.
2. No menu na parte superior, clique em **Configuração** e em **Usuários**.
   
   ![Usuários](./media/active-directory-saas-coupa-tutorial/IC791908.png "Usuários")
3. Clique em **Criar**.
   
   ![Criar Usuários](./media/active-directory-saas-coupa-tutorial/IC791909.png "Criar Usuários")
4. Na seção **Criação de Usuário**, realize as seguintes etapas:
   
   ![Detalhes do Usuário](./media/active-directory-saas-coupa-tutorial/IC791910.png "Detalhes do Usuário")
   
   1. Digite os atributos **Logon**, **Nome**, **Sobrenome**, **ID de Logon Único** e **Email** de uma conta válida do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
   2. Clique em **Criar**.
   
   > [!NOTE]
   > O titular da conta do Active Directory do Azure receberá um email com um link para confirmar a conta antes que ela se torne ativa.
   > 
   > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Coupa ou as APIs fornecidas pelo Coupa para provisionar as contas de usuário do AAD.
> 
> 

## Atribuindo usuários
Para testar sua configuração, será necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que o utilizem.

### Para atribuir usuários ao Coupa, execute as etapas a seguir:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Coupa**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-coupa-tutorial/IC791911.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-coupa-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->