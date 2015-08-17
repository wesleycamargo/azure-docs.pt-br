<properties
   pageTitle="Criando uma identidade corporativa ou de estudante no Active Directory do Azure"
   description="Descreve como criar uma identidade corporativa ou de estudante com sua identidade pessoal para ser usada com modelos de grupo de recursos ou acesso baseado em função, entre outros recursos."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure"
   ms.date="05/05/2015"
   ms.author="rasquill"/>

# Criando uma identidade corporativa ou de estudante no Active Directory do Azure

Se você criou uma conta pessoal do Azure ou tiver uma assinatura pessoal do MSDN e criou a conta do Azure para aproveitar os créditos Azure no MSDN, você usou uma identidade da *conta da Microsoft* para criá-la. Para que muitos recursos excelentes do Azure (como os [modelos de grupo de recursos](../resource-group-overview.md)) funcionem, é necessário ter uma conta corporativa ou de estudante (uma identidade gerenciada pelo Active Directory do Azure).

Felizmente, uma das melhores vantagens da conta pessoal do Azure é que ela vem com um domínio do Active Directory do Azure que pode ser usado para criar uma nova conta corporativa ou de estudante que, por sua vez, pode ser usada com recursos do Azure que a exigem.

> [AZURE.NOTE]Se você recebeu um nome de usuário e uma senha de um administrador, há boas chances de que você já tenha uma ID corporativa ou de estudante (às vezes também chamada de *ID organizacional*). Nesse caso, você pode começar a usar sua conta do Azure imediatamente para acessar recursos do Azure que a exigem. Se não for possível usar esses recursos, talvez você precise retornar a este artigo para obter ajuda. Para ver mais informações, consulte [Contas que você pode usar para entrar](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SignInAccounts) e [Como uma assinatura do Azure está relacionada ao AD do Azure](https://msdn.microsoft.com/library/azure/dn629581.aspx#BKMK_SubRelationToDir).

As etapas são simples. Você precisa localizar sua identidade de conexão no portal do Azure, descobrir o domínio padrão do Active Directory do Azure e adicionar um novo usuário a ele como um coadministrador do Azure.

## Localizar o diretório padrão no Portal do Azure

Comece fazendo logon no [portal do Azure](https://manage.windowsazure.com) com a identidade da sua conta pessoal da Microsoft. Quando estiver conectado, role para baixo no painel azul do lado esquerdo e clique em **ACTIVE DIRECTORY**.

![Active Directory do Azure](./media/resource-group-create-work-id-from-personal/azureactivedirectorywidget.png)

Vamos começar localizando algumas informações sobre sua identidade no Azure. Algo semelhante ao que se segue será exibido no painel principal, mostrando que você tem um diretório padrão.

![](./media/resource-group-create-work-id-from-personal/defaultaadlisting.png)

Vamos descobrir mais algumas informações sobre ele. Clique na linha de diretório padrão, que mostra as propriedades do diretório padrão.

![](./media/resource-group-create-work-id-from-personal/defaultdirectorypage.png)

Para exibir o nome de domínio padrão, clique em **DOMÍNIOS**.

![](./media/resource-group-create-work-id-from-personal/domainclicktoseeyourdefaultdomain.png)

Aqui você pode ver que quando a conta do Azure foi criada, o Active Directory do Azure criou um domínio padrão pessoal que é um valor hash (um número gerado por meio de uma cadeia de texto) da sua ID pessoal usado como um subdomínio de onmicrosoft.com. Esse é o domínio ao qual você adicionará um novo usuário.

## Criando um novo usuário no domínio padrão

Clique em **USUÁRIOS** e procure a sua conta pessoal única. Na coluna **ORIGINADO DE**, você verá que é uma **Conta da Microsoft**. Queremos criar um usuário no seu domínio padrão .onmicrosoft.com do Active Directory do Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryuserslisting.png)

Vamos seguir [estas instruções](https://technet.microsoft.com/library/hh967632.aspx#BKMK_1) nas próximas etapas, mas usando um exemplo específico.

No fim da página, clique em **+ADICIONAR USUÁRIO**. Na página que aparece, digite o novo nome de usuário e defina o **Tipo de Usuário** como **Novo usuário na sua organização**. Neste exemplo, o novo nome de usuário é `ahmet`. Selecione o domínio padrão descoberto anteriormente como o domínio para o endereço de email do ahmet. Clique na seta para avançar quando tiver terminado.

![](./media/resource-group-create-work-id-from-personal/addingauserwithdirectorydropdown.png)

Adicione mais detalhes para Ahmet, mas certifique-se de selecionar o valor apropriado da **FUNÇÃO**. É fácil usar **Administrador Global** para garantir que tudo esteja funcionando, mas, se possível, use uma função com menos privilégios. Este exemplo usa a função **Usuário**. (Saiba mais sobre essas funções [aqui](https://msdn.microsoft.com/library/azure/dn468213.aspx#BKMK_1).) Não habilite a autenticação multifator, a menos que queira usá-la para cada log em operação. Clique na seta para avançar quando tiver terminado.

![](./media/resource-group-create-work-id-from-personal/userprofileuseradmin.png)

Clique no botão **criar** para gerar e exibir uma senha temporária para Ahmet.

![](./media/resource-group-create-work-id-from-personal/gettemporarypasswordforuser.png)

Copie o endereço de email do nome de usuário ou use **ENVIAR SENHA EM EMAIL**. Você precisará das informações para fazer logon em breve.

![](./media/resource-group-create-work-id-from-personal/receivedtemporarypassworddialog.png)

Agora você deve ver o novo usuário, **Ahmet o desenvolvedor**, originado do Active Directory do Azure. Você criou a nova identidade corporativa ou de estudante com o Active Directory do Azure. No entanto, essa identidade ainda não tem permissões para usar os recursos do Azure.

![](./media/resource-group-create-work-id-from-personal/defaultdirectoryusersaftercreate.png)

Se você usar **ENVIAR SENHA EM EMAIL**, o seguinte tipo de email é enviado.

![](./media/resource-group-create-work-id-from-personal/emailreceivedfromnewusercreation.png)

## Adicionando direitos de coadministrador do Azure para assinaturas

Agora você precisa adicionar o novo usuário como um coadministrador de sua assinatura para que o novo usuário possa entrar no Portal de Gerenciamento. Para fazer isso, no painel inferior esquerdo clique em **Configurações**.

![](./media/resource-group-create-work-id-from-personal/thesettingswidget.png)

Na área de configurações principais, clique em **ADMINISTRADORES** na parte superior, e você deverá ver apenas a identidade de sua conta pessoal da Microsoft. Na parte inferior da página, clique em **+ ADICIONAR** para especificar um coadministrador. Aqui, insira o endereço de email do novo usuário criado, incluindo o domínio padrão. Conforme mostrado na seguinte captura de tela, uma marca de seleção verde é exibida ao lado do usuário para o diretório padrão. Lembre-se de selecionar todas as assinaturas que deseja que este usuário seja capaz de administrar.

![](./media/resource-group-create-work-id-from-personal/addingnewuserascoadmin.png)

Quando tiver terminado, você deverá ver dois usuários, incluindo sua nova identidade de coadministrador. Faça logoff no portal.

![](./media/resource-group-create-work-id-from-personal/newuseraddedascoadministrator.png)

## Fazendo logon e alterando a senha do novo usuário

Faça logon como o novo usuário criado.

![](./media/resource-group-create-work-id-from-personal/signinginwithnewuser.png)

Será solicitado que você crie uma nova senha imediatamente.

![](./media/resource-group-create-work-id-from-personal/mustupdateyourpassword.png)

Uma mensagem de que você foi bem-sucedido deverá ser exibida, como mostrado abaixo.

![](./media/resource-group-create-work-id-from-personal/successtourdialog.png)


## Próximas etapas

Você agora pode aproveitar a sua nova identidade do Active Directory do Azure para usar [modelos de grupo de recursos do Azure](xplat-cli-azure-resource-manager.md).

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

<!---HONumber=August15_HO6-->