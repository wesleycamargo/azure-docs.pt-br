---
title: 'Tutorial: Integração do Active Directory do Azure ao Bime | Microsoft Docs'
description: Saiba como usar o Bime com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
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
# Tutorial: Integração do Active Directory do Azure ao Bime
O objetivo deste tutorial é mostrar a integração do Azure ao Bime. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Bime

Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao Bime poderão fazer logon único no aplicativo em seu site de empresa do Bime (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Bime
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-bime-tutorial/IC775552.png "Cenário")

## Habilitando a integração de aplicativos para o Bime
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Bime.

### Para habilitar a integração de aplicativos para o Bime, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-bime-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-bime-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-bime-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-bime-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Bime**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-bime-tutorial/IC775553.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Bime** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Bime](./media/active-directory-saas-bime-tutorial/IC775554.png "Bime")
   
   ## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Bime com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Configurar o logon único para o Bime exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

### Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração do aplicativo **Bime**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-bime-tutorial/IC771709.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no Bime**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-bime-tutorial/IC775555.png "Configurar o logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do Bime**, digite a URL usando o padrão "*https://\<nome-locatário>.Bimeapp.com*" e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-bime-tutorial/IC775556.png "Configurar a URL do Aplicativo")
4. Na página **Configurar logon único no Bime**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente como **c:\\Bime.cer**.
   
   ![Configurar o logon único](./media/active-directory-saas-bime-tutorial/IC775557.png "Configurar o logon único")
5. Em outra janela do navegador da Web, faça logon em seu site de empresa Bime como um administrador.
6. Na barra de ferramentas, clique em **Administrador** e em **Conta**.
   
   ![Administrador](./media/active-directory-saas-bime-tutorial/IC775558.png "Administrador")
7. Na página de configuração da conta, execute as seguintes etapas:
   
   ![Configurar o logon único](./media/active-directory-saas-bime-tutorial/IC775559.png "Configurar o logon único")
   
   1. Selecione **Habilitar autenticação SAML**.
   2. No portal clássico do Azure, na página do diálogo **Configurar logon único no Bime**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon Remoto**.
   3. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Certificado**.
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. Clique em **Salvar**.
8. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-bime-tutorial/IC775560.png "Configurar o logon único")
   
   ## Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Bime, eles deverão ser provisionados no Bime. No caso do Bime, o provisionamento é uma tarefa manual.

### Para configurar o provisionamento de usuários, execute as seguintes etapas:
1. Faça logon em seu locatário do **Bime**.
2. Na barra de ferramentas, clique em **Administrador** e em **Usuários**.
   
   ![Administrador](./media/active-directory-saas-bime-tutorial/IC775561.png "Administrador")
3. Na **Lista de Usuários**, clique em **Adicionar Novo Usuário** (“+”).
   
   ![Usuários](./media/active-directory-saas-bime-tutorial/IC775562.png "Usuários")
4. Na página do diálogo **Detalhes do Usuário**, realize as seguintes etapas:
   
   ![Detalhes do Usuário](./media/active-directory-saas-bime-tutorial/IC775563.png "Detalhes do Usuário")
   
   1. Insira Nome, Sobrenome, Logon e Email de uma conta válida do AAD que deseja provisionar.
   2. Clique em Salvar.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Bime ou as APIs fornecidas pelo Bime para provisionar as contas de usuário do AAD.
> 
> 

## Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### Para atribuir usuários ao Bime, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Bime**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-bime-tutorial/IC775564.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-bime-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->