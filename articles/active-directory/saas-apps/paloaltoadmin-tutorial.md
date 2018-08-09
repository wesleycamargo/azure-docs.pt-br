---
title: 'Tutorial: integração do Azure Active Directory com o Palo Alto Networks – Admin UI | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Palo Alto Networks – Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: fed368c0df265495d9fee764f86825957fae8bab
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/02/2018
ms.locfileid: "39447417"
---
# <a name="integrate-azure-active-directory-with-palo-alto-networks---admin-ui"></a>Integrar o Azure Active Directory ao Palo Alto Networks - Admin UI

Neste tutorial, você aprenderá como integrar o Azure AD (Azure Active Directory) ao Palo Alto Networks – Admin UI.

Ao integrar o Azure AD ao Palo Alto Networks - Admin UI, você obtém os seguintes benefícios:

- No Azure AD, é possível controlar quem tem acesso ao Palo Alto Networks – Admin UI.
- Você pode permitir que os usuários façam logon automaticamente no Palo Alto Networks – Admin UI (logon único, ou SSO) com as respectivas contas do Azure AD.
- Você pode gerenciar suas contas em um único local, o portal clássico do Azure.

Para saber mais sobre a integração de aplicativos SaaS ao Azure AD, consulte [O que é o acesso de aplicativos e o logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com o Palo Alto Networks – Admin UI, você precisa dos seguintes itens:

- Uma assinatura do AD do Azure
- Um Firewall de última geração de redes Palo Alto ou Panorama (sistema de gerenciamento centralizado de firewalls)

> [!NOTE]
> Ao testar as etapas deste tutorial, recomendamos que você *não* use um ambiente de produção.

Para testar as etapas neste tutorial, siga estas recomendações:

- Não use o ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, você pode [obter uma versão de avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Descrição do cenário
Neste tutorial, você testará o logon único do Azure AD em um ambiente de teste. 

O cenário descrito neste tutorial consiste em dois blocos de construção principais:

* Adicionando o Palo Alto Networks – Admin UI da Galeria
* configurar e testar o logon único do AD do Azure

## <a name="add-palo-alto-networks---admin-ui-from-the-gallery"></a>Adicionar o Palo Alto Networks - Admin UI da galeria
Para configurar a integração do Azure AD ao Palo Alto Networks - Admin UI, adicione o Palo Alto Networks - Admin UI da galeria à sua lista de aplicativos SaaS gerenciados, da seguinte maneira:

1. No [Portal do Azure](https://portal.azure.com), no painel esquerdo, selecione **Azure Active Directory**. 

    ![O botão Azure Active Directory][1]

1. Selecione **Aplicativos empresariais** > **Todos os aplicativos**.

    ![A janela “Aplicativos empresariais”][2]
    
1. Para adicionar um novo aplicativo, selecione o botão **Novo aplicativo** na parte superior da janela.

    ![O botão “Novo aplicativo”][3]

1. Na caixa de pesquisa, digite **Palo Alto Networks - Admin UI**, selecione **Palo Alto Networks - Admin UI** no painel de resultados e, em seguida, selecione **Adicionar**.

    ![Palo Alto Networks – Admin UI na lista de resultados](./media/paloaltoadmin-tutorial/tutorial_step4-add-from-the-gallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configurar e testar logon único do Azure AD

Nesta seção, você configurará e testará o logon único do Azure AD com o Palo Alto Networks - Admin UI, com base em uma usuária de teste chamada "Brenda Fernandes."

Para que o logon único funcione, o Azure AD precisa identificar o usuário do Palo Alto Networks - Admin UI e sua contraparte do Azure AD. Em outras palavras, é necessário estabelecer uma relação de vínculo entre um usuário do Azure AD e o usuário relacionado do Palo Alto Networks - Admin UI.

Para estabelecer a relação de vínculo, atribua como o *Nome de usuário* do Palo Alto Networks - Admin UI o valor do *nome de usuário* no Azure AD.

Para configurar e testar o logon único do Azure AD com o Palo Alto Networks - Admin UI, complete os blocos de construção nas próximas cinco seções.

### <a name="configure-azure-ad-single-sign-on"></a>Configurar o logon único do Azure AD

Habilite o logon único do Azure AD no portal do Azure e configure o logon único no seu aplicativo Palo Alto Networks – Admin UI da seguinte maneira:

1. No portal do Azure, na página de integração de aplicativo **Palo Alto Networks – Admin UI**, selecione **Logon Único**.

    ![O link “Logon único”][4]

1. Na janela **Logon único**, na caixa **Modo Logon único**, selecione **Logon baseado em SAML**.
 
    ![A janela “Logon único”](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_samlbase.png)

1. Em **Domínio e URLs do Palo Alto Networks - Admin UI**, faça o seguinte:

    ![Informações de logon único de "Domínio e URLs do Palo Alto Networks - Admin UI"](./media/paloaltoadmin-tutorial/tutorial_general_show_advanced_url.png)
    
    a. Na caixa **URL de logon**, digite uma URL no seguinte formato: *https://\<Customer Firewall FQDN>/php/login.php*.

    b. Na caixa **Identificador**, digite uma URL no seguinte formato: *https://\<Customer Firewall FQDN>:443/SAML20/SP*.
    
    c. Na caixa **URL de Resposta**, digite a URL do Serviço do Consumidor de Declaração (ACS) no seguinte formato: *https://\<Customer Firewall FQDN>:443/SAML20/SP/ACS*.
    
    > [!NOTE] 
    > Os valores anteriores não são reais. Atualize-os com a URL de logon e o identificador reais. Para obter esses valores, entre em contato com a [Equipe de suporte ao cliente do Palo Alto Networks - Admin UI](https://support.paloaltonetworks.com/support). 
 
1. Como os aplicativos do Palo Alto - Admin UI esperam as instruções de declaração SAML em um formato específico, configure as declarações conforme mostrado na imagem a seguir. Gerencie os valores desses atributos na seção **Atributos de Usuário** da página **Integração de Aplicação** da seguinte maneira:
    
    ![A lista de Atributos de Token SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_attribute.png)
    
   > [!NOTE]
   > Como os valores de atributo são apenas exemplos, mapeie os valores apropriados para *username* e *adminrole*. Há outro atributo opcional, *accessdomain*, que é usado para restringir o acesso de administrador para sistemas virtuais específicos no firewall.
   >
        
    | Nome do atributo | Valor do atributo |
    | --- | --- |    
    | Nome de Usuário | user.userprincipalname |
    | adminrole | customadmin |

    a. Selecione **Adicionar Atributo**.  
    
    ![O botão "Adicionar atributo"](./media/paloaltoadmin-tutorial/tutorial_attribute_04.png)

    A janela **Adicionar atributo** será aberta.

    ![A janela de "Adicionar atributo"](./media/paloaltoadmin-tutorial/tutorial_attribute_05.png)
    
    b. Na caixa **Nome**, digite o nome do atributo mostrado para essa linha.
    
    c. Na caixa **Valor**, digite o valor de atributo mostrado para aquela linha.
    
    d. Selecione **OK**.

    > [!NOTE]
    > Para obter mais informações sobre os atributos, consulte os seguintes artigos:
    > * [Perfil de função administrativa para Interface do Usuário de Administrador (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Domínio de dispositivo de acesso para Interface do Usuário de Administrador (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)
    >

1. Em **Certificado de Autenticação SAML**, selecione **XML de Metadados** e, em seguida, selecione **Salvar**.

    ![O link de download do XML de Metadados](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_certificate.png) 

    ![O botão Salvar](./media/paloaltoadmin-tutorial/tutorial_general_400.png)

1. Abra a Interface do Usuário do Administrador do Firewall do Palo Alto Networks como um administrador em outra janela do navegador.

1. Selecione a guia **Dispositivo**.

    ![A guia Dispositivo](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

1. No painel esquerdo, selecione **Provedor de Identidade SAML** e, em seguida, selecione **Importar** para importar o arquivo de metadados.

    ![O botão Importar arquivo de metadados](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

1. Na janela do **Perfil do Servidor de Provedor de Identidade SAML**, faça o seguinte:

    ![A janela "Perfil do Servidor de Provedor de Identidade SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Na caixa **Nome do Perfil**, forneça um nome (por exemplo, **AzureAD Admin UI**).
    
    b. Em **Metadados do Provedor de Identidade**, selecione **Procurar** e selecione o arquivo metadata.xml que você baixou anteriormente no portal do Azure.
    
    c. Limpe a caixa de seleção **Validar Certificado do Provedor de Identidade**.
    
    d. Selecione **OK**.
    
    e. Para confirmar as configurações de firewall, selecione **Confirmar**.

1. No painel esquerdo, selecione **Provedor de Identidade SAML**e, em seguida, selecione o perfil do Provedor de Identidade SAML (por exemplo, **AzureAD Admin UI**) que você criou na etapa anterior. 

    ![O Perfil de Provedor de Identidade do SAML](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

1. Na janela do **Perfil do Servidor de Provedor de Identidade SAML**, faça o seguinte:

    ![A janela "Perfil do Servidor de Provedor de Identidade do SAML"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. Na caixa **URL de SLO do Provedor de Identidade** , remova a URL de SLO importada anteriormente e adicione a seguinte URL: **https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0**.
  
    b. Selecione **OK**.

1. Na Interface do Usuário do Administrador do Firewall do Palo Alto Networks, selecione **Dispositivo**e, em seguida, selecione **Funções do Administrador**.

1. Selecione o botão **Adicionar**. 

1. Na janela **Perfil da Função de Administrador**, na caixa **Nome**, forneça um nome para a função de administrador (por exemplo, **fwadmin**).  
    O nome da função de administrador deve corresponder ao nome de atributo da Função de Administrador de SAML enviado pelo Provedor de Identidade. O nome e valor da função de administrador foram criados na etapa 4.

    ![Configurar Função do Administrador de Redes Palo Alto](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
1. Na Interface do Usuário do Administrador do Firewall, selecione **Dispositivo** e em seguida, selecione **Perfil de Autenticação**.

1. Selecione o botão **Adicionar**. 

1. Na janela **Perfil de Autenticação**, faça o seguinte: 

    ![A janela "Perfil de Autenticação"](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Na caixa de texto **Nome**, forneça um nome (por exemplo, **AzureSAML_Admin_AuthProfile**).
    
    b. Na lista suspensa **Tipo**, escolha **SAML**. 
   
    c. Na lista suspensa **Perfil do Servidor de Provedor de Identidade**, selecione o perfil do Servidor de Provedor de Identidade SAML apropriado (por exemplo, **AzureAD Admin UI**).
   
    c. Selecione a caixa de seleção **Ativar o Logout Único**.
    
    d. Na caixa **Atributo da Função de Administrador**, digite o nome do atributo (por exemplo, **adminrole**). 
    
    e. Selecione a guia **Avançado** e, em seguida, na **Lista de Permissões**, selecione **Adicionar**. 
    
    ![Botão Adicionar na guia Avançado](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)
    
    f. Selecione a caixa de seleção **Todos**, ou selecione os usuários e grupos que podem se autenticar com esse perfil.  
    Quando um usuário é autenticado, o firewall faz a correspondência do nome de usuário ou do grupo associado com base nas entradas dessa lista. Se você não adicionar entradas, nenhum usuário poder autenticar.

    g. Selecione **OK**.

1. Para habilitar os administradores a usarem o SSO do SAML usando o Azure, selecione **Dispositivo** > **Instalação**. No painel **Instalação**, selecione a guia **Gerenciamento** e, em seguida, em **Configurações de Autenticação**, selecione o botão **Configurações** ("engrenagem"). 

 ![O botão Configurações](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

1. Selecione o perfil de Autenticação SAML que você criou na etapa 17 (por exemplo, **AzureSAML_Admin_AuthProfile**).

 ![O campo Perfil de Autenticação](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

1. Selecione **OK**.

1. Para confirmar a configuração, selecione **Confirmar**.


> [!TIP]
> Ao configurar o aplicativo, você pode ler as instruções anteriores em uma versão concisa no [portal do Azure](https://portal.azure.com). Depois de adicionar esse aplicativo na seção **Active Directory** > **Aplicativos Empresariais**, selecione a guia **Logon Único** e, em seguida, acesse a documentação inserida na seção **Configuração**, na parte inferior. Para saber mais informações sobre o recurso de documentação inserida, consulte [Documentação inserida do Microsoft Azure Active Directory]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Criar um usuário de teste do Azure AD

Nesta seção, você cria o usuário de teste Brenda Fernandes no portal do Azure fazendo o seguinte:

![Criar um usuário de teste do Azure AD][100]

1. No Portal do Azure, no painel esquerdo, selecione **Azure Active Directory**.

    ![O link do Azure Active Directory](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Para exibir uma lista de usuários atuais, selecione **Usuários e grupos** > **Todos os usuários**.

    ![Os links “Usuários e grupos” e “Todos os usuários”](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Na parte superior da janela **Todos os usuários**, selecione **Adicionar**.

    ![O botão Adicionar](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    A janela **Usuário** abre.

1. Na janela **Usuário**, faça o seguinte:

    ![A janela de usuário](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Na caixa **Nome**, digite **BrendaFernandes**.

    b. Na caixa **Nome de usuário**, digite o endereço de email do usuário Brenda Fernandes.

    c. Marque a caixa de seleção **Mostrar Senha** e, em seguida, anote o valor exibido na caixa **Senha**.

    d. Selecione **Criar**.
 
### <a name="create-a-palo-alto-networks---admin-ui-test-user"></a>Criar um usuário de teste do Palo Alto Networks – Admin UI

O Palo Alto Networks - Admin UI dá suporte ao provisionamento de usuário just-in-time. Se um usuário ainda não existir, ele é automaticamente criado no sistema após uma autenticação bem-sucedida. Nenhuma ação é requerida para criar o usuário.

### <a name="assign-the-azure-ad-test-user"></a>Atribuir o usuário de teste do Azure AD

Nesta seção, você permitirá que Brenda Fernandes use o logon único do Azure concedendo-lhe acesso ao Palo Alto Networks - Admin UI. Para fazer isso, faça o seguinte:

![Atribuir a função de usuário][200] 

1. No portal do Azure, abra a exibição de **Aplicativos**, acesse a exibição de **Diretório** e, em seguida, selecione **Aplicativos Empresariais** > **Todos os aplicativos**.

    ![Os links “Aplicativos empresariais” e “Todos os aplicativos”][201] 

1. Na lista de **Aplicativos**, selecione **Palo Alto Networks - Admin UI**.

    ![O link Palo Alto Networks - Admin UI](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_app.png)  

1. No painel esquerdo, selecione **Usuários e grupos**.

    ![O link “Usuários e grupos”][202]

1. Selecione **Adicionar** e, em seguida, no painel **Adicionar Atribuição**, selecione **Usuários e grupos**.

    ![O painel Adicionar Atribuição][203]

1. Na janela **Usuários e grupos**, na lista **Usuários**, selecione **Britta Simon**.

1. Escolha o botão **Selecionar**.

1. Na janela **Adicionar Atribuição**, selecione **Atribuir**.
    
### <a name="test-single-sign-on"></a>Testar logon único

Nesta seção, você testará sua configuração de logon único do Azure AD usando o Painel de Acesso.

Quando você clicar no bloco Palo Alto Networks - Admin UI no painel de acesso, seu logon deve ser realizado automaticamente no seu aplicativo Palo Alto Networks - Admin UI.

Para saber mais sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Recursos adicionais

* [Lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](tutorial-list.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/paloaltoadmin-tutorial/tutorial_general_01.png
[2]: ./media/paloaltoadmin-tutorial/tutorial_general_02.png
[3]: ./media/paloaltoadmin-tutorial/tutorial_general_03.png
[4]: ./media/paloaltoadmin-tutorial/tutorial_general_04.png

[100]: ./media/paloaltoadmin-tutorial/tutorial_general_100.png

[200]: ./media/paloaltoadmin-tutorial/tutorial_general_200.png
[201]: ./media/paloaltoadmin-tutorial/tutorial_general_201.png
[202]: ./media/paloaltoadmin-tutorial/tutorial_general_202.png
[203]: ./media/paloaltoadmin-tutorial/tutorial_general_203.png

