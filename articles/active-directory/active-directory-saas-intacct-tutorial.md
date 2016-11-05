---
title: 'Tutorial: Integração do Active Directory do Azure com o Intacct | Microsoft Docs'
description: Saiba como usar o Intacct com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!
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
# Tutorial: Integração do Active Directory do Azure com o Intacct
O objetivo deste tutorial é mostrar a integração do Azure com o Intacct. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Intacct

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Intacct poderão fazer logon único no aplicativo em seu site de empresa do Intacct (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Intacct
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-intacct-tutorial/IC790030.png "Cenário")

## Habilitando a integração de aplicativos para o Intacct
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Intacct.

### Para habilitar a integração de aplicativos com o Intacct, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-intacct-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-intacct-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-intacct-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Intacct**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-intacct-tutorial/IC790031.png "Galeria de aplicativos")
7. No painel de resultados, selecione **Intacct** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
   
   ## Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Intacct com sua conta do AD do Azure usando federação baseada em protocolo SAML. Como parte desse procedimento, é necessário criar um arquivo de certificado codificado em base 64. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

### Para configurar o logon único, execute as seguintes etapas:
1. No portal clássico do Azure, na página de integração de aplicativos do **Intacct**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar o logon único](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configurar o logon único")
2. Na página **Como você deseja que os usuários façam logon no Intacct**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.
   
   ![Configurar o logon único](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configurar o logon único")
3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Intacct**, digite a URL usando o padrão "*https://Intacct.com/company*" e clique em **Avançar**.
   
   ![Configurar a URL do Aplicativo](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configurar a URL do Aplicativo")
4. Na página **Configurar logon único no Intacct**, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
   ![Configurar o logon único](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configurar o logon único")
5. Em uma janela diferente do navegador da Web, faça logon no site da sua empresa do Intacct como administrador.
6. Clique na guia **Empresa** e em **Informações da Empresa**.
   
   ![Empresa](./media/active-directory-saas-intacct-tutorial/IC790037.png "Empresa")
7. Clique na guia **Segurança** e em **Editar**.
   
   ![Segurança](./media/active-directory-saas-intacct-tutorial/IC790038.png "Segurança")
8. Na seção **SSO (logon único)**, realize as seguintes etapas:
   
   ![Logon Único](./media/active-directory-saas-intacct-tutorial/IC790039.png "Logon Único")
   
   1. Selecione **Habilitar logon único**.
   2. Para **Tipo de provedor de identidade**, selecione **SAML 2.0**.
   3. No portal clássico do Azure, na página de diálogo **Configurar logon único no Intacct**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **URL do Emissor**.
   4. No portal clássico do Azure, na página de diálogo **Configurar logon único no Intacct**, copie o valor de **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon**.
   5. Crie um arquivo **codificado em Base 64** por meio do certificado baixado.
      
      > [!TIP]
      > Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
      > 
      > 
   6. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado**
   7. Clique em **Salvar**.
9. No Portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar o logon único](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configurar o logon único")
   
   ## Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no Intacct, eles devem ser provisionados no Intacct. No caso do Intacct, o provisionamento é uma tarefa manual.

### Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu locatário do **Intacct**.
2. Clique na guia **Empresa** e em **Usuários**.
   
   ![Usuários](./media/active-directory-saas-intacct-tutorial/IC790041.png "Usuários")
3. Clique na guia **Adicionar**
   
   ![Adicionar](./media/active-directory-saas-intacct-tutorial/IC790042.png "Adicionar")
4. Na seção **Informações do Usuário**, realize as seguintes etapas:
   
   ![Informações do usuário](./media/active-directory-saas-intacct-tutorial/IC790043.png "Informações do usuário")
   
   1. Digite a **ID de usuário**, **Sobrenome**, **Nome**, **Endereço de email**, **Título** e **Telefone** de uma conta do Azure AD que você deseja provisionar nas caixas de texto relacionadas.
   2. Selecione **Privilégios de administrador** de uma conta do Azure AD que você deseja provisionar.
   3. Clique em **Salvar**.
      
      > [!NOTE]
      > O titular da conta do AAD receberá um email e um link para confirmar sua conta antes de se tornar ativo.
      > 
      > 

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do Intacct ou APIs fornecidas pelo Intacct para provisionar as contas de usuário do AAD.
> 
> 

## Atribuindo usuários
Para testar sua configuração, é necessário conceder aos usuários do AD do Azure que você deseja que usem seu aplicativo acesso a ele.

### Para atribuir usuários ao Intacct, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Intacct**, clique em **Atribuir usuários**.
   
   ![Atribuir usuários](./media/active-directory-saas-intacct-tutorial/IC790044.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-intacct-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0713_2016-->