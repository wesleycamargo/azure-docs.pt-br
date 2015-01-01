<properties urlDisplayName="Configuring Python with Azure Websites" pageTitle="Configurar o Python com os Sites do Azure" metaKeywords="" description="This tutorial describes options for authoring and configuring a basic Web server Gateway Interface (WSGI) compliant Python application on Azure Websites." metaCanonical="" services="web-sites" documentationCenter="Python" title="Configuring Python with Azure Websites" authors="huvalo" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="08/01/2014" ms.author="huvalo" />




# Configurar o Python com os Sites do Azure #

Este tutorial descreve as opções para criação e configuração de uma Web Server Gateway Interface (WSGI) básica compatível com aplicativos Python nos sites do Azure. É fácil começar com sites do Azure e seu aplicativo Python terá espaço para expandir e estender a outros serviços do Azure. A plataforma de sites do Azure inclui o Python (2.7.3 ou 3.4.0, a sua escolha) e o manipulador genérico wfastcgi.py FastCGI para Python. Basta configurar o site para usar o manipulador do Python. 

> [WACOM.NOTE] Você pode selecionar a versão do Python que quiser usar no portal de sites do Azure abrindo a guia de Configuração do seu site e alterando as configurações da **Versão do Python**.

Para um exemplo mais complexo, configurando o framework Django em sites do Azure, consulte o seguinte tutorial: 
[http://www.windowsazure.com/pt-br/develop/python/tutorials/web-sites-with-django](http://www.windowsazure.com/pt-br/develop/python/tutorials/web-sites-with-django).  

## Suporte WSGI

WSGI é um padrão Python descrito por [PEP 3333](http://www.python.org/dev/peps/pep-3333/) definindo uma interface entre o servidor Web e Python. Ele fornece uma interface padronizada para escrever vários aplicativos da web e estruturas usando o Python.  As estruturas web populares Python de hoje usam a WSGI.  Os sites do Azure dão suporte para tais estruturas; Além disso, os usuários avançados podem até mesmo criar seus próprios, desde que o manipulador personalizado siga as diretrizes de especificação WSGI.

## Criação do site

Este tutorial assume uma assinatura existente do Azure e acesso ao Portal de Gerenciamento do Azure. Orientações detalhadas sobre como criar um site estão disponíveis em [http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-create-websites](http://www.windowsazure.com/pt-br/manage/services/web-sites/how-to-create-websites).
 
Em resumo, se você não tiver um site já existente você pode criar um Portal de Gerenciamento do Azure. Selecione o recurso de SITES e use a opção de CRIAÇÃO RÁPIDA, especificando uma URL para o site.

![](./media/web-sites-python-configure/configure-python-create-website.png)

## Publicação Git

Use as guias INÍCIO RÁPIDO ou PAINEL DE CONTROLE para o site recém criado para configurar a publicação Git.  Este tutorial usa Git para criar, gerenciar e publicar nosso site Python para sites do Azure. 

![](./media/web-sites-python-configure/configure-python-git.png)

Depois que a publicação Git estiver configurada, um repositório Git será criado e associado ao seu site.  A URL do repositório será exibida e poderá ser usada para enviar por push dados do ambiente de desenvolvimento local para a nuvem. Para publicar aplicativos via Git, certifique-se de que um cliente Git também esteja instalado e use as instruções fornecidas para enviar por push o conteúdo do site para sites do Azure.

## Conteúdo do site

Como exemplo, usamos um aplicativo Python básico com um manipulador WSGI básico que ilustra o mínimo de trabalho necessário para tirar proveito do suporte do Python em sites do Azure.  Este aplicativo Python de esqueleto pode ser usado para começar a criar uma variedade de soluções, com complexidade, do exemplo abaixo para uma estrutura de web completa.  

Abaixo está o código para o manipulador WSGI básico. É semelhante ao sugerido pela especificação [PEP 3333](http://www.python.org/dev/peps/pep-3333/) como um ponto de início para um aplicativo compatível com WSGI. Salvamos o conteúdo em um arquivo chamado ConfigurePython.py criado em uma pasta ConfigurePython na raiz do site:

	def application(environ, start_response):
	    status = '200 OK'
	    response_headers = [('Content-type', 'text/plain')]
	    start_response(status, response_headers)
	    yield 'Hello from Azure Websites\n'

*application* é um Python exigível, que servirá como o ponto de entrada chamado por um servidor compatível com WSGI. Este objeto acessível aceita dois argumentos posicionais: 

* *environ*: um dicionário com diversas variáveis de ambiente
* *start_response*: callable fornecido pelo servidor Web para transferência do cabeçalho de status e resposta HTTP

Esse manipulador retornará o texto sem formatação "Olá dos sites do Azure" para todas as solicitações feitas a ele.

## Opções de configuração

Há 2 opções diferentes para configurar seu aplicativo de Python com sites do Azure.

<h3 id="option1">Opção 1: Portal</h3>

1,1. Registre o manipulador FastCGI via guia CONFIGURE no Portal.
Neste exemplo, usamos o manipulador FastCGI para Python incluído com sites do Azure. Para fazer o mesmo, use os caminhos a seguir para o processador de script e o argumento do manipulador FastCGI:

* Caminho do processador de script de Python: D:\python27\python.exe
* Caminho do manipulador FastCGI do Python: D:\python27\scripts\wfastcgi.py

![](./media/web-sites-python-configure/configure-python-handler-mapping.png)

1,2. Configure definições de aplicativo pela mesma guia CONFIGURE no Portal.
As configurações do aplicativo são convertidas em variáveis de ambiente. Esse é um mecanismo que você pode usar para valores de configuração exigidos pelo seu aplicativo Python. Para este aplicativo de exemplo básico, podemos configurar o seguinte:

* O PYTHONPATH informa o Python sobre o diretório para procurar módulos. Os sites do Azure fornecem D:\home\site\wwwroot como um açúcar sintático apontando para a raiz do seu site. 
* WSGI\_HANDLER registra um nome de módulo ou pacote e o atributo a serem utilizados.

![](./media/web-sites-python-configure/configure-python-app-settings.png)

<h3 id="option2">Option 2: web.config</h3>
A alternativa de configuração é usar um arquivo web.config na raiz do site para as ações descritas abaixo. Usar a opção da web.config oferece uma melhor possibilidade de portabilidade para um aplicativo Web. Há 2 métodos disponíveis para rotear as solicitações para o aplicativo Web: definir um manipulador que manipula o * caminho, que instrui o IIS para encaminhar todas as solicitações recebidas pelo Python; ou definir um caminho específico que o Python manipulará e subsequentemente empregar a regravação da URL para redirecionar várias URLs para nosso caminho selecionado.  Na verdade, é recomendável a segunda abordagem - usando um arquivo de manipulador vazio sob a raiz do site para servir como o destino de solicitação (handler.fcgi em nosso exemplo) - para obter um melhor desempenho. No primeiro cenário, todas as solicitações, incluindo aquelas para conteúdo estático (por exemplo, arquivos de imagem e folhas de estilo), terão que passar pelo Python, subvertendo as otimizações que o servidor Web fornece para acessar arquivos estáticos.  Empregar a última abordagem permite servir conteúdo estático com eficiência e invocar o Python somente quando necessário.

2,1. Especificar a variável PYTHONPATH. 
> Isso informará o Python onde procurar o código do aplicativo. D:\home\site\wwwroot também é usado aqui como o caminho absoluto ao website.

2,2. Configure a variável WSGI\_HANDLER.
> Os sites do Azure usam este valor para direcionar o Python para chamar nosso manipulador de WSGI.  O valor dessa variável é uma expressão de Python que, quando executado, retornará um callable que representa um manipulador WSGI. 

2,3. Adicione um manipulador para o Python.
> Isso informará aos sites do Azure que o Python deve tratar as solicitações feitas para o caminho handler.fcgi. É importante que a sintaxe do manipulador pareça exatamente com o que temos dentro da guia <manipuladores> no exemplo abaixo, a menos que você disponibilize seu próprio manipulador FastCGI ou pilha de desenvolvimento do Python.

2,4. Regravar as URLs no handler.fcdi.
> Solicitar sempre o handler.fcgi pode não ser a melhor ideia. Para selecionar o caminho dos arquivos a serem manipulados pelo manipulador Python, usamos a regravação de URL para que todas as URLs sejam manipuladas pelo nosso manipulador Python.

	<configuration>
  		<appSettings>
    		<add key="pythonpath" value="D:\home\site\wwwroot\ConfigurePython" />
    		<add key="WSGI_HANDLER" value="ConfigurePython.application" />
  		</appSettings>
  		<system.webServer>
    		<handlers>
      			<add name="PythonHandler" 
           		verb="*" path="handler.fcgi" 
           		modules="FastCgiModule" 
           		scriptProcessor="D:\Python27\Python.exe|D:\Python27\Scripts\wfastcgi.py" 
           		resourceType="Either" />
   			</handlers>
			<rewrite>
	    		<rules>
					<rule name="Configure Python" stopProcessing="true">
		    			<match url="(.*)" ignoreCase="false" />
		    			<conditions>
							<add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
		    			</conditions>
		    			<action type="Rewrite" url="handler.fcgi/{R:1}" appendQueryString="true" />
					</rule>
	    		</rules>
			</rewrite>
  		</system.webServer>
	</configuration> 

A estrutura de pastas para o exemplo sob a raiz do site é a seguinte (o uso de letras maiúsculas e minúsculas dos nomes das pastas e arquivos Python é significativa e reflete no web.config):

* ConfigurePython\ConfigurePython.py
* web.config
* handler.fcgi

Como estamos regravando todas as URLs para o handler.fcgi e manuseando esse caminho por meio do FastCGI para o Python, precisamos criar um arquivo de espaço reservado com o mesmo nome para que o IIS não retorne um erro HTTP 404. Isso ocorre devido ao comportamento interno do módulo FastCGI do IIS, que impõe que o arquivo solicitado deve existir antes de ser passado para o aplicativo do processador de script especificado.

Navegue até o seu site para testar a configuração correta. Para este exemplo, a mensagem "Olá dos sites do Azure" está visível no acesso.

![](./media/web-sites-python-configure/configure-python-result.png)


<!--HONumber=35_1-->
