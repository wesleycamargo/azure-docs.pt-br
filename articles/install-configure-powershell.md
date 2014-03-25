<properties linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="PowerShell no Windows Azure" pageTitle="Como instalar e configurar o PowerShell no Windows Azure" description="Saiba como instalar e configurar o PowerShell no Windows Azure." umbracoNaviHide="0" disqusComments="1" writer="kathydav" editor="tysonn" manager="jeffreyg" documentationCenter="" services="" solutions="" authors="" title="Como instalar e configurar o PowerShell no Windows Azure" />

# Como instalar e configurar o PowerShell no Windows Azure#

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/pt-br/manage/install-and-configure-cli/" title="CLI entre plataformas">CLI entre plataformas</a></div>

É possível usar o Windows PowerShell para executar várias tarefas no Windows Azure, interativamente em um prompt de comando ou automaticamente por meio de scripts. O PowerShell no Windows Azure é um módulo que fornece cmdlets para gerenciar o Windows Azure por meio do Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Windows Azure Na maioria dos casos, é possível usar os cmdlets para executar as mesmas tarefas que você pode executar por meio do Portal de Gerenciamento do Windows Azure. Por exemplo, você pode criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e sites. 

O módulo é distribuído como um arquivo baixável e o código-fonte é gerenciado por meio de um repositório disponível publicamente. Um link para os arquivos baixáveis é fornecido nas instruções de instalação mais adiante neste tópico. Para obter informações sobre o código-fonte, consulte o [Repositório de código do PowerShell no Windows Azure (a página pode estar em inglês)](https://github.com/WindowsAzure/azure-sdk-tools).

Este guia fornece informações básicas sobre como instalar e configurar o PowerShell no Windows Azure para gerenciar a plataforma Windows Azure.

## Sumário
  
 * [Pré-requisitos para usar o PowerShell no Windows Azure](#Prereq)
 * [Como instalar o PowerShell no Windows Azure](#Install)  
 * [Como conectar-se a sua assinatura](#Connect)
 * [Como usar os cmdlets: um exemplo](#Ex)
 * [Obtendo ajuda](#Help)
 * [Recursos adicionais](#Resources)  


### <a id="Prereq"></a>Pré-requisitos para usar o PowerShell no Windows Azure

O Windows Azure é uma plataforma baseada em assinatura. Isso significa que uma assinatura é necessária para usar a plataforma. Na maioria dos casos, isso também significa que os cmdlets requerem informações da assinatura para executar as tarefas com sua assinatura. Alguns dos cmdlets relacionados a armazenamento podem ser usados sem essas informações. Você fornece isso configurando seu computador para conectar-se a sua assinatura. As instruções são fornecidas neste artigo em "Como conectar-se a sua assinatura".

> [WACOM.NOTE] Várias opções de assinatura estão disponíveis. Para obter informações, consulte [Comece a usar o Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Quando você instala o módulo, o instalador verifica o software necessário em seu sistema e instala todas as dependências, como a versão correta do Windows PowerShell e do .NET Framework. 

<h2> <a id="Install"></a>Como instalar o PowerShell no Windows Azure</h2>

Você pode baixar e instalar o módulo PowerShell no Windows Azure executando o [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?LinkId=320376). Quando solicitado, clique em **Executar**. O Microsoft Web Platform Installer é carregado com o módulo **PowerShell no Windows Azure** disponível para instalação. O Microsoft Web Platform Installer instala todas as dependências dos cmdlets do PowerShell no Windows Azure. Siga os prompts para concluir a instalação.

Para obter mais informações sobre as ferramentas da linha de comando disponíveis para o Windows Azure, consulte [Ferramentas da linha de comando]( http://go.microsoft.com/fwlink/?LinkId=320796).

A instalação do módulo também instala um console personalizado para o PowerShell no Windows Azure. É possível executar os cmdlets no console padrão do Windows PowerShell ou no console do PowerShell no Windows Azure.

O método usado para abrir qualquer um dos consoles depende da versão do Windows que você está executando:

- Em um computador com pelo menos o Windows 8 ou o Windows Server 2012, você pode usar a Pesquisa interna. Na tela Iniciar, comece digitando **power**. Isso produz uma lista com o escopo dos aplicativos que inclui o Windows PowerShell e o PowerShell no Windows Azure. Clique em um dos aplicativos para abrir a janela do console. (Para fixar o aplicativo na tela Iniciar, clique com o botão direito do mouse no ícone.)

- Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, você pode usar a menu Iniciar. No menu Iniciar, clique em **Todos os Programas**, clique em **Windows Azure** e, em seguida, em **PowerShell no Windows Azure**.


<h2><a id="Connect"></a>Como conectar-se a sua assinatura</h2>

O uso do Windows Azure requer uma assinatura. Se você não tiver uma assinatura, consulte [Comece a usar o Windows Azure](http://go.microsoft.com/fwlink/p/?LinkId=320795).

Os cmdlets requerem informações da sua assinatura para que possam ser usados para gerenciar seus serviços. A partir da versão .0.7 do módulo, há duas maneiras de fornecer essas informações. Você pode baixar e usar um certificado de gerenciamento que contenha as informações ou fazer logon no Windows Azure usando sua conta da Microsoft ou uma ID organizacional. Quando você faz logon, o Active Directory do Windows Azure (AD do Windows Azure) autentica as credenciais. 

Para ajudar a escolher o método de autenticação apropriado para suas necessidades, considere o seguinte:

- O método do AD do Windows Azure pode facilitar o gerenciamento do acesso a uma assinatura, mas pode prejudicar a automação. As credenciais estão disponíveis para o PowerShell no Windows Azure por 12 horas. Depois que elas expiram, você precisa fazer logon novamente.  
- Quando você usa o método de certificado, as informações da assinatura estarão disponíveis desde que a assinatura e o certificado sejam válidos. Esse método facilita o uso da automação para tarefas de execução longa. Depois de baixar e importar as informações, você não precisa fornecê-las novamente. No entanto, esse método dificulta o gerenciamento de acesso a uma assinatura compartilhada, como quando mais de uma pessoa está autorizada a acessar a conta.    

Para obter mais informações sobre o gerenciamento de autenticação e assinatura no Windows Azure, consulte [Gerenciar contas, assinaturas e funções administrativas (a página pode estar em inglês)](http://go.microsoft.com/fwlink/?LinkId=324796).

<h3>Usar o método do AD do Windows Azure</h3>

1. Abra o console do PowerShell no Windows Azure, conforme instruído em [como: instalar o PowerShell no Windows Azure](#Install)

2. Digite o seguinte comando:

    `Add-AzureAccount`

3. Na janela, digite o endereço de e-mail e a senha associada à sua conta.

4. O Windows Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

<h3>Usar o método de certificado</h3>

O módulo do PowerShell no Windows Azure inclui cmdlets que ajudam a baixar e importar o certificado.

-O cmdlet **Get-AzurePublishSettingsFile** abre uma no Portal de Gerenciamento do Windows Azure, do qual você pode baixar as informações da assinatura. As informações estão contidas em um arquivo .publishsettings.

- O **Import-AzurePublishSettingsFile** importa o arquivo .publishsettings para uso pelo módulo. Esse arquivo contém um certificado de gerenciamento que tem credenciais de segurança. 

<div class="dev-callout">
<b>Importante</b> 
<p>Recomendamos que você exclua o perfil de publicação que você baixou
usando <b>Get-AzurePublishSettingsFile</b> depois de importar essas
configurações. Como o certificado de gerenciamento inclui as credenciais de segurança, ele
não deve ser acessado por usuários não autorizados. Se precisar de informações
sobre suas assinaturas, você poderá obtê-las no <a href="http://manage.windowsazure.com/">Portal de Gerenciamento do Windows Azure</a> ou no <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Portal do Cliente do Microsoft Online Services</a>.</p> 
</div>

1. Entre no [Portal de Gerenciamento do Windows Azure](http://manage.windowsazure.com) usando as credenciais da sua conta do Windows Azure.

2. Abra o console do PowerShell no Windows Azure, conforme instruído em [Como instalar o PowerShell no Windows Azure](#Install)

3. Digite o seguinte comando:

    `Get-AzurePublishSettingsFile`

4. Quando solicitado, baixe e salve o perfil de publicação e anote o caminho e o nome do
arquivo .publishsettings. Essas informações são necessárias quando você executa o
cmdlet **Import-AzurePublishSettingsFile** para importar as configurações. O local padrão
e o formato do nome do arquivo é:

	C:\\Users\&lt;UserProfile&gt;\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings

5. Digite um comando semelhante ao seguinte, substituindo os espaços reservados pelo nome da sua conta do Windows e o caminho e nome do arquivo:

    Import-AzurePublishSettingsFile C:\Users\&lt;UserProfile&gt;\Downloads\&lt;SubscriptionName&gt;-credentials.publishsettings

> [WACOM.NOTE] Se você for adicionado a outras assinaturas como coadministrador depois de importar suas configurações de publicação, precisará repetir
esse processo para baixar um novo arquivo .publishsettings e, em seguida, importar essas configurações. Para obter informações sobre como adicionar coadministradores para ajudar a gerenciar
os serviços de uma assinatura, consulte [Adicionar e remover coadministradores de suas assinaturas do Windows Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/gg456328.aspx)

<h3> Exibir detalhes da conta e da assinatura</h3>
Você pode ter várias contas e assinaturas disponíveis para uso pelo PowerShell no Windows Azure. Você pode adicionar várias contas executando Add-AzureAccount mais de uma vez. 

Para ver as contas disponíveis, digite:

	`Get-AzureAccount`

Para ver as informações da assinatura, digite:

	`Get-AzureSubscription`

## <a id="Ex"></a>Como usar os cmdlets: um exemplo ##

Depois que tiver instalado o módulo e configurado seu computador para conectar-se à sua assinatura, você poderá criar um site como um exemplo que mostre como usar os cmdlets.

1. Se ainda não estiver aberto, abra o shell do PowerShell no Windows Azure.

2. Determine um nome para o site. Você precisará usar um nome que esteja de acordo com as convenções de nomenclatura DNS. Os nomes válidos podem conter somente letras 'a' a 'z', os números '0' a '9' e também o hífen ('-'). O nome também deve ser exclusivo dentro do Windows Azure. 

	Depois de escolher um nome, digite um comando semelhante ao seguinte. Por exemplo, para criar um site usando sua conta e um número de modo que você possa usar essa convenção mais de uma vez, digite o seguinte, substituindo o nome de sua conta:

	`New-AzureWebsite my-site-name-1`

	O cmdlet cria o site e lista suas informações.

3. Para verificar o estado do site, digite:

	`Get-AzureWebsite`

	O cmdlet lista o nome e o estado e o nome do host do novo site. 

	> [WACOM.NOTE] Executado conforme mostrado, esse comando lista informações sobre todos os sites associados à assinatura atual. 

4. Os sites são iniciados depois que são criados. Para parar o site, digite:

	`Stop-AzureWebsite -Name account-name-1`

5. Execute o comando Get-AzureWebsite novamente para verificar se o estado do site é 'parado'.
  
5. Para concluir esse teste, você pode excluir o site. Tipo:  

	`Remove-AzureWebsite -Name account-name-1`

	Confirme a remoção para concluir a tarefa.

##<a id="Help"></a>Obtendo ajuda##

Estes recursos fornecem ajuda para cmdlets específicos: 


-   No console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a esse sistema. A tabela a seguir fornece alguns exemplos de comandos que você pode usar para obter ajuda. Você pode obter mais informações no console digitando **help**.

    <table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Comando</b></td>
		<td><b>Resultado</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>help</td>
		<td>Descreve como usar o sistema de Ajuda. <p><b>Observação</b>: a descrição inclui algumas informações sobre arquivos de Ajuda que não se aplicam ao módulo do Windows Azure. Especificamente, os arquivos de Ajuda são instalados quando o módulo é instalado. Eles não estão disponíveis para download separadamente.</p>
</td>
    </tr>
    <tr align="left" valign="top">
		<td>help azure</td>
		<td>Lista todos os cmdlets do módulo do PowerShell no Windows Azure.</td>
    </tr>
	<tr align="left" valign="top">
		<td>help &lt;<b>idioma</b>&gt;-dev</td>
		<td>Lista os cmdlets para desenvolvimento e gerenciamento de aplicativos em um idioma específico. Por exemplo, help node-dev, help php-dev ou help python-dev.</td>
    </tr>
	    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt;</td>
		<td>Exibe a Ajuda sobre um cmdlet do Windows PowerShell.</td>
    </tr>
    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt; -parameter *</td>
		<td>Exibe as definições dos parâmetros de um cmdlet. Por exemplo, help get-azuresubscription -parameter *</td>
    </tr>
    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt; -examples</td>
		<td> Exibe a sintaxe e a descrição dos comandos de exemplo de um cmdlet.</td>
    </tr>
    <tr align="left" valign="top">
		<td>help &lt;<b>cmdlet</b>&gt; -full</td>
		<td>Exibe os requisitos técnicos de um cmdlet.</td>
    </tr>
    </tbody>
    </table>



- Informações de referência sobre os cmdlets no módulo PowerShell no Windows Azure também estão disponíveis na biblioteca do Windows Azure. Para obter informações, consulte [Referência de cmdlets do Windows Azure](http://msdn.microsoft.com/pt-br/library/windowsazure/jj554330.aspx).

Para obter ajuda da comunidade, tente estes fóruns populares:

- [Fórum do Windows Azure no MSDN (a página pode estar em inglês)]( http://go.microsoft.com/fwlink/p/?LinkId=320212) 
- [Stackoverflow](http://go.microsoft.com/fwlink/?LinkId=320213)


## <a id="Resources"></a>Recursos adicionais ##

Esses são alguns dos recursos disponíveis que você pode usar para aprender a usar o Windows Azure e o Windows PowerShell. 

- Para fornecer comentários sobre os cmdlets, relatar problemas ou acessar o código-fonte, consulte [Repositório de código do PowerShell no Windows Azure (a página pode estar em inglês)](https://github.com/WindowsAzure/azure-sdk-tools).

- Para saber mais sobre o ambiente de script e de linha de comando do Windows PowerShell, consulte o [Script Center do TechNet](http://go.microsoft.com/fwlink/p/?LinkId=320211)

- Para obter informações sobre como instalar, aprender, usar e personalizar o Windows PowerShell, consulte [Scripts com o Windows PowerShell](http://go.microsoft.com/fwlink/p/?LinkId=320210)

- Para obter informações sobre o que são scripts e como executá-los no Windows PowerShell, consulte [Executando scripts (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkId=320627). Este artigo inclui informações básicas sobre como criar scripts e configurar seu computador para executar scripts. 

- Para obter informações sobre cmdlets do AD do Windows Azure, consulte [Gerenciar o AD do Windows Azure usando o Windows PowerShell (a página pode estar em inglês)](http://go.microsoft.com/fwlink/p/?LinkId=320628).



  
  
  [Portal do Cliente do Microsoft Online Services]: https://mocp.microsoftonline.com/site/default.aspx
 
  


