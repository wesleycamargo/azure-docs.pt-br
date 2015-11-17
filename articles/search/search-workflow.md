<properties
	pageTitle="Fluxo de trabalho típico para o desenvolvimento de Pesquisa do Azure | Microsoft Azure | Serviço de pesquisa hospedado na nuvem"
	description="Um fluxo de trabalho ou roteiro para criar aplicativos de protótipo e produção que se integram à Pesquisa do Azure, um serviço de pesquisa hospedado na nuvem."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Fluxo de trabalho típico para o desenvolvimento da Pesquisa do Azure

Este artigo é um roteiro para a inclusão da Pesquisa do Azure, um serviço de pesquisa hospedado na nuvem, como um componente que oferece experiência de pesquisa no seu aplicativo personalizado. Dependendo se você estiver testando as coisas ou pronto para começar de vez, orientações preliminares sobre como integrar a Pesquisa do Azure no seu projeto de desenvolvimento personalizado podem ser úteis.

Nas seções a seguir, vamos separar um fluxo de trabalho típico para um protótipo inicial que ajudarão você a avaliar como o Azure pesquisa atende aos requisitos de pesquisa do seu aplicativo. A parte dois deste artigo aborda as decisões de design importantes que levam a um esforço de desenvolvimento de aplicativo mais intenso.

Antes de começar a criação de protótipos, recomendamos aprimorar-se com um dos nossos tutoriais de Introdução ou esta [apresentação de aprofundamento de vídeo de uma hora](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/). Os tutoriais de introdução são oferecidos nestas linguagens: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md) e [Node.JS](search-get-started-nodejs.md).

## Desenvolvimento de protótipo

O caminho mais rápido para um protótipo bem-sucedido normalmente inclui as etapas indicadas nesta seção. Tais etapas incluem provisionar um serviço, definir um esquema para o índice, carregar o índice com documentos e consultar o índice.

Para aplicativos com dados voláteis (por exemplo, se o caso comum inclui alterações rápidas em estoque ou conteúdo), o protótipo deve incluir um componente de atualização de documentos.

   ![][1]

### Etapa 1: provisionar o serviço

A Pesquisa do Azure é um serviço online totalmente gerenciado disponível por meio de uma assinatura do Azure. [Depois que você se inscrever no Azure](http://azure.microsoft.com/pricing/free-trial/), é muito rápido adicionar o serviço de Pesquisa. Visite [Criar um serviço de Pesquisa no portal](search-create-service-portal.md) para obter instruções sobre como adicionar um serviço de pesquisa à sua assinatura.

Há duas camada de preços à sua escolha. Recomendamos o serviço compartilhado (gratuito) para criação de protótipos, com a limitação de que você terá de trabalhar com um pequeno subconjunto dos dados. O serviço compartilhado é gratuito para os assinantes já existentes (com assinaturas de avaliação ou regular) e de rápida instalação, mas restringe o número de índices e documentos que você pode usar a até três índices, até 10 mil documentos por índice ou 50 MB de armazenamento no total, o que ocorrer primeiro.

### Etapa 2: Criar o índice

Depois de criar o serviço, você está pronto para criar um índice, começando com a definição de esquema.

A maneira mais rápida e fácil de criar um índice é por meio do portal do Azure. No mínimo, cada documento deve ter uma chave exclusiva e pelo menos um campo contendo dados pesquisáveis. Para começar, consulte [Criar um índice no portal](search-create-index-portal.md).

> [AZURE.NOTE]Dentro de um índice da Pesquisa do Azure
>
> Um *índice* são dados organizados e persistentes que servem como o *corpus de pesquisa* para todas as operações de pesquisa subsequentes. O corpus de pesquisa é armazenado na nuvem como parte da sua assinatura do serviço de Pesquisa, que permite que operações de pesquisa sejam executadas rapidamente e consistentemente. Na terminologia de pesquisa, um item em seu corpus de pesquisa é chamado de *documento*, sendo a soma total de todos os documentos a *coleção de documentos*.
>
>Um *esquema de índice* define todos os campos dentro de um documento por nome, tipo de dados e atributos que especificam se o campo é pesquisável, filtrável, facetável e assim por diante.
>
> Além da estrutura do documento, um esquema de índice também especifica os perfis de pontuação que fornecem critérios para impulsionar a pontuação de pesquisa e definições de configuração que habilitam consultas de preenchimento automático (sugestores) e solicitações de consulta CORS entre domínios. *Para protótipos, recomendamos começar simplesmente especificando apenas os campos em um documento*, e adicionar outros recursos gradativamente (consulte a etapa 5 para obter uma lista de recursos adicionais a serem adicionados mais tarde).
>
> Aplicado a um exemplo real, considere um aplicativo de comércio eletrônico. O índice de pesquisa conteria todos os produtos ou serviços que podem ser pesquisados no seu aplicativo (tudo o que é retornado nos resultados da pesquisa). Deve haver um documento para cada SKU. Cada documento inclui o nome do produto, marca, tamanhos, preço, cores e até mesmo referências a imagens ou outros arquivos de recurso que você deseja que sejam retornados nos resultados da pesquisa.

### Etapa 3: Carregar documentos

Depois de salvar o índice na Pesquisa do Azure, a próxima etapa é popular o índice com documentos. Nesta etapa, os dados são carregados, analisados, seus tokens são aplicados e eles são então armazenados em estruturas de dados (tais como índices invertidos) projetadas para cargas de trabalho de pesquisa.

Os dados que você carrega um índice devem estar de acordo com o esquema definido na etapa anterior. Os dados dos documentos são representados por um conjunto de pares de chaves/valores para cada campo, no formato JSON. Se o esquema especificar um campo de ID (chave), um campo de nome, um campo de número e um campo de URL (que pode ocorrer se imagens externas fizerem parte dos resultados da pesquisa), todos os documentos enviados ao índice devem ter valores existentes (ou nulo) para cada campo.

Há várias maneiras de carregar documentos, porém até o presente momento todas exigem uma API. Para a maioria dos protótipos, esta etapa pode ser a mais demorada devido a um requisito de codificação. As opções são descritas posteriormente neste artigo.

> [AZURE.NOTE]Lembre-se de que o serviço compartilhado está limitado a 10 mil documentos por índice. Certifique-se de reduzir o seu conjunto de dados para que ele fique abaixo dos limites. Consulte [Limites e restrições](search-limits-quotas-capacity.md) para obter mais informações.

#### Como carregar dados para um índice

Nossa abordagem é usar um indexador. Para fontes de dados relacionais do Banco de Dados de Documentos ou o SQL Server no Azure (especificamente o Banco de Dados SQL do Azure ou o SQL Server em uma VM do Azure), você pode usar [indexadores](https://msdn.microsoft.com/library/dn946891.aspx) para recuperar documentos de uma fonte de dados com suporte. Os exemplos de código que usam indexadores para carregar documentos podem ser encontrados em qualquer um desses Tutoriais de Introdução: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md) e [Node.JS](search-get-started-nodejs.md).

Uma segunda opção é escrever um programa simples usando a API REST ou a biblioteca .NET para carregar os documentos:

- [Adicionar, atualizar ou excluir documentos (API REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Classe DocumentOperationsExtensions](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

Uma terceira opção que funciona para pequenos conjuntos de dados é usar o [Fiddler](search-fiddler.md) ou o [Chrome Postman](search-chrome-postman.md) para carregar documentos.

Uma quarta opção, talvez a mais fácil delas, é obter código do [Exemplo de API REST do Adventure Works C#](https://azuresearchadventureworksdemo.codeplex.com/) que carrega os documentos de um banco de dados incorporado (.mdf) na solução ou do [Exemplo de API REST de Perfis de Pontuação C#](https://azuresearchscoringprofiles.codeplex.com/) que carrega dados de arquivos de dados JSON incluídos na solução.

> [AZURE.TIP]Você pode modificar e executar o [exemplo de perfis de pontuação](https://azuresearchscoringprofiles.codeplex.com/), substituindo os arquivos JSON de dados e o arquivo schema.json com dados válidos para seu aplicativo.

### Etapa 4: Consultar documentos

Depois que os documentos forem carregados no índice, você poderá escrever sua primeira consulta.

A maneira mais rápida de obter resultados da pesquisa iniciais de seu serviço de Pesquisa é usar o [Fiddler](search-fiddler.md) ou o [Chrome Postman](search-chrome-postman.md) para exibir uma resposta, mas na verdade, é mais útil escrever um código simples da interface de usuário para exibir os resultados em um formato legível.

APIs para operações de pesquisa incluem:

- [Operação de pesquisa de documentos](https://msdn.microsoft.com/library/dn798927.aspx)
- [Classe SearchIndexClient](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

Consultas de Pesquisa do Azure podem ser muito simples. Incluir `search=*` no URI retornará os primeiros 50 itens no seu corpus de pesquisa. Especificar `search=<some phrase>` executará uma pesquisa de texto completo na frase, retornando até 50 documentos, supondo que haja pelo menos 50 documentos que contêm uma correspondência do termo de entrada.

O padrão é de 50 documentos. Você pode alterar o número de itens retornados usando o parâmetro de consulta `$Count`. Esse parâmetro é documentado em [Pesquisar documentos](https://msdn.microsoft.com/library/dn798927.aspx).

> [AZURE.TIP]Uma lista mais abrangente dos exemplos de consulta pode ser encontrada em [Pesquisar documentos](https://msdn.microsoft.com/library/dn798927.aspx), mas você também poderá examinar a [referência de sintaxe](https://msdn.microsoft.com/library/dn798920.aspx) para examinar a lista de operadores com suporte.

### Etapa 5: Explorar mais recursos

Agora que você já tem um serviço e um índice, poderá explorar recursos para desenvolver ainda mais a experiência de pesquisa. Uma pequena lista de recursos a investigar é listada a seguir.

**Páginas de pesquisa** geralmente incluem contagens de documentos em um conjunto de resultados ou use paginação para subdividir os resultados em números mais fáceis de administrar. Consulte [Paginação](search-pagination-page-layout.md) para obter detalhes.

**searchMode=all** é um parâmetro de consulta que altera como a Pesquisa do Azure avalia o operador NOT. Por padrão, consultas que incluem NOT (-) expandem os resultados em vez de limitá-los. Você pode definir esse parâmetro para alterar a maneira como o operador é avaliado. Ele está documentado em [Pesquisar documentos](https://msdn.microsoft.com/library/dn798927.aspx) ou [Enumeração SearchMode](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx).

**Perfis de pontuação** são usados para melhorar os resultados de pesquisa, fazendo com que os itens que atendem a critérios predefinidos apareçam nos resultados da pesquisa. Consulte [Introdução aos perfis de pontuação](search-get-started-scoring-profiles.md) para repassar esse recurso.

**Filtros** são usados para restringir os resultados da pesquisa, fornecendo critérios adicionais na seleção. Expressões de filtro são colocadas dentro da consulta. Consulte [Pesquisar documentos](https://msdn.microsoft.com/library/dn798927.aspx) para obter mais detalhes.

A **Navegação facetada** é usada para filtragem autodirigida. A Pesquisa do Azure cria e retorna a estrutura e seu código processa a estrutura de navegação facetada em uma página de resultados da pesquisa. Consulte [Navegação facetada](search-faceted-navigation.md) para obter detalhes.

**Sugestores** refere-se a consultas de preenchimento preditivo ou automático que retornam termos de pesquisa sugeridos conforme o usuário digita os primeiros caracteres de uma frase de pesquisa. Consulte [Operação de sugestões](https://msdn.microsoft.com/library/dn798936.aspx) ou [Classe Suggesters](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx) para obter mais informações.

**Analisadores de idioma** fornecem as regras linguísticas usadas durante a análise de texto. O analisador de idioma padrão para a pesquisa do Azure é Lucene inglês, mas você pode usar outros, ou até mesmo vários analisadores especificando-os em seu índice. Analisadores Lucene estão disponíveis em todas as APIs. Os processadores de idioma natural da Microsoft só estão disponíveis na [API REST 2015-02-28-Visualização](search-api-2015-02-28-preview.md). Consulte [Suporte a idioma](https://msdn.microsoft.com/library/dn879793.aspx) para obter mais informações.

### Etapa 6: Atualizar índices e documentos

Alguns dos recursos que você deseja avaliar podem exigir uma atualização do índice, o que geralmente tem o efeito posterior de requerer a atualização aos seus documentos.

Se você precisar atualizar um índice ou documentos, por exemplo, para adicionar sugestores ou especificar analisadores nos idioma em campos que você adicionou para essa finalidade, consulte os seguintes links para obter instruções:

- [Operação de atualização de índice (API REST)](https://msdn.microsoft.com/library/dn800964.aspx)
- [Operação de atualização de indexador (API REST)](https://msdn.microsoft.com/library/dn946892.aspx)
- [Operação de adição, atualização ou exclusão de documentos (API REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Classe Index (biblioteca .NET)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [Classe Documents (biblioteca .NET)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

Depois de criar um protótipo que estabelece a verificação de conceito, você pode avançar ainda mais o que aprendeu, criando um projeto de desenvolvimento que dá suporte a cargas de trabalho de produção.

## Desenvolvimento de aplicativo

Avançar para a próxima fase agora requer decisões sobre quais APIs usar, como gerenciar documentos e carregar a frequência e se deseja incluir recursos externos nos resultados da pesquisa.

O design da sua solução ainda precisa incluir todas as etapas descritas para protótipos, mas em vez de priorizar o caminho mais vantajoso, você precisará considerar quais métodos são mais compatíveis com a solução geral.

### Escolher uma API

A Pesquisa do Azure fornece dois modelos de programação: a biblioteca .NET para código gerenciado e uma API REST para linguagens de programação como Java, JavaScript ou outras linguagens não destinadas ao Microsoft .NET Framework.

Atualmente, um pequeno subconjunto de recursos ainda não estão presentes na biblioteca .NET, por isso mesmo se você preferir escrever código gerenciado, convém usar a API REST para obter os recursos desejados. Recursos que só estão disponíveis na API REST:

- [Processadores de Idioma Natural da Microsoft - somente visualização](../search-api-2015-02-28-preview/)
- [Recurso moreLikeThis - somente visualização](../search-api-2015-02-28-preview/)
- [API de Gerenciamento](https://msdn.microsoft.com/library/dn832684.aspx)

Você pode verificar periodicamente a artigo de [Novidades](search-latest-updates.md) para monitorar as alterações no status do recurso.

### Determinar métodos de sincronização de dados: Push ou Pull

Os modelos push e pull referem-se a como os documentos são atualizados no índice. Em geral, a situação dita qual modelo é o ideal para você.

Se sua empresa for uma loja online, você provavelmente precisará de um modelo push para enviar por push ou em gravação dupla qualquer alteração no estoque para o banco de dados OLTP e seu índice de Pesquisa do Azure. Quando um SKU específico está esgotado ou uma cor ou tamanho fica indisponível, você precisa que o índice seja atualizado assim que possível para evitar a insatisfação do cliente. Somente modelos push podem fornecer atualizações quase em tempo real ao índice de pesquisa.

Não há nenhum mecanismo específico na Pesquisa do Azure para implementar um modelo push. Seu código de aplicativo da camada de dados deve lidar com a operação de atualização de documentos usando a [API REST](https://msdn.microsoft.com/library/dn798935.aspx) ou a [biblioteca .NET](https://msdn.microsoft.com/library/dn951165.aspx) para atualizar os documentos na coleção. Como um detalhe de implementação, usar um SKU de produto para a chave de documento pode ajudar com essa tarefa.

Modelos pull geralmente são operações agendadas que recuperam dados de fontes de dados externas. Na Pesquisa do Azure, um modelo pull está disponível por meio de [Indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), por sua vez disponíveis para fontes de dados específicas: Banco de Dados de Documentos do Azure ou Banco de Dados SQL do Azure (e também o SQL Server em VMs do Azure).

### Carregar documentos em lotes

Recomendamos adicionar documentos em lotes para melhorar a produtividade. Você pode criar lotes de até 1.000 documentos, presumindo um tamanho médio de documento de cerca de 1 a 2 KB.

Há um código de status geral para a solicitação POST. Os códigos de status são HTTP 200 (sucesso) ou HTTP 207 (multi-status), caso haja uma cominação de documentos bem-sucedidos em com falha. Além do código de status para a solicitação POST, a Pesquisa do Azure mantém um campo de status para cada documento. Diante do carregamento de um lote, você precisa de um modo de obter o status por documento que indica se a inserção de cada documento foi bem-sucedida ou falhou. O campo de status fornece essas informações. Ele será definido como falso caso haja falha no carregamento do documento.

Diante de grandes cargas, não é incomum que haja algumas falhas no carregamento. Caso isto ocorra, o código de status geral será 207, indicando um sucesso parcial, e os documentos que não forem indexados terão a propriedade de “status” definida como falsa.

> [AZURE.NOTE]Quando o serviço recebe os documentos, eles ficam em fila para indexação e podem não ser incluídos imediatamente nos resultados da pesquisa. Quando não há uma grande carga, normalmente, os documentos são indexados dentro de alguns segundos.

Ao atualizar um índice, você pode combinar múltiplas ações (inserir, mesclar, excluir) no mesmo lote, eliminando a necessidade de diversos processos. Atualmente, a Pesquisa do Azure não dá suporte a atualizações parciais (HTTP PATCH), de modo que, se precisar atualizar um índice, você precisará reenviar a definição do índice.

### Integrar dados externos em resultados da pesquisa

A Pesquisa do Azure usa o armazenamento interno para os índices e documentos usados em operações de pesquisa. A análise de texto e índice depende de ter todos os campos pesquisáveis e os tributos associados prontamente disponíveis.

Contudo, nem todos os campos em um documento serão pesquisáveis. Por exemplo, se o seu aplicativo for um catálogo online de música ou vídeos, recomendamos armazenar arquivos binários no serviço Blob do Azure ou algum outro formato de armazenamento. Os arquivos binários em si não são pesquisáveis e, portanto, não há necessidade de persistir com eles no armazenamento da Pesquisa do Azure. Embora você deva armazenar imagens, vídeos e arquivos de áudio em outros serviços ou locais, deve incluir um campo que faça referência ao URL para o local do arquivo. Dessa forma, você pode retornar os dados externos como parte dos resultados da pesquisa.

Para usar dados externos, você deve definir um campo no índice que armazena um ponteiro de URL para o arquivo de dados externo. Se você emitir uma solicitação de [documentos de pesquisa](https://msdn.microsoft.com/library/dn798929.aspx) ou incluir o campo nos resultados da pesquisa, o arquivo binário aparece no contexto de um documento.

### Planejamento da capacidade

Um do recursos mais interessantes da Pesquisa do Azure é a facilidade com a qual você pode escalar ou reduzir verticalmente os recursos em resposta à demanda. Embora esse recurso não elimine a necessidade de planejamento de capacidade, ele pode minimizar a maioria dos riscos. Você não fica obrigado a usar hardware adicional ou o hardware errado para executar suas cargas de trabalho de pesquisa.

Como última etapa, examine os níveis de recursos existentes para partições e réplicas e determine se é necessário realizar ajustes. É a maneira mais fácil de ajustar a capacidade está no [Portal do Azure](https://ms.portal.azure.com/).

Lembre-se de que somente a camada de preços Standard pode ser escalada ou reduzida verticalmente. Além disso, dependendo do grau de ajuste, ela pode levar de alguns minutos a várias horas para implantar clusters adicionais para o serviço.

> [AZURE.NOTE]A capacidade pode ser ajustada com programação usando a API REST de gerenciamento. Para obter mais informações, consulte [API REST de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx).


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png

<!---HONumber=Nov15_HO3-->