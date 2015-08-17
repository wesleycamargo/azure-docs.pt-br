<properties
	pageTitle="Configurações avançadas e extensões de aplicativo Web do Serviço de Aplicativo do Azure"
	description="Use as declarações de XDT (Transformação de documento XML) para transformar o arquivo ApplicationHost.config em seu aplicativo Web do Serviço de Aplicativo do Azure e adicionar extensões privadas para habilitar ações de administração personalizadas."
	authors="cephalin"
	editor="mollybos"
	manager="wpickett"
	services="app-service\web"
	documentationCenter=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015"
	ms.author="cephalin"/>

# Configurações avançadas e extensões de aplicativo Web do Serviço de Aplicativo do Azure

Usando declarações [XDT (Transformação de documento XML)](http://msdn.microsoft.com/library/dd465326.aspx), você pode transformar o arquivo [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) em seu aplicativo Web no Serviço de Aplicativo do Azure. Também é possível usar declarações XDT para adicionar extensões de site privadas a fim de habilitar ações de administração de aplicativo Web personalizadas. Este artigo inclui uma extensão de aplicativo Web do Gerenciador de PHP de exemplo, que habilita o gerenciamento das configurações de PHP por meio de uma interface da Web.

##<a id="transform"></a>Configuração avançada por meio de ApplicationHost.config
A plataforma de Serviço de Aplicativo fornece flexibilidade e controle para configuração de aplicativos Web. Embora o arquivo de configuração ApplicationHost.config do IIS padrão não esteja disponível para edição direta no Serviço de Aplicativo, a plataforma dá suporte a um modelo de transformação ApplicationHost.config declarativo com base em XDT (Transformação de documento XML).

Para aproveitar essa funcionalidade de transformação, você cria um arquivo ApplicationHost.xdt com conteúdo XDT e o coloca na raiz do aplicativo Web. Talvez seja necessário reiniciar o aplicativo Web para que as alterações entrem em vigor.

O exemplo applicationHost.xdt a seguir mostra como adicionar uma nova variável de ambiente personalizada para um aplicativo Web que utilize PHP 5.4.

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.webServer>
    			<fastCgi>
      				<application>
         				<environmentVariables>
            					<environmentVariable name="CONFIGTEST" value="TEST" xdt:Transform="Insert" xdt:Locator="XPath(/configuration/system.webServer/fastCgi/application[contains(@fullPath,'5.4')]/environmentVariables)" />
         				</environmentVariables>
      				</application>
    			</fastCgi>
  		</system.webServer>
	</configuration>


Um arquivo de log com status e detalhes de transformação está disponível na raiz FTP em LogFiles\\Transform.

Para ver exemplos adicionais, consulte [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

**Observação**<br /> os elementos da lista de módulos em `system.webServer` não podem ser removidos ou reorganizados, mas adições à lista são possíveis.


##<a id="extend"></a> Estender seu aplicativo Web

###<a id="overview"></a> Visão geral das extensões privadas de aplicativo Web

O Serviço de Aplicativo dá suporte a extensões de aplicativo Web como um ponto de extensibilidade para ações administrativas. Na verdade, alguns recursos de plataforma do Serviço de Aplicativo são implementados como extensões pré-instaladas. Embora as extensões de plataforma pré-instaladas não possam ser modificadas, é possível criar e configurar extensões privadas para seu próprio aplicativo Web. Essa funcionalidade também se baseia em declarações XDT. As etapas principais para a criação de uma extensão privada de aplicativo Web são as seguintes:

1. **Conteúdo** de extensão de aplicativo Web: criar qualquer aplicativo Web ao qual o Serviço de Aplicativo dá suporte
2. **Declaração** de extensão de aplicativo Web: criar um arquivo ApplicationHost.xdt
3. **Implantação** de extensão de aplicativo Web: colocar o conteúdo na pasta SiteExtensions, em `root`

Os links internos para o aplicativo Web devem apontar para um caminho relativo ao caminho de aplicativo especificado no arquivo ApplicationHost.xdt. Qualquer alteração feita ao arquivo ApplicationHost.xdt exige uma reciclagem do aplicativo Web.

**Observação**: informações adicionais sobre esses elementos-chave estão disponíveis em [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions).

Um exemplo detalhado está incluído para ilustrar as etapas para criar e habilitar uma extensão privada de aplicativo Web. O código-fonte do exemplo Gerenciador de PHP a seguir pode ser baixado de [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager).

###<a id="SiteSample"></a> Exemplo de extensão de aplicativo Web: Gerenciador de PHP

O Gerenciador de PHP é uma extensão de aplicativo Web que permite que os administradores de aplicativo Web exibam e definam facilmente suas configurações de PHP usando uma interface da Web em vez de precisar modificar arquivos .ini PHP diretamente. Entre os arquivos de configuração comuns para PHP estão o arquivo php. ini localizado em Arquivos de Programas e o arquivo .user.ini localizado na pasta raiz do aplicativo Web. Como o arquivo php.ini não é editável diretamente na plataforma do Serviço de Aplicativo, a extensão do Gerenciador de PHP usa o arquivo .user.ini para aplicar alterações de configuração.

####<a id="PHPwebapp"></a> O aplicativo Web do Gerenciador de PHP

A seguir está a página inicial de implantação do Gerenciador de PHP:

![TransformSitePHPUI][TransformSitePHPUI]

Como você pode ver, uma extensão do aplicativo Web é exatamente como um aplicativo Web normal, mas com um arquivo ApplicationHost.xdt adicional colocado na pasta raiz do aplicativo Web (mais detalhes sobre o arquivo ApplicationHost.xdt estão disponíveis na próxima seção deste artigo).

A extensão Gerenciador de PHP foi criada usando-se o modelo de aplicativo Web MVC 4 do ASP.NET do Visual Studio. A exibição a seguir do Gerenciador de Soluções mostra a estrutura da extensão Gerenciador de PHP.

![TransformSiteSolEx][TransformSiteSolEx]

A única lógica especial necessária para E/S de arquivo é indicar onde o diretório wwwroot do aplicativo Web está localizado. Como mostra o seguinte exemplo de código, a variável de ambiente "HOME" indica o caminho da raiz do aplicativo Web, e o caminho wwwroot pode ser construído acrescentando-se "site\\wwwroot":

	/// <summary>
	/// Gives the location of the .user.ini file, even if one doesn't exist yet
	/// </summary>
	private static string GetUserSettingsFilePath()
	{
    		var rootPath = Environment.GetEnvironmentVariable("HOME"); // For use on Azure Websites
    		if (rootPath == null)
    		{
        		rootPath = System.IO.Path.GetTempPath(); // For testing purposes
    		};
    		var userSettingsFile = Path.Combine(rootPath, @"site\wwwroot\.user.ini");
    		return userSettingsFile;
	}


Já com o caminho do diretório, você pode usar operações de E/S de arquivo regulares para ler e gravar arquivos.

Um ponto de cuidado em relação às extensões do aplicativo Web se refere à identificação de links internos. Se tiver quaisquer links em seus arquivos HTML que indiquem caminhos absolutos para links internos no aplicativo Web, você deverá garantir que esses links sejam acrescentados ao início do nome da extensão, como sua raiz. Isso é necessário porque a raiz da extensão agora é "/`[your-extension-name]`/" em vez de ser apenas "/", logo, todo link interno deve ser atualizado adequadamente. Por exemplo, suponhamos que o código inclua um link para o seguinte:

`"<a href="/Home/Settings">PHP Settings</a>"`

Quando o link fizer parte de uma extensão de aplicativo Web, esse link deverá estar no seguinte formato:

`"<a href="/[your-site-name]/Home/Settings">Settings</a>"`

Você pode contornar esse requisito usando apenas caminhos relativos no aplicativo Web ou, no caso de aplicativos do ASP.NET, usando o método `@Html.ActionLink`, que cria os links apropriados para você.

####<a id="XDT"></a> O arquivo applicationHost.xdt

O código da extensão de aplicativo Web fica em %HOME%\\SiteExtensions[nome-da-extensão]. Chamaremos ele de a raiz da extensão.

Para registrar a extensão do aplicativo Web com o arquivo applicationHost.config, você precisa colocar um arquivo chamado ApplicationHost.xdt na raiz da extensão. O conteúdo do arquivo ApplicationHost.xdt deve ser conforme demonstrado a seguir:

	<?xml version="1.0"?>
	<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
  		<system.applicationHost>
    			<sites>
      				<site name="%XDT_SCMSITENAME%" xdt:Locator="Match(name)">
						<!-- NOTE: Add your extension name in the application paths below -->
        				<application path="/[your-extension-name]" xdt:Locator="Match(path)" xdt:Transform="Remove" />
        				<application path="/[your-extension-name]" applicationPool="%XDT_APPPOOLNAME%" xdt:Transform="Insert">
          					<virtualDirectory path="/" physicalPath="%XDT_EXTENSIONPATH%" />
        				</application>
      				</site>
    			</sites>
  		</system.applicationHost>
	</configuration>

O nome que você seleciona como o nome da extensão deve ser igual ao nome da pasta raiz da extensão.

Isso tem o efeito de adicionar um novo caminho de aplicativo à lista de sites `system.applicationHost` no site SCM. O site SCM é um ponto de extremidade de administração de site com credenciais de acesso específicas. Tem a URL `https://[your-site-name].scm.azurewebsites.net`.

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites[your-website]\VirtualDirectory0\SiteExtensions[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

###<a id="deploy"></a> Implantação de extensão de aplicativo Web

Para instalar a extensão de aplicativo Web, você pode usar FTP para copiar todos os arquivos do seu aplicativo Web para a pasta `\SiteExtensions[your-extension-name]` do aplicativo Web no qual você deseja instalar a extensão. Não se esqueça de copiar o arquivo ApplicationHost.xdt para esse local também. Reinicie o aplicativo Web para habilitar a extensão.

Você deve ser capaz de ver a extensão do aplicativo Web em:

`https://[your-site-name].scm.azurewebsites.net/[your-extension-name]`

Observe que a URL se parece com a URL do aplicativo Web, exceto por usar HTTPS e conter ".scm".

É possível desabilitar todas as extensões (não pré-instaladas) privadas do seu aplicativo Web durante o desenvolvimento e investigações adicionando uma configuração de aplicativo com a chave `WEBSITE_PRIVATE_EXTENSIONS` e um valor de `0`.

>[AZURE.NOTE]Se você deseja começar com o Serviço de Aplicativo do Azure antes de se inscrever em uma conta do Azure, vá até [Experimentar o Serviço de Aplicativo](http://go.microsoft.com/fwlink/?LinkId=523751), em que você pode criar imediatamente um aplicativo Web inicial de curta duração no Serviço de Aplicativo. Nenhum cartão de crédito é exigido, sem compromissos.

## O que mudou
* Para obter um guia sobre a alteração de Sites para o Serviço de Aplicativo, consulte: [Serviço de Aplicativo do Azure e seu impacto sobre os serviços do Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obter um guia sobre a alteração do portal antigo para o novo portal, confira: [Referência para navegar no portal de visualização](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
 

<!---HONumber=August15_HO6-->