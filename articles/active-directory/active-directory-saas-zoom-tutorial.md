---
title: 'Tutorial: Integração do Active Directory do Azure com o Zoom | Microsoft Docs'
description: Saiba como usar o Zoom com o Active Directory do Azure para habilitar logon único, provisionamento automatizado e muito mais.
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/16/2016
ms.author: jeedes

---
# Tutorial: Integração do Active Directory do Azure com o Zoom
O objetivo deste tutorial é mostrar a integração do Azure com o Zoom. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Zoom

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Zoom poderão fazer logon único no aplicativo em seu site de empresa do Zoom (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Zoom
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-zoom-tutorial/IC784693.png "Cenário")

## Habilitando a integração de aplicativos para o Zoom
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Zoom.

### Para habilitar a integração de aplicativos com o Zoom, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zoom-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-zoom-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-zoom-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-zoom-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Zoom**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-zoom-tutorial/IC784694.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Zoom** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Zoom](./media/active-directory-saas-zoom-tutorial/IC784695.png "Zoom")

## Configurando o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Zoom com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Zoom**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-zoom-tutorial/IC784696.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no Zoom**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-zoom-tutorial/IC784697.png "Configurar o logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Zoom**, digite a URL usando o padrão "**http://company.zoom.us**" e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-zoom-tutorial/IC784698.png "Configurar a URL do Aplicativo
   ")
4. Na página **Configurar logon único no Zoom**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-zoom-tutorial/IC784699.png "Configurar o logon único")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa Zoom como administrador.
6. Clique na guia **Logon Único**.
   
   ![Logon único](./media/active-directory-saas-zoom-tutorial/IC784700.png "Logon único")
7. Clique na guia **Controle de Segurança** e vá para as configurações de **Logon Único**.
8. Na seção de Configurações de Logon Único, execute as seguintes etapas:
   
   ![Logon único](./media/active-directory-saas-zoom-tutorial/IC784701.png "Logon único")
   
   1. No portal clássico do Azure, na página do diálogo **Configurar logon único no Zoom**, copie o valor da **URL de Serviço de Logon Único** e cole-o na caixa de texto **URL da página de entrada**.
   2. No portal clássico do Azure, na página do diálogo **Configurar logon único no Zoom**, copie o valor da **URL de Serviço de Logoff Único** e cole-o na caixa de texto **URL da página de saída**.
   3. Crie um arquivo **codificado em base 64** usando o certificado baixado.
      
      > [!TIP]
      > Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   4. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do provedor de identidade**
   5. No portal clássico do Azure, na página de diálogo **Configurar logon único no Zoom**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **Emissor**.
   6. Clique em **Salvar**.
9. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-zoom-tutorial/IC784702.png "Configurar o logon único")

## Configurando o provisionamento de usuários
Para permitir que os usuários do AD do Azure façam logon no Zoom, eles devem ser provisionados no Zoom. No caso do Zoom, o provisionamento é uma tarefa manual.

### Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **Zoom** como administrador.
2. Clique na guia **Gerenciamento de Contas** e, em seguida, clique em **Gerenciamento de Usuários**.
3. Na seção Gerenciamento de Usuários, clique em **Adicionar usuários**.
   
   ![Gerenciamento de usuário](./media/active-directory-saas-zoom-tutorial/IC784703.png "Gerenciamento de usuário")
4. Na página **Adicionar usuários**, realize as seguintes etapas:
   
   ![Adicionar usuários](./media/active-directory-saas-zoom-tutorial/IC784704.png "Adicionar usuários")
   
   1. Para **Tipo de Usuário**, selecione **Básico**.
   2. Na caixa de texto **Emails**, digite o endereço de email de uma conta de AAD válida que você deseja provisionar.
   3. Clique em **Adicionar**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Zoom ou APIs fornecidas pelo Zoom para provisionar as contas de usuário do AAD.
> 
> 

## Atribuindo usuários
Para testar sua configuração, é necessário atribuir acesso ao aplicativo aos usuários do AD do Azure que deseja que usem seu aplicativo.

### Para atribuir usuários ao Zoom, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração do aplicativo **Zoom**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-zoom-tutorial/IC784705.png "Atribuir usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-zoom-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0817_2016-->