---
title: Configurar a declaração de Função emitida no token SAML para aplicativos empresariais no Azure Active Directory | Microsoft Docs
description: Saiba como configurar a declaração de Função emitida no token SAML para aplicativos empresariais no Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 58656e2aa3b052d9bd9aa14edeb6215858d55ea4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Configurar a declaração de Função emitida no token SAML para aplicativos empresariais no Azure Active Directory

Esse recurso permite que os usuários personalizem o tipo de declaração para a declaração de "funções" no token de resposta recebido ao autorizar um aplicativo utilizando o Microsoft Azure AD.

## <a name="prerequisites"></a>pré-requisitos
- Uma assinatura do Microsoft Azure AD com configuração de diretório
- Uma assinatura habilitada para logon único
- É necessário configurar o SSO com o aplicativo

## <a name="when-to-use-this-feature"></a>Quando usar esse recurso

Se o aplicativo espera que as funções personalizadas sejam passadas na resposta SAML, será necessário usar esse recurso. Esse recurso permite criar quantas funções forem necessárias a serem passadas do Microsoft Azure AD para o aplicativo.

## <a name="steps-to-use-this-feature"></a>Etapas para usar esse recurso

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite o nome do aplicativo, selecione o aplicativo no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![Aplicativo na lista de resultados](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a **ID de Objeto**

    ![Página Propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    a. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.

    b. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário, utilizando a consulta a seguir:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se você estiver usando vários diretórios, siga este padrão `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Da lista de entidades de serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Pesquise a **ID de objeto** que você copiou da página Propriedades e use a consulta a seguir para acessar a respectiva Entidade de Serviço.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Extraia a propriedade appRoles do objeto da entidade de serviço.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Agora, é necessário gerar novas funções para aplicativo. É possível baixar o Gerador de função do Microsoft Azure AD [aqui](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Abra o Gerador do Microsoft Azure AD e execute as etapas a seguir -

    ![Gerador do Microsoft Azure AD](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Insira o **Nome da Função**, a **Descrição da Função** e o **Valor da Função**. Clique em **Adicionar** para adicionar a função
    
    Após adicionar todas as funções necessárias, clique em **Gerar**
    
    Copie o conteúdo clicando em **Copiar Conteúdo**

    > [!NOTE] 
    > Certifique-se de que você tenha a função de usuário **msiam_access** e que a ID esteja correspondendo na função gerada.

    g. Retorne para o Explorador do Graph. Altere o método de **GET** para **PATCH**. Atualize o objeto da entidade de serviço para ter appRoles desejados, atualizando a propriedade appRoles com os valores copiados. Clique em **Executar Consulta**.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > A seguir, um exemplo do objeto appRoles. 
    ```
    {
       "appRoles": [
    {
        "allowedMemberTypes": [
        "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
        "isEnabled": true,
        "origin": "Application",
        "value": null
    },
    {
        "allowedMemberTypes": [
        "User"
        ],
        "description": "teacher",
        "displayName": "teacher",
        "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
        "isEnabled": ,
        "origin": "ServicePrincipal",
        "value": "teacher"
    }
    ] 
    }   
    ```
7. Após a atualização da entidade de serviço com mais funções, será possível atribuir usuários às respectivas funções. Isso pode ser feito, acessando o Portal e navegando até o respectivo aplicativo. Em seguida, clique na guia **Usuários e Grupos** na parte superior. Esse processo listará todos os usuários ou grupos.

    ![Configurar logon único Add](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Para atribuir uma função a qualquer usuário, basta selecionar o usuário/grupo específico e clicar no botão **Atribuir** na parte inferior da página.

    ![Configurar logon único Add](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Ao clicar nisso será exibido um pop-up para selecionar uma função a partir de diferentes funções definidas para a respectiva entidade de serviço.

    c. Escolha a função necessária e clique em enviar.

8. Após atribuir funções aos usuários, será necessário atualizar a tabela **Atributos** para definir o mapeamento personalizado da declaração **função**.

9. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|    
    | Nome da função      | user.assignedrole |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configurar o atributo Logon único](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

10. Para testar o aplicativo no logon único iniciado pelo IDP, efetue logon no Painel de Acesso (https://myapps.microsoft.com) e clique no bloco do aplicativo. No token SAML, você deve ver todas as funções atribuídas ao usuário com o nome da declaração fornecido.

## <a name="update-existing-role"></a>Atualizar função existente

1. Para atualizar uma função existente, execute as etapas a seguir -

    a. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    b. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.
    
    c. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário, utilizando a consulta a seguir:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se você estiver usando vários diretórios, siga este padrão `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Da lista de entidades de serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Pesquise a **ID de objeto** que você copiou da página Propriedades e use a consulta a seguir para acessar a respectiva Entidade de Serviço.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Extraia a propriedade appRoles do objeto da entidade de serviço.
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Para atualizar a função existente, siga as etapas abaixo:

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Altere o método de **GET** para **PATCH**.

    * Copie as funções existentes do aplicativo e cole-as no **Corpo da Solicitação**.
    
    * Atualize o valor da função, substituindo a **Descrição da função**, o **Valor da função** e o **Nome para exibição da função** de acordo com o requisito da organização.
    
    * Após atualizar todas as funções necessárias, clique em **Executar Consulta**.
        
## <a name="delete-existing-role"></a>Excluir função existente

Para excluir uma função existente, execute as etapas a seguir:

a. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

b. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.

c. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário, utilizando a consulta a seguir:
    
`https://graph.microsoft.com/beta/servicePrincipals`
    
Se você estiver usando vários diretórios, siga este padrão `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
d. Da lista de entidades de serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Pesquise a **ID de objeto** que você copiou da página Propriedades e use a consulta a seguir para acessar a respectiva Entidade de Serviço.
     
`https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
e. Extraia a propriedade appRoles do objeto da entidade de serviço.
    
![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

f. Para excluir a função existente, siga as etapas abaixo:

![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

Altere o método de **GET** para **PATCH**.

Copie as funções existentes do aplicativo e cole-as no **Corpo da Solicitação**.
    
Definir o valor **IsEnabled** como **falso** para a função que deseja excluir

Clique em **Executar Consulta**.
    
> [!NOTE] 
> Certifique-se de que você tenha a função de usuário **msiam_access** e que a ID esteja correspondendo na função gerada.
    
g. Após fazer o processo acima, mantenha o método como **PATCH** e cole o conteúdo da função restante no **Corpo da Solicitação** e clique em **Executar Consulta**.
    
![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

h. Depois de executar a consulta, a função será excluída.
    
> [!NOTE]
> A função deve ser desabilitada primeiro, antes que possa ser removida. 

## <a name="next-steps"></a>Próximas etapas

Consulte a [Documentação do Aplicativo ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-tutorial-list) para as etapas adicionais.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
