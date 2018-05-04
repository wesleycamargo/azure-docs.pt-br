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
ms.openlocfilehash: d869a21230661e473ffff6bbdb1ea29b1ea336d5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
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

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a **ID de Objeto**.

    ![Página Propriedades](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.PNG)

6. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    a. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.

    b. Você precisa ter permissões suficientes para criar as funções. Clique em **modificar permissões** para obter as permissões necessárias. 

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Selecione as permissões da lista (se você já não tiver) a seguir e clique em "Modificar Permissões" 

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Isso pedirá para fazer logon novamente e aceitar o consentimento. Depois de aceitar o consentimento, você estará logado no sistema novamente.

    e. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário, utilizando a consulta a seguir:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se você estiver usando vários diretórios, siga este padrão `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Da lista de entidades de serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Pesquise a **ID de objeto** que você copiou da página Propriedades e use a consulta a seguir para acessar a respectiva Entidade de Serviço.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Extraia a propriedade appRoles do objeto da entidade de serviço. 

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Se você estiver usando o aplicativo personalizado (fora da galeria), você verá as funções padrão - Usuário e msiam_access. No caso de aplicativo de galeria, o msiam_access é apenas a função padrão. Você não precisa fazer qualquer alteração nas funções padrão.

    h. Agora, é necessário gerar novas funções para aplicativo. 

    i. Abaixo, um exemplo do objeto appRoles. Crie um objeto semelhante para adicionar as funções que você deseja para o aplicativo. 

    ```
    {
       "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Administrators Only",
            "displayName": "Admin",
            "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "Administrator"
        }
    ],
    }
    ```
    > [!Note]
    > Você pode apenas adicionar as novas funções depois do **msiam_access** para a operação de patch. Além disso, você pode adicionar quantas regras quiser de acordo com a necessidade da sua Organização. O Microsoft Azure AD enviará o **valor** dessas funções conforme o valor de solicitação na resposta SAML.
    
    j. Volte ao Explorador Graph e altere o método de **GET** para **PATCH**. Atualize o objeto da Entidade de Serviço para ter as funções desejadas, atualizando a propriedade appRoles semelhante àquela exibida acima no exemplo. Clique em **Executar Consulta** para executar o operação de patch. Uma mensagem confirma a criação da função.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Após a atualização da entidade de serviço com mais funções, será possível atribuir usuários às respectivas funções. Isso pode ser feito, acessando o Portal e navegando até o respectivo aplicativo. Clique na guia **Usuários e Grupos** na parte superior. Isso relaciona todos os usuários e grupos que já foram atribuídos ao aplicativo. Você pode adicionar novos usuários na nova função e também pode selecionar o usuário existente e clicar em **Editar** para alterar a função.

    ![Configurar logon único Add](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Para atribuir a função ao qualquer usuário, selecione a nova função e clique no botão **Atribuir** na parte inferior da página.

    ![Configurar logon único Add](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Observe que você precisa atualizar a sua sessão no Portal do Azure para ver as novas funções.

8. Após atribuir funções aos usuários, será necessário atualizar a tabela **Atributos** para definir o mapeamento personalizado da declaração **função**.

9. Na seção **Atributos do Usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML como mostra a imagem e execute as etapas a seguir:
    
    | Nome do atributo | Valor do atributo |
    | -------------- | ----------------|    
    | Nome da função      | user.assignedrole |

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Configurar o atributo Logon único](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome**, digite o nome do atributo conforme necessário. Neste exemplo, nós usamos o **Nome da Função** como nome da declaração.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Deixe o **Namespace** em branco.
    
    e. Clique em **OK**.

10. Para testar o aplicativo no logon único iniciado pelo IDP, efetue logon no Painel de Acesso (https://myapps.microsoft.com) e clique no bloco do aplicativo. No token SAML, você deve ver todas as funções atribuídas ao usuário com o nome da declaração fornecido.

## <a name="update-existing-role"></a>Atualizar função existente

Para atualizar uma função existente, execute as etapas a seguir -

1. Abrir o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer).

2. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.
    
3. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário, utilizando a consulta a seguir:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se você estiver usando vários diretórios, siga este padrão `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Da lista de Entidades de Serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Pesquise a **ID de objeto** que você copiou da página Propriedades e use a consulta a seguir para acessar a respectiva Entidade de Serviço.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extraia a propriedade appRoles do objeto da entidade de serviço.
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Para atualizar a função existente, siga as etapas abaixo:

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Altere o método de **GET** para **PATCH**.

    * Copie as funções existentes e cole-as no **Corpo da Solicitação**.

    * Atualize o valor da função atualizando a **Descrição da Função**, **Valor da função** ou **Função displayname** conforme necessário.

    * Após atualizar todas as funções necessárias, clique em **Executar Consulta**.
        
## <a name="delete-existing-role"></a>Excluir função existente

Para excluir uma função existente, execute as etapas a seguir:

1. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

2. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.

3. Altere a versão para **beta** e busque a lista de entidades de serviço do locatário, utilizando a consulta a seguir:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Se você estiver usando vários diretórios, siga este padrão `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Da lista de entidades de serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Pesquise a **ID de objeto** que você copiou da página Propriedades e use a consulta a seguir para acessar a respectiva Entidade de Serviço.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Extraia a propriedade appRoles do objeto da entidade de serviço.
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Para excluir a função existente, siga as etapas abaixo:

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Altere o método de **GET** para **PATCH**.

    * Copie as funções existentes do aplicativo e cole-as no **Corpo da Solicitação**.
        
    * Definir o valor **IsEnabled** como **falso** para a função que deseja excluir

    * Clique em **Executar Consulta**.
    
    > [!NOTE] 
    > Certifique-se de que você tenha a função de usuário **msiam_access** e que a ID esteja correspondendo na função gerada.
    
7. Uma vez que a função é desabilitada, exclua o bloco da função da seção appRoles, mantenha o método como **PATCH** e clique em **Run Query**.
    
8. Depois de executar a consulta, a função será excluída.
    
    > [!NOTE]
    > A função deve ser desabilitada primeiro, antes que possa ser removida. 

## <a name="next-steps"></a>Próximas etapas

Consulte a [Documentação do Aplicativo ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) para as etapas adicionais.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
