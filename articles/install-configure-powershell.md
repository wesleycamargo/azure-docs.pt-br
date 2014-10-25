<properties linkid="Install-Config-Windows-Azure-PowerShell" urlDisplayName="Azure PowerShell" pageTitle="How to install and configure Azure PowerShell" description="Learn how to install and configure Azure PowerShell." umbracoNaviHide="0" disqusComments="1" editor="tysonn" manager="stevenka" documentationCenter="" services="" solutions="" authors="stevenka" title="How to install and configure Azure PowerShell" />

<tags ms.service="multiple" ms.workload="multiple" ms.tgt_pltfrm="powershell" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stevenka;juneb"></tags>

# Como instalar e configurar o PowerShell do Azure

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/install-and-configure-windows-powershell/" title="PowerShell" class="current">PowerShell</a><a href="/pt-br/manage/install-and-configure-cli/" title="CLI entre plataformas">CLI entre plataformas</a></div>

É possível usar o Windows PowerShell para executar várias tarefas no Azure, interativamente em um prompt de comando ou automaticamente por meio de scripts. O PowerShell no Azure é um módulo que fornece cmdlets para gerenciar o Azure por meio do Windows PowerShell. É possível usar os cmdlets para criar, testar, implantar e gerenciar soluções e serviços entregues pela plataforma Azure. Na maioria dos casos, é possível usar os cmdlets para executar as mesmas tarefas que você pode executar por meio do Portal de Gerenciamento do Azure. Por exemplo, você pode criar e configurar serviços de nuvem, máquinas virtuais, redes virtuais e sites.

O módulo é distribuído como um arquivo baixável e o código-fonte é gerenciado por meio de um repositório disponível publicamente. Um link para os arquivos baixáveis é fornecido nas instruções de instalação mais adiante neste tópico. Para obter informações sobre o código-fonte, consulte o [Repositório de código do PowerShell no Azure (a página pode estar em inglês)][Repositório de código do PowerShell no Azure (a página pode estar em inglês)].

Este guia fornece informações básicas sobre como instalar e configurar o PowerShell no Azure para gerenciar a plataforma Azure.

## Sumário

-   [Pré-requisitos para usar o PowerShell no Azure][Pré-requisitos para usar o PowerShell no Azure]
-   [Como: Instale o Azure PowerShell][Como: Instale o Azure PowerShell]
-   [Como: Conecte-se as suas assinaturas][Como: Conecte-se as suas assinaturas]
-   [Como usar os cmdlets: Um exemplo][Como usar os cmdlets: Um exemplo]
-   [Obtendo ajuda][Obtendo ajuda]
-   [Recursos adicionais][Recursos adicionais]

### <span id="Prereq"></span></a>Pré-requisitos para usar o PowerShell no Azure

O Azure é uma plataforma baseada em assinatura. Isso significa que uma assinatura é necessária para usar a plataforma. Na maioria dos casos, isso também significa que os cmdlets requerem informações da assinatura para executar as tarefas com sua assinatura. Alguns dos cmdlets relacionados a armazenamento podem ser usados sem essas informações. Você fornece isso configurando seu computador para conectar-se a sua assinatura. As instruções são fornecidas neste artigo em “Como: Conectar-se as suas assinaturas”.

> [WACOM.NOTE]A partir da versão 0.8.5, os módulos do PowerShell do Azure requer o Microsoft .NET Framework 4.5.

Quando você instala o módulo, o instalador verifica o software necessário em seu sistema e instala todas as dependências, como a versão correta do Windows PowerShell e do .NET Framework.

## <span id="Install"></span></a>Como: Instale o Azure PowerShell

Você pode baixar e instalar o módulo PowerShell no Azure executando o [Microsoft Web Platform Installer][Microsoft Web Platform Installer]. Quando solicitado, clique em **Executar**. O Microsoft Web Platform Installer instala os módulos do PowerShell do Azure e todas as dependências. Siga os prompts para concluir a instalação.

Para obter mais informações sobre as ferramentas da linha de comando disponíveis para o Azure, consulte [Ferramentas da linha de comando][Ferramentas da linha de comando].

A instalação do módulo também instala um console personalizado para o PowerShell no Azure. É possível executar os cmdlets no console padrão do Windows PowerShell ou no console do PowerShell no Azure.

O método usado para abrir qualquer um dos consoles depende da versão do Windows que você está executando:

-   Em um computador com pelo menos o Windows 8 ou o Windows Server 2012, você pode usar a Pesquisa interna. Na tela Iniciar, comece digitando **power**. Isso retorna uma lista com o escopo dos aplicativos que inclui o Windows PowerShell e o PowerShell no Azure. Clique em um dos aplicativos para abrir o console. (Para fixar o aplicativo na tela Iniciar, clique com o botão direito do mouse no ícone.)

-   Em um computador com uma versão anterior ao Windows 8 ou ao Windows Server 2012, use o menu Iniciar. No menu Iniciar, clique em **Todos os Programas**, clique em **Azure** e, em seguida, em **PowerShell no Azure**.

## <span id="Connect"></span></a>Como: Conecte-se as suas assinaturas

O uso do Azure requer uma assinatura. Se você não tiver uma assinatura, consulte [Comece a usar o Azure][Comece a usar o Azure].

Os cmdlets precisam de sua assinatura para que possam gerenciar seus serviços. Existem duas maneiras de fornecer as informações de sua assinatura para o Windows PowerShell. Você pode usar um certificado de gerenciamento que contenha as informações ou entrar no Azure usando sua conta da Microsoft ou uma conta organizacional. Quando você entra, o Active Directory do Azure (AD do Azure) autentica as credenciais e retorna um token de acesso que permite que o Azure PowerShell gerencie sua conta.

Para ajudar a escolher o método de autenticação apropriado para suas necessidades, considere o seguinte:

-   O AD do Azure é o método de autenticação recomendado desde que ele torna mais fácil gerenciar acesso a uma assinatura. Com a atualização na versão 0.8.6, ele habilita o cenário de automação com a autenticação do AD do Azure, bem como se a conta Organizacional é usada. Ele funciona com a API do Gerenciador de Recursos do Azure também.
-   Quando você usa o método de certificado, as informações da assinatura estarão disponíveis desde que a assinatura e o certificado sejam válidos. No entanto, esse método dificulta o gerenciamento de acesso a uma assinatura compartilhada, como quando mais de uma pessoa está autorizada a acessar a conta. Além disso, a API do Gerenciador de Recursos do Azure não aceita autenticação de certificado.

Para obter mais informações sobre o gerenciamento de autenticação e assinatura no Azure, consulte [Gerenciar contas, assinaturas e funções administrativas (a página pode estar em inglês)][Gerenciar contas, assinaturas e funções administrativas (a página pode estar em inglês)].

### Usar o método do AD do Azure

1.  Abra o console do PowerShell do Azure, conforme instruído em [Como: Instalar o Azure PowerShell][Como: Instale o Azure PowerShell].

2.  Digite o seguinte comando:

    `Add-AzureAccount`

3.  Na janela, digite o endereço de e-mail e a senha associada à sua conta.

4.  O Azure autentica e salva as informações de credenciais e, em seguida, fecha a janela.

5.  Iniciando a partir do 0.8.6, se você tem uma conta organizacional, você pode digitar o seguinte comando para desviar da janela pop-up. Isso abrirá a janela de credencial do Windows PowerShell padrão para que você digite seu nome de usuário e senha da conta organizacional.

        $cred = Get-Credential
        Add-AzureAccount -Credential $cred

6.  Se estiver usando isso em um script de automação e deseja evitar qualquer janela pop-up, use o seguinte trecho

        $userName = "<your organizational account user name>"
        $securePassword = ConvertTo-SecureString -String "<your organizational account password>" -AsPlainText -Force
        $cred = New-Object System.Management.Automation.PSCredential($userName, $securePassword)
        Add-AzureAccount -Credential $cred 

    > [WACOM.NOTE] Este método de logon não interativo funciona apenas com conta organizacional. Uma conta institucional é um usuário gerenciado pela organização e definido no locatário do Active Directory do Azure da organização. Se não tiver uma conta institucional e estiver usando uma conta da Microsoft para fazer logon na assinatura do Azure, você poderá criar facilmente uma usando as etapas a seguir.
    >
    > 1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]e clique em **Active Directory**.
    >
    > 2.  Se não houver diretório, selecione **Criar o diretório** e forneça as informações solicitadas.
    >
    > 3.  Selecione o diretório e adicione um novo usuário. Esse novo usuário é uma conta institucional.
    >
    >     Durante a criação do usuário, você receberá um endereço de email para o usuário e uma senha temporária. Salve essas informações como elas são usadas em outra etapa.
    >
    > 4.  No Portal de Gerenciamento, selecione **Configurações** e **Administradores**. Selecione **Adicionar** e adicione o novo usuário como um coadministrador. Isso permite que a conta institucional gerencie a assinatura do Azure.
    >
    > 5.  Por fim, faça logoff do portal do Azure e refaça logon usando a nova conta institucional. Se este for o primeiro logon usando essa conta, você deverá alterar a senha.
    >
    > Para obter mais informações sobre contas institucionais no Microsoft Azure, consulte [Inscreva-se no Microsoft Azure como uma organização][Inscreva-se no Microsoft Azure como uma organização].

### Usar o método de certificado

O módulo do Azure inclui cmdlets que ajudam você a baixar e importar o certificado.

-   O cmdlets **Get-AzurePublishSettingsFile** abre uma página da web no Portal de Gerenciamento do Azure, do qual você pode baixar as informações da assinatura. As informações estão contidas em um arquivo .publishsettings.

-   O **Import-AzurePublishSettingsFile** importa o arquivo .publishsettings para uso pelo módulo. Esse arquivo contém um certificado de gerenciamento que tem credenciais de segurança.

<div class="dev-callout"> 
<b>Observa&ccedil;&atilde;o</b>
<p>Os cmdlets no m&oacute;dulo AzureResourceManager requer o m&eacute;todo AD do Azure (Add-AzureAccount). Estes cmdlets n&atilde;o suportam publicar arquivos de configura&ccedil;&atilde;o. Para obter mais informa&ccedil;&otilde;es sobre o cmdlets no m&oacute;dulo AzureResourceManager, consulte <a href="http://go.microsoft.com/fwlink/?LinkID=394765">Cmdlets do Gerenciador de Recursos do Azure</a>.</p> 
</div>

<div class="dev-callout"> 
<b>Importante</b> 
<p>Recomendamos que voc&ecirc; excluiu o perfil de publica&ccedil;&atilde;o que voc&ecirc;
baixou usando <b>Get-AzurePublishSettingsFile</b>, ap&oacute;s importar estas
configura&ccedil;&otilde;es. Como o certificado de gerenciamento inclui as credenciais de seguran&ccedil;a, ele
n&atilde;o deve ser acessado por usu&aacute;rios n&atilde;o autorizados. Se voc&ecirc; precisa de ajuda
sobre suas assinaturas, voc&ecirc; poder&aacute; obt&ecirc;-las no <a href="http://manage.windowsazure.com/">Portal de Gerenciamento do Azure</a> ou no <a href="http://go.microsoft.com/fwlink/p/?LinkId=324875">Portal do Cliente do Microsoft Online Services</a>.</p> 
</div>

1.  Entre no [Portal deGerenciamento do Azure][Portal deGerenciamento do Azure] usando as credenciais da sua conta do Azure.

2.  Abra o console do PowerShell do Azure, conforme instruído em [Como: Instalar o Azure PowerShell][Como: Instale o Azure PowerShell].

3.  Digite o seguinte comando:

    `Get-AzurePublishSettingsFile`

4.  Quando solicitado, baixe e salve o perfil de publicação e anote o caminho e o nome do arquivo .publishsettings. Essas informações são necessárias quando você executa o cmdlet **Import-AzurePublishSettingsFile** para importar as configurações. O local padrão e o formato do nome do arquivo são:

	`C:\\Users\<UserProfile>\\Download\\[*MySubscription*-...]-*downloadDate*-credentials.publishsettings`

5.  Digite um comando semelhante ao seguinte, substituindo os espaços reservados pelo nome da sua conta do Windows e o nome do arquivo para os espaços reservados:

    `Import-AzurePublishSettingsFile C:\Users\<UserProfile>\Downloads\<SubscriptionName>-credentials.publishsettings`

> Se você for adicionado a outras assinaturas como co-administrador depois de importar suas configurações de publicação, precisará repetir esse processo para baixar um novo arquivo .publishsettings e, em seguida, importar essas configurações. Para obter informações sobre como adicionar coadministradores para ajudar a gerenciar  os serviços de uma assinatura, consulte [Adicionar e remover coadministradores de suas assinaturas do Azure][Adicionar e remover coadministradores de suas assinaturas do Azure].

### Exibir detalhes da conta e da assinatura

Você pode ter várias contas e assinaturas disponíveis para uso pelo PowerShell no Azure. Você pode adicionar várias contas executando Add-AzureAccount mais de uma vez.

Para obter as contas do Azure disponíveis, digite:

    Get-AzureAccount

Para obter as assinaturas do Azure, digite:

    Get-AzureSubscription

## <span id="Ex"></span></a>Como usar os cmdlets: Um exemplo

Depois que tiver instalado o módulo e configurado seu computador para conectar-se à sua assinatura, você poderá criar um site do Azure. Este exemplo fará você começar a usar o cmdlets do Azure.

1.  Iniciar o console do PowerShell do Azure.

2.  Escolha um nome para o seu site. Escolha um nome que esteja de acordo com as convenções de nomenclatura DNS. Os nomes válidos podem conter somente letras “a” a “'z”, os números “0” a “9” e também o hífen (“-”).

    O nome do site deve ser exclusivo no Azure. Usaremos o “mySite” neste exemplo, mas certifique-se de escolher um nome diferente, como o nome de sua conta, seguido por um número.

    Depois de escolher um nome, digite um comando semelhante ao seguinte. Substitua o nome do seu site por “mySite”.

    `New-AzureWebsite mySite`

    O cmdlet cria o site e retorna um objeto que representa o novo site. As propriedades do objeto incluem informações úteis sobre o site.

3.  Para obter mais informações sobre o site, digite este comando. Ele retorna um pouco de informações sobre todos os sites na assinatura, incluindo um dos que você acabou de criar.

    `Get-AzureWebsite`

4.  Para obter mais informações sobre seu site, inclua o nome do site no comando. Certifique-se de substituir o nome do seu site por “mySite”.

    `Get-AzureWebsite -Name mySite`

5.  Os sites são iniciados depois que são criados. Para parar o site, digite este comando, incluindo o nome de seu site.

    `Stop-AzureWebsite -Name mySite`

6.  Para verificar se o estado do site está “parado”, execute o comando Get-AzureWebsite novamente.

    `Get-AzureWebsite`

7.  Para concluir esse teste, exclua o site. Tipo:

    `Remove-AzureWebsite -Name mySite`

8.  Para concluir a tarefa, confirme que o site foi excluído.

    `Get-AzureWebsite -Name mySite`

## <span id="Help"></span></a>Obtendo ajuda

Estes recursos fornecem ajuda para cmdlets específicos:

-   No console, você pode usar o sistema de Ajuda interno. O cmdlet **Get-Help** fornece acesso a esse sistema. A tabela a seguir fornece alguns exemplos de comandos que você pode usar para obter ajuda. Você pode obter mais informações no console digitando **help**.

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tbody>
    <tr class="odd">
    <td align="left"><strong>Comando</strong></td>
    <td align="left"><strong>Resultado</strong></td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help</td>
    <td align="left">Descreve como usar o sistema de Ajuda.
    <p><strong>Observação</strong>: A descrição inclui algumas informações sobre arquivos de Ajuda que não se aplicam ao módulo do Azure. Especificamente, os arquivos de Ajuda são instalados quando o módulo é instalado. Eles não estão disponíveis para download separadamente.</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help do Azure</td>
    <td align="left">Obtém todos os cmdlets do módulo no Azure.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>idioma</strong>&gt;-dev</td>
    <td align="left">Obtém os cmdlets para desenvolvimento e gerenciamento de aplicativos em um idioma específico. Por exemplo, help node-dev, help php-dev ou help python-dev.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt;</td>
    <td align="left">Obtém ajuda sobre um cmdlet do Windows PowerShell. Substitua <cmdlet> pelo nome cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Parâmetro *</td>
    <td align="left">Obtém descrições dos parâmetros cmdlet. O asterisco (*) significa “tudo”.</td>
    </tr>
    <tr class="odd">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Exemplos</td>
    <td align="left">Obtém a sintaxe e os exemplos ao usar o cmdlet.</td>
    </tr>
    <tr class="even">
    <td align="left">Get-Help &lt;<strong>cmdlet</strong>&gt; -Completo</td>
    <td align="left">Obtém toda a ajuda para um cmdlet, incluindo detalhes técnicos.</td>
    </tr>
    </tbody>
    </table>

-   Informações de referência sobre os cmdlets nos módulos PowerShell no Azure também estão disponíveis na biblioteca do Azure. Para obter informações, consulte [Referência de cmdlets do Azure][Referência de cmdlets do Azure].

Para obter ajuda da comunidade, tente estes fóruns populares:

-   [Fórum do Azure no MSDN][Fórum do Azure no MSDN]
-   [StackOverflow][StackOverflow]

## <span id="Resources"></span></a> Recursos adicionais

Esses são alguns dos recursos disponíveis que você pode usar para aprender a usar o Azure e o Windows PowerShell.

-   Para fornecer comentários sobre os cmdlets, relatar problemas ou acessar o código-fonte, consulte [Repositório de código do PowerShell no Azure][Repositório de código do PowerShell no Azure (a página pode estar em inglês)].

-   Para saber mais sobre o ambiente de script e de linha de comando do Windows PowerShell, consulte o [Script Center do TechNet][Script Center do TechNet].

-   Para obter informações sobre como instalar, aprender, usar e personalizar o Windows PowerShell, consulte [Scripts com o Windows PowerShell][Scripts com o Windows PowerShell].

-   Para obter informações sobre o que são scripts e como executá-los no Windows PowerShell, consulte [Executando scripts (a página pode estar em inglês)][Executando scripts (a página pode estar em inglês)]. Este artigo inclui informações básicas sobre como criar scripts e configurar seu computador para executar scripts.

-   Para obter informações sobre cmdlets do AD do Azure, consulte [Gerenciar o AD do Azure usando o Windows PowerShell][Gerenciar o AD do Azure usando o Windows PowerShell].

  [PowerShell]: /pt-br/manage/install-and-configure-windows-powershell/ "PowerShell"
  [CLI entre plataformas]: /pt-br/manage/install-and-configure-cli/ "CLI entre plataformas"
  [Repositório de código do PowerShell no Azure (a página pode estar em inglês)]: https://github.com/WindowsAzure/azure-sdk-tools
  [Pré-requisitos para usar o PowerShell no Azure]: #Prereq
  [Como: Instale o Azure PowerShell]: #Install
  [Como: Conecte-se as suas assinaturas]: #Connect
  [Como usar os cmdlets: Um exemplo]: #Ex
  [Obtendo ajuda]: #Help
  [Recursos adicionais]: #Resources
  [Microsoft Web Platform Installer]: http://go.microsoft.com/fwlink/p/?LinkId=320376
  [Ferramentas da linha de comando]: http://go.microsoft.com/fwlink/?LinkId=320796
  [Comece a usar o Azure]: http://go.microsoft.com/fwlink/p/?LinkId=320795
  [Gerenciar contas, assinaturas e funções administrativas (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/?LinkId=324796
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com
  [Inscreva-se no Microsoft Azure como uma organização]: http://azure.microsoft.com/pt-br/documentation/articles/sign-up-organization/
  [Cmdlets do Gerenciador de Recursos do Azure]: http://go.microsoft.com/fwlink/?LinkID=394765
  [1]: http://manage.windowsazure.com/
  [Portal do Cliente do Microsoft Online Services]: http://go.microsoft.com/fwlink/p/?LinkId=324875
  [Portal deGerenciamento do Azure]: http://manage.windowsazure.com
  [Adicionar e remover coadministradores de suas assinaturas do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/gg456328.aspx
  [Referência de cmdlets do Azure]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj554330.aspx
  [Fórum do Azure no MSDN]: http://go.microsoft.com/fwlink/p/?LinkId=320212
  [StackOverflow]: http://go.microsoft.com/fwlink/?LinkId=320213
  [Script Center do TechNet]: http://go.microsoft.com/fwlink/p/?LinkId=320211
  [Scripts com o Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320210
  [Executando scripts (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?LinkId=320627
  [Gerenciar o AD do Azure usando o Windows PowerShell]: http://go.microsoft.com/fwlink/p/?LinkId=320628
