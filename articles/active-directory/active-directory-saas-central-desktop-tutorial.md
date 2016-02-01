<properties 
    pageTitle="Tutorial: Integração do Active Directory do Azure ao Central Desktop | Microsoft Azure" 
    description="Saiba como usar o Central Desktop com o Active Directory do Azure para habilitar o logon único, provisionamento automatizado e muito mais!" 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#Tutorial: Integração do Active Directory do Azure ao Central Desktop

O objetivo deste tutorial é mostrar a integração do Azure ao Central Desktop. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura habilitada para logon único do Central Desktop/locatário do Central Desktop

O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos para o Central Desktop
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Cenário")
##Habilitando a integração de aplicativos para o Central Desktop

O objetivo desta seção é descrever como habilitar a integração de aplicativos para o Central Desktop.

###Para habilitar a integração de aplicativos para o Central Desktop, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **Central Desktop**.

    ![Galeria de aplicativos](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **Central Desktop** e clique em **Concluir** para adicionar o aplicativo.

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
##Configurando o logon único

O objetivo desta seção é descrever como permitir que os usuários se autentiquem no Central Desktop com a respectiva conta do AD do Azure usando federação baseada no protocolo SAML. Como parte deste procedimento, é necessário carregar um certificado codificado em base 64 no locatário do Desktop Central. Se você não estiver familiarizado com esse procedimento, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o).



###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração de aplicativos do **Central Desktop**, clique em **Configurar logon único** para abrir o diálogo **Configurar Logon Único**.

    ![Configurar o logon único](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Configurar logon único")

2.  Na página **Como você deseja que os usuários façam logon no Central Desktop**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar logon único](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Configurar logon único")

3.  Na página **Configurar URL do Aplicativo**, realize as etapas a seguir e clique em **Avançar**:

    -   Na caixa de texto **URL de Logon do Central Desktop**, digite a URL do seu locatário do Central Desktop (por exemplo: **http://contoso.centraldesktop.com*)).
-   Na caixa de texto URL de Resposta do Central Desktop, digite a URL AssertionConsumerService do Central Desktop (por exemplo: https://contoso.centraldesktop.com/saml2-assertion.php)).

    >[AZURE.NOTE]Você pode obter o valor nos metadados do Central Desktop (por exemplo: **http://contoso.centraldesktop.com*)).

    ![Configurar a URL do aplicativo](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "Configurar a URL do aplicativo")

4.  Na página **Configurar logon único no Central Desktop**, para baixar seu certificado, clique em **Baixar certificado** e salve o arquivo de certificado no computador.

    ![Configurar logon único](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Configurar o logon único")

5.  Faça logon em seu locatário do **Central Desktop**.

6.  Vá para **Configurações**, clique em **Avançadas** e em **Logon Único**.

    ![Configuração — Avançada](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Configuração — Avançada")

7.  Na página **Configurações de Logon Único**, realize as seguintes etapas:

    ![Configurações de Logon Único](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Configurações de Logon Único")

    1.  Selecione **Habilitar Logon Único do SAML v2**.
    2.  No portal do AD do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL do Emissor** e cole-o na caixa de texto **URL de SSO**.
    3.  No portal do AD do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL de Logon Remoto** e cole-o na caixa de texto **URL de SSO**.
    4.  No portal do AD do Azure, na página **Configurar logon único no Central Desktop**, copie o valor da **URL do Serviço de Saída Única** e cole-o na caixa de texto **URL de Logoff de SSO**.

8.  Na seção **Método de Verificação de Assinatura de Mensagem**, realize as seguintes etapas:

    ![Método de Verificação de Assinatura da Mensagem](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Método de Verificação de Assinatura da Mensagem")

    1.  Selecione **Certificado**.
    2.  Na lista **Certificado de SSO**, selecione **RSH SHA256**.
    3.  Crie um arquivo de texto por meio do certificado baixado, copie o conteúdo do arquivo de texto e cole-o no campo **Certificado de SSO**.  

        >[AZURE.TIP]Para obter mais detalhes, veja [Como converter um certificado binário em um arquivo de texto](http://youtu.be/PlgrzUZ-Y1o)

    4.  Selecione **Exibir um link para a sua página de logon do SAMLv2**.

9.  Clique em **Atualizar**.

10. No portal do AD do Azure, selecione a confirmação de configuração de logon único e, em seguida, clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Configurar o logon único")
##Configurando o provisionamento de usuários

Para que os usuários do AAD possam fazer logon, eles devem ser provisionados no aplicativo Central Desktop. Esta seção descreve como criar contas de usuário do AAD no Central Desktop.

###Para provisionar contas de usuário no Central Desktop:

1.  Faça logon no seu locatário do Central Desktop.

2.  Vá para **Pessoas > Membros Internos**.

3.  Clique em **Adicionar Membros Internos**.

    ![Pessoas](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "Pessoas")

4.  Na caixa de texto **Endereço de Email dos Novos Membros**, digite uma conta do AAD que você deseja provisionar e clique em **Avançar**.

    ![Endereços de Email dos Novos Membros](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Endereços de Email dos Novos Membros")

5.  Clique em **Adicionar Membro(s) interno(s)**.

    ![Adicionar Membro Interno](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Adicionar Membro Interno")

    >[AZURE.NOTE]Os usuários que você adicionou receberão um email com um link de confirmação em que eles precisam clicar para ativar a conta.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do Central Desktop ou as APIs fornecidas pelo Central Desktop para provisionar as contas de usuário do AAD.

##Atribuindo usuários

Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do Azure AD que você deseja que usem seu aplicativo.

###Para atribuir usuários ao Central Desktop, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **Central Desktop**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e, em seguida, clique em **Sim** para confirmar a atribuição.

    ![Sim](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Sim")

Se você quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, veja [Introdução ao Painel de Acesso](active-directory-saas-access-panel-introduction.md).

<!---HONumber=AcomDC_0121_2016-->