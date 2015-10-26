<properties      
    pageTitle="Particionar e dimensionar dados no Banco de Dados de Documentos com fragmentação | Microsoft Azure"      
    description="Saiba como dimensionar dados com uma técnica chamada fragmentação. Saiba mais sobre fragmentos, como particionar dados no Banco de Dados de Documentos e quando usar o particionamento hash e por intervalos."         
    keywords="Scale data, shard, sharding, documentdb, azure, Microsoft azure"
	services="documentdb"      
    authors="arramac"      
    manager="jhubbard"      
    editor="monicar"      
    documentationCenter=""/>
<tags       
    ms.service="documentdb"      
    ms.workload="data-services"      
    ms.tgt_pltfrm="na"      
    ms.devlang="na"      
    ms.topic="article"      
    ms.date="10/05/2015"      
    ms.author="arramac"/>

# Particionar e dimensionar dados no Banco de Dados de Documentos

O [Banco de Dados de Documentos do Microsoft Azure](../../services/documentdb/) foi criado para ajudá-lo a obter um desempenho rápido e previsível e para *expandir* diretamente com seu aplicativo conforme ele cresce. O Banco de Dados de Documentos tem sido usado para alimentar serviços de produção de grande escala na Microsoft, como o Repositório de Dados do Usuário que alimenta o pacote MSN de aplicativos Web e para celular.

Você pode conseguir reduzir horizontalmente quase infinitamente em termos de armazenamento e produtividade para seu aplicativo no Banco de Dados de Documentos, particionando seus dados horizontalmente - um conceito comumente chamado de **fragmentação**. Contas do Banco de Dados de Documentos podem ser dimensionadas linearmente com o custo, por meio de unidades empilháveis chamadas de **coleções**. A melhor forma de particionar os dados entre as coleções depende do formato dos dados e dos padrões de acesso.

Depois de ler este artigo sobre dimensionamento, você poderá responder às seguintes perguntas:

 - O que é particionamento hash e por intervalos?
 - Quando você usaria cada uma das técnicas de particionamento e por quê?
 - Como você pode criar um aplicativo particionado no Banco de Dados de Documentos do Azure?

Este artigo apresenta alguns conceitos sobre a fragmentação. Se você estiver pronto para escrever um código que particiona os dados usando o SDK do .NET do Banco de Dados de Documentos, consulte [Particionamento de dados com o SDK do .NET do Banco de Dados de Documentos](documentdb-sharding.md).

## Coleções = Partições

Antes de nos aprofundarmos mais nas técnicas de particionamento e dimensionamento de dados, é importante compreender o que é e o que não é uma coleção. Talvez você já saiba que uma coleção é um contêiner para seus documentos JSON. No Banco de Dados de Documentos, as coleções não são apenas contêineres *lógicos*, mas também contêineres *físicos*. Eles são o limite de transação para procedimentos e gatilhos armazenados e o ponto de entrada para consultas e operações CRUD. A cada coleção é atribuída uma produtividade, que não é compartilhada com outras coleções da mesma conta. Portanto, você pode escalar horizontalmente seu aplicativo em termos de armazenamento e de produtividade, acrescentando mais coleções e distribuindo seus documentos entre elas.

Coleções não são o mesmo que tabelas em bancos de dados relacionais. As coleções não impõem o esquema. Sendo assim, você pode armazenar tipos diferentes de documentos com vários esquemas na mesma coleção. No entanto, você pode optar por usar coleções para armazenar objetos de apenas um tipo, como faria com as tabelas. O melhor modelo depende apenas de como os dados aparecem juntos em consultas e transações.

## Particionando com o Banco de Dados de Documentos

Há duas abordagens que podem ser usadas para particionar dados com o Banco de Dados de Documentos do Azure (ou qualquer sistema distribuído para esse fim) — e elas são * particionamento por intervalos* e *particionamento hash*. Isso envolve escolher um único nome de propriedade JSON dentro do seu documento como sua *chave de partição*, geralmente a propriedade de ID natural, por exemplo, "userID" para armazenamento do usuário ou "deviceId" para cenários IoT. Em dados seriais de tempo, o "carimbo de data/hora" é usado como a chave de partição, uma vez que os dados geralmente são inseridos e pesquisados por intervalos de tempo. Embora seja comum usar uma única propriedade, essa pode ser uma propriedade diferente para diferentes tipos de documento, por exemplo, use "id" para documentos de usuário e "ownerUserId" para comentários. A próxima etapa é rotear todas as operações, como criações e consultas, para as coleções certas usando a chave de partição incluída em uma solicitação.

Vejamos essas técnicas com mais detalhes.

## Particionamento por intervalos

No particionamento por intervalos, as partições são atribuídas com base no intervalo em que se encontra a chave de partição. Normalmente, ele é usado para fazer o particionamento com propriedades de *carimbo de data/hora* (por exemplo, eventTime entre 1º de fevereiro de 2015 e 2 de fevereiro de 2015).

> [AZURE.TIP]Você deve usar o particionamento por intervalos se suas consultas forem restritas a valores em intervalos específicos com relação à chave de partição.

Um caso especial de particionamento por intervalos é quando o intervalo é um único valor. Normalmente, ele é usado para particionamento por valores discretos, como região (por exemplo, a partição da Escandinávia contém Noruega, Dinamarca e Suécia).

> [AZURE.TIP]O particionamento por intervalos oferece o maior grau de controle no gerenciamento de um aplicativo multilocatário. Você pode atribuir múltiplos locatários a uma coleção, um locatário a uma coleção, ou mesmo um locatário a várias coleções.

## Particionamento hash

No particionamento hash, as partições são atribuídas com base no valor de uma função hash, permitindo distribuir uniformemente solicitações e dados entre várias partições. Ele costuma ser usado para particionar dados produzidos ou consumidos por um grande número de clientes diferentes e é útil para armazenar perfis de usuários, itens de catálogo e dados de telemetria de dispositivo IoT ("Internet das Coisas").

> [AZURE.TIP]Você deve usar o particionamento hash sempre que houver entidades demais para enumerar (como usuários ou dispositivos), e a taxa de solicitação for relativamente uniforme entre as entidades.

## Escolhendo a técnica de particionamento ideal

Qual técnica de particionamento é melhor para você? Depende do tipo de dados e dos padrões de acesso comuns. Escolher a técnica de particionamento certa no momento do design permite evitar deficiências técnicas e lidar com o aumento do tamanho dos dados e dos volumes de solicitação.

- O **particionamento por intervalos** geralmente é usado no contexto de datas, pois oferece um mecanismo simples e natural para remover partições com base no carimbo de data/hora. Ele também é útil quando as consultas ficam restritas a um intervalo de tempo, uma vez que ele é alinhado aos limites de particionamento. Ele também permite agrupar e organizar conjuntos de dados desordenados e não relacionados de uma maneira natural, como agrupar locatários por organização ou estados por região. O intervalo também oferece um controle refinado da migração de dados entre coleções. 
- O **particionamento hash** é útil para fazer o balanceamento de cargas uniforme das solicitações, para usar de maneira eficaz o armazenamento e a produtividade provisionados. Usar algoritmos de *hash consistentes* permite reduzir a quantidade de dados que precisam ser movidos quando uma partição é adicionada ou removida.

Você não precisa escolher apenas uma técnica de particionamento. Uma *mescla* dessas técnicas também pode ser útil dependendo da situação. Por exemplo, se você estiver armazenando dados de telemetria de um veículo, uma boa abordagem seria particionar os dados de telemetria do dispositivo segundo intervalo do carimbo de data/hora para facilitar o gerenciamento das partições e, depois, subparticionar segundo o VIN (número de identificação de veículo), para expandir e obter mais produtividade (particionamento de composição e hash por intervalos).

## Desenvolvendo um aplicativo particionado
Há três áreas principais do design às quais é necessário prestar atenção ao desenvolver um aplicativo particionado no Banco de Dados de Documentos.

- Como você encaminha suas criações e leituras (incluindo as consultas) para as coleções certas.
- Como você mantém e recupera a configuração de resolução da partição, ou seja, ou mapas de partição.
- Como você adiciona/remove partições conforme os volumes de dados e solicitações aumentam.

Vejamos cada uma dessas áreas com mais detalhes.

## Encaminhando criações e consultas

O roteamento de solicitações de criação de documento é direto para o particionamento hash e por intervalos. O documento é criado na partição com o valor hash ou de intervalo correspondente à chave de partição.

Consultas e leituras normalmente devem ser direcionadas a uma única chave de partição, de modo que as consultas sejam distribuídas apenas às partições correspondentes. Para consultar todos os dados, no entanto, você precisa *distribuir* a solicitação em várias partições e depois mesclar os resultados. Tenha em mente que algumas consultas talvez precisem executar uma lógica personalizada para mesclar resultados (por exemplo, para buscar os N resultados principais).

## Gerenciando seu mapa de partições

Você também precisa decidir como vai armazenar seu mapa de partições, como seus clientes o carregam e recebem atualizações quando ele muda e como ele é compartilhado entre vários clientes. Se o mapa de partições não mudar com frequência, você pode simplesmente salvá-lo no arquivo de configurações do aplicativo.

Se não, você pode armazená-lo em qualquer armazenamento persistente. Um padrão de design comum que vemos na produção é serializar os mapas de partições como JSON e armazená-los dentro de coleções do Banco de Dados de Documentos. Os clientes podem, então, armazenar o mapa em cache para evitar viagens de ida e volta extra e pesquisar periodicamente por mudanças. Se houver a possibilidade se seus clientes modificarem o mapa de fragmento, certifique-se de que eles usam um esquema de nomenclatura consistente e simultaneidade otimista (eTags) para possibilitar atualizações consistentes do mapa de partições.

## Adicionando e removendo partições para dimensionar dados

Com o Banco de Dados de Documentos, você pode adicionar e remover as coleções a qualquer momento e usá-las para armazenar novos dados ou balancear novamente dados disponíveis em coleções existentes. Veja na página [Limites](documentdb-limits.md) o número de coleções. E você sempre pode nos contatar para aumentar os limites.

Adicionar e remover uma nova partição usando particionamento por intervalos é bem simples. Por exemplo, para adicionar uma nova região ou um novo intervalo de tempo para dados recentes, você precisa apenas acrescentar as novas partições ao mapa existente. Dividir uma partição existente em várias partições ou mesclar duas partições demanda um pouco mais de esforço. Você precisa

- Deixar o fragmento offline para leituras.
- Encaminhar leituras para as duas partições usando a configuração de particionamento antiga, bem como a nova configuração de particionamento durante a migração. Observe que as garantias de nível de consistência e transação não estarão em vigor até a conclusão da migração.

Com o hash, é um pouco mais complicado adicionar e remover partições. Técnicas de hash simples causarão embaralhamento e demandarão que a maioria dos dados seja movida. Usar **hash consistente** garante que apenas uma fração dos dados precise ser movida.

Um modo relativamente fácil de adicionar novas partições sem precisar mover dados é "transbordar" seus dados para uma nova coleção e distribuir as solicitações nas coleções nova e antiga. Essa abordagem, no entanto, deve ser usada apenas em situações raras (por exemplo, transbordar durante picos de cargas de trabalho e para reter os dados temporariamente até que eles possam ser movidos).

## Próximas etapas
Neste artigo, apresentamos algumas técnicas comuns para particionar dados com o Banco de Dados de Documentos, bem como quando usar qual técnica ou uma combinação delas.

-   Em seguida, consulte este [artigo](documentdb-sharding.md) sobre como você pode particionar dados usando os resolvedores de partição com o SDK do Banco de Dados de Documentos. 
-   Baixe uma dos [SDKs com suporte](https://msdn.microsoft.com/library/azure/dn781482.aspx)
-   Entre em contato conosco por meio dos [fóruns de suporte do MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) se tiver dúvidas.
   


 

<!---HONumber=Oct15_HO3-->