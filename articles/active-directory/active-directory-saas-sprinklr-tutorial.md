---
title: "Tutorial: Integração do Azure Active Directory ao Sprinklr | Microsoft Docs"
description: "Saiba como usar o Sprinklr com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 44b5314a250d88f7ea2f8db2c1270a9090f083cd
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Tutorial: Integração do Active Directory do Azure com o Sprinklr
O objetivo deste tutorial é mostrar a integração do Azure com o Sprinklr.  
O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Sprinklr

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Sprinklr poderão fazer logon único no aplicativo em seu site de empresa do Sprinklr (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para o Sprinklr
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-sprinklr-tutorial/IC782900.png "Cenário")

## <a name="enable-the-application-integration-for-sprinklr"></a>Habilitar a integração de aplicativos para o Sprinklr
O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Sprinklr.

**Para habilitar a integração de aplicativos com o Sprinklr, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-sprinklr-tutorial/IC700993.png "Active Directory")

2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
    ![Aplicativos](./media/active-directory-saas-sprinklr-tutorial/IC700994.png "Aplicativos")

4. Clique em **Adicionar** na parte inferior da página.
   
    ![Adicionar aplicativo](./media/active-directory-saas-sprinklr-tutorial/IC749321.png "Adicionar aplicativo")

5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-sprinklr-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6. Na **caixa de pesquisa**, digite **Sprinklr**.
   
    ![Galeria de Aplicativos](./media/active-directory-saas-sprinklr-tutorial/IC782901.png "Galeria de Aplicativos")

7. No painel de resultados, selecione **Sprinklr** e clique em **Concluir** para adicionar o aplicativo.
   
    ![Sprinklr](./media/active-directory-saas-sprinklr-tutorial/IC782902.png "Sprinklr")

## <a name="configure-single-sign-on"></a>Configurar o logon único
O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Sprinklr com sua conta do AD do Azure usando federação baseada no protocolo SAML. 


Como parte desse procedimento, será necessário criar um arquivo de certificado codificado em base 64.  

Se você não estiver familiarizado com este procedimento, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).

**Para configurar o logon único, execute as seguintes etapas:**

1. No portal clássico do Azure, na página de integração do aplicativo **Sprinklr**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
    ![Configurar logon único](./media/active-directory-saas-sprinklr-tutorial/IC782903.png "Configurar logon único")

2. Na página **Como você deseja que os usuários façam logon no Sprinklr**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
    ![Configurar logon único](./media/active-directory-saas-sprinklr-tutorial/IC782904.png "Configurar logon único")

3. Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Entrada do Sprinklr**, digite a URL usando o padrão "*https://\<tenant-name\>.sprinklr.com*" e clique em **Avançar**.
   
    ![Configurar URL do Aplicativo](./media/active-directory-saas-sprinklr-tutorial/IC782905.png "Configurar URL do Aplicativo")

4. Na página **Configurar logon único no Sprinklr**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.
   
    ![Configurar logon único](./media/active-directory-saas-sprinklr-tutorial/IC782906.png "Configurar logon único")

5. Em outra janela do navegador da Web, faça logon em seu site de empresa do Sprinklr como um administrador.

6. Vá para **Administração \> Configurações**.
   
    ![Administração](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administração")

7. Vá para **Gerenciar Parceiro \> Logon Único** no painel à esquerda.
   
    ![Gerenciar Parceiro](./media/active-directory-saas-sprinklr-tutorial/IC782908.png "Gerenciar Parceiro")

8. Clique em **+Adicionar Logons Únicos**.
   
    ![Logons Únicos](./media/active-directory-saas-sprinklr-tutorial/IC782909.png "Logons Únicos")

9. Na página **Logon Único** , realize as seguintes etapas:
   
    ![Logons Únicos](./media/active-directory-saas-sprinklr-tutorial/IC782910.png "Logons Únicos")
  1. Na caixa de texto **Nome** , digite um nome para a sua configuração (por exemplo: *WAADSSOTest*).
  2. Selecione **Habilitado**.
  3. Selecione **Usar novo Certificado de SSO**.
  4. Crie um arquivo **codificado em base 64** usando o certificado baixado.  
  
     >[!TIP]
     >Para obter mais detalhes, consulte [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o). 
     >    
     
  5. Abra seu certificado codificado em Base 64 no bloco de notas, copie o conteúdo dele na área de transferência e cole-o na caixa de texto **Certificado do Provedor de Identidade**.
  6. No Portal Clássico do Azure, na caixa de diálogo **Configurar SSO no Sprinklr**:
     *  Copie o valor de **ID do Provedor de Identidade** e cole-o na caixa de texto **ID da Entidade**.
     * Copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de Logon do Provedor de Identidade**.
     * Copie o valor da **URL de Logoff Remoto** e cole-o na caixa de texto **URL de Logoff do Provedor de Identidade**.
  7. Para **Tipo de ID de Usuário do SAML**, selecione **A declaração contém o nome de usuário de sprinklr.com do Usuário**.
  8. Para **Local da ID de Usuário do SAML**, selecione **A ID de Usuário está contida no elemento Identificador de Nome da instrução Subject**.
  9. Clique em **Salvar**.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/IC782911.png "SAML")
10. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
    
    ![Configurar logon único](./media/active-directory-saas-sprinklr-tutorial/IC782912.png "Configurar logon único")

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
Para que os usuários do AAD possam fazer logon, eles deverão ser provisionados no aplicativo Sprinklr.  
Esta seção descreve como criar contas de usuário do AAD no Sprinklr.

### <a name="to-provision-a-user-account-in-sprinklr-perform-the-following-steps"></a>Para provisionar uma conta de usuário no Sprinklr, execute as seguintes etapas:
1. Faça logon em seu site de empresa Sprinklr como um administrador.

2. Vá para **Administração \> Configurações**.
   
    ![Administração](./media/active-directory-saas-sprinklr-tutorial/IC782907.png "Administração")

3. Vá para **Gerenciar Cliente \> Usuários** no painel à esquerda.
   
    ![Configurações](./media/active-directory-saas-sprinklr-tutorial/IC782914.png "Configurações")

4. Clique em **Adicionar Usuário**.
   
    ![Configurações](./media/active-directory-saas-sprinklr-tutorial/IC782915.png "Configurações")

5. No diálogo **Editar usuário** , realize as seguintes etapas:
   
    ![Editar usuário](./media/active-directory-saas-sprinklr-tutorial/IC782916.png "Editar usuário") 
  1. Nas caixas de texto **Email**, **Nome** e **Sobrenome**, digite as informações de uma conta de usuário do Azure AD que você deseja provisionar.
  2. Selecione **Senha Desabilitada**.
  3. Selecione um **Idioma**.
  4. Selecione um **Tipo de Usuário**.
  5. Clique em **Atualizar**.
   
     >[!IMPORTANT]
     >**Senha Desabilitada** deve ser marcada para permitir que um usuário faça logon por meio de um Provedor de identidade. 
     > 

6. Vá para **Função**e realize as seguintes etapas:
   
    ![Funções de Parceiro](./media/active-directory-saas-sprinklr-tutorial/IC782917.png "Funções de Parceiro")
 1. Na lista **Global**, selecione **ALL\_Permissions**.  
 2. Clique em **Atualizar**.

>[!NOTE]
>Você pode usar qualquer outra ferramenta de criação da conta de usuário do Sprinklr ou APIs fornecidas pelo Sprinklr para provisionar contas de usuário do AD do Azure. 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Sprinklr, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.

2. Na página de integração do aplicativo **Sprinklr**, clique em **Atribuir usuários**.
   
    ![Atribuir usuários](./media/active-directory-saas-sprinklr-tutorial/IC782918.png "Atribuir usuários")

3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
    ![Sim](./media/active-directory-saas-sprinklr-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


