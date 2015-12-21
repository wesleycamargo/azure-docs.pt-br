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
	ms.date="12/02/2015"
	ms.author="coreyp"/>

# Como instalar e configurar o PowerShell do Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="CLI do Azure">CLI do Azure</a></div>

##O que é o Azure PowerShell?#
O Azure PowerShell é um módulo que fornece cmdlets para gerenciar o Azure com o Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Azure. Na maioria dos casos, os cmdlets podem ser usados para as mesmas tarefas que o Portal de Gerenciamento do Azure, como criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e aplicativos Web.

O módulo e o código-fonte estão disponíveis para download em um repositório publicamente disponível:

- [PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi)
- [Código-fonte do Azure PowerShell 1.0.1](https://github.com/Azure/azure-powershell/archive/v1.0.1-November2015.zip)

<a id="Install"></a>
## Etapa 1: instalar
Baixe e instale o [ PowerShell 1.0.1](https://github.com/Azure/azure-powershell/releases/download/v1.0.1-November2015/azure-powershell.1.0.1.msi) <a id="Connect"></a>

## Etapa 2: iniciar
O módulo instala um console personalizado para o Azure PowerShell. É possível executar os cmdlets no console padrão do Windows PowerShell ou no console do Azure PowerShell.

## Etapa 3: conectar
Os cmdlets precisam de sua assinatura para que possam gerenciar seus serviços. Se ainda não tiver uma assinatura do Azure, você pode adquiri-la. Para obter instruções, consulte [Como comprar o Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

1. Digite **Add-AzureAccount**

2. Digite o endereço de e-mail e a senha associada à sua conta. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

--OU--

Entre com sua conta corporativa ou de estudante:

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

> [AZURE.NOTE]Esse método de logon não interativo funciona somente com uma conta corporativa ou de estudante. Uma conta corporativa ou de estudante é um usuário gerenciado pela sua empresa ou escola e definido na instância do Active Directory do Azure para sua empresa ou escola. Se você não tiver uma conta corporativa ou de estudante no momento, e estiver usando uma conta da Microsoft para fazer logon na assinatura do Azure, você poderá criar uma facilmente usando as etapas a seguir.

> 1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)e clique em **Active Directory**.

> 2. Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.

> 3. Selecione o diretório e adicione um novo usuário. Esse novo usuário pode entrar usando uma conta corporativa ou de estudante. Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações como elas são usadas na etapa 5 abaixo.

> 4. No Portal de Gerenciamento, selecione **Configurações** e **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta corporativa ou de estudante gerencie sua assinatura do Azure.

> 5. Por fim, faça logoff do portal do Azure e faça logon outra vez usando a nova conta corporativa ou de estudante. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.

> Para obter mais informações sobre como se inscrever no Microsoft Azure com uma conta corporativa ou de estudante, consulte [Inscrever-se no Microsoft Azure como uma organização](sign-up-organization.md).

### Exibir detalhes da conta e da assinatura

Você pode ter várias contas e assinaturas disponíveis para uso pelo PowerShell no Azure. Você pode adicionar várias contas executando **Add-AzureAccount** mais de uma vez.

Para exibir as contas do Azure disponíveis, digite **Get-AzureAccount**.

Para exibir suas assinaturas do Azure, digite **Get-AzureSubscription**.

## Etapa 4: Teste<a id="Ex"></a>


Depois que tiver instalado o módulo e configurado seu computador para conectar-se à sua assinatura, você poderá criar um aplicativo Web do Azure para ter certeza de que tudo está funcionando.

1. Inicie o console do PowerShell do Azure.

2. Escolha um nome para seu aplicativo web. Escolha um nome que esteja de acordo com as convenções de nomenclatura DNS. Os nomes válidos podem conter somente letras "a" a "z", os números "0" a "9" e também o hífen ("-").

	O nome do aplicativo web deve ser exclusivo no Azure. Usaremos o "mySite" neste exemplo, mas certifique-se de escolher um nome diferente, como o nome de sua conta, seguido por um número.

	Depois de escolher um nome, digite um comando semelhante ao seguinte. Substitua o nome do aplicativo web por "mySite".

		New-AzureWebsite mySite

	O cmdlet cria o aplicativo web e retorna um objeto que representa o novo aplicativo web. As propriedades do objeto incluem informações úteis sobre o aplicativo web.

3. Para obter mais informações sobre o aplicativo web, digite esse comando. Ele retorna um pouco de informações sobre todos os aplicativos web na assinatura, incluindo um dos que você acabou de criar.

		Get-AzureWebsite

4. Para obter mais informações sobre seu aplicativo web, inclua o nome do aplicativo web no comando. Certifique-se de substituir o nome do aplicativo web por "mySite".

		Get-AzureWebsite -Name mySite

5. Os aplicativos web são iniciados depois de serem criados. Para interromper o aplicativo web, digite este comando, incluindo o nome do aplicativo web.

		Stop-AzureWebsite -Name mySite

6. Para verificar se o estado do site está "parado", execute o comando Get-AzureWebsite novamente.

		Get-AzureWebsite

7. Para concluir esse teste, exclua o aplicativo web. Digite:

		Remove-AzureWebsite -Name mySite

7. Para concluir a tarefa, verifique se o aplicativo web foi excluído.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Obtendo ajuda##

Estes recursos fornecem ajuda para cmdlets específicos:


-   No console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a esse sistema. 



- Informações de referência sobre os cmdlets nos módulos PowerShell no Azure também estão disponíveis na biblioteca do Azure. Para obter informações, consulte [Referência de cmdlets do Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para obter ajuda da comunidade, experimente estes fóruns populares:

- [Fórum do Azure no MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)

<!---HONumber=AcomDC_1210_2015-->