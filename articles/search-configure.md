<properties title="Configure Search in the Azure Preview Portal" pageTitle="Configure Search in the Azure Preview Portal" description="Configure Search in the Azure Preview Portal" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" videoId="" scriptId="" />

# Configurar a pesquisa no Portal de Visualização do Azure

[WACOM.INCLUDE [Este artigo usa o Portal de Visualização do Azure](../includes/preview-portal-note.md)]

A pesquisa no Microsoft Azure (visualização pública) está disponível no Portal de Visualização. Como administrador, você pode adicionar o serviço de Pesquisa a uma assinatura existente sem custos adicionais ao selecionar o serviço compartilhado, ou por um valor reduzido ao optar por recursos dedicados. Este artigo possui as seguintes seções:

<!--Table of contents -->

-   [Começar com o serviço gratuito][Começar com o serviço gratuito]
-   [Atualizar para a pesquisa padrão][Atualizar para a pesquisa padrão]
-   [Testar operações de serviço][Testar operações de serviço]
-   [Explorar as páginas de configuração do serviço de Pesquisa][Explorar as páginas de configuração do serviço de Pesquisa]
-   [Experimentar][Experimentar]

## Começar com o serviço gratuito

Os assinantes obtêm automaticamente acesso gratuito a um serviço de Pesquisa compartilhado, multilocatários, que você pode utilizar para fins de aprendizado, testes de prova de conceito ou pequenos projetos de desenvolvimento de pesquisa. Inscreva-se na versão gratuita por meio das seguintes etapas.

1.  Entre no [Portal de Visualização do Azure][Portal de Visualização do Azure] usando sua assinatura existente. Observe que esta URL leva você até o Portal de Visualização.

2.  Clique em **Novo** na parte inferior da página.

    ![][0]

3.  Clique em **Tudo**, no início da página.

    ![][1]

4.  Na Galeria, clique em **Dados, armazenamento, cache, + backup**.

    ![][2]

5.  Clique em **Ver Tudo** para expandir a lista de todos os serviços relacionados a dados.

    ![][3]

6.  Em Serviços de dados, clique em **Pesquisar**.

    ![][4]

7.  No final da página de Pesquisa, clique em **CRIAR**.

8.  Digite um nome de serviço em letras minúsculas para ser usado na URL do serviço, evitando traços, espaços e se atendo ao limite de 15 caracteres.

    ![][5]

9.  Clique na seta em **Camada de Preços** para selecionar uma opção de preço. Escolha **GRATUITO** e clique em **SELECIONAR** no final da página. A versão gratuita oferece capacidade suficiente para testar tutoriais e escrever código de prova de conceito, mas não deve ser utilizada para aplicativos de produção.

    ![][6]

10. Clique na seta em **Grupo de Recursos** para selecionar um grupo existente ou criar um novo grupo. Grupos de recursos são contêineres para serviços e contêineres usados para um fim comum. Por exemplo, se for compilar um aplicativo de pesquisa personalizado baseado na Pesquisa do Azure, Sites do Azure e armazenamento em Blob do Azure, você pode criar um grupo de recursos que mantém esses serviços juntos nas páginas de gerenciamento do portal.

11. Clique na seta em **Assinatura** se tiver múltiplas assinaturas e quiser usar uma assinatura diferente para este serviço de pesquisa.

12. Clique na seta em **Local** para escolher uma região de datacenter. Nesta visualização, você pode selecionar entre Oeste dos EUA, Leste dos EUA, Norte da Europa e Sudeste Asiático. Mais tarde, quando outras regiões estiverem disponíveis, selecione uma região para o serviço que você estiver criando. A distribuição de recursos em múltiplos datacenters não será uma configuração suportada para a visualização pública.

13. Clique em **CRIAR** para provisionar o serviço. Observe que **CRIAR** será disponibilizado somente após você preencher todos os valores obrigatórios.

Em alguns minutos, o serviço é criado. Você pode voltar para as configurações para obter a URL ou as chaves de API. As conexões com seu serviço de Pesquisa requerem que você tenha a URL e a chave da API para autenticar a chamada. Veja como encontrar esses valores facilmente:

1.  Vá para **Navegar** | **Tudo** | **Dados, armazenamento, cache, + backup** | **Ver Tudo** | **Serviços de Pesquisa**, clique em seu serviço de pesquisa para abrir o painel do serviço.

    ![][7]

2.  No painel do serviço, você verá os blocos referentes a **PROPRIEDADES** e **CHAVES**, e informações de uso que mostram rapidamente o uso de recursos.

    ![][8]

**PROPRIEDADES** contém a URL do serviço.

**CHAVES** contém as chaves de API usadas para autenticação.

**USO** mostra a contagem de documentos, recursos disponíveis e limites de armazenamento.

Continue até [Testar operações de serviço][Testar operações de serviço] para obter instruções de como se conectar ao serviço usando esses valores.

## Atualizar para a pesquisa padrão

A pesquisa padrão fornece a você recursos dedicados em um datacenter do Azure que só pode ser usado por você. Cargas de trabalho de pesquisa requerem réplicas de serviço e armazenamento. Quando se inscreve para a pesquisa padrão, você pode otimizar a configuração de serviço para usar mais de qualquer um dos recursos que for mais importante no seu contexto.

Ter recursos dedicados dará a você uma maior escala e melhor desempenho, mas nenhum recurso adicional. A pesquisa compartilhada e a pesquisa padrão oferecem os mesmos recursos.

Para usar a pesquisa padrão, crie um novo serviço de Pesquisa selecionando a camada de preço Padrão. Observe que o upgrade não é um upgrade no local da versão gratuita. A alteração para padrão, com seu potencial de dimensionamento, requer um novo serviço. Você precisará recarregar os índices e documentos usados por seu aplicativo de pesquisa.

Configurar recursos dedicados pode levar algum tempo (15 minutos ou mais).

**Etapa 1 - Criar um novo serviço com a Camada de Preços definida como Padrão**

1.  Entre no [Portal de Visualização do Azure][Portal de Visualização do Azure] usando sua assinatura existente.

2.  Clique em **Novo** na parte inferior da página.

3.  Clique em **Tudo**, no início da página.

4.  Na Galeria, clique em **Dados, armazenamento, cache, + backup**.

5.  Clique em **Ver Tudo** para expandir a lista de todos os serviços relacionados a dados.

6.  Em Serviços de dados, clique em **Pesquisar**.

7.  No final da página de Pesquisa, clique em **CRIAR**.

8.  Digite um nome de serviço em letras minúsculas para ser usado na URL do serviço, evitando traços, espaços e se atendo ao limite de 15 caracteres.

9.  Clique na seta em **Camada de Preços** para selecionar uma opção de preço. Escolha **PADRÃO** e clique em **SELECIONAR** no final da página.

![][9]

**Etapa 2 - Ajustar as unidades de pesquisa com base nos requisitos de escala**

A pesquisa padrão começa com uma réplica e uma partição cada, mas pode ser redimensionada facilmente para níveis de recursos maiores.

1.  Após o serviço ser criado, volte para o painel do serviço e clique no bloco **Escala**.

2.  Use os controles deslizantes para adicionar réplicas, partições ou ambas.

Réplicas e partições adicionais são cobradas em unidades de pesquisa. O total de unidades de pesquisa necessárias para dar suporte a qualquer configuração de recursos em particular é mostrado na página, conforme você adiciona recursos. Você pode verificar os [Detalhes de Preços][Detalhes de Preços] para obter as informações de cobrança por unidade.

![][10]

## Testar operações de serviço

Confirmar que seu serviço está funcionando e pode ser acessado por meio de um aplicativo cliente é a etapa final da configuração da Pesquisa. Este procedimento usa o Fiddler, disponível como um [download gratuito do Telerik][download gratuito do Telerik], para emitir solicitações HTTP e ver respostas. Usando o Fiddler, você pode testar a API imediatamente, sem precisar escrever nenhum código.

O procedimento a seguir funciona para a pesquisa padrão e a compartilhada. Nas etapas abaixo, você criará um índice, carregará documentos, consultará o índice e consultará o sistema quanto a informações de serviço.

### Crie um índice

1.  Inicie o Fiddler. No menu Arquivo, desabilite **Capturar Tráfego** para ocultar atividades HTTP externas não relacionadas à atividade atual. Na guia Composer, você formulará uma solicitação semelhante a esta:

    ![][11]

2.  Selecione **PUT**.

3.  Insira uma URL que especifique a URL do serviço (que você pode encontrar na página Propriedades), solicite atributos e a versão da API. Alguns aspectos a ter em mente:
	-   Use HTTPS como o prefixo
	-   O atributo de solicitação é "/indexes/hotels". Isso diz à Pesquisa para criar um índice chamado “hotels”.
	-   A versão da API fica em letras minúsculas, especificada como "?api-version=2014-07-31-preview". As versões da API são importantes porque a Pesquisa do Azure implanta atualizações regularmente. Em ocasiões raras, uma atualização do serviço pode introduzir uma alteração de última hora na API. Usando as versões da API, você pode continuar usando sua versão existente e atualizando para a seguinte quando for conveniente.

    A URL completa deve se parecer com o exemplo a seguir:

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

1.  Especifique o cabeçalho de solicitação, substituindo o host e a chave de API (em letra minúscula) pelos valores que são válidos para o seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

2.  No Corpo da Solicitação, copie os campos que compõem a definição do índice.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
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

3.  Clique em **Executar**.

Em alguns segundos, você deverá ver uma resposta HTTP 204 na lista de sessões, indicando que o índice foi criado com êxito.

Se obtiver o HTTP 504, veja se a URL especifica HTTPS. Se obtiver o HTTP 404, verifique a sintaxe novamente. Um HTTP 400 significa que há um erro com a chave de API (uma chave inválida ou um problema se sintaxe com como a chave de API for especificada).

### Carregue os documentos

Na guia Composer, sua solicitação para publicar documentos terá a seguinte aparência. O corpo da solicitação contém os dados de pesquisa de 4 hotéis.

![][12]

1.  Selecione **POST**.

2.  Insira uma URL iniciada por HTTPS, seguida da URL do serviço, seguida de "/indexes/\<'indexname'\>/docs/index?api-version=2014-07-31-preview". A URL completa deve se parecer com o exemplo a seguir:

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.  O Cabeçalho da solicitação deve ser igual ao anterior. Lembre-se de que você substituiu o host e a chave de API por valores que são válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  O Corpo da solicitação contém quatro documentos a serem adicionados ao índice de hotéis.

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

5.  Clique em **Executar**.

Em alguns segundos, você verá uma resposta HTTP 200 na lista de sessões. Isso indica que os documentos foram criados com êxito. Se você obtiver um 207, houve falha no carregamento de pelo menos um documento. Se você obtiver um 404, há um erro de sintaxe no cabeçalho ou no corpo da solicitação.

### Consulte o índice

Agora que o índice e os documentos foram carregados, você pode consultá-los. Na guia Composer, um comando GET que consulta seu serviço será semelhante ao seguinte:

![][13]

1.  Selecione **GET**.

2.  Insira uma URL iniciada por HTTPS, seguida da URL do serviço, seguida por "/indexes/\<'indexname'\>/docs?", seguido de parâmetros de consulta. Para um exemplo, use a URL a seguir, substituindo o nome de host de amostra por um que seja válido para seu serviço.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    Esta consulta pesquisa o termo “motel” e recupera categorias facetadas para as classificações.

3.  O Cabeçalho da solicitação deve ser igual ao anterior. Lembre-se de que você substituiu o host e a chave de API por valores que são válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

O código de resposta deve ser 200, e a saída de resposta deve ser semelhante à ilustração a seguir.

![][14]

O exemplo de consulta a seguir é da [operação Pesquisar Índice (API da Pesquisa do Azure)][operação Pesquisar Índice (API da Pesquisa do Azure)] no MSDN. Muitos dos exemplos de consulta deste tópico incluem espaços, que não são permitidos no Fiddler. Substitua cada espaço por um caractere + antes de colar na cadeia de consulta e tentar realizar a consulta no Fiddler:

**Antes da substituição dos espaços:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**Após a substituição dos espaços por +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### Consulte o sistema

Você também pode consultar o sistema para obter informações de contagens de documentos e consumo de armazenamento. Na guia Composer, sua solicitação será semelhante à seguinte, e a resposta retornará uma contagem do número de documentos e do espaço usado.

![][15]

1.  Selecione **GET**.

2.  Insira uma URL que inclui a URL do seu serviço, seguida por "/indexes/hotels/stats?api-version=2014-07-31-Preview":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.  Especifique o cabeçalho da solicitação, substituindo o host e a chave da API por valores que sejam válidos para seu serviço.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Deixe o corpo da solicitação vazio.

5.  Clique em **Executar**. Você deverá ver um código de status HTTP 200 na lista de sessões. Selecione a entrada publicada para seu comando.

6.  Clique na guia Inspetores | Cabeçalhos e selecione o formato JSON. Você deverá ver a contagem de documentos e o tamanho do armazenamento (em KB).

    ![][16]

## Explore as páginas de configuração do serviço de Pesquisa

Se você precisar de um lembrete de onde encontrar as páginas de configuração, siga as etapas a seguir para localizar o painel de serviço.

1.  Entre no [Portal de Visualização do Azure][Portal de Visualização do Azure] usando sua assinatura existente.
2.  Clique em **Navegar** | **Tudo**.

    ![][17]

3.  Selecione **Serviços de pesquisa** na lista. Você deverá ver uma lista de todos os serviços de Pesquisa criados em suas assinaturas.

4.  Clique em um serviço para abrir seu painel. Observe que os comandos **Iniciar**, **Parar** e **Excluir** estão no topo. O painel de serviço inclui blocos para ver as Propriedades e as Chaves e um Início rápido com links para informações e instruções. Role para baixo para ver o uso.

5.  Clique em **PROPRIEDADES**. Observe que a página de Propriedades abre à direita. A URL do serviço está no topo da página. Para obter as chaves de API usadas para autenticar o serviço, clique em **CHAVES**.

    ![][8]

<!--Next steps and links -->

## Experimentar

Pronto para a próxima etapa? Os links a seguir levam você a materiais adicionais que mostram como compilar e gerenciar aplicativos de pesquisa que usam a Pesquisa do Azure.

[Criar sua primeira solução de pesquisa do Azure][Criar sua primeira solução de pesquisa do Azure]

[Gerenciar sua solução de pesquisa no Microsoft Azure][Gerenciar sua solução de pesquisa no Microsoft Azure]

[Visão geral técnica da Pesquisa do Azure][Visão geral técnica da Pesquisa do Azure]

[API REST da Pesquisa do Azure][API REST da Pesquisa do Azure]

<!--Anchors--> 
<!--Image references--> 
<!--Link references-->

  [Começar com o serviço gratuito]: #sub-1
  [Atualizar para a pesquisa padrão]: #sub-2
  [Testar operações de serviço]: #sub-3
  [Explorar as páginas de configuração do serviço de Pesquisa]: #sub-4
  [Experimentar]: #next-steps
  [Portal de Visualização do Azure]: https://portal.azure.com
  [0]: ./media/search-configure/AzureSearch_Configure1_1_New.PNG
  [1]: ./media/search-configure/AzureSearch_Configure1_2_Everything.PNG
  [2]: ./media/search-configure/Azuresearch_Configure1_3_Gallery.PNG
  [3]: ./media/search-configure/AzureSearch_Configure1_4_GallerySeeAll.PNG
  [4]: ./media/search-configure/AzureSearch_Configure1_5_DataServicesList.PNG
  [5]: ./media/search-configure/AzureSearch_Configure1_6_URL.PNG
  [6]: ./media/search-configure/AzureSearch_Configure1_7_Free.PNG
  [7]: ./media/search-configure/AzureSearch_Configure1_8_SearchServiceList.PNG
  [8]: ./media/search-configure/AzureSearch_Configure1_18_Explore.PNG
  [9]: ./media/search-configure/AzureSearch_Configure1_9_Standard.PNG
  [Detalhes de Preços]: http://go.microsoft.com/fwlink/p/?LinkID=509792
  [10]: ./media/search-configure/AzureSearch_Configure1_10_ScaleUp.PNG
  [download gratuito do Telerik]: http://www.telerik.com/fiddler
  [11]: ./media/search-configure/AzureSearch_Configure1_11_PUTIndex.PNG
  [12]: ./media/search-configure/AzureSearch_Configure1_12_POSTDocs.PNG
  [13]: ./media/search-configure/AzureSearch_Configure1_13_GETQuery.PNG
  [14]: ./media/search-configure/AzureSearch_Configure1_14_GETQueryResponse.PNG
  [operação Pesquisar Índice (API da Pesquisa do Azure)]: http://msdn.microsoft.com/pt-br/library/dn798927.aspx
  [15]: ./media/search-configure/AzureSearch_Configure1_15_Stats.PNG
  [16]: ./media/search-configure/AzureSearch_Configure1_16_StatsResponse.PNG
  [17]: ./media/search-configure/AzureSearch_Configure1_17_BrowseEverything.PNG
  [Criar sua primeira solução de pesquisa do Azure]: ../search-create-first-solution/
  [Gerenciar sua solução de pesquisa no Microsoft Azure]: ../search-manage/
  [Visão geral técnica da Pesquisa do Azure]: http://msdn.microsoft.com/pt-br/library/dn798933.aspx
  [API REST da Pesquisa do Azure]: http://msdn.microsoft.com/pt-br/library/dn798935.aspx
