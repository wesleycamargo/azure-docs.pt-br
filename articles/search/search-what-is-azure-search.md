<properties
	pageTitle="O que é a Pesquisa do Azure"
	description="Visão geral técnica e resumo dos recursos de Pesquisa do Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# O que é a Pesquisa do Azure?

O Serviço de Pesquisa do Azure é um serviço de nuvem totalmente gerenciado que permite que os desenvolvedores criem aplicativos avançados de pesquisa usando um SDK de .NET ou APIs REST. Ele inclui a pesquisa de texto completo com escopo sobre o seu conteúdo mais comportamentos de pesquisa avançados semelhantes àqueles encontrados nos mecanismos de pesquisa da web comercial, tais como, sugestões de preenchimento automático de consulta com base em um termo parcial de entrada, realce de ocorrências e navegação facetada. O suporte de linguagem natural é incorporado, usando as regras linguísticas apropriadas para o idioma especificado.

Você pode dimensionar o seu serviço com base na necessidade do aumento de pesquisa ou capacidade de armazenamento. Por exemplo, os varejistas podem aumentar a capacidade para atender os volumes adicionais associados aos eventos promocionais ou compras no feriado.

Pesquisa do Azure é um serviço de API para desenvolvedores e integradores de sistema que sabem como trabalhar com serviços Web e HTTP. A Pesquisa do Azure usa a complexidade de gerenciar um serviço de pesquisa de nuvem e simplifica a criação de aplicativos Web baseados em pesquisa e móveis.

##Como ele funciona

A Pesquisa do Azure é um [serviço PaaS](https://wikipedia.org/wiki/Platform_as_a_service) que delega o gerenciamento de servidores e infra-estrutura para a Microsoft, deixando você com um serviço pronto para uso que você preenche com dados de pesquisa e, em seguida, acessa de seu aplicativo. Dependendo de como você configura o serviço, você usará o serviço gratuito que é compartilhado com outros assinantes de Pesquisa do Azure, ou a camada de preços Standard que oferece recursos dedicados usados apenas pelo seu serviço. A Pesquisa padrão é escalonável, com opções para atender às demandas crescentes de armazenamento ou cargas de consulta.

A Pesquisa do Azure armazena seus dados em um índice que pode ser pesquisado por meio de consultas de texto completo. O esquema desses índices pode ser criado no Portal do Azure, ou programaticamente usando a biblioteca de cliente ou APIs REST. Quando o esquema é definido, você pode carregar, em seguida, os dados no serviço de Pesquisa do Azure onde ele será indexado posteriormente.

Você pode usar os modelos push ou pull para carregar dados para o índice. O modelo de pull é fornecido por meio de indexadores podem ser configurados para sob demanda ou atualizações agendadas (consulte [(API de REST de serviço de pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn946891.aspx)) as operações do indexador), que permite uma fácil inclusão dos dados e as alterações de dados de um Banco de Dados de Documentos do Azure, Banco de Dados SQL do Azure, ou SQL Server hospedado em uma VM do Azure. O modelo de push é fornecido por meio do SDK ou APIs REST usados para enviar documentos atualizados para um índice. Você pode enviar dados de praticamente qualquer conjunto de dados, mas eles devem estar no formato JSON. Consulte [Adicionar, atualizar ou excluir Documentos](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Como usar o SDK do .NET)](search-howto-dotnet-sdk.md) para obter orientação sobre como carregar dados.

Alguns desenvolvedores escolherão um indexador para a conveniência que ele fornecer. Para outros desenvolvedores, o modelo de push compensa um pouco de trabalho extra. Os motivos para escolher um modelo de push são dois. Primeiro, você pode evitar a carga extra que os mecanismos do tipo rastreador colocam nos seus servidores de dados. Em segundo lugar, você pode evitar a latência inerente que vem com a indexação agendada. Na Cyber Monday ou Black Friday, você desejará que a pesquisa reflita o status de backup a cada segundo sobre o estoque disponível. Um modelo de push pode fornecer esse nível de precisão.

##O que você irá compilar e armazenar

O fluxo de trabalho típico é criar o esquema e os documentos em um ambiente de desenvolvimento local e, em seguida, usar o SDK do .NET ou a API REST para carregar, processar e, por fim, consultar os dados. Todos os dados indexados são mantidos na Pesquisa do Azure para melhorar o desempenho e para garantir a consistência nas operações de pesquisa.

Você pode usar o editor interno do portal para criar o esquema de índice e os perfis de pontuação, que é ideal para a criação de protótipos. Os desenvolvedores que precisam de uma abordagem repetitiva e automatizada talvez prefiram criar o índice no código. Novos recursos são adicionados à API primeiro, portanto, dependendo dos requisitos do aplicativo, uma abordagem programática pode ser a única opção.

Quando você cria o esquema, você está definindo os campos e seus atributos com suporte em seu aplicativo de pesquisa. Os campos contêm dados pesquisáveis, como nomes de produto, descrições, comentários do cliente, marcas, preços, notificações promocionais e assim por diante. Os atributos informam os tipos de operações que podem ser executadas. Os atributos mais usados são exemplos de se um campo oferece suporte à pesquisa de texto completo (searchable= true), filtros (filterable=true), ou facetas (facetable=true).

Os campos também contêm dados não pesquisáveis relevantes para o aplicativo de pesquisa, como valores usados internamente em filtros e perfis de pontuação ou talvez URLs para conteúdo armazenado em outras plataformas de armazenamento (por exemplo, vídeo ou arquivos de imagem mantidos no armazenamento de BLOB). Um exemplo de um campo somente interno frequentemente citado é uma margem de lucro ou outro valor que indica o potencial de receita. Talvez o aplicativo de pesquisa precise promover itens específicos que trazem maior benefício financeiro para sua empresa. O esquema pode incluir atributos de campo, permitindo esses tipos de comportamentos de pesquisa.

Os documentos são os dados detalhados retornados pelo mecanismo de pesquisa nos resultados da pesquisa. Por exemplo, se o aplicativo de pesquisa é um catálogo online, você teria um documento para cada SKU e a página de resultados da pesquisa seria criada usando valores retornados de documentos que correspondem aos termos de pesquisa.

##Veja esse recurso em ação

Assista a nossos vídeos para aprender sobre os recursos e cenários. Visite [Pesquisa do Azure: tutoriais, demonstrações em vídeo e exemplos](search-video-demo-tutorial-list.md) para obter links para o conteúdo de vídeo.

##Busca detalhada do recurso

A Pesquisa do Azure oferece valor em várias categorias, incluindo provisionamento e escala, programação, acesso e controle e nos recursos que você optar por implementar em seu aplicativo de pesquisa personalizada.

A lista de verificação de recurso a seguir pode ajudar a avaliar a Pesquisa do Azure para suas necessidades de aplicativo de pesquisa. Os lançamentos de novos recursos podem ser encontrados em atualizações mais recentes para a Pesquisa do Azure. Você também pode examinar a página de solicitação de recurso de Pesquisa do Azure para verificar o status dos recursos ainda não implementados.

Perguntas sobre um recurso específico? Experimente o [Fórum de Pesquisa do Azure no MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch). Você também pode examinar a [página de solicitação de recurso de Pesquisa do Azure](http://feedback.azure.com/forums/263029-azure-search) para verificar o status dos recursos ainda não implementados.

###Restrições e recursos de capacidade e dimensionamento

O serviço pode ser executado como Standard ou uma implantação de serviços compartilhados. A Pesquisa Standard oferece suporte a recursos dedicados que podem ser dimensionados com base em cargas de trabalho. O serviço compartilhado é gratuito e destina-se a testar a funcionalidade de serviços, já que não há nenhuma garantia de desempenho.

**Escalonável** em incrementos de contagens de armazenamento e o documento (partições) ou na carga de consulta (réplicas). Cada réplica executa uma cópia de um índice. Alta disponibilidade requer 2 réplicas para cargas de trabalho de consulta e 3 réplicas para cargas de trabalho de leitura / gravação (consulta e indexação). Consulte os Limites e restrições (Pesquisa do Azure) para obter mais informações sobre o planejamento de capacidade.

A Pesquisa do Azure passa automaticamente índices e documentos por meio das partições alocadas para esse serviço. Isso significa que você não pode fazer o peg índices e documentos para um conjunto de partições e réplicas.

Partições e réplicas são recursos de todo o serviço, com todos os índices em execução em todas as réplicas. Se você precisa isolar um índice ou talvez você precise de requisitos de dispersão geográfica de serviços e recursos em data centers diferentes, você pode criar um segundo serviço.

Há limites no armazenamento e no número de índices e documentos carregados no serviço. O limite efetivo será o que ocorrer primeiro: esgotamento de armazenamento físico ou atingir o limite superior de índices e contagens de documentos. Veja [Limites e restrições (Pesquisa do Azure)](search-limits-quotas-capacity.md) para obter mais detalhes.

###Programação

A API REST consiste em solicitações HTTP e respostas, com conteúdo no formato JSON. Há uma API para acessar o serviço e uma API para gerenciar o serviço. Consulte a [API de REST do Serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx) e a [API de REST de Gerenciamento de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn832684.aspx) para obter detalhes.

O SDK do .NET inclui classes que facilitam usar o serviço de Pesquisa do Azure sem precisar trabalhar diretamente com HTTP e JSON. Consulte [Como usar o SDK do .NET da Pesquisa do Azure](search-howto-dotnet-sdk.md) para obter mais informações.

###Controle e acesso

A pesquisa só é acessível via HTTPS.

A autenticação de seu aplicativo de host para a Pesquisa do Azure é por meio de uma chave de api do administrador em um cabeçalho HTTP. Não há nenhum modelo de autenticação ou autorização por usuário. No entanto, você pode usar o $filter para restringir o acesso por identidade do usuário. Você também pode usar várias consultas de chaves de api que você pode atribuir a aplicativos cliente diferentes. Para obter mais informações sobre gerenciamento de chaves, consulte [Gerenciar o serviço de pesquisa no Microsoft Azure](search-manage.md). Consulte [Pesquisar Documentos (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes sobre $filter.

###Índices e documentos

Você pode ter vários índices (veja [Limites e restrições (Pesquisa do Azure)](search-limits-quotas-capacity.md) para obter os limites baseados em camadas de preços). Observe que atualmente não existe suporte para ingressar em índices. Uma solicitação de pesquisa pode especificar um índice.

**Documentos** contêm campos e atributos associados. Os campos incluem texto pesquisável, principais valores utilizados (ou até mesmo exclusivamente) em filtros e perfis de pontuação, e muito provavelmente – URLs ou ponteiros para conteúdo, como imagens em outros armazenamentos de dados. Muitos aplicativos de pesquisa usam várias formas de armazenamento. Imagens ou vídeos podem ser armazenados de forma menos dispendiosa em outra mídia de armazenamento, como o armazenamento Blob do Azure.

**Indexadores** podem ser usados para agendar atualizações de índice de dados alterados no Banco de Dados SQL do Azure, SQL Server em máquinas virtuais do Azure ou Banco de Dados de Documentos do Azure. Consulte [Operações do indexador (API de REST de Serviço de Pesquisa do Azure)]((https://msdn.microsoft.com/library/azure/dn946891.aspx) para obter detalhes.

**Consultas** podem ser formuladas usando a sintaxe de OData para filtros Booleanos e uma sintaxe de consulta simples para pesquisa de texto completo. Consulte [Sintaxe de expressão OData para Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798921.aspx) e [Sintaxe de consulta simples na Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798920.aspx) para obter detalhes.

Você pode procurar documentos específicos com base em sua ID para recuperar rapidamente itens específicos. Isso é útil quando um usuário clica em um resultado de pesquisa específico e você deseja pesquisar detalhes específicos sobre esse documento. Consulte [Documento de pesquisa (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798929.aspx) para obter detalhes.

##Recursos do aplicativo de pesquisa

**Pesquisa de texto completo** (ativada por padrão para campos de texto) está habilitada para qualquer campo que tiver o atributo pesquisável. A pesquisa de texto completo é baseada no prefixo correspondente, o que significa que as correspondências são baseadas na primeira parte de um termo de pesquisa e não no meio ou no fim da palavra. Consulte [Criar Índice (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) ou [Criar um índice no portal](search-create-index-portal.md) para saber as etapas sobre como definir um índice.

**Perfis de pontuação** são usados para criar modelos de classificação que otimizam a pesquisa com base nos objetivos de negócios. Por exemplo, talvez você queira que produtos mais recentes ou com desconto apareçam primeiro nos resultados da pesquisa. Você também pode criar perfis de pontuação usando marcas de pontuação personalizadas com base nas preferências de pesquisa do cliente que você controlou e armazenou separadamente. Consulte [Adicionar perfis de pontuação para um índice de pesquisa (API de REST de serviço de pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798928.aspx) para obter detalhes.

**Suporte ao idioma** é incorporado para 50 idiomas diferentes, usando várias opções de pilhas de processamento do idioma natural, incluindo os bem conhecidos analisadores de Lucene e analisadores da Microsoft que fortalecem o Microsoft Office e o Bing (somente visualização). Consulte [Suporte ao idioma (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn879793.aspx) para Lucene, e [API de REST de Serviço de Pesquisa do Azure Versão 2015-02-28-Visualização](search-api-2015-02-28-Preview.md) para os processadores de idioma natural.

**Navegação facetada** refere-se a uma árvore de categorização usada para pesquisa autodirigida, geralmente com base em marcas, modelos, tamanhos ou qualquer categoria que faça sentido para seus dados. Navegação facetada é implementada por meio de atributos no índice, combinado com um campo de faceta fornecido em uma consulta. Consulte [Navegação facetada](search-faceted-navigation.md) para obter detalhes.

**Sugestões** para preenchimento automático ou consultas de preenchimento automático são suportadas por meio de atributos no índice. A Pesquisa do Azure suporta correspondência difusa e infixa (em qualquer parte do conteúdo do campo). Você pode fazer ambos; eles não são mutuamente exclusivos. Consulte [Criar Índice (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) para obter informações sobre como habilitar sugestões, e [Sugestões (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798936.aspx) para usá-los.

**Filtros** podem ser usados para implementar a navegação facetada, usada na construção de consulta ou globalmente para restringir as operações de pesquisa para filtrar critérios estabelecidos por você no código. Os filtros são habilitados para campos específicos por meio de seu esquema de índice e por meio do parâmetro de pesquisa $Filter. Consulte [Criar Índice (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) e [Pesquisar documentos (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes.

**Classificação** é também habilitada para campos específicos por meio do esquema de índice e, em seguida, implementado como um parâmetro de pesquisa por meio do parâmetro $orderBy. Mais uma vez, [Criar Índice (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx) e [Pesquisar documentos (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) possui os detalhes

**Contagem** das ocorrências de pesquisa retornadas para uma consulta e a capacidade de limitar o número de ocorrências retornadas ao mesmo tempo, são implementadas por meio de $top e $skip. Consulte [Pesquisar Documentos (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter detalhes.

**Ocorrências realçadas** são especificadas por meio do parâmetro de consulta de realce, e permitem que você exiba a um usuário um trecho de texto realçando as palavras-chave encontradas em termos de pesquisa inseridos pelo usuário. Consulte [Pesquisar documentos (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para obter mais informações sobre parâmetros de consulta.

##Relatório e análises

O uso do recurso é exibido no painel de serviço para que você possa obter rapidamente uma ideia de como o armazenamento está sendo usado.

Contagem de índice, contagem de documentos e consumo de armazenamento estão disponíveis no portal. Você também pode usar a API. Consulte [Obter estatísticas de índice (API de REST de Serviço de Pesquisa do Azure)](https://msdn.microsoft.com/library/azure/dn798942.aspx) para obter detalhes.

Não há nenhum mecanismo interno para medir a taxa de transferência de consulta ou outras operações de serviço. Além disso, atualmente não há suporte para registro em log ou análise dos resultados de pesquisa (por exemplo, recuperar uma lista de termos de pesquisa que não gerou resultados ou gerar relatórios sobre a origem das solicitações de pesquisa).

##Experimentar

Visite [Criar um Serviço de Pesquisa do Azure](search-create-service-portal.md) para configurar o serviço ou [Experimente o Serviço de Aplicativo do Azure](http://go.microsoft.com/fwlink/p/?LinkId=618214) para uma sessão de uma hora gratuita sem necessidade de nenhuma configuração ou assinatura.

Você também pode tentar estes tutoriais:

[Como usar a Pesquisa do Azure no .NET](search-howto-dotnet-sdk.md) [Introdução ao .NET da Pesquisa do Azure](search-get-started-dotnet.md) [Pesquisa do Azure: tutoriais, demonstrações em vídeo e exemplos](search-video-demo-tutorial-list.md)

<!---HONumber=Oct15_HO3-->