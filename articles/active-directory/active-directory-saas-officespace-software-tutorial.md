<properties pageTitle="Tutorial: Integração do Active Directory do Azure com o OfficeSpace Software | Microsoft Azure" description="Saiba como usar o OfficeSpace Software com o Active Directory do Azure para habilitar o logon único, o provisionamento automatizado e muito mais!" services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#Tutorial: Integração do Active Directory do Azure com o OfficeSpace Software
>[AZURE.TIP]Para ver comentários, clique [aqui](http://go.microsoft.com/fwlink/?LinkId=524940).
  
O objetivo deste tutorial é mostrar a integração do Azure com o OfficeSpace Software. O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

-   Uma assinatura válida do Azure
-   Uma assinatura do OfficeSpace Software com logon único habilitado
  
Depois de concluir este tutorial, os usuários do AD do Azure atribuídos ao OfficeSpace Software poderão fazer logon único no aplicativo, em seu site de empresa da OfficeSpace Software (logon iniciado pelo provedor de serviços) ou usando a [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586)
  
O cenário descrito neste tutorial consiste nos seguintes blocos de construção:

1.  Habilitando a integração de aplicativos com o OfficeSpace Software
2.  Configurando o logon único
3.  Configurando o provisionamento de usuários
4.  Atribuindo usuários

![Cenário](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Cenário")
##Habilitando a integração de aplicativos com o OfficeSpace Software
  
O objetivo desta seção é descrever como habilitar a integração de aplicativos com o OfficeSpace Software.

###Para habilitar a integração de aplicativos com o OfficeSpace Software, execute as seguintes etapas:

1.  No Portal de Gerenciamento do Azure, no painel navegação à esquerda, clique em **Active Directory**.

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  Na lista **Diretório**, selecione o diretório para o qual você deseja habilitar a integração de diretórios.

3.  Para abrir a visualização dos aplicativos, na exibição do diretório, clique em **Aplicativos** no menu principal.

    ![Aplicativos](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Aplicativos")

4.  Clique em **Adicionar** na parte inferior da página.

    ![Adicionar aplicativo](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Adicionar aplicativo")

5.  Na caixa de diálogo **O que você deseja fazer**, clique em **Adicionar um aplicativo da galeria**.

    ![Adicionar um aplicativo da galeria](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Adicionar um aplicativo da galeria")

6.  Na **caixa de pesquisa**, digite **OfficeSpace Software**.

    ![Galeria de aplicativos](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Galeria de aplicativos")

7.  No painel de resultados, selecione **OfficeSpace Software** e clique em **Concluir** para adicionar o aplicativo.

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##Configurando o logon único
  
O objetivo desta seção é descrever como permitir que os usuários autentiquem no OfficeSpace Software com a própria conta no AD do Azure usando federação baseada no protocolo SAML. Configurar o logon único para o OfficeSpace Software exige que você recupere um valor de impressão digital de um certificado. Se você não estiver familiarizado com esse procedimento, consulte [Como recuperar o valor de impressão digital do certificado](http://youtu.be/YKQF266SAxI).

###Para configurar o logon único, execute as seguintes etapas:

1.  No portal do AD do Azure, na página de integração do aplicativo **OfficeSpace Software**, clique em **Configurar logon único** para abrir a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configurar o logon único")

2.  Na página **Como você deseja que os usuários façam logon no OfficeSpace Software**, selecione **Logon Único do AD do Microsoft Azure** e clique em **Avançar**.

    ![Configurar o logon único](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configurar o logon único")

3.  Na página **Configurar URL do Aplicativo**, na caixa de texto **URL de Logon do OfficeSpace Software**, digite a URL usada pelos seus usuários para fazer logon em seu aplicativo OfficeSpace Software (por exemplo: “ **https://company.officespacesoftware.com*") e clique em **Avançar**:

    ![Configurar a URL do Aplicativo](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configurar a URL do Aplicativo")

4.  Na página **Configurar logon único no OfficeSpace Software**, para baixar o certificado, clique em **Baixar certificado** e salve o arquivo do certificado em seu computador.

    ![Configurar o logon único](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configurar o logon único")

5.  Em uma janela diferente do navegador da Web, faça logon em seu site de empresa do OfficeSpace Software como administrador.

6.  Vá para**Administração > Conectores**.

    ![Administrador](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Administrador")

7.  Clique em **Autorização do SAML**.

    ![Conectores](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Conectores")

8.  Na seção **Autorização do SAML**, execute as seguintes etapas:

    ![Configuração de SAML](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "Configuração de SAML")

    1.  No portal do Azure, na página **Configurar logon único no OfficeSpace Software**, copie o valor da **URL de Logon Remoto** e cole-a na caixa de texto **URL do provedor de logout**.
    2.  No portal do Azure, na página **Configurar logon único no OfficeSpace Software**, copie o valor da **URL de Logout Remoto** e cole-a na caixa de texto **URL de destino de idp cliente**.
    3.  Copie o valor de **Impressão Digital** do certificado exportado e cole-o na caixa de texto **Impressão digital de certificado de idp de cliente**.  

        >[AZURE.TIP]Para obter mais detalhes, consulte [Como recuperar o valor de impressão digital de um certificado](http://youtu.be/YKQF266SAxI)

    4.  Clique em **Salvar Configurações**.

9.  No portal do AD do Azure, selecione a confirmação da configuração de logon único e clique em **Concluir** para fechar a caixa de diálogo **Configurar logon único**.

    ![Configurar o logon único](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configurar o logon único")
##Configurando o provisionamento de usuários
  
Para habilitar usuários do AD do Azure a fazer logon no OfficeSpace Software, eles devem ser provisionados no OfficeSpace Software. No caso do OfficeSpace Software, o provisionamento é uma tarefa automatizada. Não há nenhum item de ação para você. Os usuários são criados automaticamente, se necessário, durante a primeira tentativa de logon único.

>[AZURE.NOTE]É possível usar qualquer outra ferramenta de criação da conta de usuário do OfficeSpace Software ou as APIs fornecidas pelo OfficeSpace Software para provisionar as contas de usuário do AAD.

##Atribuindo usuários
  
Para testar sua configuração, é necessário conceder acesso ao aplicativo aos usuários do AD do Azure que você deseja que usem seu aplicativo.

###Para atribuir usuários ao OfficeSpace Software, execute as seguintes etapas:

1.  No portal do AD do Azure, crie uma conta de teste.

2.  Na página de integração de aplicativos do **OfficeSpace Software**, clique em **Atribuir usuários**.

    ![Atribuir usuários](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Atribuir usuários")

3.  Selecione seu usuário de teste, clique em **Atribuir** e em **Sim** para confirmar sua atribuição.

    ![Sim](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Sim")
  
Se quiser testar suas configurações de logon único, abra o Painel de Acesso. Para obter mais detalhes sobre o Painel de Acesso, consulte [Introdução ao Painel de Acesso](https://msdn.microsoft.com/library/dn308586).

<!---HONumber=August15_HO7-->