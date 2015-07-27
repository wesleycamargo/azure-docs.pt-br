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
	ms.date="02/20/2015"
	ms.author="coreyp"/>

# Como instalar e configurar o PowerShell do Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/manage/install-and-configure-cli/" title="CLI do Azure">CLI do Azure</a></div>

É possível usar o Windows PowerShell para executar várias tarefas no Azure, interativamente em um prompt de comando ou automaticamente por meio de scripts. O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Azure. Na maioria dos casos, é possível usar os cmdlets para executar as mesmas tarefas que você pode executar por meio do Portal de Gerenciamento do Azure. Por exemplo, você pode criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e sites.

O módulo é distribuído como um arquivo baixável e o código-fonte é gerenciado por meio de um repositório disponível publicamente. Um link para os arquivos baixáveis é fornecido nas instruções de instalação mais adiante neste tópico. Para obter informações sobre o código-fonte, consulte o [Repositório de código do PowerShell no Azure (a página pode estar em inglês)](https://github.com/Azure/azure-powershell).

Este guia fornece informações básicas sobre como instalar e configurar o PowerShell no Azure para gerenciar a plataforma Azure.

### <a id="Prereq"></a>Pré-requisitos para usar o Azure PowerShell

O Azure é uma plataforma baseada em assinatura. Isso significa que uma assinatura é necessária para usar a plataforma. Na maioria dos casos, isso também significa que os cmdlets requerem informações da assinatura para executar as tarefas com sua assinatura. Alguns dos cmdlets relacionados a armazenamento podem ser usados sem essas informações. Você fornece isso configurando seu computador para conectar-se a sua assinatura. As instruções são fornecidas neste artigo em "Como conectar-se a sua assinatura".

> [AZURE.NOTE]A partir da versão 0.8.5, os módulos do PowerShell do Azure exigem o Microsoft .NET Framework 4.5.

Quando você instala o módulo, o instalador verifica o software necessário em seu sistema e instala todas as dependências, como a versão correta do Windows PowerShell e do .NET Framework.

<a id="Install"></a>
## Como instalar o Azure PowerShell

Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376). Quando solicitado, clique em **Executar**. O Web Platform Installer instala os módulos do PowerShell do Azure e todas as dependências. Siga os prompts para concluir a instalação.

> [AZURE.NOTE]Se você quiser baixar o instalador do PowerShell, visite https://github.com/Azure/azure-powershell/releases. O código-fonte para os cmdlets do PowerShell pode ser encontrado nesse repositório também

Para obter mais informações sobre as ferramentas da linha de comando disponíveis para o Azure, consulte [Ferramentas da linha de comando](http://go.microsoft.com/fwlink/?LinkId=320796).

A instalação do módulo também instala um console personalizado para o PowerShell no Azure. É possível executar os cmdlets no console padrão do Windows PowerShell ou no console do PowerShell no Azure.

O método usado para abrir qualquer um dos consoles depende da versão do Windows que você está executando:

- Em um computador com pelo menos o Windows 8 ou o Windows Server 2012, você pode usar a Pesquisa interna. Na tela Iniciar, comece digitando **power**. Isso retorna uma lista com o escopo dos aplicativos que inclui o Windows PowerShell e o PowerShell no Azure. Clique em um dos aplicativos para abrir o console. (Para fixar o aplicativo na tela Iniciar, clique com o botão direito do mouse no ícone.)

- Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, use o menu Iniciar. No menu Iniciar, clique em **Todos os Programas**, clique em **Azure** e, em seguida, em **PowerShell no Azure**.

<a id="Connect"></a>
## Como conectar-se a sua assinatura

O uso do Azure requer uma assinatura. Se você não tiver uma assinatura, consulte [Comece a usar o Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Os cmdlets precisam de sua assinatura para que possam gerenciar seus serviços. Existem duas maneiras de fornecer as informações de sua assinatura para o Windows PowerShell. Você pode usar um certificado de gerenciamento que contenha as informações ou entrar no Azure usando sua conta da Microsoft ou uma conta de estudante. Quando você entra, o Active Directory do Azure (AD do Azure) autentica as credenciais e retorna um token de acesso que permite que o PowerShell do Azure gerencie sua conta.

Para ajudar a escolher o método de autenticação apropriado para suas necessidades, considere o seguinte:

- O AD do Azure é o método de autenticação recomendado desde que ele torna mais fácil gerenciar acesso a uma assinatura. Com a atualização na versão 0.8.6, ela habilita o cenário de automação com a autenticação do AD do Azure, bem como se é usada uma conta corporativa ou de estudante. Ele funciona com a API do Gerenciador de Recursos do Azure também.
- Quando você usa o método de certificado, as informações da assinatura estarão disponíveis desde que a assinatura e o certificado sejam válidos. No entanto, esse método dificulta o gerenciamento de acesso a uma assinatura compartilhada, como quando mais de uma pessoa está autorizada a acessar a conta. Além disso, a API do Gerenciador de Recursos do Azure não aceita autenticação de certificado.

Para obter mais informações sobre o gerenciamento de autenticação e assinatura no Azure, consulte [Gerenciar contas, assinaturas e funções administrativas (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=324796).

### Usar o método do AD do Azure

1. Abra o console do Azure PowerShell, conforme instruído em [Como instalar o Azure PowerShell](#Install).

2. Digite o seguinte comando:

		Add-AzureAccount

3. Na janela, digite o endereço de e-mail e a senha associada à sua conta.

4. O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

5. A partir da versão 0.8.6, se você entrar usando uma conta corporativa ou de estudante, poderá digitar o seguinte comando para ignorar a janela pop-up. Isso abrirá a janela de credencial do Windows PowerShell padrão para que você digite seu nome de usuário e senha da conta corporativa ou de estudante.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

	> [AZURE.NOTE]Para obter mais informações sobre segurança e o uso de credenciais, consulte [Práticas recomendadas para implantação de senhas e outros dados confidenciais em Sites do Azure e ASP.NET](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

	> [AZURE.NOTE]Esse método de logon não interativo funciona somente com uma conta corporativa ou de estudante. Uma conta corporativa ou de estudante é um usuário gerenciado pela sua empresa ou escola e definido na instância do Active Directory do Azure para sua empresa ou escola. Se você não tiver uma conta corporativa ou de estudante no momento, e estiver usando uma conta da Microsoft para fazer logon na assinatura do Azure, você poderá criar uma facilmente usando as etapas a seguir.
	>
	> 1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com)e clique em **Active Directory**.
	>
	> 2. Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.
	>
	> 3. Selecione o diretório e adicione um novo usuário. Esse novo usuário pode entrar usando uma conta corporativa ou de estudante.
	>
	>     Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações como elas são usadas em outra etapa.
	>
	> 4. No Portal de Gerenciamento, selecione **Configurações** e **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta corporativa ou de estudante gerencie sua assinatura do Azure.
	>
	> 5. Por fim, faça logoff do portal do Azure e faça logon outra vez usando a nova conta corporativa. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.
	>
	>Para obter mais informações sobre como se inscrever no Microsoft Azure com uma conta corporativa ou de estudante, consulte [Inscrever-se no Microsoft Azure como uma organização](sign-up-organization.md).

### Usar o método de certificado

O módulo do Azure inclui cmdlets que ajudam você a baixar e importar o certificado.

> [AZURE.NOTE]Os cmdlets no módulo AzureResourceManager requer o método AD do Azure (Add-AzureAccount). Estes cmdlets não suportam publicar arquivos de configuração. Para obter mais informações sobre o cmdlets no módulo AzureResourceManager, consulte [Cmdlets do Gerenciador de Recursos do Azure](http://go.microsoft.com/fwlink/?LinkID=394765).


- O cmdlet **Get-AzurePublishSettingsFile** abre uma página da web no Portal de Gerenciamento do Azure, do qual você pode baixar as informações da assinatura. As informações estão contidas em um arquivo .publishsettings.

- O **Import-AzurePublishSettingsFile** importa o arquivo .publishsettings para uso pelo módulo. Esse arquivo contém um certificado de gerenciamento que tem credenciais de segurança.

> [AZURE.IMPORTANT]Recomendamos que você exclua o perfil de publicação baixado usando <b>Get-AzurePublishSettingsFile</b> depois de importar essas configurações. Uma vez que o certificado de gerenciamento inclui credenciais de segurança, ele não deve ser acessado por usuários não autorizados. Se você precisar de informações sobre suas assinaturas, pode obtê-las no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com/) ou no [Portal do Cliente do Microsoft Online Services](http://go.microsoft.com/fwlink/p/?LinkId=324875).

1. Entre no [Portal deGerenciamento do Azure](http://manage.windowsazure.com) usando as credenciais da sua conta do Azure.

2. Abra o console do Azure PowerShell, conforme instruído em [Como instalar o Azure PowerShell](#Install).

3. Digite o seguinte comando:

		Get-AzurePublishSettingsFile

4. Quando solicitado, baixe e salve o perfil de publicação e anote o caminho e o nome do arquivo .publishsettings. Essas informações são necessárias quando você executa o cmdlet **Import-AzurePublishSettingsFile** para importar as configurações. O formato de local e nome de arquivo padrão é:

			C:\\Users<UserProfile>\\Download\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. Digite um comando semelhante ao seguinte, substituindo os espaços reservados pelo nome da sua conta do Windows e o nome do arquivo para os espaços reservados:

		Import-AzurePublishSettingsFile C:\Users<UserProfile>\Downloads<SubscriptionName>-credentials.publishsettings

> [AZURE.NOTE]Se você for adicionado a outras assinaturas como coadministrador depois de importar suas configurações de publicação, precisará repetir esse processo para baixar um novo arquivo .publishsettings e, em seguida, importar essas configurações. Para obter informações sobre como adicionar coadministradores para ajudar a gerenciar os serviços de uma assinatura, consulte [Adicionar e remover coadministradores de suas assinaturas do Azure](http://msdn.microsoft.com/library/windowsazure/gg456328.aspx).

### Exibir detalhes da conta e da assinatura

Você pode ter várias contas e assinaturas disponíveis para uso pelo PowerShell no Azure. Você pode adicionar várias contas executando Add-AzureAccount mais de uma vez.

Para obter as contas do Azure disponíveis, digite:

	Get-AzureAccount

Para obter as assinaturas do Azure, digite:

	Get-AzureSubscription

## <a id="Ex"></a>Como usar os cmdlets: um exemplo ##

Depois que tiver instalado o módulo e configurado seu computador para conectar-se à sua assinatura, você pode criar um aplicativo web do Azure. Este exemplo fará você começar a usar o cmdlets do Azure.

1. Iniciar o console do PowerShell do Azure.

2. Escolha um nome para seu aplicativo web. Escolha um nome que esteja de acordo com as convenções de nomenclatura DNS. Os nomes válidos podem conter somente letras “a” a “'z”, os números “0” a “9” e também o hífen (“-”).

	O nome do aplicativo web deve ser exclusivo no Azure. Usaremos o “mySite” neste exemplo, mas certifique-se de escolher um nome diferente, como o nome de sua conta, seguido por um número.

	Depois de escolher um nome, digite um comando semelhante ao seguinte. Substitua o nome do aplicativo web por "mySite".

		New-AzureWebsite mySite

	O cmdlet cria o aplicativo web e retorna um objeto que representa o novo aplicativo web. As propriedades do objeto incluem informações úteis sobre o aplicativo web.

3. Para obter mais informações sobre o aplicativo web, digite esse comando. Ele retorna um pouco de informações sobre todos os aplicativos web na assinatura, incluindo um dos que você acabou de criar.

		Get-AzureWebsite

4. Para obter mais informações sobre seu aplicativo web, inclua o nome do aplicativo web no comando. Certifique-se de substituir o nome do aplicativo web por “mySite”.

		Get-AzureWebsite -Name mySite

5. Os aplicativos web são iniciados depois de serem criados. Para interromper o aplicativo web, digite este comando, incluindo o nome do aplicativo web.

		Stop-AzureWebsite -Name mySite

6. Para verificar se o estado do site está “parado”, execute o comando Get-AzureWebsite novamente.

		Get-AzureWebsite

7. Para concluir esse teste, exclua o aplicativo web. Digite:

		Remove-AzureWebsite -Name mySite

7. Para concluir a tarefa, verifique se o aplicativo web foi excluído.

		Get-AzureWebsite -Name mySite

##<a id="Help"></a>Obtendo Ajuda##

Estes recursos fornecem ajuda para cmdlets específicos:


-   No console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a esse sistema. A tabela a seguir fornece alguns exemplos de comandos que você pode usar para obter ajuda. Você pode obter mais informações no console digitando **help**.

    <table border="1" cellspacing="4" cellpadding="4">
<tbody>
<tr align="left" valign="top">
	<td><b>Comando</b></td>
	<td><b>Resultado</b></td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help</td>
	<td>Descreve como usar o sistema de Ajuda. <p><b>Observação</b>: a descrição inclui algumas informações sobre arquivos de Ajuda que não se aplicam ao módulo do Azure. Especificamente, os arquivos de Ajuda são instalados quando o módulo é instalado. Eles não estão disponíveis para download separadamente.</p>
</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help do Azure</td>
	<td>Obtém todos os cmdlets do módulo no Azure.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>idioma</b>>-dev</td>
	<td>Obtém os cmdlets para desenvolvimento e gerenciamento de aplicativos em um idioma específico. Por exemplo, help node-dev, help php-dev ou help python-dev.</td>
</tr>
    <tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>></td>
	<td>Obtém ajuda sobre um cmdlet do Windows PowerShell. Substitua <cmdlet> pelo nome cmdlet.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Parâmetro *</td>
	<td>Obtém descrições dos parâmetros cmdlet. O asterisco (*) significa “tudo”.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Exemplos</td>
	<td>Obtém a sintaxe e os exemplos ao usar o cmdlet.</td>
</tr>
<tr align="left" valign="top">
	<td>Get-Help &lt;<b>cmdlet</b>> -Completo</td>
	<td>Obtém toda a ajuda para um cmdlet, incluindo detalhes técnicos.</td>
</tr>
</tbody>
</table>



- Informações de referência sobre os cmdlets nos módulos PowerShell no Azure também estão disponíveis na biblioteca do Azure. Para obter informações, consulte [Referência de cmdlets do Azure](http://msdn.microsoft.com/library/windowsazure/jj554330.aspx).

Para obter ajuda da comunidade, tente estes fóruns populares:

- [Fórum do Azure no MSDN](http://go.microsoft.com/fwlink/p/?LinkId=320212)
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Recursos adicionais ##

Esses são alguns dos recursos disponíveis que você pode usar para aprender a usar o Azure e o Windows PowerShell.

- Para obter informações sobre como acessar os componentes de armazenamento do Azure, consulte [Usando o Azure PowerShell com o Armazenamento do Azure](storage-powershell-guide-full.md).

- Para fornecer comentários sobre os cmdlets, relatar problemas ou acessar o código-fonte, consulte [Repositório de código do PowerShell no Azure](https://github.com/WindowsAzure/azure-sdk-tools).

- Para saber mais sobre o ambiente de script e linha de comando do Windows PowerShell, consulte o [Script Center da TechNet](http://go.microsoft.com/fwlink/p/?LinkId=320211).

- Para obter informações sobre instalar, aprender, usar e personalizar o Windows PowerShell, consulte [Scripts com o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210).

- Para obter informações sobre o que são scripts e como executá-los no Windows PowerShell, consulte [Executando scripts (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkId=320627). Este artigo inclui informações básicas sobre como criar scripts e configurar seu computador para executar scripts.

- Para obter informações sobre cmdlets do AD do Azure, consulte [Gerenciar o AD do Azure usando o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320628).





  [Microsoft Online Services Customer Portal]: https://mocp.microsoftonline.com/site/default.aspx

<!---HONumber=July15_HO3-->