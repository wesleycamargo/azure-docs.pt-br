---
title: "Tutorial: integração do Azure Active Directory ao ShiftPlanning | Microsoft Docs"
description: "Saiba como usar o ShiftPlanning com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 6104bd7e22d855c4c8737ef1080bfd445b29eafb


---
# <a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>Tutorial: Integração do Active Directory do Azure com o ShiftPlanning
O objetivo deste tutorial é mostrar a integração do Azure com o ShiftPlanning.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Uma assinatura habilitada para logon único do ShiftPlanning

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao ShiftPlanning poderão fazer logon único no aplicativo em seu site de empresa do ShiftPlanning (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos com o ShiftPlanning
2. Configurando o logon único
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Cenário")

## <a name="enabling-the-application-integration-for-shiftplanning"></a>Habilitando a integração de aplicativos com o ShiftPlanning
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o ShiftPlanning.

### <a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>Para habilitar a integração de aplicativos para o ShiftPlanning, execute as seguintes etapas:
1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **ShiftPlanning**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Galeria de Aplicativos")

7. No painel de resultados, selecione **ShiftPlanning** e clique em **Concluir** para adicionar o aplicativo.
   
    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
   
## <a name="configuring-single-sign-on"></a>Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no ShiftPlanning com sua conta do AD do Azure usando federação baseada no protocolo SAML.  
Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base&64;.  
Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Para configurar o logon único, execute as seguintes etapas:
1. No Portal Clássico do Azure, na página de integração de aplicativos do **ShiftPlanning**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar Logon Único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configurar Logon Único")

2. Na página **Como você deseja que os usuários façam logon no ShiftPlanning**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar Logon Único](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configurar Logon Único")

3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do ShiftPlanning**, digite a URL usando o padrão "*https://empresa.shiftplanning.com/includes/saml/*" e clique em **Avançar***.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no ShiftPlanning**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
    ![Configurar Logon Único](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configurar Logon Único")

5. Em outra janela do navegador da Web, faça logon em seu site de empresa do **ShiftPlanning** como administrador.
6. No menu na parte superior, clique em **Administrador**.
   
    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

7. Em **Integração**, clique em **Logon Único**.
   
    ![Logon Único](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Logon Único")

8. Na seção **Logon Único** , realize as seguintes etapas:
   
    ![Logon Único](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Logon Único")
   
    1. Selecione **SAML Habilitado**.

    2. Selecione **Permitir Logon de Senha**

    3. No portal clássico do Azure, na página de diálogo **Configurar logon único no ShiftPlanning**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL do Emissor do SAML**.

    4. No portal clássico do Azure, na página de diálogo **Configurar logon único no ShiftPlanning**, copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff Remoto**.

    5. Crie um arquivo **codificado em base&64;** usando o certificado baixado.  
       
    > [!TIP]
    > Para obter mais detalhes, confira [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 

    6. Abra seu certificado codificado em Base&64; no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado X.509**

    7. Clique em **Salvar Configurações**.

9. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
    ![Configurar Logon Único](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configurar Logon Único")
   
## <a name="configuring-user-provisioning"></a>Configurando o provisionamento de usuários

Para permitir que os usuários do AD do Azure façam logon no ShiftPlanning, eles deverão ser provisionados no ShiftPlanning.  
No caso do ShiftPlanning, o provisionamento será uma tarefa manual.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Para provisionar contas de usuário, execute as seguintes etapas:
1. Faça logon em seu site de empresa do **ShiftPlanning** como administrador.

2. Clique em **Administrador**.
   
    ![Admin](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

3. Clique em **Equipe**.
   
    ![Equipe](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Equipe")

4. Em **Ações**, clique em **Adicionar Funcionário**.
   
    ![Adicionar Funcionários](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Adicionar Funcionários")

5. Na seção **Adicionar Funcionários** , realize as seguintes etapas:
   
    ![Salvar Funcionários](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Salvar Funcionários")
   
    1. Nas caixas de texto correspondentes, digite o **Nome**, o **Sobrenome** e o **Email** de uma conta válida do AAD que você deseja provisionar.

    2. Clique em **Salvar Funcionários**.

> [!NOTE]
> É possível usar qualquer outra ferramenta de criação da conta de usuário do ShiftPlanning ou as APIs fornecidas pelo ShiftPlanning para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assigning-users"></a>Atribuindo usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

### <a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>Para atribuir usuários ao ShiftPlanning, execute as seguintes etapas:
1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração de aplicativos do **ShiftPlanning**, clique em **Atribuir usuários**.
   
    ![Atribuir Usuários](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Atribuir Usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Sim")
 
Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).




<!--HONumber=Jan17_HO1-->


