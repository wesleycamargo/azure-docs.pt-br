<properties 
	pageTitle="Fornecer conteúdo da CDN do Azure em seu aplicativo Web" 
	description="Um tutorial que ensina a usar o conteúdo de uma CDN para melhorar o desempenho do seu aplicativo Web." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="erikre" 
	editor="jimbe"/>

<tags 
	ms.service="cdn" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/29/2016" 
	ms.author="casoper"/>

# Fornecer conteúdo da CDN do Azure em seu aplicativo Web #

> [AZURE.NOTE] Este tutorial se aplica ao serviço CDN clássico. Estamos trabalhando bastante na produção de uma atualização para a versão atual do CDN.

Este tutorial mostra como aproveitar a CDN do Azure para melhorar o alcance e o desempenho de seu aplicativo Web. A CDN do Azure pode ajudar a melhorar o desempenho do seu aplicativo Web quando:

- Você tem muitos links para conteúdos estáticos ou semiestáticos em suas páginas
- Seu aplicativo é acessado por clientes globalmente
- Você quer descarregar o tráfego de seu servidor Web
- Você quer reduzir o número de conexões cliente simultâneas a seu servidor Web (há uma ótima discussão sobre o assunto em [Agrupamento e Minificação](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)) 
- Você quer aumentar o tempo percebido de carga/atualização de suas páginas

## O que você aprenderá ##

Neste tutorial, você aprenderá a fazer o seguinte:

-	[Fornecer conteúdo estático de um ponto de extremidade da CDN do Azure](#deploy)
-	[Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN](#upload)
-	[Configurar o cache da CDN para refletir a atualização de conteúdo desejada](#update)
-	[Fornecer conteúdo novo logo após usar cadeias de consulta](#query)

## O que será necessário ##

Este tutorial tem os seguintes pré-requisitos:

-	Uma [conta do Microsoft Azure](/account/) ativa. Você pode se inscrever para uma conta de avaliação
-	Visual Studio 2013 com o [SDK do Azure](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) para GUI de gerenciamento de blob
-	[PowerShell do Azure](http://go.microsoft.com/?linkid=9811175&clcid=0x409) (usado para [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN](#upload))

> [AZURE.NOTE] Você de uma conta do Azure para concluir este tutorial:
> + Você pode [abrir uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) - Você obtém créditos que podem ser usados para experimentar serviços pagos do Azure e, mesmo após eles serem utilizados, você pode manter a conta e utilizar os serviços gratuitos do Azure, como os Sites.
> + Você pode [ativar benefícios para assinantes do MSDN](/pricing/member-offers/msdn-benefits-details/) - Todos os meses, sua assinatura do MSDN oferece créditos que podem ser usados para serviços pagos do Azure.

<a name="static"></a>
## Fornecer conteúdo estático por meio de um ponto de extremidade da CDN do Azure ##

Nesta seção do tutorial, você aprenderá como criar uma CDN e a usará para fornecer seu conteúdo estático. As principais etapas envolvidas são:

1. Criar uma conta de armazenamento
2. Criar uma CDN vinculada à conta de armazenamento
3. Criar um contêiner de blob em sua conta de armazenamento
4. Carregar conteúdo para o contêiner de blob
5. Vincular ao conteúdo que você carregou usando o URL da CDN

Vamos lá. Siga as etapas abaixo para começar a usar a CDN do Azure:

1. Para criar um ponto de extremidade CDN, faça logon no [portal de gerenciamento do Azure](http://manage.windowsazure.com/). 
1. Crie uma conta de armazenamento clicando em **Novo > Serviços de Dados > Armazenamento > Criação Rápida**. Especifique um URL, um local e clique em **Criar Conta de Armazenamento**. 

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG)

	>[AZURE.NOTE] Observe que estou usando Ásia Oriental como a região, pois é distante o suficiente para que eu teste minha CDN por meio da América do Norte mais tarde.

2. Quando o status da nova conta de armazenamento for **Online**, crie um novo ponto de extremidade da CDN que esteja vinculado à conta de armazenamento criada. Clique em **Novo > Serviços de Aplicativos > CDN > Criação Rápida**. Selecione a conta de armazenamento criada e clique em **Criar**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG)

	>[AZURE.NOTE] Após a criação da CDN, o portal do Azure mostrará sua URL e o domínio de origem ao qual ela está vinculada. No entanto, pode levar algum tempo para que a configuração do ponto de extremidade da CDN seja totalmente propagada a todos os locais de nó.

3. Teste seu ponto de extremidade da CDN para garantir que ele esteja online executando um ping. Se o seu ponto de extremidade da CDN não tiver propagado para todos os nós, você verá uma mensagem semelhante à mensagem abaixo.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG)

	Aguarde mais uma hora e teste novamente. Quando tiver terminado de propagar para os nós, seu ponto de extremidade da CDN deverá responder aos pings.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG)

4. Neste ponto, você já pode ver onde o ponto de extremidade da CDN determina que seja o nó da CDN mais próximo a você. No meu computador desktop, o endereço IP de resposta é **93.184.215.201**. Conecte-o a um site como [www.ip-address.org](http://www.ip-address.org) e veja que o servidor está localizado em Washington D.C.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG)

	Para obter uma lista de localizações de todos os nós da CDN, consulte [Locais dos Nós da Rede de Distribuição de Conteúdo (CDN) do Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

3. No portal do Azure, na guia **CDN**, clique no nome do ponto de extremidade CDN que você acabou de criar.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG)

3. Clique em **Habilitar Cadeia de Consulta** para habilitar cadeias de consulta no cache da CDN do Azure. Após você ter habilitado, o mesmo link acessado com diferentes cadeias de consulta será armazenado em cache como entradas diferentes.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG)

	>[AZURE.NOTE] Embora habilitar a cadeia de consulta não seja necessário para esta seção do tutorial, é aconselhável fazer isso o quanto antes para maior conveniência, uma vez que qualquer alteração aqui levará tempo para ser propagada para o restante dos nós, e você não quer um acúmulo de conteúdo não habilitado para cadeia de consulta no cache da CDN (a atualização do conteúdo da CDN será abordada mais adiante). Você verá como aproveitar isso em [Fornecer conteúdo novo imediatamente por meio de cadeias de consulta](#query).

6. No Visual Studio 2013, no Gerenciador de Servidores, clique no botão **Conectar ao Microsoft Azure**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG)

7.  Siga os prompts para entrar na sua conta do Azure.
8.  Após entrar, expanda **Microsoft Azure > Armazenamento > sua conta de armazenamento**. Clique com o botão direito em **Blob** e selecione **Criar Contêiner de Blob**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG)

8.	Especifique um nome de contêiner de blob e clique em **OK**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG)

9.	No Gerenciador de Servidores, clique duas vezes no contêiner de blob que criou para gerenciá-lo. Você deve ver a interface de gerenciamento no painel central.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG)

10.	Clique no botão **Carregar blob** para carregar imagens, scripts ou folhas de estilo que são usadas pelas páginas da Web no contêiner de blob. O progresso do carregamento será exibido no **Log de Atividade do Azure**, e os blobs aparecerão na exibição de contêiner quando forem carregados.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG)

11.	Agora que carregou os blobs, você precisa torná-los públicos para acessá-los. No Gerenciador de Servidores, clique com o botão direito no contêiner e selecione **Propriedades**. Defina a propriedade **Acesso de Leitura Público** como **Blob**.

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG)

12.	Teste o acesso público a seus blogs navegando até a URL de um dos blobs em um navegador. Por exemplo, eu posso testar a primeira imagem de minha lista carregada com `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png`.

	Observe que não estou usando o endereço HTTPS fornecido na interface de gerenciamento do blob no Visual Studio. Usando HTTP, você testa se o conteúdo está acessível publicamente, o que é um requisito da CDN do Azure.

13.	Se puder ver o blob renderizado corretamente no navegador, altere a URL de `http://<yourStorageAccountName>.blob.core.windows.net` para a URL de sua CDN do Azure. No meu caso, para testar a primeira imagem do meu ponto de extremidade da CDN, eu usaria `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`.

	>[AZURE.NOTE] Você pode encontrar a URL do ponto de extremidade da CDN no portal de gerenciamento do Azure, na guia CDN.

	Se comparar o desempenho do acesso direto ao blob e do acesso pela CDN, você verá a melhoria de desempenho decorrente do uso da CDN do Azure. Abaixo, a captura de tela das ferramentas de desenvolvedor F12 do Internet Explorer 11 para acesso à URL do blob para minha imagem:

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG)

	E para acesso à URL da CDN para a mesma imagem

	![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG)

 	Fique atendo aos números do tempo de **Solicitação**, que é o tempo até o primeiro byte, ou o tempo necessário para enviar a solicitação e receber a primeira resposta do servidor. Quando acesso o blob, que é hospedado na região da Ásia Oriental, são necessários 266 ms - uma vez que a solicitação precisa atravessar todo o oceano Pacífico somente para chegar ao servidor. No entanto, quando acesso a CDN do Azure, são necessários somente 16 ms, o que representa um ganho de quase **20 vezes no desempenho**!
	
15.	Agora, basta usar o novo link em sua página da Web. Por exemplo, ei posso adicionar a seguinte marcação de imagem:

		<img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

Nesta seção, você aprendeu como criar um ponto de extremidade da CDN, carregar conteúdo nela e vincular a conteúdo da CDN por meio de qualquer página da Web.

<a name="upload"></a>
## Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN ##

Se quiser carregar de maneira simples todo o conteúdo de seu aplicativo Web ASP .NET para o ponto de extremidade da CDN, ou se implanta o aplicativo usando fornecimento contínuo (para ver um exemplo, consulte [Fornecimento contínuo para Serviços de nuvem no Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md)), você pode usar o PowerShell do Azure para automatizar a sincronização dos arquivos de conteúdo mais recentes com blobs do Azure sempre que implantar sua aplicação da Web. Por exemplo, você pode executar o script em [Carregar Arquivos de Conteúdo de um Aplicativo ASP.NET para Blobs do Azure](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a) para carregar todos os arquivos de conteúdo em um aplicativo ASP.NET. Para usar este script:

4. No menu **Iniciar**, execute **Windows PowerShell**.
5. Na janela do PowerShell, execute `Get-AzurePublishSettingsFile` para baixar um arquivo de configurações de publicação para sua conta do Azure.
6. Após ter baixado o arquivo de configurações de publicação, execute o seguinte: 

		Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

	>[AZURE.NOTE] Após importar o arquivo de configurações de publicação, será a conta do Azure padrão usada para todas as sessões do PowerShell do Azure. Isso significa que as etapas acima precisam ser realizadas somente uma vez.
	
1. Baixe o script da [página de download](http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a). Salve-o na pasta do projeto do seu aplicativo ASP.NET.
2. Clique com o botão direito do mouse no script baixado e clique em **Propriedades**.
3. Clique em **Desbloquear**.
4. Abra uma janela do PowerShell e execute o seguinte:

		cd <ProjectFolder>
		.\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

Este script carrega todos os arquivos de suas pastas *\\Content* e *\\Scripts* na conta de armazenamento e contêiner especificados. Ele tem as seguintes vantagens:

-	Replicar automaticamente a estrutura de arquivo de seu projeto do Visual Studio
-	Criar automaticamente contêineres de blob conforme necessário
-	Reutilizar a mesma conta de armazenamento do Azure e ponto de extremidade da CDN para diversos aplicativos da Web, cada um em um contêiner de blob separado
-	Atualizar facilmente a CDN do Azure com novo conteúdo. Para mais informações sobre a atualização de conteúdo, consulte [Configurar o cache da CDN para refletir a atualização de conteúdo desejada](#update).

Para o parâmetro `-StorageContainer`, faz sentido usar o nome de seu aplicativo Web ou o nome do projeto do Visual Studio. Embora eu tenha usado o nome de contêiner genérico "cdn" anteriormente, usar o nome do aplicativo Web permite que o conteúdo relacionado seja organizado no mesmo contêiner facilmente identificável.

Após a conclusão do carregamento do conteúdo, você pode vincular a qualquer item em suas pastas *\\Content* e *\\Scripts* no código HTML, como nos arquivos .cshtml, usando `http://<yourCDNName>.vo.msecnd.net/<containerName>`. Aqui está um exemplo de algo que posso usar em uma exibição de Razor:

	<img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Para obter um exemplo de integração de scripts do PowerShell à sua configuração de fornecimento contínuo, consulte [Fornecimento contínuo de serviços de nuvem no Azure](../cloud-services/cloud-services-dotnet-continuous-delivery.md).

<a name="update"></a>
## Configurar o cache da CDN para refletir a atualização de conteúdo desejada ##

Agora, suponha que após ter carregado os arquivos estáticos do aplicativo Web num contêiner de blob, você faça uma alteração em um dos arquivos o projeto e o carregue no contêiner de blob novamente. Você pode achar que ele é atualizado automaticamente no ponto de extremidade da CDN, mas na verdade fica confuso porque não vê a atualização refletida quando acessa o URL da CDN do conteúdo.

A verdade é que a CDN realmente atualiza automaticamente por meio do armazenamento de blob, mas faz isso aplicando uma regra padrão de cache de 7 dias ao conteúdo. Isso significa que após um nó da CDN efetuar pull do conteúdo do armazenamento de blob, o mesmo conteúdo não é atualizado até que expire no cache.

A boa notícia é que é possível personalizar o tempo de validade do cache. De maneira semelhante à maioria dos navegadores, a CDN do Azure respeita o tempo de validade especificado no cabeçalho de controle de cache do conteúdo. Você pode especificar um valor personalizado de cabeçalho de controle de cache navegando até o contêiner de blob no portal do Azure e editando as propriedades de blob. A captura de tela abaixo mostra a validade do cache definida como 1 hora (3.600 segundos).

![](media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG)

Você também pode fazer isso no script PowerShell para definir todos os cabeçalhos de controle de cache do blob. Para o script em [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN](#upload), encontre o seguinte trecho de código:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType} `
        -Force

e modifique-o da seguinte forma:

    Set-AzureStorageBlobContent `
        -Container $StorageContainer `
        -Context $context `
        -File $file.FullName `
        -Blob $blobFileName `
        -Properties @{ContentType=$contentType; CacheControl="public, max-age=3600"} `
        -Force

Talvez você ainda precise esperar que todo o conteúdo armazenado em cache por 7 dias expire na CDN do Azure antes que ela efetue pull do novo conteúdo, com o novo cabeçalho de controle de cache. Isso ilustra o fato de que valores de cache personalizados não ajudam se você quer que seu conteúdo fique ativo imediatamente, como atualizações JavaScript ou CSS. No entanto, você pode solucionar este problema de forma alternativa renomeando os arquivos ou controlando suas versões por meio de cadeias de consulta. Para obter mais informações, consulte [Fornecer conteúdo novo logo após usar cadeias de consulta](#query).

Há, é claro, hora e lugar para o cache. Por exemplo, você pode ter conteúdo que não precise de atualização frequente, como os jogos da Copa do Mundo que podem ser atualizados a cada três horas, mas que receba tráfego global suficiente para que você queira descarregá-lo se seu servidor Web. Pode se tratar de um bom candidato para uso do armazenamento em cache da CDN do Azure.

<a name="query"></a>
## Fornecer conteúdo novo logo após usar cadeias de consulta ##

Na CDN do Azure, você pode habilitar cadeias de consulta para que o conteúdo de URLs com cadeias de consulta específicas seja armazenado em cache separadamente. É um ótimo recurso para ser usado se você quiser enviar por push determinados conteúdos para os navegadores cliente imediatamente, em vez de esperar que o conteúdo em cache da CDN expire. Suponhamos que eu publique minha página da Web com um número de versão na URL.
  
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0" rel="stylesheet"/>

Quando eu publico uma atualização de CSS e uso um número de versão diferente em minha URL CSS:

	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1" rel="stylesheet"/>

Para um ponto de extremidade da CDN que tenha cadeias de consulta habilitadas, os dois URLs são únicos um para o outro, e será feita uma nova solicitação para meu servidor Web para recuperar o novo *bootstrap.css*. Para um ponto de extremidade da CDN que não tem cadeias de consulta habilitadas, no entanto, se trata do mesmo URL, e ele simplesmente fornecerá o *bootstrap.css* armazenado em cache.

O truque, então, é atualizar o número de versão automaticamente. No Visual Studio, isso é fácil de fazer. Em um arquivo .cshtml, onde eu usaria o link acima, posso especificar um número de versão com base no número de assembly.

	@{
	    var cdnVersion = System.Reflection.Assembly.GetAssembly(
	        typeof(MyMvcApp.Controllers.HomeController))
	        .GetName().Version.ToString();
	}
	
	...
	
	<link href="http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=@cdnVersion" rel="stylesheet"/>

Se você alterar o número de assembly como parte de cada ciclo de publicação você poderá, do mesmo modo, certificar-se de obter um número de versão único sempre que publicar seu aplicativo Web, que permanecerá o mesmo até o próximo ciclo de publicação. Ou você pode fazer com que o Visual Studio incremente automaticamente o número de versão de assembly sempre que o aplicativo Web for compilado abrindo *Properties\\AssemblyInfo.cs* no projeto do Visual Studio e usar `*` em `AssemblyVersion`. Por exemplo:

	[assembly: AssemblyVersion("1.0.0.*")]

## E quanto aos scripts e folhas de estilo agrupados em ASP.NET? ##

Com os [Aplicativos Web do Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/?LinkId=529714) e os [Serviços de Nuvem do Azure](/services/cloud-services/), você obtém a melhor integração da CDN do Azure com [agrupamento e minificação do ASP.NET.](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

A integração do Serviço de Aplicativo do Azure ou dos Serviços de Nuvem do Azure com a CDN do Azure oferece as seguintes vantagens:

- Integrar a implantação de conteúdo (imagens, scripts e folhas de estilo) como parte do processo de [implantação contínua ](../app-service-web/web-sites-publish-source-control.md) de seu aplicativo Web do Azure
- Atualizar facilmente seus pacotes NuGet servidas pela CDN, como jQuery ou versões de Bootstrap 
- Gerenciar seu aplicativo Web e o conteúdo fornecido por CDN por meio da mesma interface do Visual Studio

Para tutoriais relacionados, consulte:
- [Usar a CDN do Azure no Serviço de Aplicativo do Azure](../app-service-web/cdn-websites-with-cdn.md)
- [Integrar um serviço de nuvem à CDN do Azure](cdn-cloud-service-with-cdn.md)

Sem integração aos Aplicativos Web do Serviço de Aplicativo ou aos Serviços de Nuvem do Azure, é possível usar a CDN do Azure para seus grupos de scripts, com os seguintes empecilhos:

- Você precisa carregar manualmente os scripts agrupados no armazenamento de blob. Uma solução programática é proposta em [stackoverflow](http://stackoverflow.com/a/13736433).
- Em seus arquivos .cshtml, transforme as marcações de script/CSS renderizadas para usar a CDN do Azure. Por exemplo:

		@Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

## Mais informações ##
- [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure](cdn-overview.md)
- [Usar a CDN do Azure no Serviço de Aplicativo do Azure](../app-service-web/cdn-websites-with-cdn.md)
- [Integrar um serviço de nuvem à CDN do Azure](cdn-cloud-service-with-cdn.md)
- [Como mapear o conteúdo da CDN (rede de distribuição de conteúdo) para um domínio personalizado](http://msdn.microsoft.com/library/azure/gg680307.aspx)
- [Usando o Azure CDN](cdn-create-new-endpoint.md)
 

<!---HONumber=AcomDC_0504_2016-->