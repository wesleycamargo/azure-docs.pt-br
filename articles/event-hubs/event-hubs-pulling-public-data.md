<properties
    pageTitle="Efetuar pull de dados públicos para Hubs de Eventos do Azure | Microsoft Azure"
    description="Visão geral da importação dos Hubs de Eventos dos exemplos da Web"
    services="event-hubs"
    documentationCenter="na"
    authors="spyrossak"
    manager="timlt"
    editor=""/>

<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/31/2016"
    ms.author="spyros;sethm" />

# Extraindo dados públicos nos Hubs de Eventos do Azure

Em cenários típicos de Internet das Coisas (IoT), há dispositivos que você pode programar para enviar dados por push ao Azure, um Hub de Eventos do Azure ou um Hub IoT. Esses dois hubs são pontos de entrada no Azure para armazenamento, análise e visualização de uma infinidade de ferramentas disponibilizadas no Microsoft Azure. No entanto, os dois exigem que você envie dados, formate-os como JSON e os proteja de maneiras específicas. Isso nos leva à seguinte pergunta. O que fazer se você quiser trazer dados de fontes públicas ou privadas, onde os dados são expostos como um serviço Web ou algum tipo de feed, mas não tem a capacidade de alterar a forma como os dados são publicados? Considere o clima ou tráfego ou a cotação de ações; não é possível dizer ao NOAA ou ao WSDOT ou ao NASDAQ para configurar um envio por push para seu Hub de Eventos. Para resolver esse problema, escrevemos e disponibilizamos como software livre uma pequena amostra em nuvem que você pode modificar e implantar e que extrairá os dados dessas fontes e os enviará ao seu Hub de Eventos. A partir daí, você poderá fazer o que quiser com ele, é claro que de acordo com os termos de licença do produtor. Você pode encontrar o aplicativo [aqui](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/).

Observe que o código neste exemplo mostra apenas como extrair dados de feeds típicos da Web e como gravar em um Hub de Eventos do Azure. Ele NÃO tem a intenção de ser um aplicativo de produção, e nenhuma tentativa foi feita para torná-lo adequado para uso em tal ambiente. Ele é estritamente um exemplo do tipo "faça você mesmo" voltado para desenvolvedores. Além disso, a existência desse exemplo NÃO é semelhante a uma recomendação de que você deva **extrair** dados para o Azure em vez de enviá-los por **push**. Você deve revisar a segurança, o desempenho, a funcionalidade e os fatores de custo antes de definir uma arquitetura de ponta a ponta.

## Estrutura de aplicativo

O aplicativo é escrito em C# e a [descrição do exemplo](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/) contém todas as informações necessárias para modificar, compilar e publicar o aplicativo. As seções a seguir fornecem uma visão geral de alto nível do que o aplicativo faz.

Vamos começar com a suposição de que você tenha acesso a um feed de dados. Por exemplo, talvez você queira extrair os dados de tráfego do Departamento de transporte de Washington, ou os dados meteorológicos do NOAA, para exibir relatórios personalizados ou para combinar esses dados com outros dados em seu aplicativo. Você também precisará configurar um Hub de Eventos do Azure e conhecer a cadeia de conexão necessária para acessá-lo.

Quando a solução GenericWebToEH é iniciada, ela lê um arquivo de configuração (App.config) para obter diversas coisas:

1. A URL, ou uma lista de URLs, do site de publicação dos dados. Idealmente, esse é um site que publica dados em JSON, como aqueles citados pelo WSDOT [aqui](http://www.wsdot.wa.gov/Traffic/api/). 
2. Credenciais para a URL, se forem necessárias. Muitas fontes públicas não precisam de credenciais, ou você pode colocar as credenciais na cadeia de caracteres da URL. Outras exigem que você as forneça separadamente. (Observe que você pode especificar apenas um conjunto de credenciais nesse aplicativo e, portanto, isso só funcionará se você especificar somente uma URL, não uma lista de URLs).
3. A cadeia de conexão do Barramento de Serviço e o nome do Hub de Eventos no namespace desse Barramento de Serviço, ao qual você enviará os dados por push. Você pode encontrar essas informações no portal clássico do Azure.
4. Um intervalo de suspensão, em milissegundos, para o intervalo entre a sondagem do site de dados públicos. Essa configuração exige um pouco de criatividade. Se você realizar sondagens com muito pouca frequência, poderá perder dados. Por outro lado, se você realizar sondagens com muita frequência, poderá obter muitos dados repetitivos ou, pior ainda, poderá ser bloqueado como um bot perigoso. Considere a frequência de atualização da fonte de dados; os dados meteorológicos ou de tráfego podem ser atualizados a cada 15 minutos, mas talvez as cotações de ações sejam atualizadas em intervalos de segundos, dependendo de onde você as obtêm. 
5. Um sinalizador para informar ao aplicativo se os dados são coletados como XML ou JSON. Como você precisa enviar os dados para um Hub de Eventos, o aplicativo tem um módulo para converter XML em JSON antes de enviar.

Depois de ler o arquivo de configuração, o aplicativo entra em um loop, acessando o site público, convertendo os dados, se for necessário, gravando-os em seu Hub de Eventos e aguardando o intervalo de suspensão antes de fazer tudo isso novamente. Especificamente:

  * Leitura do site público. Para receber dados prontos para envio, a instância da classe RawXMLWithHeaderToJsonReader é usada no Azure/GenericWebToEH/ApiReaders/RawXMLWithHeaderToJsonReader.cs. Ela lê o fluxo de origem no método GetData() e, em seguida, divide em partes menores (ou seja, registros) usando GetXmlFromOriginalText. Este método lerá XML, como também um JSON bem formado ou uma matriz JSON. Em seguida, o processamento é iniciado com a configuração MergeToXML do App.config (default=empty).
  * Os dados de envio e recebimento são implementados como um loop no método Process() em Program.cs. Depois de receber os resultados de saída do GetData(), o método enfileira valores separados para o Hub de Eventos.

## Próximas etapas

Para implantar a solução, clone ou baixe o aplicativo [GenericWebToEH](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-importfromweb/), edite o arquivo App.config, compile-o e finalmente publique-o. Depois de publicar o aplicativo, você poderá vê-lo em execução no portal clássico do Azure em Serviços de Nuvem, e pode alterar algumas das definições de configuração (como o destino do Hub de Eventos e o intervalo de suspensão) na guia **Configurar**.

Veja mais exemplos de Hubs de Eventos na [galeria de exemplos do Azure](https://azure.microsoft.com/documentation/samples/?service=event-hubs) e no [MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5).

<!---HONumber=AcomDC_0601_2016-->