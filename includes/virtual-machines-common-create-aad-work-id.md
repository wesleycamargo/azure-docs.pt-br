
<br>

> [!NOTE]
> Se você recebeu um nome de usuário e uma senha de um administrador, há boas chances de que você já tenha uma ID corporativa ou de estudante (às vezes também chamada de *ID organizacional*). Nesse caso, você pode começar a usar sua conta do Azure imediatamente para acessar recursos do Azure que a exigem. Se não for possível usar esses recursos, talvez você precise retornar a este artigo para obter ajuda. Para saber mais, confira [Contas que você pode usar para entrar](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) e [Qual é a relação de uma assinatura do Azure como Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).
> 
> 

As etapas são simples. Você precisa localizar sua identidade de conexão no portal clássico do Azure, descobrir o domínio padrão do Active Directory do Azure e adicionar um novo usuário a ele como um coadministrador do Azure.

## <a name="locate-your-default-directory-in-the-azure-classic-portal"></a>Localize seu diretório padrão no portal clássico do Azure
Comece fazendo logon no [Portal clássico do Azure](https://manage.windowsazure.com) com a identidade de sua conta pessoal da Microsoft. Quando estiver conectado, role para baixo no painel azul do lado esquerdo e clique em **ACTIVE DIRECTORY**.

![Azure Active Directory](./media/virtual-machines-common-create-aad-work-id/azureactivedirectorywidget.png)

Vamos começar localizando algumas informações sobre sua identidade no Azure. Algo semelhante ao que se segue será exibido no painel principal, mostrando que você tem um diretório padrão.

![](./media/virtual-machines-common-create-aad-work-id/defaultaadlisting.png)

Vamos descobrir mais algumas informações sobre ele. Clique na linha de diretório padrão, que mostra as propriedades do diretório padrão.  

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectorypage.png)

Para exibir o nome de domínio padrão, clique em **DOMÍNIOS**.

![](./media/virtual-machines-common-create-aad-work-id/domainclicktoseeyourdefaultdomain.png)

Aqui você pode ver que quando a conta do Azure foi criada, o Active Directory do Azure criou um domínio padrão pessoal que é um valor hash (um número gerado por meio de uma cadeia de texto) da sua ID pessoal usado como um subdomínio de onmicrosoft.com. Esse é o domínio ao qual você adicionará um novo usuário.

## <a name="creating-a-new-user-in-the-default-domain"></a>Criando um novo usuário no domínio padrão
Clique em **USUÁRIOS** e procure a sua conta pessoal única. Na coluna **ORIGINADO DE**, você deverá ver que é uma **conta da Microsoft**. Queremos criar um usuário no seu domínio padrão .onmicrosoft.com do Active Directory do Azure.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryuserslisting.png)

Seguiremos [estas instruções](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) nas próximas etapas, mas usando um exemplo específico.

No fim da página, clique em **+ADICIONAR USUÁRIO**. Na página que aparece, digite o novo nome de usuário e defina o **Tipo de Usuário** como um **Novo usuário em sua organização**. Neste exemplo, o novo nome de usuário é `ahmet`. Selecione o domínio padrão descoberto anteriormente como o domínio para o endereço de email do ahmet. Clique na seta para avançar quando tiver terminado.

![](./media/virtual-machines-common-create-aad-work-id/addingauserwithdirectorydropdown.png)

Adicione mais detalhes para Ahmet, mas certifique-se de selecionar o valor apropriado de **FUNÇÃO**. É fácil usar o **Administrador Global** para garantir que tudo esteja funcionando, mas, se possível, use uma função com menos privilégios. Este exemplo usa a função **Usuário**. (Saiba mais em [Permissões de administrador por função](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) Não habilite a autenticação multifator, a menos que queira usá-la para cada log em operação. Clique na seta para avançar quando tiver terminado.

![](./media/virtual-machines-common-create-aad-work-id/userprofileuseradmin.png)

Clique no botão **criar** para gerar e exibir uma senha temporária para Ahmet.

![](./media/virtual-machines-common-create-aad-work-id/gettemporarypasswordforuser.png)

Copie o endereço de email do nome de usuário ou use **ENVIAR SENHA EM EMAIL**. Você precisará das informações para fazer logon em breve.

![](./media/virtual-machines-common-create-aad-work-id/receivedtemporarypassworddialog.png)

Agora você deve ver o novo usuário, **Ahmet o desenvolvedor**, originado do Active Directory do Azure. Você criou a nova identidade corporativa ou de estudante com o Active Directory do Azure. No entanto, essa identidade ainda não tem permissões para usar os recursos do Azure.

![](./media/virtual-machines-common-create-aad-work-id/defaultdirectoryusersaftercreate.png)

Se você usar **ENVIAR SENHA EM EMAIL**, o tipo de email a seguir será enviado.

![](./media/virtual-machines-common-create-aad-work-id/emailreceivedfromnewusercreation.png)

## <a name="adding-azure-co-administrator-rights-for-subscriptions"></a>Adicionando direitos de coadministrador do Azure para assinaturas
Agora você precisa adicionar o novo usuário como um coadministrador de sua assinatura para que o novo usuário possa entrar no Portal de Gerenciamento. Para fazer isso, no painel inferior esquerdo, clique em **Configurações**.

![](./media/virtual-machines-common-create-aad-work-id/thesettingswidget.png)

Na área de configurações principais, clique em **ADMINISTRADORES** na parte superior, e você deverá ver apenas a identidade de sua conta pessoal da Microsoft. Na parte inferior da página, clique em **+ ADICIONAR** para especificar um coadministrador. Aqui, insira o endereço de email do novo usuário criado, incluindo o domínio padrão. Conforme mostrado na seguinte captura de tela, uma marca de seleção verde é exibida ao lado do usuário para o diretório padrão. Lembre-se de selecionar todas as assinaturas que deseja que este usuário seja capaz de administrar.

![](./media/virtual-machines-common-create-aad-work-id/addingnewuserascoadmin.png)

Quando tiver terminado, você deverá ver dois usuários, incluindo sua nova identidade de coadministrador. Faça logoff no portal.

![](./media/virtual-machines-common-create-aad-work-id/newuseraddedascoadministrator.png)

## <a name="logging-in-and-changing-the-new-users-password"></a>Fazendo logon e alterando a senha do novo usuário
Faça logon como o novo usuário criado.

![](./media/virtual-machines-common-create-aad-work-id/signinginwithnewuser.png)

Será solicitado que você crie uma nova senha imediatamente.

![](./media/virtual-machines-common-create-aad-work-id/mustupdateyourpassword.png)

Uma mensagem de que você foi bem-sucedido deverá ser exibida, como mostrado abaixo.

![](./media/virtual-machines-common-create-aad-work-id/successtourdialog.png)

## <a name="next-steps"></a>Próximas etapas
Agora você pode usar sua nova identidade do Active Directory do Azure para usar os [Modelos de grupo de recursos do Azure](../articles/xplat-cli-azure-resource-manager.md).

    azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@aztrainpassxxxxxoutlook.onmicrosoft.com
    Password: *********
    /info:    Added subscription Azure Pass
    info:    Setting subscription Azure Pass as default
    +
    info:    login command OK
    ralph@local:~$ azure config mode arm
    info:    New mode is arm
    ralph@local:~$ azure group list
    info:    Executing command group list
    + Listing resource groups
    info:    No matched resource groups were found
    info:    group list command OK
    ralph@local:~$ azure group create newgroup westus
    info:    Executing command group create
    + Getting resource group newgroup
    + Creating resource group newgroup
    info:    Created resource group newgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/newgroup
    data:    Name:                newgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK
