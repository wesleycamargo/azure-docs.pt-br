<properties linkid="develop-python-django-with-visual-studio" urlDisplayName="Django com o Visual Studio" pageTitle="Django com o Visual Studio (Python) - tutorial do Azure" metaKeywords="aplicativo Web Django para o Azure, máquina virtual do Django para o Azure" description="Um tutorial que ensina a compilar um aplicativo Web Django hospedado em uma máquina virtual do Azure." metaCanonical="" services="cloud-services" documentationCenter="Python" title="Criando aplicativos Django com o Python Tools for Visual Studio 1.5" authors="" solutions="" manager="" editor="" />





#Criando aplicativos Django com o Python Tools for Visual Studio 1.5

**Observação:** este tutorial também está encapsulado em um [vídeo do Youtube](http://www.youtube.com/watch?v=UsLti4KlgAY).

**Observação:** um [tutorial mais recente e amplo][] voltado para o PTVS 2.0 Beta agora está disponível.

O desenvolvimento para o Azure é fácil quando as ferramentas disponíveis são usadas.
Este tutorial pressupõe que você não tem experiência anterior com o Azure.
Após concluir este guia, você terá um aplicativo Django em funcionamento na nuvem.

Você aprenderá a:

-   Criar um aplicativo Django básico
-   Executar e depurar seu aplicativo Django localmente usando o servidor de teste do Django
-   Executar seu aplicativo Django localmente no emulador de computação
-   Publicar e republicar seu aplicativo no Azure.

Seguindo este tutorial, você criará um simples aplicativo da web Hello World. O aplicativo será hospedado em uma instância de uma função web que, quando executado no Azure, será ele próprio hospedado em uma máquina virtual (VM) dedicada.

A seguinte é uma captura de tela do aplicativo concluído:

![](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <a id="setup"> </a>Configurar o ambiente de desenvolvimento

Antes de começar a desenvolver seu aplicativo do Azure, você precisa
obter as ferramentas e configurar seu ambiente de desenvolvimento. Para obter detalhes sobre como obter e instalar o SDK do Azure para Python, consulte [Como instalar o Python][].

**Observação:** este tutorial requer o Python 2.7 e o Django 1.4. Essas versões estão incluídas no SDK do Azure atual para Python.

**Observação:** a implantação no emulador de computação e/ou no Azure requer a versão completa do Visual Studio (não há suporte para o shell integrado).

## Criando um novo aplicativo Django

Para criar um novo Django aplicativo, primeiro inicie o Visual Studio e, depois, crie um novo projeto usando **Arquivo -> Novo Projeto**.  Localize a guia Python (disponível no nível superior ou na área Outros Idiomas) e selecione o modelo de aplicativo Django:



![Novos modelos de projeto do Python](./media/python-django-app-tools-visual-studio/ptvs-dj-NewProject.png)



**Clique em OK** e terá criado seu primeiro aplicativo Django.


![O Visual Studio é aberto para o seu primeiro projeto do Django](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstProject.png)

Em seguida, você desejará começar a desenvolver o primeiro aplicativo Django.  Você pode clique com o botão direito do mouse no nó do módulo e selecionar **Adicionar novo aplicativo Django**... para configurar um novo aplicativo no seu projeto:

![Adicionar o item de menu Novo Aplicativo](./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewApp.png)

Após o qual você poderá inserir o novo nome do seu aplicativo:

![Adicionar prompt de nome do Novo Aplicativo](./media/python-django-app-tools-visual-studio/ptvs-dj-AddNewAppPrompt.png)

Após inserir um nome para o aplicativo e clicar em **OK**, um novo aplicativo será adicionado ao seu projeto:

![Gerenciador de Soluções com novo aplicativo adicionado](./media/python-django-app-tools-visual-studio/ptvs-dj-MyFirstApp.png)

Agora, atualize **settings.py** para que o seu aplicativo seja registrado.  Isso fará com que o Django descubra automaticamente os arquivos de modelo adicionados ao diretório de modelos dos seus aplicativos. Adicione o nome do aplicativo à seção INSTALLED_APPS:

	'DjangoApplication.MyFirstApp',

![Adicione o aplicativo a settings.py em INSTALLED_APPS](./media/python-django-app-tools-visual-studio/ptvs-dj-InstallApp.png)

Em seguida, vamos adicionar um código a **views.py** do aplicativo, para que possamos retornar um arquivo de modelo simples:

	from django.http import HttpResponse
	from django.template.loader import render_to_string
	def home(request):
		return HttpResponse(render_to_string(
											'index.html',
											{'content': 'Hello World'}
											))


![Adicione o aplicativo a settings.py em INSTALLED_APPS](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstView.png)

E, em seguida, permite adicionar um arquivo de modelo simples que será renderizado quando você visitar esse modo de exibição.  Para fazer isso, podemos clicar com o botão direito do mouse na pasta Templates e selecionar **"Adicionar Novo Item"**:

![Adicionar novo item à pasta Templates](./media/python-django-app-tools-visual-studio/ptvs-dj-AddFirstTemplate.png)

Agora, você pode selecionar "Modelo HTML do Django" na lista de modelos e digitar **index.html** como o nome do arquivo:

![Adicionar novo item à pasta Templates](./media/python-django-app-tools-visual-studio/ptvs-dj-NewDjangoTemplate.png)

Após o qual o modelo será adicionado ao projeto e aberto.  Aqui, você pode ver o início do realce de uma sintaxe para as marcas de modelo:

![Modelo adicionado ao Gerenciador de Soluções](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateAdded.png)

Você pode prosseguir e começar a atualizar o modelo neste ponto para alterar o HTML renderizado e obterá o intellisense interessante enquanto estiver fazendo isso:

![Intellisense de modelo para filtros do Django](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateIntellisense.png)

Você pode manter ou não a capitalização, isso de qualquer maneira não alterará muito os resultados deste tutorial.  Finalmente, você só precisa registrar seu modo de exibição com os padrões de url em **urls.py**.  Adicione isto a **urlpatterns**:

	url(r'^$', 'DjangoApplication.MyFirstApp.views.home', name='home'),

![Registrar URL](./media/python-django-app-tools-visual-studio/ptvs-dj-RegisterUrl.png)

## Executando seu aplicativo localmente no servidor de teste

Neste ponto, você terá criado seu primeiro aplicativo Django.  Agora, você pode executá-lo localmente simplesmente **pressionando F5**.  

![Olá Mundo de Django em um navegador e o servidor de teste](./media/python-django-app-tools-visual-studio/ptvs-dj-DjangoHelloWorldTestServer.png)

Isso iniciará o interpretador do Python em execução no **manage.py** do Django para executar o servidor de teste.  Depois que o servidor de teste for iniciado com êxito, ele também iniciará um navegador da web para exibir o site.  Como você iniciou com F5, isso será iniciado sob o depurador, portanto, também podemos definir pontos de interrupção em qualquer código do Python como o código de exibição ou nos próprios arquivos de modelo:

![Depurador interrompido em um ponto de interrupção do modelo](./media/python-django-app-tools-visual-studio/ptvs-dj-TemplateBreakpoint.png)

Agora, você pode **clicar no botão Parar** e mover-se para a execução no emulador de computação do Azure.

## Executando seu aplicativo localmente no emulador	

Para executar no emulador de computação, basta adicionar um projeto de implantação do Azure à sua solução para o projeto do Django.  

**Observação:** a implantação no emulador de computação e/ou no Azure requer a versão completa do Visual Studio (não há suporte para o shell integrado).

Isso pode ser feito clicando com o botão direito do mouse no nó do projeto do Django no Gerenciador de Soluções e selecionando **"Adicionar projeto de serviço de nuvem do Azure"**:

![Adicionar o projeto de implantação](./media/python-django-app-tools-visual-studio/ptvs-dj-AddDeploymentProject.png)

Após executar esse comando, você verá um projeto recém-adicionado no gerenciador de soluções:

![Após adicionar o projeto de implantação](./media/python-django-app-tools-visual-studio/ptvs-dj-AfterDeployProjAdded.png)

Esse novo projeto agora também é marcado como o projeto de inicialização na solução.  Neste ponto, você precisará **reiniciar o Visual Studio como Administrador** para poder executar o emulador de computação, mas, depois de fazer isso, podemos simplesmente **pressionar F5** e o aplicativo será executado e implantado no emulador de computação:

![Após adicionar o projeto de implantação](./media/python-django-app-tools-visual-studio/ptvs-dj-ComputeEmulator.png)

Agora, você pode ver que estamos vendo a mesma página da web, mas em uma URL ligeiramente diferente.  Você também pode ver que não há nenhum python.exe que executa o servidor de teste do Django.  Em vez disso, estamos executando Django pelo IIS usando um gateway FastCGI incluído e configurado automaticamente durante o trabalho no Visual Studio.  

Durante a execução no emulador de computação, você poderá iterar rapidamente no seu aplicativo - apenas alterne novamente para o Visual Studio, atualize seus arquivos e atualize o navegador da web.  Você verá os resultados imediatamente!

## Implantando o aplicativo no Azure

Agora, você está pronto para implantar o projeto no Azure. Para fazer isso, simplesmente clique com o botão direito do mouse no projeto de implantação do Azure no Gerenciador de Soluções e **selecione Publicar**:

![Menu do aplicativo de pacote](./media/python-django-app-tools-visual-studio/ptvs-dj-publish1.png)

Após selecionar Publicar, você será solicitado a entrar no Azure.  Você pode importar suas credenciais existentes aqui ou configurar novas credenciais:

![Assinatura do pacote](./media/python-django-app-tools-visual-studio/ptvs-dj-publish2.png)

Após selecionar as credenciais, você verá a tela Configurações de Publicação do Azure.  Você pode selecionar as várias opções sobre como sua implantação continuará ou pode simplesmente **pressionar Publicar**:

![Configurações do pacote](./media/python-django-app-tools-visual-studio/ptvs-dj-publish3.png)

Agora, você precisará aguardar o aplicativo ser configurado e implantado.

![Implantação do pacote](./media/python-django-app-tools-visual-studio/ptvs-dj-publish4.png)

Depois que tudo estiver configurado, você poderá clicar no link sob Nome DNS para ver seu site em execução na nuvem:


![Seu aplicativo Django na nuvem](./media/python-django-app-tools-visual-studio/ptvs-dj-FirstAppInCloud.png)


[Como instalar o Python]: ../python-how-to-install/
[tutorial mais recente e amplo]: ../web-sites-python-create-deploy-django-app/


