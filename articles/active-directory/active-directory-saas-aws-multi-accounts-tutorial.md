---
title: 'Tutorial: Integração do Azure Active Directory com o Amazon Web Services (AWS) para conectar várias contas | Microsoft Docs'
description: Saiba como configurar o logon único entre o Microsoft Azure AD e várias contas do Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: 929caab0aafdad24062e372e458f16a5f36cce73
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Tutorial: Integração do Azure Active Directory com várias contas Amazon Web Services (AWS)

Neste tutorial, você aprenderá a integrar o Azure Active Directory (Microsoft Azure AD) a várias contas do AWS (Amazon Web Services).

A integração do AWS (Amazon Web Services) ao Azure AD oferece os seguintes benefícios:

- Você pode controlar no Microsoft Azure AD quem tem acesso ao AWS (Amazon Web Services).
- Você pode habilitar seus usuários a fazerem logon automaticamente no AWS (Amazon Web Services) (logon único) com suas contas do Microsoft Azure AD.
- Você pode gerenciar suas contas em um único local central – o portal do Azure.

Para conhecer mais detalhadamente a integração de aplicativos de SaaS ao Azure AD, consulte [o que é o acesso a aplicativos e logon único com o Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>pré-requisitos

Para configurar a integração do Azure AD com o AWS (Amazon Web Services), você precisa dos seguintes itens:

- Obtenha uma assinatura básica ou premium do Microsoft Azure AD
- Contas habilitadas de logon único do AWS (Amazon Web Services)

> [!NOTE]
> Para testar as etapas deste tutorial, nós não recomendamos o uso de um ambiente de produção.

Para testar as etapas deste tutorial, você deve seguir estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. O cenário descrito neste tutorial consiste em dois blocos de construção principais:

1. Adicionar o AWS (Amazon Web Services) da galeria
2. configurar e testar o logon único do AD do Azure

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Adicionar o AWS (Amazon Web Services) da galeria
Para configurar a integração do AWS (Amazon Web Services) com o Azure AD, você precisa adicionar o AWS (Amazon Web Services), por meio da galeria, à sua lista de aplicativos de SaaS gerenciados.

**Para adicionar o AWS (Amazon Web Services) da galeria, execute as seguintes etapas:**

1. No **[Portal do Azure](https://portal.azure.com)**, no painel navegação à esquerda, clique no ícone **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

2. Navegue até **aplicativos empresariais**. Em seguida, vá para **todos os aplicativos**.

    ![A folha Aplicativos empresariais][2]
    
3. Clique no botão **Novo aplicativo** na parte superior da caixa de diálogo para adicionar o novo aplicativo.

    ![O botão Novo aplicativo][3]

4. Na caixa de pesquisa, digite **AWS (Amazon Web Services)**, selecione **AWS (Amazon Web Services)** no painel de resultados e clique no botão **Adicionar** para adicionar o aplicativo.

    ![AWS (Amazon Web Services) na lista de resultados](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Depois que o aplicativo for adicionado, vá para a página **Propriedades** e copie a **ID de Objeto**.

    ![AWS (Amazon Web Services) na lista de resultados](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você vai configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), com base em um usuário de teste chamado "Brenda Fernandes".

Para que o logon único funcione, o Azure AD precisa saber qual usuário do AWS (Amazon Web Services) é equivalente a um usuário do Azure AD. Em outras palavras, uma relação de link entre um usuário do Azure AD e o usuário relacionado no serviço AWS (Amazon Web Services) deve ser estabelecida.

No Amazon Web Services (AWS), atribua o valor do **nome de usuário** no Microsoft Azure AD como o valor do **Nome de usuário** para estabelecer a relação de vínculo.

Para configurar e testar o logon único do Azure AD com o AWS (Amazon Web Services), você precisa concluir os seguintes blocos de construção:

1. **[Configurar o logon único do Azure AD](#configure-azure-ad-single-sign-on)** – para habilitar seus usuários a usar esse recurso.
2. **[Teste o logon único](#test-single-sign-on)** – para verificar se a configuração funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Nesta seção, você vai habilitar o logon único do Azure AD no Portal do Azure e configurar o logon único em seu aplicativo AWS (Amazon Web Services).

**Para configurar o logon único do Azure AD com o AWS (Amazon Web Services), execute as seguintes etapas:**

1. No portal do Azure, na página de integração de aplicativos do **AWS (Amazon Web Services)**, clique em **Logon único**.

    ![Link Configurar logon único][4]

2. Na caixa de diálogo **Logon único**, selecione **Modo** como **Logon baseado em SAML** para habilitar o logon único.
 
    ![Caixa de diálogo Logon único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na seção **Domínio e URLs do AWS (Amazon Web Services)**, o usuário não precisa seguir as etapas, uma vez que o aplicativo já está pré-integrado ao Azure.

    ![Informações de logon único de Domínio e URLs do AWS (Amazon Web Services)](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. O aplicativo AWS (Amazon Web Services) espera que as declarações SAML estejam em um formato específico. Configure as declarações a seguir para este aplicativo. Você pode gerenciar os valores desses atributos da seção "**Atributos de Usuário**" na página de integração do aplicativo. A captura de tela a seguir mostra um exemplo disso.

    ![Configurar o atributo Logon único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. Na seção **Atributos do usuário**, na caixa de diálogo **Logon único**, configure o atributo do token SAML na imagem acima e siga as etapas abaixo:
    
    | Nome do atributo  | Valor do atributo | Namespace |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Função            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Você precisa configurar o provisionamento do usuário no Azure AD para buscar todas as funções no Console do AWS. Veja as etapas de provisionamento abaixo.

    a. Clique em **Adicionar atributo** para abrir o diálogo **Adicionar Atributo**.

    ![Configurar logon único Add](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Configurar o atributo Logon único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. Na caixa de texto **Nome** , digite o nome do atributo mostrado para essa linha.

    c. Na lista **Valor**, digite o valor do atributo mostrado para essa linha.

    d. Na caixa de texto **Namespace**, digite o valor de namespace mostrado para essa linha.
    
    d. Clique em **OK**.

6. Na seção **Certificado de Autenticação SAML**, clique em **Metadados XML** e, em seguida, salve o arquivo de metadados em seu computador.

    ![O link de download do Certificado](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Clique no botão **Salvar** .

    ![Botão Salvar em Configurar Logon Único](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. Em uma janela de navegador diferente, entre no site de sua empresa do AWS (Amazon Web Services) como Administrador.

9. Clique na **Página inicial do AWS**.
   
    ![Configurar página inicial de logon único][11]

10. Clique em **IAM** (Gerenciamento de Identidade e Acesso). 
   
    ![Configurar identidade de logon único][12]

11. Clique em **Provedores de identidade**, e, em seguida, clique em **Criar provedor**. 
   
    ![Configurar provedor de logon único][13]

12. Na página da caixa de diálogo **Configurar provedor** , execute as seguintes etapas: 
   
    ![Configurar diálogo logon único][14]
 
    a. Como **Tipo de provedor**, selecione **SAML**.

    b. Na caixa de texto **Nome do provedor**, digite um nome de provedor (por exemplo: *WAAD*).

    c. Para carregar seu **arquivo de metadados** baixado do Portal do Azure, clique em **Escolher Arquivo**.

    d. Clique em **Próxima etapa**.

13. Na página de diálogo **Verificar informações do provedor**, clique em **Criar**. 
    
    ![Configurar verificação de logon único][15]

14. Clique em **Funções** e, em seguida, clique em **Criar função**. 
    
    ![Configurar funções de logon único][16]

15. Na página **Criar função**, realize as seguintes etapas:  
    
    ![Configurar confiança de logon único][19] 

    a. Selecione **Federação do SAML 2.0** em **Selecionar tipo de entidade confiável**.

    b. Em **Escolher uma seção do provedor do SAML 2.0**, selecione o **provedor do SAML** criado anteriormente (por exemplo: *WAAD*)

    c. Selecione **Permitir acesso do Console de Gerenciamento do AWS e programação**.
  
    d. Clique em **Próximo: Permissões**.

16. Na caixa de diálogo **Anexar Políticas de Permissões** clique em **Próximo: Revisão**.  
    
    ![Configurar política de logon único][33]

17. Na caixa de diálogo **Examinar** , execute as seguintes etapas:   
    
    ![Configurar revisão de logon único][34] 

    a. Na caixa de texto **Nome da função**, insira o nome da função.

    b. Na caixa de texto **Descrição da função**, insira a descrição.

    a. Clique em **Criar função**.

    b. Crie quantas funções forem necessárias e mapeie-as para o Provedor de Identidade.

18. Saia da atual conta AWS e faça logon com outra conta em que você deseja configurar o logon único com o Microsoft Azure AD.

19. Execute as etapas de 9 a 17 para criar várias funções que você deseja configurar para esta conta. Se você tiver mais de duas contas, execute as mesmas etapas para todas as contas criarem funções para elas.

20. Depois que todas as funções são criadas nas contas, elas aparecem na lista **Funções** para essas contas.

    ![Instalação de funções](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. É necessário capturar todas as Funções ARN e Entidades Confiáveis para todas as funções através de todas as contas, que pode ser necessário mapear manualmente com o aplicativo Microsoft Azure AD. 

22. Clique nas funções para copiar os valores da **Função ARN** e das **Entidades Confiáveis**. Esses valores são necessários para todas as funções que você precisa criar no Microsoft Azure AD.

    ![Instalação de funções](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Execute a etapa acima para todas as funções em todas as contas e armazene todas elas em formato **de Função ARN, Entidades Confiáveis** em um bloco de notas. 

24. Abra o [Explorador do Graph do Microsoft Azure AD](https://developer.microsoft.com/graph/graph-explorer) em outra janela.

    a. Entre no site do Explorador do Graph usando as credenciais de Administrador/Coadministrador globais para o locatário.

    b. Você precisa ter permissões suficientes para criar as funções. Clique em **modificar permissões** para obter as permissões necessárias. 

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Selecione as permissões da lista (se você já não tiver) a seguir e clique em "Modificar Permissões" 

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Isso pedirá para fazer logon novamente e aceitar o consentimento. Depois de aceitar o consentimento, você estará logado no Explorador do Graph novamente.

    e. Alterar a lista suspensa de versão para **beta**. Para buscar todos as Entidades de Serviço a partir do seu locatário, use a consulta a seguir:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Se você estiver usando vários diretórios, você poderá usar o padrão a seguir, que tem o seu domínio primário nele`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Caixa de diálogo do explorador do Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. Da lista de Entidades de Serviço buscada, obtenha o que é necessário modificar. Você também pode usar o Ctrl+F para pesquisar o aplicativo de todos os ServicePrincipals listados. Você pode usar a consulta a seguir usando a **ID de objeto** que você copiou da página Propriedades do Microsoft Azure AD e usar a consulta a seguir para acessar a respectiva Entidade de Serviço.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extraia a propriedade appRoles do objeto da entidade de serviço. 

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Você pode apenas adicionar as novas funções depois do **msiam_access** para a operação de patch. Além disso, você pode adicionar quantas regras quiser de acordo com a necessidade da sua Organização. O Microsoft Azure AD enviará o **valor** dessas funções conforme o valor de solicitação na resposta SAML.
    
    j. Volte ao Explorador Graph e altere o método de **GET** para **PATCH**. Atualize o objeto da Entidade de Serviço para ter as funções desejadas, atualizando a propriedade appRoles semelhante àquela exibida acima no exemplo. Clique em **Executar Consulta** para executar o operação de patch. Uma mensagem confirma a criação da função do seu aplicativo Amazon Web Services.

    ![Caixa de diálogo do explorador do Graph](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Após a atualização da Entidade de Serviço com mais funções, será possível atribuir Usuários/Grupos às respectivas funções. Isso pode ser feito, acessando o portal e navegando até o aplicativo Amazon Web Services. Clique na guia **Usuários e Grupos** na parte superior. 

26. Recomendamos que você crie novos grupos para cada função AWS para poder atribuir essa função específica a esse grupo. Observe que este é um mapeamento de um grupo para uma função. Em seguida, você pode adicionar os membros que pertencem ao grupo.

27. Uma vez que os Grupos são criados, selecione o grupo e atribua ao aplicativo. 

    ![Configurar logon único Add](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Para atribuir a função ao grupo, selecione a função e clique no botão **Atribuir** na parte inferior da página.

    ![Configurar logon único Add](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Observe que você precisa atualizar a sua sessão no Portal do Azure para ver as novas funções.

### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Ao clicar no bloco AWS (Amazon Web Services) no Painel de Acesso, você deve entrar na página do aplicativo AWS (Amazon Web Services) com a opção para selecionar a função.

![Configurar logon único Add](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Você também pode verificar a resposta SAML para ver as funções sendo passadas como declarações.

![Configurar logon único Add](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

