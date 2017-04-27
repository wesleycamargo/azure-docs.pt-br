---
title: "Tutorial: Integração do Azure Active Directory com o Picturepark | Microsoft Docs"
description: "Saiba como usar o Picturepark com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a6b6610bb4d4b427f525934146340a9cca6f52cb
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Integração do Active Directory do Azure com o Picturepark
O objetivo deste tutorial é mostrar a integração do Azure com o Picturepark.  

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Uma assinatura válida do Azure
* Um locatário do Picturepark

Depois de concluir este tutorial, os usuários do Azure AD atribuídos ao Picturepark poderão fazer logon único no aplicativo em seu site de empresa do Picturepark (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1. Habilitando a integração de aplicativos para Picturepark
2. Configuração do SSO (logon único)
3. Configurando o provisionamento de usuários
4. Atribuindo usuários

![Cenário](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Cenário")

## <a name="enable-the-application-integration-for-picturepark"></a>Habilitar a integração de aplicativos para Picturepark
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o Picturepark.

**Para habilitar a integração de aplicativos com o Picturepark, execute as seguintes etapas:**

1. No Portal clássico do Azure, no painel de navegação à esquerda, clique em **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. Na lista **Diretório** , selecione o diretório para o qual você deseja habilitar a integração de diretórios.
3. Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.
   
   ![Aplicativos](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Aplicativos")
4. Clique em **Adicionar** na parte inferior da página.
   
   ![Adicionar aplicativo](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Adicionar aplicativo")
5. Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.
   
   ![Adicionar um aplicativo da galeria](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Adicionar um aplicativo da galeria")
6. Na **caixa de pesquisa**, digite **Picturepark**.
   
   ![Galeria de Aplicativos](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Galeria de Aplicativos")
7. No painel de resultados, selecione **Picturepark** e clique em **Concluir** para adicionar o aplicativo.
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")


## <a name="configure-single-sign-on"></a>Configurar o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Picturepark com sua conta do AD do Azure usando federação baseada em protocolo SAML.  

Configurar o SSO para o Picturepark exige que você recupere um valor de impressão digital de um certificado.  

Se você não estiver familiarizado com esse procedimento, veja [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).

**Para configurar o logon único, execute as seguintes etapas:**

1. No Portal Clássico do Azure, na página de integração de aplicativos do **Picturepark**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configurar Logon Único")
2. Na página **Como você deseja que os usuários façam logon no Picturepark**, selecione **Logon Único do Microsoft Azure AD** e clique em **Avançar**.
   
   ![Configurar Logon Único](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configurar Logon Único")
3. Na página **Configurar a URL do Aplicativo**, na caixa de texto **URL de Logon do Picturepark**, digite a URL usando o padrão "*http://company.picturepark.com*" e clique em **Avançar**.
   
   ![Configurar URL do Aplicativo](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configurar URL do Aplicativo")
4. Na página **Configurar logon único no Picturepark**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado localmente no computador.
   
   ![Configurar Logon Único](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configurar Logon Único")
5. Em uma janela de navegador da Web diferente, faça logon no site de sua empresa do Picturepark como administrador.
6. Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas** e em **Console de Gerenciamento**.
   
   ![Console de Gerenciamento](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Console de Gerenciamento")
7. Clique em **Autenticação** e em **Provedores de identidade**.
   
   ![Autenticação](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Autenticação")
8. Na seção **Configuração do provedor de identidade** , realize as seguintes etapas:
   
   ![Configuração do Provedor de Identidade](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Configuração do Provedor de Identidade")
   
   1. Clique em **Adicionar**.
   2. Digite um nome para sua configuração.
   3. Selecione **Definir como padrão**.
   4. No portal clássico do Azure, na página de diálogo **Configurar logon único no Picturepark**, copie o valor da **URL de SSO do SAML** e cole-o na caixa de texto **URI do Emissor**.
   5. Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão Digital do Emissor Confiável**.  
      
      >[!TIP]
      >Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI).
      >
      >

9. Clique em **JoinDefaultUsersGroup**.
10. Para definir o atributo **Emailaddress** na caixa de texto **Declaração**, digite **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** e clique em **Salvar**.

      ![Configuração](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuração")
11. No portal clássico do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.
   
   ![Configurar Logon Único](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configurar Logon Único")

## <a name="configure-user-provisioning"></a>Configurar provisionamento do usuário
Para permitir que os usuários do AD do Azure façam logon no Picturepark, eles devem ser provisionados no Picturepark.  

 * No caso do Picturepark, o provisionamento é uma tarefa manual.

**Para provisionar contas de usuário, execute as seguintes etapas:**

1. Faça logon em seu locatário do **Picturepark** .
2. Na barra de ferramentas na parte superior, clique em **Ferramentas administrativas** e em **Usuários**.
   
   ![Usuários](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Usuários")
3. Na guia **Visão geral de usuários**, clique em **Novo**.
   
   ![Gerenciamento de usuário](./media/active-directory-saas-picturepark-tutorial/IC795068.png "Gerenciamento de usuário")
4. No diálogo **Criar Usuário** , realize as seguintes etapas:
   
   ![Criar usuário](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Criar usuário")
   
  1. Digite o **Endereço de Email**, **Senha**, **Confirmar Senha**, **Nome**, **Sobrenome**, **Empresa**, **País**, **CEP**, **Cidade** de um usuário válido do Active Directory do Azure que você deseja provisionar nas caixas de texto relacionadas.
  2. Selecione um **Idioma**.
  3. Clique em **Criar**.

>[!NOTE]
>É possível usar qualquer outra ferramenta de criação da conta de usuário do Picturepark ou as APIs fornecidas pelo Picturepark para provisionar as contas de usuário do AAD.
> 
> 

## <a name="assign-users"></a>Atribuir usuários
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

**Para atribuir usuários ao Picturepark, execute as seguintes etapas:**

1. No Portal clássico do Azure, crie uma conta de teste.
2. Na página de integração de aplicativos do **Picturepark**, clique em **Atribuir usuários**.
   
   ![Atribuir Usuários](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Atribuir Usuários")
3. Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.
   
   ![Sim](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de SSO, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).


