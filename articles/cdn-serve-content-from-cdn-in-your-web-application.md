<properties linkid="cdn-serve-content-from-cdn-in-your-web-application" urlDisplayName="Use Content from a CDN in Your Web Application" pageTitle="Use Content from a CDN in Your Web Application" metaKeywords="Azure tutorial, Azure web app tutorial, ASP.NET, CDN" description="A tutorial that teaches you how to use content from a CDN to improve the performance of your Web application." metaCanonical="" services="cdn" documentationCenter=".NET" title="Use Content from a CDN in Your Web Application" authors="cephalin" solutions="" manager="wpickett" editor="tysonn" />

<tags ms.service="cdn" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Fornecer conteúdo da CDN do Azure em seu aplicativo Web

***Atualizado em 23 de julho de 2014***

Este tutorial mostra como aproveitar a CDN do Azure para melhorar o alcance e o desempenho de seu aplicativo Web. A CDN do Azure pode ajudar a melhorar o desempenho do seu aplicativo Web quando:

-   Você tem muitos links para conteúdos estáticos ou semiestáticos em suas páginas
-   Seu aplicativo é acessado por clientes globalmente
-   Você quer descarregar o tráfego de seu servidor Web
-   Você quer reduzir o número de conexões cliente simultâneas a seu servidor Web (há uma ótima discussão sobre o assunto em [Agrupamento e Minificação][Agrupamento e Minificação])
-   Você quer aumentar o tempo percebido de carga/atualização de suas páginas

## O que você aprenderá

Neste tutorial, você aprenderá a fazer o seguinte:

-   [Fornecer conteúdo estático por meio de um ponto de extremidade da CDN do Azure][Fornecer conteúdo estático por meio de um ponto de extremidade da CDN do Azure]
-   [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN][Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN]
-   [Configurar o cache da CDN para refletir a atualização de conteúdo desejada][Configurar o cache da CDN para refletir a atualização de conteúdo desejada]
-   [Fornecer conteúdo novo logo após usar cadeias de consulta][Fornecer conteúdo novo logo após usar cadeias de consulta]

## O que será necessário

Este tutorial tem os seguintes pré-requisitos:

-   Uma [conta do Microsoft Azure][conta do Microsoft Azure] ativa. Você pode se inscrever para uma conta de avaliação
-   Visual Studio 2013 com o [SDK do Azure][SDK do Azure]
-   Um aplicativo ASP.NET MVC simples para testar URLs da CDN. [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN][Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN] usa um aplicativo ASP.NET MVC como exemplo.
-   [PowerShell do Azure][PowerShell do Azure] (usado para [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN][Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN])

<div class="wa-note">
  <span class="wa-icon-bulb"></span>
  <h5><a name="note"></a>Para concluir este tutorial, voc&ecirc; precisa de uma conta do Azure:</h5>
  <ul>
    <li>Voc&ecirc; pode <a href="http://azure.microsoft.com/pt-br/pricing/free-trial/?WT.mc_id=A261C142F">abrir uma conta do Azure gratuitamente</a> - Voc&ecirc; obt&eacute;m cr&eacute;ditos que podem ser usados para experimentar servi&ccedil;os pagos do Azure e, mesmo ap&oacute;s eles serem utilizados, voc&ecirc; pode manter a conta e utilizar os servi&ccedil;os gratuitos do Azure, como os Sites.</li>
    <li>Voc&ecirc; pode <a href="http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F">ativar benef&iacute;cios para assinantes do MSDN</a> - Todos os meses, sua assinatura do MSDN oferece cr&eacute;ditos que podem ser usados para servi&ccedil;os pagos do Azure.</li>
  <ul>
</div>

<a name="static"></a>

## Fornecer conteúdo estático por meio de um ponto de extremidade da CDN do Azure

Nesta seção do tutorial, você aprenderá como criar uma CDN e a usará para fornecer seu conteúdo estático. As principais etapas envolvidas são:

1.  Criar uma conta de armazenamento
2.  Criar uma CDN vinculada à conta de armazenamento
3.  Criar um contêiner de blob em sua conta de armazenamento
4.  Carregar conteúdo para o contêiner de blob
5.  Vincular ao conteúdo que você carregou usando o URL da CDN

Vamos lá. Siga as etapas abaixo para começar a usar a CDN do Azure:

1.  Para criar um ponto de extremidade CDN, faça logon no [portal de gerenciamento do Azure][portal de gerenciamento do Azure].
2.  Crie uma conta de armazenamento clicando em **Novo \> Serviços de Dados \> Armazenamento \> Criação Rápida**. Especifique um URL, um local e clique em **Criar Conta de Armazenamento**.

    ![][]

    > [WACOM.NOTE] Observe que estou usando Ásia Oriental como a região, pois é distante o suficiente para que eu teste minha CDN por meio da América do Norte mais tarde.

3.  Quando o status da nova conta de armazenamento for **Online**, crie um novo ponto de extremidade da CDN que esteja vinculado à conta de armazenamento criada. Clique em **Novo \> Serviços de Aplicativos \> CDN \> Criação Rápida**. Selecione a conta de armazenamento criada e clique em **Criar**.

    ![][1]

    > [WACOM.NOTE] Após a criação da CDN, o portal do Azure mostrará seu URL e o domínio de origem ao qual ele está vinculado. No entanto, pode levar algum tempo para que a configuração do ponto de extremidade da CDN seja totalmente propagada a todos os locais de nó.

4.  Teste seu ponto de extremidade da CDN para garantir que ele esteja online executando um ping. Se o seu ponto de extremidade da CDN não tiver propagado para todos os nós, você verá uma mensagem semelhante à mensagem abaixo.

    ![][2]

    Aguarde mais uma hora e teste novamente. Quando tiver terminado de propagar para os nós, seu ponto de extremidade da CDN deverá responder aos pings.

    ![][3]

5.  Neste ponto, você já pode ver onde o ponto de extremidade da CDN determina que seja o nó da CDN mais próximo a você. No meu computador desktop, o endereço IP de resposta é **93.184.215.201**. Conecte-o a um site como [www.ip-address.org][www.ip-address.org] e veja que o servidor está localizado em Washington D.C.

    ![][4]

    Para obter uma lista de localizações de todos os nós da CDN, consulte [Locais dos Nós da Rede de Distribuição de Conteúdo (CDN) do Azure][Locais dos Nós da Rede de Distribuição de Conteúdo (CDN) do Azure].

6.  No portal do Azure, na guia **CDN**, clique no nome do ponto de extremidade CDN que você acabou de criar.

    ![][5]

7.  Clique em **Habilitar Cadeia de Consulta** para habilitar cadeias de consulta no cache da CDN do Azure. Após você ter habilitado, o mesmo link acessado com diferentes cadeias de consulta será armazenado em cache como entradas diferentes.

    ![][6]

    > [WACOM.NOTE] Embora habilitar a cadeia de consulta não seja necessário para esta seção do tutorial, é aconselhável fazer isso o quanto antes para maior conveniência, uma vez que qualquer alteração aqui levará tempo para ser propagada para o restante dos nós, e você não quer um acúmulo de conteúdo não habilitado para cadeia de consulta no cache do CDN (a atualização do conteúdo CDN será abordada mais adiante). Você verá como aproveitar isso em [Fornecer conteúdo novo imediatamente por meio de cadeias de consulta][Fornecer conteúdo novo logo após usar cadeias de consulta].

8.  No Visual Studio 2013, no Gerenciador de Servidores, clique no botão **Conectar ao Microsoft Azure**.

    ![][7]

9.  Siga os prompts para entrar na sua conta do Azure.
10. Após entrar, expanda **Microsoft Azure \> Armazenamento \> sua conta de armazenamento**. Clique com o botão direito em **Blob** e selecione **Criar Contêiner de Blob**.

    ![][8]

11. Especifique um nome de contêiner de blob e clique em **OK**.

    ![][9]

12. No Gerenciador de Servidores, clique duas vezes no contêiner de blob que criou para gerenciá-lo. Você deve ver a interface de gerenciamento no painel central.

    ![][10]

13. Clique no botão **Carregar blob** para carregar imagens, scripts ou folhas de estilo que são usadas pelas páginas da Web no contêiner de blob. O progresso do carregamento será exibido no **Log de Atividade do Microsoft Azure**, e os blobs aparecerão na exibição de contêiner quando forem carregados.

    ![][11]

14. Agora que carregou os blobs, você precisa torná-los públicos para acessá-los. No Gerenciador de Servidores, clique com o botão direito no contêiner e selecione **Propriedades**. Defina a propriedade **Acesso de Leitura Público** como **Blob**.

    ![][12]

15. Teste o acesso público a seus blogs navegando até o URL de um dos blobs em um navegador. Por exemplo, eu posso testar a primeira imagem de minha lista carregada com `http://cephalinstorage.blob.core.windows.net/cdn/cephas_lin.png`.

    Observe que não estou usando o endereço HTTPS fornecido na interface de gerenciamento do blob no Visual Studio. Usando HTTP, você testa se o conteúdo está acessível publicamente, o que é um requisito da CDN do Azure.

16. Se puder ver o blob renderizado corretamente no navegador, altere o URL de `http://<yourStorageAccountName>.blob.core.windows.net` para o URL de sua CDN do Azure. No meu caso, para testar a primeira imagem do meu ponto de extremidade da CDN, eu usaria `http://az623979.vo.msecnd.net/cdn/cephas_lin.png`.

    > [WACOM.NOTE] Você pode encontrar o URL do ponto de extremidade da CDN no portal de gerenciamento do Azure, na guia CDN.

    Se comparar o desempenho do acesso direto ao blob e do acesso pela CDN, você verá a melhoria de desempenho decorrente do uso da CDN do Azure. Abaixo, a captura de tela das ferramentas de desenvolvedor F12 do Internet Explorer 11 para acesso ao URL do blob para minha imagem:

    ![][13]

    E para acesso ao URL da CDN para a mesma imagem

    ![][14]

    Fique atendo aos números do tempo de **Solicitação**, que é o tempo até o primeiro byte, ou o tempo necessário para enviar a solicitação e receber a primeira resposta do servidor. Quando acesso o blob, que é hospedado na região da Ásia Oriental, são necessários 266 ms - uma vez que a solicitação precisa atravessar todo o oceano Pacífico somente para chegar ao servidor. No entanto, quando acesso a CDN do Azure, são necessários somente 16 ms, o que representa um ganho de quase **20 vezes no desempenho**!

17. Agora, basta usar o novo link em sua página da Web. Por exemplo, ei posso adicionar a seguinte marcação de imagem:

        <img alt="Mugshot" src="http://az623979.vo.msecnd.net/cdn/cephas_lin.png" />

Nesta seção, você aprendeu como criar um ponto de extremidade da CDN, carregar conteúdo nela e vincular a conteúdo da CDN por meio de qualquer página da Web.

<a name="upload"></a>

## Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN

Se quiser carregar de maneira simples todo o conteúdo de seu aplicativo Web ASP .NET para o ponto de extremidade da CDN, ou se implanta o aplicativo usando fornecimento contínuo (para ver um exemplo, consulte [Fornecimento contínuo para Serviços de nuvem no Azure][Fornecimento contínuo para Serviços de nuvem no Azure]), você pode usar o PowerShell do Azure para automatizar a sincronização dos arquivos de conteúdo mais recentes com blobs do Azure sempre que implantar sua aplicação da Web. Por exemplo, você pode executar o script em [Carregar Arquivos de Conteúdo de um Aplicativo ASP.NET para Blobs do Azure][Carregar Arquivos de Conteúdo de um Aplicativo ASP.NET para Blobs do Azure] para carregar todos os arquivos de conteúdo em um aplicativo ASP.NET. Para usar este script:

1.  No menu **Iniciar**, execute o **PowerShell do Microsoft Azure**.
2.  Na janela do PowerShell do Azure, execute `Get-AzurePublishSettingsFile` para baixar um arquivo de configurações de publicação para sua conta do Azure.
3.  Após ter baixado o arquivo de configurações de publicação, execute o seguinte:

        Import-AzurePublishSettingsFile "<yourDownloadedFilePath>"

    > [WACOM.NOTE] Após importar o arquivo de configurações de publicação, será a conta do Azure padrão usara para todas as sessões do PowerShell do Azure. Isso significa que as etapas acima precisam ser realizadas somente uma vez.

4.  Baixe o script na [página de download](<http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a>). Salve-o na pasta do projeto do aplicativo ASP.NET.
5.  Clique com o botão direito do mouse no script baixado e clique em **Propriedades**.
6.  Clique em **Desbloquear**.
7.  Abra uma janela do PowerShell e execute o seguinte:

        cd <ProjectFolder>
        .\UploadContentToAzureBlobs.ps1 -StorageAccount "<yourStorageAccountName>" -StorageContainer "<yourContainerName>"

Este script carrega todos os arquivos de suas pastas *\\Content* e *\\Scripts* na conta de armazenamento e contêiner especificados. Ele tem as seguintes vantagens:

-   Replicar automaticamente a estrutura de arquivo de seu projeto do Visual Studio
-   Criar automaticamente contêineres de blob conforme necessário
-   Reutilizar a mesma conta de armazenamento do Azure e ponto de extremidade da CDN para diversos aplicativos da Web, cada um em um contêiner de blob separado
-   Atualizar facilmente a CDN do Azure com novo conteúdo. Para mais informações sobre a atualização de conteúdo, consulte [Configurar o cache da CDN para refletir a atualização de conteúdo desejada][Configurar o cache da CDN para refletir a atualização de conteúdo desejada].

Para o parâmetro `-StorageContainer`, faz sentido usar o nome de seu aplicativo Web ou o nome do projeto do Visual Studio. Embora eu tenha usado o nome de contêiner genérico "cdn" anteriormente, usar o nome do aplicativo Web permite que o conteúdo relacionado seja organizado no mesmo contêiner facilmente identificável.

Após a conclusão do carregamento do conteúdo, você pode vincular a qualquer coisa em suas pastas *\\Content* e *\\Scripts* no código HTML, como nos arquivos .cshtml, usando `http://<yourCDNName>.vo.msecnd.net/<containerName>`. Aqui está um exemplo de algo que posso usar em uma exibição de Razor:

    <img alt="Mugshot" src="http://az623979.vo.msecnd.net/MyMvcApp/Content/cephas_lin.png" />

Para obter um exemplo de integração de scripts do PowerShell à sua configuração de fornecimento contínuo, consulte [Fornecimento contínuo de serviços de nuvem no Azure][Fornecimento contínuo para Serviços de nuvem no Azure].

<a name="update"></a>

## Configurar o cache da CDN para refletir a atualização de conteúdo desejada

Agora, suponha que após ter carregado os arquivos estáticos do aplicativo Web num contêiner de blob, você faça uma alteração em um dos arquivos o projeto e o carregue no contêiner de blob novamente. Você pode achar que ele é atualizado automaticamente no ponto de extremidade da CDN, mas na verdade fica confuso porque não vê a atualização refletida quando acessa o URL da CDN do conteúdo.

A verdade é que a CDN realmente atualiza automaticamente por meio do armazenamento de blob, mas faz isso aplicando uma regra padrão de cache de 7 dias ao conteúdo. Isso significa que após um nó da CDN efetuar pull do conteúdo do armazenamento de blob, o mesmo conteúdo não é atualizado até que expire no cache.

A boa notícia é que é possível personalizar o tempo de validade do cache. De maneira semelhante à maioria dos navegadores, a CDN do Azure respeita o tempo de validade especificado no cabeçalho de controle de cache do conteúdo. Você pode especificar um valor personalizado de cabeçalho de controle de cache navegando até o contêiner de blob no portal do Azure e editando as propriedades de blob. A captura de tela abaixo mostra a validade do cache definida como 1 hora (3.600 segundos).

![][15]

Você também pode fazer isso no script PowerShell para definir todos os cabeçalhos de controle de cache do blob. Para o script em [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN][Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN], encontre o seguinte trecho de código:

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
        -Properties @{ContentType=$contentType, CacheControl="public, max-age=3600"} `
        -Force

Talvez você ainda precise esperar que todo o conteúdo armazenado em cache por 7 dias expire na CDN do Azure antes que ela efetue pull do novo conteúdo, com o novo cabeçalho de controle de cache. Isso ilustra o fato de que valores de cache personalizados não ajudam se você quer que seu conteúdo fique ativo imediatamente, como atualizações JavaScript ou CSS. No entanto, você pode solucionar este problema de forma alternativa renomeando os arquivos ou controlando suas versões por meio de cadeias de consulta. Para obter mais informações, consulte [Fornecer conteúdo novo logo após usar cadeias de consulta][Fornecer conteúdo novo logo após usar cadeias de consulta].

Há, é claro, hora e lugar para o cache. Por exemplo, você pode ter conteúdo que não precise de atualização frequente, como os jogos da Copa do Mundo que podem ser atualizados a cada três horas, mas que receba tráfego global suficiente para que você queira descarregá-lo se seu servidor Web. Pode se tratar de um bom candidato para uso do cache da CDN do Azure.

<a name="query"></a>

## Fornecer conteúdo novo logo após usar cadeias de consulta

Na CDN do Azure, você pode habilitar cadeias de consulta para que o conteúdo de URLs com cadeias de consulta específicas seja armazenado em cache separadamente. É um ótimo recurso para ser usado se você quiser enviar por push determinados conteúdos para os navegadores cliente imediatamente, em vez de esperar que o conteúdo em cache da CDN expire. Suponhamos que eu publique minha página da Web com um número de versão no URL.

<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.0.0&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
Quando eu publico uma atualização de CSS e uso um número de versão diferente em meu URL CSS:

<pre class="prettyprint">
&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css?v=3.1.1&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

</p>
Para um ponto de extremidade da CDN que tenha cadeias de consulta habilitadas, os dois URLs são únicos um para o outro, e será feita uma nova solicitação para meu servidor Web para recuperar o novo *bootstrap.css*. Para um ponto de extremidade da CDN que não tem cadeias de consulta habilitadas, no entanto, se trata do mesmo URL, e ele simplesmente fornecerá o *bootstrap.css* armazenado em cache.

O truque, então, é atualizar o número de versão automaticamente. No Visual Studio, é fácil fazer isso. Em um arquivo .cshtml onde usaria o link acima, eu posso especificar um número de versão baseado no número de assembly.

<pre class="prettyprint">
@{
    <mark>var cdnVersion = System.Reflection.Assembly.GetAssembly(
        typeof(MyMvcApp.Controllers.HomeController))
        .GetName().Version.ToString();</mark>
}

...

&lt;link href=&quot;http://az623979.vo.msecnd.net/MyMvcApp/Content/bootstrap.css<mark>?v=@cdnVersion</mark>&quot; rel=&quot;stylesheet&quot;/&gt;
</pre>

Se alterar o número de assembly como parte de cada ciclo de publicação, você pode, da mesma forma, certificar-se de obter um número de versão único sempre que publicar seu aplicativo Web, que permanecerá o mesmo até o próximo ciclo de publicação. Ou você pode fazer com que o Visual Studio incremente automaticamente o número de versão de assembly sempre que o aplicativo Web for compilado abrindo *Properties\\AssemblyInfo.cs* no projeto do Visual Studio e usando `*` em `AssemblyVersion`. Por exemplo:

    [assembly: AssemblyVersion("1.0.0.*")]

## E quando a scripts e CSS agrupados?

Atualmente, o único lugar onde você encontrará uma integração adequada entre agrupamento ASP.NET e a CDN do Azure é nos [Serviços de nuvem do Azure][Serviços de nuvem do Azure]. Sem os Serviços de nuvem do Azure, é possível usar a CDN do Azure para seus grupos de scripts, com os seguintes empecilhos:

-   Você precisa carregar manualmente os scripts agrupados no armazenamento de blob. Uma solução programática é proposta em [stackoverflow][stackoverflow].
-   Em seus arquivos .cshtml, transforme as marcações de script/CSS renderizadas para usar a CDN do Azure. Por exemplo:

        @Html.Raw(Styles.Render("~/Content/css").ToString().Insert(0, "http://<yourCDNName>.vo.msecnd.net"))

Para obter mais informações sobre como integrar a CDN do Azure aos Serviços de nuvem do Azure, consulte [Integrar um aplicativo em nuvem à CDN do Azure][Integrar um aplicativo em nuvem à CDN do Azure].

# Mais informações

-   [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure][Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure]
-   [Integrar um aplicativo em nuvem à CDN do Azure][16]
-   [Usando a CDN para Azure][Usando a CDN para Azure]

  [Agrupamento e Minificação]: http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification
  [Fornecer conteúdo estático por meio de um ponto de extremidade da CDN do Azure]: #deploy
  [Automatizar o carregamento de conteúdo de seu aplicativo ASP.NET ao ponto de extremidade da CDN]: #upload
  [Configurar o cache da CDN para refletir a atualização de conteúdo desejada]: #update
  [Fornecer conteúdo novo logo após usar cadeias de consulta]: #query
  [conta do Microsoft Azure]: http://azure.microsoft.com/pt-br/account/
  [SDK do Azure]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [PowerShell do Azure]: http://go.microsoft.com/?linkid=9811175&clcid=0x409
  [abrir uma conta do Azure gratuitamente]: http://azure.microsoft.com/pt-br/pricing/free-trial/?WT.mc_id=A261C142F
  [ativar benefícios para assinantes do MSDN]: http://azure.microsoft.com/pt-br/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
  [portal de gerenciamento do Azure]: http://manage.windowsazure.com/
  []: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-1.PNG
  [1]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2.PNG
  [2]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-fail.PNG
  [3]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-3-succeed.PNG
  [www.ip-address.org]: http://www.ip-address.org
  [4]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-4.PNG
  [Locais dos Nós da Rede de Distribuição de Conteúdo (CDN) do Azure]: http://msdn.microsoft.com/pt-br/library/azure/gg680302.aspx
  [5]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequerya.PNG
  [6]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-2-enablequeryb.PNG
  [7]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-5.PNG
  [8]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-6.PNG
  [9]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-7.PNG
  [10]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-8.PNG
  [11]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-9.PNG
  [12]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-10.PNG
  [13]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-blob.PNG
  [14]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-static-11-cdn.PNG
  [Fornecimento contínuo para Serviços de nuvem no Azure]: http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-dotnet-continuous-delivery/
  [Carregar Arquivos de Conteúdo de um Aplicativo ASP.NET para Blobs do Azure]: http://gallery.technet.microsoft.com/scriptcenter/Upload-Content-Files-from-41c2142a
  [15]: media/cdn-serve-content-from-cdn-in-your-web-application/cdn-updates-1.PNG
  [Serviços de nuvem do Azure]: http://azure.microsoft.com/pt-br/services/cloud-services/
  [stackoverflow]: http://stackoverflow.com/a/13736433
  [Integrar um aplicativo em nuvem à CDN do Azure]: http://azure.microsoft.com/pt-br/documentation/articles/cloud-services-how-to-create-deploy/
  [Visão geral da Rede de Distribuição de Conteúdo (CDN) do Azure]: http://msdn.microsoft.com/library/azure/ff919703.aspx
  [16]: http://azure.microsoft.com/pt-br/Documentation/Articles/cdn-cloud-service-with-cdn/
  [Usando a CDN para Azure]: http://azure.microsoft.com/pt-br/documentation/articles/cdn-how-to-use/
