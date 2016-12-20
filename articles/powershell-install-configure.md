---
title: Como instalar e configurar o PowerShell do Azure
description: Saiba como instalar e configurar o PowerShell do Azure.
editor: tysonn
manager: dongill
documentationcenter: 
services: 
author: coreyp-at-msft
ms.assetid: 802b28e0-1a8c-4872-a9ea-c889b15b9498
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: coreyp
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f20adb27f41cdbd6918340ae688b4ce00dd2d593


---
# <a name="how-to-install-and-configure-azure-powershell"></a>Como instalar e configurar o PowerShell do Azure
<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="Azure CLI">CLI do Azure</a></div>

## <a name="what-is-azure-powershell"></a>O que é o Azure PowerShell?
O Azure PowerShell é um conjunto de módulo que fornece cmdlets para gerenciar o Azure com o Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Azure. Na maioria dos casos, os cmdlets podem ser usados para as mesmas tarefas que o Portal do Azure, como criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e aplicativos Web.

## <a name="how-versioning-works"></a>Como o controle de versão funciona
O Azure PowerShell usa o controle de versão semântico, o que significa que, se a versão A > versão B, então a versão A terá as APIs mais atualizadas. Além disso, significa que as alterações na versão principal significam uma alteração interruptiva em um ou mais cmdlets.  Assim, por exemplo, a versão 1.7.0 é um hotfix para resolver uma alteração interruptiva nas versões 1.x do Azure PowerShell.

Para obter mais informações sobre as práticas de Controle de Versão Semântico no Azure PowerShell, consulte a especificação de Controle de Versão Semântico em: http://semver.org

Para obter as APIs mais recentes, você deve usar a versão 2.x. No entanto, se você tiver scripts escritos para a versão 1. x e não desejar absorver as alterações interruptivas na versão 2.x descritas nas [notas de versão](https://github.com/Azure/azure-powershell/blob/dev/documentation/release-notes/migration-guide.2.0.0.md) da versão 2.x, você deverá instalar a 1.7.0.

Se a versão mais recente do módulo perfil estiver instalada e uma versão anterior de um módulo que depende dele for subsequentemente carregada, isso poderá resultar em uma incompatibilidade de versão. A maneira mais simples de resolver o problema é instalar do .msi mais recente. O arquivo .msi limpa automaticamente versões mais antigas dos módulos.

### <a name="installing-module-versions-side-by-side"></a>Instalando versões de módulo lado a lado
A Versão 2.1.0 (e a versão 1.2.6 para AzureStack) são as primeiras versões do módulo projetadas para serem instaladas e usadas lado a lado. Como o Azure PowerShell usa módulos binários, você deve abrir uma nova janela do PowerShell e usar **Import-Module** para importar uma versão específica dos cmdlets do AzureRM:

    Import-Module AzureRM -RequiredVersion 2.1.0**

Versões anteriores à 2.1.0 (exceto a 1.2.6) não funcionam bem lado a lado com outras versões de módulo do Azure PowerShell. Ao carregar uma versão anterior dos módulos do Azure PowerShell usando um comando como o mostrado acima, versões incompatíveis do módulo **AzureRM.Profile** serão carregadas, resultando nos cmdlets solicitando que você faça logon sempre que você executar um cmdlet, mesmo depois de você ter feito logon.

A maneira mais fácil de resolver que isso é instalar o Azure PowerShell mais recente do feed do WebPI ou .msi – isso remove as versões anteriores dos módulos instalados da galeria. 

Observe que os módulos do Azure e AzureRM têm dependências em comum, portanto, se você usar ambos os módulos, ao atualizar um, você deverá atualizar ambos. Versões anteriores do módulo do Azure tem o mesmo problema com o carregamento de módulos lado a lado que as versões anteriores do módulo do AzureRM têm.

<a id="Install"></a>

## <a name="step-1-install"></a>Etapa 1: instalar
Veja a seguir os dois métodos segundo os quais você pode instalar o Azure PowerShell. Você pode instalá-lo do WebPI ou da Galeria do PowerShell.

### <a name="installing-azure-powershell-from-the-powershell-gallery"></a>Instalando o Azure PowerShell por meio da Galeria do PowerShell
A melhor maneira é usar a Galeria do PowerShell. É necessário que o módulo PowerShellGet use a Galeria do PowerShell. Ela está disponível aqui: [PowerShellGallery.com](https://www.powershellgallery.com/)

Instale o Azure PowerShell 1.3.0 ou superior da Galeria do PowerShell usando um prompt elevado do Windows PowerShell ou do ISE (Ambiente de Script Integrado) do PowerShell usando os seguintes comandos:

    # Install the Azure Resource Manager modules from the PowerShell Gallery
    Install-Module AzureRM

    # Install the Azure Service Management module from the PowerShell Gallery
    Install-Module Azure

#### <a name="more-about-these-commands"></a>Mais informações sobre esses comandos
* **Install-Module AzureRM** instala um módulo de rollup para os cmdlets do Azure Resource Manager. O módulo do AzureRM depende de 
* um intervalo de versão específico para cada módulo do Azure Resource Manager. O intervalo de versão incluído garante que alterações de módulo sem interrupção possam ser incluídas ao instalar módulos do AzureRM com a mesma versão principal. Ao instalar o módulo AzureRM, qualquer módulo do Azure Resource Manager que não tiver sido instalado anteriormente será baixado e instalado usando a Galeria do PowerShell. Para obter mais informações sobre o controle de versão semântico usado pelos módulos do Azure PowerShell, consulte [semver.org](http://semver.org). 
* **Install-Module Azure** instala o módulo do Azure. Este é o módulo de Gerenciamento de Serviços do Azure PowerShell 0.9.x. Ele não deve ter grandes alterações e deve ser intercambiável com a versão anterior do módulo do Azure.

### <a name="installing-azure-powershell-from-webpi"></a>Instalando o Azure PowerShell do WebPI
Instalar o Azure PowerShell 1.0 e superior do WebPI é feito da mesma forma como era para o 0.9.x. Baixe o [Azure PowerShell](http://aka.ms/webpi-azps) e inicie a instalação. Se você tiver o Azure PowerShell 0.9.x instalado, a versão 0.9.x será desinstalada como parte da atualização. Se você tiver instalado módulos do Azure PowerShell da Galeria do PowerShell, o instalador removerá automaticamente os módulos antes da instalação para assegurar a consistência do Ambiente do Azure PowerShell.

> [!NOTE]
> Se você já tiver instalado módulos do Azure da Galeria do PowerShell, o instalador vai removê-los automaticamente. Isso evita confusão sobre quais versões do módulo que você instalou e onde estão localizadas. Os módulos da Galeria do PowerShell normalmente são instalados em **%ProgramFiles%\WindowsPowerShell\Modules**. Por outro lado, o instalador do WebPI instalará os módulos do Azure em **%ProgramFiles(x86)%\Microsoft SDKs\Azure\PowerShell\**. Se ocorrer um erro durante a instalação, você poderá remover manualmente as pastas do Azure* em sua pasta **%ProgramFiles%\WindowsPowerShell\Modules** e tentar a instalação novamente.
> 
> 

Quando a instalação for concluída, a configuração do ```$env:PSModulePath``` deverá incluir os diretórios que contêm os cmdlets do Azure PowerShell.

> [!NOTE]
> Há um problema conhecido com o PowerShell **$env: PSModulePath** que pode ocorrer durante a instalação do WebPI. Se o computador precisar de uma reinicialização devido a atualizações do sistema ou outras instalações, poderá fazer com que **$env: PSModulePath** de atualizações não inclua o caminho em que o Azure PowerShell está instalado. Se isso ocorrer, você verá uma mensagem dizendo “cmdlet não reconhecido” ao tentar usar os cmdlets do Azure PowerShell após a instalação ou atualização. Se isso ocorrer, reiniciar o computador deverá corrigir o problema.
> 
> 

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

Isso pode ser corrigido ao reiniciar a máquina ou importar os cmdlets de C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\ como a seguir (em que XXXX é a versão instalada do PowerShell:

```
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\azure.psd1"
Import-Module "C:\Program Files\WindowsPowerShell\Modules\Azure\XXXX\expressroute\expressroute.psd1"
```

## <a name="step-2-start"></a>Etapa 2: iniciar
Você pode executar os cmdlets a partir do console padrão do Windows PowerShell ou do ISE (Integrated Scripting Environment) do PowerShell.
O método usado para abrir qualquer um dos consoles depende da versão do Windows que você está executando:

* Em um computador com pelo menos o Windows 8 ou o Windows Server 2012, você pode usar a Pesquisa interna. Na tela **Iniciar**, comece digitando power. Isso retorna uma lista com o escopo dos aplicativos que inclui o Windows PowerShell. Clique em um dos aplicativos para abrir o console. (Para fixar o aplicativo na tela **Iniciar**, clique com o botão direito do mouse no ícone.)
* Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, use o **menu Iniciar**. No menu **Iniciar**, clique em **Todos os Programas**, clique em **Acessórios**, clique na pasta **Windows PowerShell** e clique em **Windows PowerShell**.

Você também pode executar o **ISE do Windows PowerShell** para usar itens de menu e atalhos de teclado para executar muitas das mesmas tarefas que executaria no console do Windows PowerShell. Para usar o ISE, no console do Windows PowerShell, Cmd.exe, ou na caixa **Executar**, digite **powershell_ise.exe**.

### <a name="commands-to-help-you-get-started"></a>Comandos para ajudá-lo a começar
    # To make sure the Azure PowerShell module is available after you install
    Get-Module –ListAvailable 

    # To log in to Azure Resource Manager
    Login-AzureRmAccount

    # You can also use a specific Tenant if you would like a faster log in experience
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


## <a name="step-3-connect"></a>Etapa 3: conectar
Os cmdlets precisam de sua assinatura para que possam gerenciar seus serviços. Você pode comprar uma assinatura do Azure caso ainda não tenha uma. Para obter instruções, consulte [Como comprar o Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Digite **Login-AzureRmAccount**
2. Digite o endereço de e-mail e a senha associada à sua conta. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

--OU--

Entre com sua conta corporativa ou de estudante:

    $cred = Get-Credential
    Login-AzureRmAccount -Credential $cred
> [!NOTE]
> Se você tiver mais de um locatário associado à sua conta organizacional, especifique o parâmetro TenantId:
> 
> 

    $loadersubscription = Get-AzureRmSubscription -SubscriptionName $YourSubscriptionName -TenantId $YourAssociatedSubscriptionTenantId


> [!NOTE]
> Esse método de logon não interativo funciona somente com uma conta corporativa ou de estudante. Uma conta corporativa ou de estudante é um usuário gerenciado pela sua empresa ou escola e definido na instância do Active Directory do Azure para sua empresa ou escola. Se não tiver uma conta corporativa ou de estudante e estiver usando uma conta da Microsoft para fazer logon na assinatura do Azure, você poderá criar facilmente uma usando as etapas a seguir.
> 
> 1. Faça logon no [Portal clássico do Azure](https://manage.windowsazure.com) e clique em **Active Directory**.
> 2. Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.
> 3. Selecione o diretório e adicione um novo usuário. Esse novo usuário pode entrar usando uma conta corporativa ou de estudante. Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações como elas são usadas na etapa 5 abaixo.
> 4. No Portal clássico do Azure, selecione **Configurações** e, em seguida, **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta corporativa ou de estudante gerencie a assinatura do Azure.
> 5. Por fim, faça logoff do portal clássico do Azure e faça logon outra vez usando a nova conta corporativa ou de estudante. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.
> 
> Para obter mais informações sobre como se inscrever no Microsoft Azure com uma conta corporativa ou de estudante, consulte [Inscrever-se no Microsoft Azure como uma organização](active-directory/sign-up-organization.md).
> 
> Para obter mais informações sobre o gerenciamento de autenticação e assinatura no Azure, consulte [Gerenciar contas, assinaturas e funções administrativas](http://go.microsoft.com/fwlink/?LinkId=324796) (a página pode estar em inglês).
> 
> 

### <a name="view-account-and-subscription-details"></a>Exibir detalhes da conta e da assinatura
Você pode ter várias contas e assinaturas disponíveis para uso pelo Azure PowerShell. Você pode adicionar várias contas executando **Add-AzureRmAccount** mais de uma vez.

Para exibir as contas do Azure disponíveis, digite **Get-AzureAccount**.

Para exibir suas assinaturas do Azure, digite **Get-AzureRmSubscription**.

## <a name="a-idhelpagetting-help"></a><a id="Help"></a>Obtendo ajuda
Estes recursos fornecem ajuda para cmdlets específicos:

* No console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a esse sistema. 
* Para obter ajuda da comunidade, experimente estes fóruns populares:
  
  * [Fórum do Azure no MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
  * [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

## <a name="learn-more"></a>Saiba mais
Consulte os recursos a seguir para saber mais sobre o uso dos cmdlets:

Para obter instruções básicas sobre como usar o Windows PowerShell, consulte [Como usar o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=321939).

Para obter informações de referência sobre os cmdlets, consulte [Referência de cmdlet do Azure](https://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para exemplos de scripts e instruções para ajudá-lo a aprender como usar scripts para gerenciar o Azure, consulte o [Script Center](http://go.microsoft.com/fwlink/p/?LinkId=321940).




<!--HONumber=Nov16_HO3-->


