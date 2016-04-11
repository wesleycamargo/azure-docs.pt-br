<properties
	pageTitle="Como instalar e configurar o PowerShell do Azure"
	description="Saiba como instalar e configurar o PowerShell do Azure."
	editor="tysonn"
	manager="stevenka"
	documentationCenter=""
	services=""
	authors="coreyp-at-msft"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="powershell"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="coreyp"/>

# Como instalar e configurar o PowerShell do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="CLI do Azure">CLI do Azure</a></div>

##O que é o Azure PowerShell?
O Azure PowerShell é um conjunto de módulo que fornece cmdlets para gerenciar o Azure com o Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Azure. Na maioria dos casos, os cmdlets podem ser usados para as mesmas tarefas que o Portal de Gerenciamento do Azure, como criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e aplicativos Web.

<a id="Install"></a>
## Etapa 1: instalar

Veja a seguir os dois métodos segundo os quais você pode instalar o Azure PowerShell. Você pode instalá-lo do WebPI ou da Galeria do PowerShell:

###Instalando o Azure PowerShell do WebPI

Instalar o Azure PowerShell 1.0 e superior do WebPI é feito da mesma forma como era para o 0.9.x. Baixe o [Azure PowerShell](http://aka.ms/webpi-azps) e inicie a instalação. Se você tiver o Azure PowerShell 0.9.x instalado, a versão 0.9.x será desinstalada como parte da atualização. Se você tiver instalado módulos do Azure PowerShell da Galeria do PowerShell, o instalador removerá automaticamente os módulos antes da instalação para assegurar a consistência do Ambiente do Azure PowerShell.

> [AZURE.NOTE] Se você já tiver instalado módulos do Azure da Galeria do PowerShell, o instalador vai removê-los automaticamente. Isso evita confusão sobre quais versões do módulo que você instalou e onde estão localizadas. Os módulos da Galeria do PowerShell normalmente são instalados em **%ProgramFiles%\\WindowsPowerShell\\Modules**. Por outro lado, o instalador do WebPI instalará os módulos do Azure em **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\PowerShell**. Se ocorrer um erro durante a instalação, você poderá remover manualmente as pastas do Azure * em sua pasta **%ProgramFiles%\\WindowsPowerShell\\Modules** e tentar a instalação novamente.

Quando a instalação for concluída, a configuração do ```$env:PSModulePath``` deverá incluir os diretórios que contêm os cmdlets do Azure PowerShell.

> [AZURE.NOTE] Há um problema conhecido com o PowerShell **$env: PSModulePath** que pode ocorrer durante a instalação do WebPI. Se o computador precisar de uma reinicialização devido a atualizações do sistema ou outras instalações, poderá fazer com que **$env: PSModulePath** de atualizações não inclua o caminho em que o Azure PowerShell está instalado. Se isso ocorrer, você verá uma mensagem dizendo “cmdlet não reconhecido” ao tentar usar os cmdlets do Azure PowerShell após a instalação ou atualização. Se isso ocorrer, reiniciar o computador deverá corrigir o problema.

Se você receber uma mensagem semelhante à seguinte ao tentar carregar ou executar cmdlets:

```
    PS C:\> Get-AzureRmResource
    Get-AzureRmResource : The term 'Get-AzureRmResource' is not recognized as the name of a cmdlet, function,
    script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is
    correct and try again.
    At line:1 char:1
    + Get-AzureRmResource
    + ~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : ObjectNotFound: (get-azurermresourcefork:String) [], CommandNotFoundException
        + FullyQualifiedErrorId : CommandNotFoundException
```

Isso pode ser corrigido reiniciando o computador.

###Instalando o Azure PowerShell por meio da Galeria do PowerShell

Instale o Azure PowerShell 1.3.0 ou superior da Galeria do PowerShell usando um prompt elevado do Windows PowerShell ou do ISE (Ambiente de Script Integrado) do PowerShell usando os seguintes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

####Mais informações sobre esses comandos

- **Install-Module AzureRM** instala um módulo de rollup para os cmdlets do Azure Resource Manager. O módulo do AzureRM depende de um intervalo de versão específico para cada módulo do Azure Resource Manager. O intervalo de versão incluído garante que alterações de módulo sem interrupção possam ser incluídas ao instalar módulos do AzureRM com a mesma versão principal. Ao instalar o módulo AzureRM, qualquer módulo do Azure Resource Manager que não tiver sido instalado anteriormente será baixado e instalado usando a Galeria do PowerShell. Para obter mais informações sobre o controle de versão semântico usado pelos módulos do Azure PowerShell, consulte [semver.org](http://semver.org). 
- **Install-Module Azure** instala o módulo do Azure. Este é o módulo de Gerenciamento de Serviços do Azure PowerShell 0.9.x. Ele não deve ter grandes alterações e deve ser intercambiável com a versão anterior do módulo do Azure.

## Etapa 2: iniciar
Você pode executar os cmdlets a partir do console padrão do Windows PowerShell ou do ISE (Integrated Scripting Environment) do PowerShell. O método usado para abrir qualquer um dos consoles depende da versão do Windows que você está executando:

- Em um computador com pelo menos o Windows 8 ou o Windows Server 2012, você pode usar a Pesquisa interna. Na tela **Iniciar**, comece digitando power. Isso retorna uma lista com o escopo dos aplicativos que inclui o Windows PowerShell. Clique em um dos aplicativos para abrir o console. (Para fixar o aplicativo na tela **Iniciar**, clique com o botão direito do mouse no ícone.)

- Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, use o **menu Iniciar**. No menu **Iniciar**, clique em **Todos os Programas**, clique em **Acessórios**, clique na pasta **Windows PowerShell** e clique em **Windows PowerShell**.

Você também pode executar o **ISE do Windows PowerShell** para usar itens de menu e atalhos de teclado para executar muitas das mesmas tarefas que executaria no console do Windows PowerShell. Para usar o ISE, no console do Windows PowerShell, Cmd.exe, ou na caixa **Executar**, digite **powershell\_ise.exe**.

###Comandos para ajudá-lo a começar

    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 
	
    # To login to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster login experience
    # Login-AzureRmAccount -TenantId xxxx

    # To view all subscriptions for your account
    Get-AzureRmSubscription

    # To select a default subscription for your current session
    Get-AzureRmSubscription –SubscriptionName “your sub” | Select-AzureRmSubscription

    # View your current Azure PowerShell session context
    # This session state is only applicable to the current session and will not affect other sessions
    Get-AzureRmContext

    # To select the default storage context for your current session
    Set-AzureRmCurrentStorageAccount –ResourceGroupName “your resource group” –StorageAccountName “your storage account name”

    # View your current Azure PowerShell session context
    # Note: the CurrentStorageAccount is now set in your session context
    Get-AzureRmContext

    # To list all of the blobs in all of your containers in all of your accounts
    Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob


## Etapa 3: conectar
Os cmdlets precisam de sua assinatura para que possam gerenciar seus serviços. Se ainda não tiver uma assinatura do Azure, você pode adquiri-la. Para obter instruções, consulte [Como comprar o Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Digite **Login-AzureRmAccount**

2. Digite o endereço de e-mail e a senha associada à sua conta. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

--OU--

Entre com sua conta corporativa ou de estudante:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [AZURE.NOTE] Se você tiver mais de um locatário associado à sua conta organizacional, especifique o parâmetro TenantId:

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [AZURE.NOTE] Esse método de logon não interativo funciona somente com uma conta corporativa ou de estudante. Uma conta corporativa ou de estudante é um usuário gerenciado pela sua empresa ou escola e definido na instância do Active Directory do Azure para sua empresa ou escola. Se você não tiver uma conta corporativa ou de estudante no momento, e estiver usando uma conta da Microsoft para fazer logon na assinatura do Azure, você poderá criar uma facilmente usando as etapas a seguir.

> 1. Faça logon no [portal clássico do Azure](https://manage.windowsazure.com) e clique em **Active Directory**.

> 2. Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.

> 3. Selecione o diretório e adicione um novo usuário. Esse novo usuário pode entrar usando uma conta corporativa ou de estudante. Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações como elas são usadas na etapa 5 abaixo.

> 4. No portal clássico do Azure, selecione **Configurações** e, em seguida, **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta corporativa ou de estudante gerencie sua assinatura do Azure.

> 5. Por fim, faça logoff do portal clássico do Azure e faça logon outra vez usando a nova conta corporativa ou de estudante. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.

> Para obter mais informações sobre como se inscrever no Microsoft Azure com uma conta corporativa ou de estudante, consulte [Inscrever-se no Microsoft Azure como uma organização](./active-directory/sign-up-organization.md).

> Para obter mais informações sobre o gerenciamento de autenticação e assinatura no Azure, consulte [Gerenciar contas, assinaturas e funções administrativas (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=324796).

### Exibir detalhes da conta e da assinatura

Você pode ter várias contas e assinaturas disponíveis para uso pelo PowerShell no Azure. Você pode adicionar várias contas executando **Add-AzureRmAccount** mais de uma vez.

Para exibir as contas do Azure disponíveis, digite **Get-AzureAccount**.

Para exibir suas assinaturas do Azure, digite **Get-AzureRmSubscription**.

##<a id="Help"></a>Obtendo ajuda##

Estes recursos fornecem ajuda para cmdlets específicos:


-   No console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a esse sistema. 

- Para obter ajuda da comunidade, experimente estes fóruns populares:

 - [Fórum do Azure no MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
 - [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

##Saiba mais


Consulte os recursos a seguir para saber mais sobre o uso dos cmdlets:

Para obter instruções básicas sobre como usar o Windows PowerShell, consulte [Como usar o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para obter informações de referência sobre os cmdlets, consulte [Referência de cmdlet do Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para exemplos de scripts e instruções para ajudá-lo a aprender como usar scripts para gerenciar o Azure, consulte o [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=321940).

<!---HONumber=AcomDC_0330_2016-->