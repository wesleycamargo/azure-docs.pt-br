<properties linkid="dev-net-transform-extend-site" urlDisplayName="Tópicos do barramento de serviço" pageTitle="Transformar e estender o site" metaKeywords="nenhum" description="TBD" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="timamm" writer="timamm" editor="mollybos" manager="paulettm" title="Transformar e estender o site"/>

# Transformar e estender o site

Usando declarações [XDT](http://msdn.microsoft.com/pt-br/library/dd465326.aspx) (XML Document Transformation), você pode transformar o arquivo [ApplicationHost.config](http://www.iis.net/learn/get-started/planning-your-iis-architecture/introduction-to-applicationhostconfig) nos sites do Azure. Também é possível usar declarações XDT para adicionar extensões de site particulares a fim de habilitar ações de administração de site personalizadas. Este artigo inclui uma extensão de site do PHP Manager de exemplo que permite o gerenciamento das configurações de PHP por meio de uma interface da Web.


<!-- MINI TOC -->

* [Transformar a configuração do site em ApplicationHost.config](#transform)
* [Estender o site](#extend)
	* [Visão geral das extensões de site particular](#overview)
	* [Exemplo de extensão do site: Gerenciador de PHP](#SiteSample)
		* [O aplicativo Web Gerenciador de PHP](#PHPwebapp)
		* [O arquivo applicationHost.xdt](#XDT)
	* [Implantação da extensão do site](#deploy)

<h2><a id="transform"></a>Transformar a configuração do site em ApplicationHost.config</h2>
A plataforma Sites do Azure fornece flexibilidade e controle para configuração do site. Embora o arquivo de configuração ApplicationHost.config do IIS padrão não esteja disponível para edição direta nos Sites do Azure, a plataforma dá suporte a um modelo de transformação ApplicationHost.config declarativo com base no XDT (XML Document Transformation).

Para aproveitar essa funcionalidade de transformação, você cria um arquivo ApplicationHost.xdt com conteúdo XDT e o coloca na raiz do site. Em seguida, na página **Configurar** do Portal do Azure, você define a configuração de aplicativo `WEBSITE_PRIVATE_EXTENSIONS` como 1 (talvez você precise reiniciar o site). 

O exemplo applicationHost.xdt a seguir mostra como adicionar uma nova variável de ambiente personalizada a um site que usa PHP 5.4.

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

 
Um arquivo de log com status e detalhes de transformação está disponível na raiz FTP em LogFiles\Transform.

Para ver exemplos adicionais, consulte [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)

**Observação**<br />
Os elementos da lista de módulos em `system.webServer` não podem ser removidos ou reorganizados, mas adições à lista são possíveis. 


<h2><a id="extend"></a>Estender o site</h2>
<h3><a id="overview"></a>Visão geral das extensões de site particular</h3>
Os Sites do Azure dão suporte a extensões de site como um ponto de extensibilidade para ações administrativas do site. Na verdade, alguns recursos da plataforma Sites do Azure são implementados como extensões de site pré-instaladas. Embora as extensões de plataforma pré-instaladas não possam ser modificadas, é possível criar e configurar extensões particulares para os próprios sites. Essa funcionalidade também se baseia em declarações XDT. As etapas principais para a criação de uma extensão de site particular são as seguintes:

1. Extensão de site **conteúdo**: criar qualquer aplicativo Web compatível com Sites do Azure
2. Extensão de site **declaração**: criar um arquivo ApplicationHost.xdt
3. Extensão de site **implantação**: colocar o conteúdo na pasta SiteExtensions em `raiz`
4.  Extensão de site **ativação**: definir a configuração de aplicativo `WEBSITE_PRIVATE_EXTENSIONS` como 1

Os links internos para o aplicativo Web devem apontar para um caminho relativo ao caminho de aplicativo especificado no arquivo ApplicationHost.xdt. Qualquer alteração feita no arquivo ApplicationHost.xdt exige uma reciclagem do site. 

**Observação**: informações adicionais sobre esses elementos-chave estão disponíveis em [https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) Um exemplo detalhado está incluído para ilustrar as etapas para criar e habilitar uma extensão de site particular. O código-fonte do exemplo Gerenciador de PHP a seguir pode ser baixado em [https://github.com/projectkudu/PHPManager](https://github.com/projectkudu/PHPManager)

<h3><a id="SiteSample"></a>Exemplo de extensão do site: Gerenciador de PHP</h3>

Gerenciador de PHP é uma extensão de site que permite que os administradores de site exibam e definam facilmente suas configurações de PHP usando uma interface da Web em vez de precisar modificar arquivos . ini PHP diretamente. Entre os arquivos de configuração comuns para PHP estão o arquivo php. ini localizado em Arquivos de Programas e o arquivo .user.ini localizado na pasta raiz do site. Como o arquivo php. ini não é editável diretamente na plataforma Sites do Azure, a extensão Gerenciador de PHP usa o arquivo .user.ini para aplicar alterações de configuração.

<h4><a id="PHPwebapp"></a>O aplicativo Web Gerenciador de PHP</h4>
	
Está é a página inicial do site do Gerenciador de PHP:

![TransformSitePHPUI][TransformSitePHPUI]

Como é possível ver, uma extensão de site é como um aplicativo Web normal, mas com um arquivo ApplicationHost.xdt adicional colocado na pasta raiz do site (mais detalhes sobre o arquivo do ApplicationHost.xdt estão disponíveis na próxima seção deste artigo).

A extensão Gerenciador de PHP foi criada usando-se o modelo de aplicativo Web MVC 4 do ASP.NET do Visual Studio. A exibição a seguir do Gerenciador de Soluções mostra a estrutura da extensão de site Gerenciador de PHP.

![TransformSiteSolEx][TransformSiteSolEx]

A única lógica especial necessária para E/S de arquivo é indicar onde o diretório wwwroot do site está localizado. Como mostra o seguinte exemplo de código, a variável de ambiente "HOME" indica o caminho da raiz do site e o caminho wwwroot pode ser construído acrescentando-se "site\wwwroot":


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

Um ponto de cuidado em relação às extensões do site se refere à identificação de links internos.  Se tiver links em seus arquivos HTML que indiquem caminhos absolutos para links internos no site, você deverá garantir que esses links sejam acrescentados ao nome da extensão como a raiz do site. Isso é necessário porque a raiz do site da extensão agora é "/`[o nome da extensão]`/" em vez de ser apenas "/", logo, todo link interno deve ser atualizado adequadamente. Por exemplo, suponhamos que o código inclua um link para o seguinte: 

`"<a href="/Home/Settings">Configurações de PHP</a>"`

Quando o link fizer parte de uma extensão de site, o link deverá estar na seguinte forma:

`"<a href="/[your-site-name]/Home/Settings">Configurações</a>"` 

Você pode contornar esse requisito usando apenas caminhos relativos no site ou, no caso de sites do ASP.NET, usando o método `@Html.ActionLink` que cria os links apropriados para você.

<h4><a id="XDT"></a>O arquivo applicationHost.xdt</h4>

O código da extensão de site fica em %HOME%\SiteExtensions\[o nome da extensão]. Chamaremos ele de a raiz da extensão.  

Para registrar a extensão do site com o arquivo applicationHost config, você precisa colocar um arquivo chamado ApplicationHost.xdt na raiz da extensão. O conteúdo do arquivo ApplicationHost.xdt deve ser o seguinte:

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

Isso tem o efeito de adicionar um novo caminho de aplicativo à lista de sites `system.applicationHost` no site SCM. O site SCM é um ponto de extremidade de administração de site com credenciais de acesso específicas. Ele tem a URL `https://[o nome do site]. scm.azurewebsites.net`  

	<system.applicationHost>
  		...
  		<site name="~1[your-website]" id="1716402716">
      			<bindings>
        			<binding protocol="http" bindingInformation="*:80: [your-website].scm.azurewebsites.net" />
        			<binding protocol="https" bindingInformation="*:443: [your-website].scm.azurewebsites.net" />
      			</bindings>
      			<traceFailedRequestsLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles" />
      			<detailedErrorLogging enabled="false" directory="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\LogFiles\DetailedErrors" />
      			<logFile logSiteId="false" />
      			<application path="/" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="D:\Program Files (x86)\SiteExtensions\Kudu\1.24.20926.5" />
      			</application>
				<!-- Note the custom changes that go here -->
      			<application path="/[your-extension-name]" applicationPool="[your-website]">
        			<virtualDirectory path="/" physicalPath="C:\DWASFiles\Sites\[your-website]\VirtualDirectory0\SiteExtensions\[your-extension-name]" />
      			</application>
    		</site>
  	</sites>
	  ...
	</system.applicationHost>

<h3><a id="deploy"></a>Implantação da extensão do site</h3>

Para instalar a extensão do site, é possível usar o FTP para copiar todos os arquivos do aplicativo Web para a pasta `\SiteExtensions\[o nome da extensão]` na qual você deseja instalar a extensão.  Não se esqueça de copiar o arquivo ApplicationHost.xdt para esse local também.

Em seguida, no Portal de Sites do Azure, vá até a guia **Configurar** do site que tem a extensão. Na seção **configurações do aplicativo**, adicione a chave `WEBSITE_PRIVATE_EXTENSIONS` e atribua a ela um valor igual a `1`.

![TransformSiteappSettings][TransformSiteappSettings]

Por fim, no Portal do Azure, reinicie o site da web para habilitar sua extensão.

![TransformSiteRestart][TransformSiteRestart]

Você deve ser capaz de ver a extensão do site em:


`https://[o nome do site].scm.azurewebsites.net/[o nome da extensão]` 

Observe que a URL se parece com a URL do site, exceto por usar HTTPS e conter ".scm". 

<!-- IMAGES -->
[TransformSitePHPUI]: ./media/web-sites-transform-extend/TransformSitePHPUI.png
[TransformSiteSolEx]: ./media/web-sites-transform-extend/TransformSiteSolEx.png
[TransformSiteappSettings]: ./media/web-sites-transform-extend/TransformSiteappSettings.png
[TransformSiteRestart]: ./media/web-sites-transform-extend/TransformSiteRestart.png




