<properties 
	pageTitle="Introdução à Pesquisa do Azure | Microsoft Azure" 
	description="Introdução à Pesquisa do Azure" 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""
    tags="azure-portal"/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="07/08/2015" 
	ms.author="heidist"/>

# Introdução à Pesquisa do Azure

A Pesquisa do Microsoft Azure é um novo serviço que permite que você incorpore a funcionalidade de pesquisa em aplicativos personalizados. Ela fornece o mecanismo de pesquisa e armazenamento para seus dados, os quais você acessa e gerencia usando uma biblioteca .NET ou uma API REST.

Este artigo apresenta a API REST de Pesquisa do Azure.

Uma abordagem alternativa para desenvolvedores do .NET é usar o SDK do .NET da Pesquisa do Azure. Confira [Introdução à pesquisa do Azure no .NET](search-get-started-dotnet.md) ou [Como usar o SDK do .NET da Pesquisa do Azure](search-howto-dotnet-sdk.md) para obter detalhes.


> [AZURE.NOTE]Para concluir este tutorial, é necessário ter uma [Assinatura do Azure](../includes/free-trial-note.md). Se você não estiver pronto para se inscrever para uma assinatura de avaliação, pode ignorar este tutorial e optar por [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/). Essa opção alternativa fornece uma Pesquisa do Azure com um aplicativo Web ASP.NET gratuitamente, uma hora por sessão, sem precisar de uma assinatura.
 
<a id="sub-1"></a>
## Começar com o serviço gratuito

Como administrador, você pode adicionar o serviço de Pesquisa a uma assinatura existente sem custos adicionais ao selecionar o serviço compartilhado, ou por um valor reduzido ao optar por recursos dedicados.

Os assinantes obtêm automaticamente acesso gratuito a um serviço de Pesquisa compartilhado, multilocatários, que você pode utilizar para fins de aprendizado, testes de prova de conceito ou pequenos projetos de desenvolvimento de pesquisa. Inscreva-se na versão gratuita por meio das seguintes etapas.

1. Entre no [Portal do Azure](https://portal.azure.com) usando sua assinatura existente. Observe que esta URL leva você até o Portal de Visualização. O uso do Portal de Visualização é um requisito. 

2. Clique em **Novo** na parte superior da página.
 
  	![][6]

3. Clique em **Dados + Armazenamento** | **Pesquisa**.

	- Digite um nome de serviço em letras minúsculas para ser usado na URL do serviço, evitando espaços e se atendo ao limite de 15 caracteres.

	- Clique na seta em **Camada de Preços** para selecionar uma opção de preço. Escolha **GRATUITO** e clique em **SELECIONAR** no final da página. A versão gratuita oferece capacidade suficiente para testar tutoriais e escrever código de prova de conceito, mas não deve ser utilizada para aplicativos de produção.

	- Clique na seta em **Grupo de Recursos** para selecionar um grupo existente ou criar um novo grupo. Grupos de recursos são contêineres para serviços e contêineres usados para um fim comum. Por exemplo, se for compilar um aplicativo de pesquisa personalizado baseado na Pesquisa do Azure, Sites do Azure e armazenamento em Blob do Azure, você pode criar um grupo de recursos que mantém esses serviços juntos nas páginas de gerenciamento do portal.

	- Clique na seta em **Assinatura** se tiver múltiplas assinaturas e quiser usar uma assinatura diferente para este serviço de pesquisa.

	- Clique na seta em **Local** para escolher uma região de datacenter. Nesta visualização, você pode selecionar entre Oeste dos EUA, Leste dos EUA, Norte da Europa e Sudeste Asiático. Mais tarde, quando outras regiões estiverem disponíveis, selecione uma região para o serviço que você estiver criando. A distribuição de recursos em múltiplos datacenters não será uma configuração suportada para a visualização pública.

4. Clique em **CRIAR** para provisionar o serviço. Observe que **CRIAR** será disponibilizado somente após você preencher todos os valores obrigatórios.

Em alguns minutos, o serviço é criado. Você pode voltar para as configurações para obter a URL ou as chaves de API. As conexões com seu serviço de Pesquisa requerem que você tenha a URL e a chave da API para autenticar a chamada. Veja como encontrar esses valores facilmente:

14. Vá para **Início** para abrir o painel. Clique no serviço de Pesquisa para abrir o painel do serviço. 

  	![][13]

15.	No painel do serviço, você verá os blocos referentes a **PROPRIEDADES** e **CHAVES**, e informações de uso que mostram rapidamente o uso de recursos.

Continue até [Testar operações de serviço](#sub-3) para obter instruções de como se conectar ao serviço usando esses valores.

<a id="sub-2"></a>
## Atualizar para a pesquisa padrão

A pesquisa padrão fornece a você recursos dedicados em um datacenter do Azure que só pode ser usado por você. Cargas de trabalho de pesquisa requerem réplicas de serviço e armazenamento. Quando se inscreve para a pesquisa padrão, você pode otimizar a configuração de serviço para usar mais de qualquer um dos recursos que for mais importante no seu contexto.

Ter recursos dedicados dará a você uma maior escala e melhor desempenho, mas nenhum recurso adicional. A pesquisa compartilhada e a pesquisa padrão oferecem os mesmos recursos.

Para usar a pesquisa padrão, crie um novo serviço de Pesquisa selecionando a camada de preço Padrão. Observe que o upgrade não é um upgrade no local da versão gratuita. A alteração para padrão, com seu potencial de dimensionamento, requer um novo serviço. Você precisará recarregar os índices e documentos usados por seu aplicativo de pesquisa.

Configurar recursos dedicados pode levar algum tempo (15 minutos ou mais).

**Etapa 1 - Criar um novo serviço com a Camada de preços definida como Standard**

1. Entre no [Portal do Azure](https://portal.azure.com) usando sua assinatura existente. 

2. Clique em **Novo** na parte inferior da página.

4. Na Galeria, clique em **Dados + Armazenamento** |**Pesquisa**.

7. Preencha as definições de configuração do serviço e, em seguida, clique em **CRIAR**.

8. Selecione uma opção de preço na **Camada de preços**. Escolha **PADRÃO** e clique em **SELECIONAR** no final da página.

**Etapa 2 - Ajustar unidades de pesquisa com base nos requisitos de escala**

A pesquisa padrão começa com uma réplica e uma partição cada, mas pode ser redimensionada facilmente para níveis de recursos maiores.

1.	Após o serviço ser criado, volte para o painel do serviço e clique no bloco **Escala**.

2.	Use os controles deslizantes para adicionar réplicas, partições ou ambas.

Réplicas e partições adicionais são cobradas em unidades de pesquisa. O total de unidades de pesquisa necessárias para dar suporte a qualquer configuração de recursos em particular é mostrado na página, conforme você adiciona recursos.

Você pode verificar os [Detalhes de Preços](http://go.microsoft.com/fwlink/p/?LinkID=509792) para obter as informações de cobrança por unidade. Consulte [Limites e restrições](http://msdn.microsoft.com/library/azure/dn798934.aspx) para ajudá-lo a decidir como configurar combinações de partição e de réplica.

 ![][15]

<a id="sub-3"></a>
## Testar operações de serviço

Confirmar que seu serviço está funcionando e pode ser acessado por meio de um aplicativo cliente é a etapa final da configuração da Pesquisa. Este procedimento usa o Fiddler, disponível como um [download gratuito do Telerik](http://www.telerik.com/fiddler), para emitir solicitações HTTP e ver respostas. Usando o Fiddler, você pode testar a API imediatamente, sem precisar escrever nenhum código.

O procedimento a seguir funciona para a pesquisa padrão e a compartilhada. Nas etapas abaixo, você criará um índice, carregará documentos, consultará o índice e consultará o sistema quanto a informações de serviço.

### Crie um índice

1. Inicie o Fiddler. No menu Arquivo, desabilite **Capturar Tráfego** para ocultar atividades HTTP externas não relacionadas à atividade atual. Na guia Composer, você formulará uma solicitação semelhante a esta: 

  	![][16]

2. Selecione **PUT**.

3. Insira uma URL que especifique a URL do serviço (que você pode encontrar na página Propriedades), solicite atributos e a versão da API. Alguns aspectos a ter em mente:
   + Use HTTPS como o prefixo
   + O atributo de solicitação é "/indexes/hotels". Isso diz à Pesquisa para criar um índice chamado “hotels”.
   + A versão da API fica em letras minúsculas, especificada como "?api-version=2015-02-28". As versões da API são importantes porque a Pesquisa do Azure implanta atualizações regularmente. Em ocasiões raras, uma atualização do serviço pode introduzir uma alteração de última hora na API. Usando as versões da API, você pode continuar usando sua versão existente e atualizando para a seguinte quando for conveniente.

    A URL completa deve se parecer com o exemplo a seguir:

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	Especifique o cabeçalho da solicitação, substituindo o host e a chave da API por valores que sejam válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	No Corpo da Solicitação, copie os campos que compõem a definição do índice.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
          {"name": "category", "type": "Edm.String"},
          {"name": "tags", "type": "Collection(Edm.String)"},
          {"name": "parkingIncluded", "type": "Edm.Boolean"},
          {"name": "smokingAllowed", "type": "Edm.Boolean"},
          {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
          {"name": "rating", "type": "Edm.Int32"},
          {"name": "location", "type": "Edm.GeographyPoint"}
         ] 
        }

6.	Clique em **Executar**.

Em alguns segundos, você deverá ver uma resposta HTTP 201 na lista de sessões, indicando que o índice foi criado com êxito.

Se obtiver o HTTP 504, veja se a URL especifica HTTPS. Caso veja HTTP 400 ou 404, confira o corpo da solicitação para verificar se não houve erros ao copiar e colar. Um HTTP 403 normalmente indica um problema com a chave de API (uma chave inválida ou um problema de sintaxe com o modo que a chave de API está especificada).

### Carregue os documentos

Na guia Composer, sua solicitação para publicar documentos terá a seguinte aparência. O corpo da solicitação contém os dados de pesquisa de 4 hotéis.

   ![][17]

1. Selecione **POST**.

2.	Insira uma URL iniciada por HTTPS, seguida da URL do serviço, seguida de "/indexes/<'nomedoíndice'>/docs/index?api-version=2015-02-28". A URL completa deve se parecer com o exemplo a seguir:

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	O Cabeçalho da solicitação deve ser igual ao anterior. Lembre-se de que você substituiu o host e a chave de API por valores que são válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	O Corpo da solicitação contém quatro documentos a serem adicionados ao índice de hotéis.

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
        	"hotelName": "Fancy Stay",
        	"category": "Luxury",
        	"tags": ["pool", "view", "wifi", "concierge"],
        	"parkingIncluded": false,
        	"smokingAllowed": false,
        	"lastRenovationDate": "2010-06-27T00:00:00Z",
        	"rating": 5,
        	"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "2",
        	"baseRate": 79.99,
        	"description": "Cheapest hotel in town",
        	"hotelName": "Roach Motel",
        	"category": "Budget",
        	"tags": ["motel", "budget"],
        	"parkingIncluded": true,
        	"smokingAllowed": true,
        	"lastRenovationDate": "1982-04-28T00:00:00Z",
        	"rating": 1,
        	"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	Clique em **Executar**.

Em alguns segundos, você verá uma resposta HTTP 200 na lista de sessões. Isso indica que os documentos foram criados com êxito. Se você obtiver um 207, houve falha no carregamento de pelo menos um documento. Se você obtiver um 404, há um erro de sintaxe no cabeçalho ou no corpo da solicitação.

### Consulte o índice

Agora que o índice e os documentos foram carregados, você pode consultá-los. Na guia Composer, um comando GET que consulta seu serviço será semelhante ao seguinte:

   ![][18]

1.	Selecione **GET**.

2.	Insira uma URL iniciada por HTTPS, seguida da URL do serviço, seguida por "/indexes/<'indexname'>/docs?", seguido de parâmetros de consulta. Para um exemplo, use a URL a seguir, substituindo o nome de host de amostra por um que seja válido para seu serviço.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Esta consulta pesquisa o termo “motel” e recupera categorias facetadas para as classificações.

3.	O Cabeçalho da solicitação deve ser igual ao anterior. Lembre-se de que você substituiu o host e a chave de API por valores que são válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

O código de resposta deve ser 200, e a saída de resposta deve ser semelhante à ilustração a seguir.
 
   ![][19]

O exemplo de consulta a seguir é da [operação Pesquisar Índice (API da Pesquisa do Azure)](http://msdn.microsoft.com/library/dn798927.aspx) no MSDN. Muitos dos exemplos de consulta deste tópico incluem espaços, que não são permitidos no Fiddler. Substitua cada espaço por um caractere + antes de colar na cadeia de consulta e tentar realizar a consulta no Fiddler:

**Antes da substituição dos espaços:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Após a substituição dos espaços por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### Consulte o sistema

Você também pode consultar o sistema para obter informações de contagens de documentos e consumo de armazenamento. Na guia Composer, sua solicitação será semelhante à seguinte, e a resposta retornará uma contagem do número de documentos e do espaço usado.

   ![][20]

1.	Selecione **GET**.

2.	Insira uma URL que inclua a URL do seu serviço, seguida por "/indexes/hotels/stats?api-version=2015-02-28":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28 

3.	Especifique o cabeçalho da solicitação, substituindo o host e a chave da API por valores que sejam válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Deixe o corpo da solicitação vazio.

5.	Clique em **Executar**. Você deverá ver um código de status HTTP 200 na lista de sessões. Selecione a entrada publicada para seu comando.

6.	Clique na guia Inspetores | Cabeçalhos e selecione o formato JSON. Você deverá ver a contagem de documentos e o tamanho do armazenamento (em KB).

 	![][21]

<a id="sub-4"></a>
## Explorar o painel do serviço de Pesquisa

Se você precisar de um lembrete de onde encontrar as páginas de configuração, siga as etapas a seguir para localizar o painel de serviço.

1.	Entre no [Portal do Azure](https://portal.azure.com) usando sua assinatura existente. 
2.	Clique em **Início** e, em seguida, clique no bloco do serviço de Pesquisa.

 	![][22]

4.	Clicar no bloco abre o painel de serviço. Observe que os comandos **Iniciar**, **Parar** e **Excluir** estão no topo.

5.	Observe que a URL do serviço está no topo da página. Você precisará dessa URL para se conectar a seu serviço de Pesquisa do Azure.
	
7.	Clique no ícone **CHAVES** para exibir as chaves de api. Você precisará de uma chave de Administração para se autenticar no serviço. Você pode usar a primária ou a secundária. Opcionalmente, você pode criar chaves de consulta para acesso somente leitura ao serviço.


<!--Next steps and links -->
<a id="next-steps"></a>
## Experimentar

Pronto para a próxima etapa? Os links a seguir levam você a materiais adicionais que mostram como compilar e gerenciar aplicativos de pesquisa que usam a Pesquisa do Azure.

- [Criar um exemplo de Pesquisa Geoespacial na Pesquisa do Azure](search-create-geospatial.md)

- [Gerenciar sua solução de pesquisa no Microsoft Azure](search-manage.md)

- [O que é a Pesquisa do Azure?](search-what-is-azure-search.md)

- [API REST da Pesquisa do Azure](http://msdn.microsoft.com/library/dn798935.aspx)

- [SDK do .NET para Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [Vídeo do Canal 9: Introdução à pesquisa do Azure](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [Vídeo do Channel 9: Pesquisa do Azure e Dados Geoespaciais](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [Episódio 152 do Cloud Cover: gerar um índice de pesquisa do Azure](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=July15_HO4-->