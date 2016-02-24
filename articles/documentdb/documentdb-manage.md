<properties 
	pageTitle="Banco de Dados de Documentos — Capacidade — Armazenamento de documentos | Microsoft Azure" 
	description="Saiba mais sobre o armazenamento de dados e o armazenamento de documentos no Banco de Dados de Documentos e como você pode dimensionar o Banco de Dados de Documentos para atender às necessidades de capacidade do seu aplicativo." 
	keywords="armazenamento de documentos"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/27/2016" 
	ms.author="mimig"/>

# Saiba sobre a capacidade e o armazenamento de documentos no Banco de Dados de Documentos
O Banco de Dados de Documentos é um serviço de banco de dados NoSQL orientado a documentos, escalonável e totalmente gerenciado para documentos JSON. Com o Banco de Dados de Documentos, você não precisa alugar máquinas virtuais, implantar software, monitorar banco de dados nem se preocupar com recuperação de desastres. O Banco de Dados de Documentos é operado e continuamente monitorado por engenheiros da Microsoft para a melhor fornecer disponibilidade, desempenho e proteção aos dados do cliente.

Você pode começar a usar o Banco de Dados de Documentos [criando um banco de dados](documentdb-create-account.md) pelo [Portal do Azure](https://portal.azure.com/). O Banco de Dados de Documentos é oferecido em unidades de armazenamento com suporte de SSD (unidade de estado sólido) e taxa de transferência. Essas unidades são provisionadas criando coleções de banco de dados em uma conta de banco de dados. Cada coleção inclui 10 GB de armazenamento de banco de dados com taxa de transferência reservada. Se os requisitos de taxa de transferência do seu aplicativo forem alterados, você os altera dinamicamente definindo o [nível de desempenho](documentdb-performance-levels.md) de cada coleção.

Quando seu aplicativo ultrapassa os níveis de desempenho para uma ou várias coleções, as solicitações são limitadas por coleção. Isso significa que algumas solicitações do aplicativo podem ser bem-sucedidas enquanto outras são limitadas.

Este artigo fornece uma visão geral dos recursos e métricas disponíveis para gerenciar a capacidade e planejar o armazenamento de dados.

## Conta de banco de dados e recursos administrativos
Como assinante do Azure, você pode provisionar uma ou mais contas do Banco de Dados de Documentos. Cada conta de banco de dados vem com uma cota de recursos administrativos incluindo bancos de dados, usuários e permissões. Esses recursos estão sujeitos a [cotas e limites](documentdb-limits.md). Se você precisar de recursos administrativos adicionais, [entre em contato com o suporte](documentdb-increase-limits.md).

O Portal do Azure fornece métricas de uso para monitoramento de contas de banco de dados, bancos de dados e coleções. Para obter mais informações, confira [Monitorar uma conta de Banco de Dados de Documentos](documentdb-monitor-accounts.md).

## Bancos de dados com armazenamento de documentos ilimitado
Um único banco de dados do Banco de Dados de Documentos pode conter praticamente uma quantidade ilimitada de armazenamento de documentos particionado por coleções. As coleções formam os domínios de transação para os documentos contidos neles. Um banco de dados do Banco de Dados de Documentos é elástico em termos de tamanho, indo de alguns GB a, possivelmente, terabytes de armazenamento de documento com suporte de SSD e taxa de transferência provisionada. Diferentemente de um banco de dados RDBMS tradicional, um banco de dados do Banco de Dados de Documentos não é vinculado a um único computador.

Com o Banco de Dados de Documentos, conforme precisar dimensionar seus aplicativos, você pode criar mais coleções ou bancos de dados, ou ambos. Vários aplicativos primários na Microsoft estão usando o Banco de Dados de Documentos na escala do consumidor ao criar bancos de dados do Banco de Dados de Documentos extremamente grandes, cada um contendo centenas ou milhares de coleções com terabytes de armazenamento de documentos. É possível expandir ou reduzir um banco de dados adicionando ou removendo coleções para atender os requisitos de escada do seu aplicativo.

## Coleções de banco de dados
Cada banco de dados do Banco de Dados de Documentos pode conter uma ou mais coleções. As coleções atuam como partições de dados altamente disponíveis para processamento e armazenamento de documentos. Cada coleção pode armazenar documentos com esquema heterogêneo. Os recursos de consulta e indexação automática do Banco de Dados de Documentos permitem que você filtre e recupere documentos com facilidade. Uma coleção fornece o escopo para armazenamento de documentos e execução de consultas. Uma coleção também é um domínio de transação para todos os documentos contidos nele. A taxa de transferência é alocada às coleções com base no nível de desempenho especificado. Isso pode ser ajustado dinamicamente por meio do Portal do Azure ou um dos SDKs.

Você pode criar qualquer número de coleções para atender aos requisitos de escala de produtividade e armazenamento de dados de seus aplicativos. As coleções podem ser criadas por meio do [Portal do Azure](https://portal.azure.com/) ou de qualquer um dos [SDKs do Banco de Dados de Documentos](documentdb-sdk-dotnet.md).

>[AZURE.NOTE] Cada coleção permite até 10 GB de armazenamento de dados de documento. Para armazenar conjuntos de dados maiores, você deve particioná-los entre várias coleções. Confira [Como particionar os dados no Banco de Dados de Documentos com o SDK do .NET](documentdb-sharding.md) para obter mais informações.
 
## Unidades de solicitação e operações de banco de dados
O Banco de Dados de Documentos proporciona um amplo conjunto de operações de banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e disparadores – todos operando em documentos dentro de uma coleção de banco de dados. O custo de processamento associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para realizar a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.

Unidades de solicitação são provisionadas e atribuídas com base no nível de desempenho definido para uma coleção. A cada coleção é designado um nível de desempenho no momento da criação. Esse nível de desempenho determina a capacidade de processamento de uma coleção em unidades de solicitação por segundo. Os níveis de desempenho podem ser ajustados durante a vida útil de uma coleção para se adaptar a mudanças na necessidade de processamento e nos padrões de acesso do seu aplicativo. Para obter mais informações, consulte [Níveis de desempenho do Banco de Dados de Documentos](documentdb-performance-levels.md).

>[AZURE.IMPORTANT] Coleções são entidades faturáveis. O custo é determinado pelo nível de desempenho atribuído à coleção.

O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Para aplicativos que ultrapassam a taxa de unidades solicitação provisionada para uma coleção, as solicitações a essa coleção são limitadas até que a taxa caia para baixo do nível reservado. Se seu aplicativo exigir um nível mais alto de taxa de transferência, você pode ajustar o nível de desempenho das coleções existentes ou espalhar as solicitações de aplicativos por novas coleções.

Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única unidade de solicitação representa a capacidade de processamento necessária para ler (por meio de self link) um único documento JSON de 1 KB que consiste em 10 valores de propriedade exclusivos. O custo de unidade de solicitação pressupõe um nível de consistência definido como a "Sessão" padrão e todos os documentos indexados automaticamente. Uma solicitação para inserir, substituir ou excluir o mesmo documento consumirá mais processamento do serviço e, portanto, mais unidades de solicitação. Cada resposta do serviço inclui um cabeçalho personalizado (x-ms-request-charge) que mede as unidades de solicitação consumidas para a solicitação. Esse cabeçalho também está acessível por meio dos [SDKs](documentdb-sdk-dotnet.md). No SDK .NET, RequestCharge é uma propriedade do objeto ResourceResponse.

>[AZURE.NOTE] A linha de base de uma unidade de solicitação para um documento de 1 KB corresponde a um GET simples por self link do documento.

Há vários fatores que afetam as unidades de solicitação consumidas para uma operação em relação a uma conta do Banco de Dados de Documentos. Esses fatores incluem:

- Tamanho do documento. À medida que os tamanhos de documento aumentam, as unidades consumidas para ler ou gravar os dados também aumentam.
- Contagem de propriedades. Pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento aumentarão conforme a contagem de propriedades aumentar.
- Consistência de dados. Ao serem usados níveis de consistência de dados Strong ou Bounded Staleness, unidades adicionais serão consumidas para ler documentos.
- Propriedades indexadas. Uma política de indexação em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação limitando o número de propriedades indexadas. 
- Indexação de documentos. Por padrão, cada documento é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus documentos.

Consultas, procedimentos armazenados e gatilhos consumem unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.

## Escolha do nível de consistência e da taxa de transferência
A escolha do nível de consistência padrão tem impacto na taxa de transferência e na latência. Você pode definir o nível de consistência padrão de forma programática e por meio do Portal do Azure. Você também pode substituir o nível de consistência com base em cada solicitação. Por padrão, o nível de consistência é o Session, que fornece leitura/gravação monotônica e lê suas garantias de gravação. A consistência Session é excelente para aplicativos centrados no usuário e fornece um equilíbrio ideal de compensações de consistência e desempenho.

Para obter instruções sobre como alterar o nível de consistência no Portal do Azure, veja [Como gerenciar uma Conta do Banco de Dados de Documentos](documentdb-manage-account.md#consistency). Ou, para obter mais informações sobre os níveis de consistência, consulte [Usando níveis de consistência](documentdb-consistency-levels.md).

## Armazenamento de documentos provisionado e sobrecarga de índice
Com cada coleção criada, são provisionados 10 GB de armazenamento de documentos com suporte de SSD. O armazenamento de documentos de 10 GB inclui os documentos e o armazenamento para o índice. Por padrão, uma coleção do Banco de Dados de Documentos está configurada para indexar automaticamente todos os documentos sem requerer explicitamente nenhum índice ou esquema secundário. Com base nos aplicativos que usam o Banco de Dados de Documentos, a sobrecarga de índice típica está entre 2 e 20%. A tecnologia de indexação usada pelo Banco de Dados de Documentos garante que, independentemente dos valores das propriedades, a sobrecarga de índice não excede mais que 80% do tamanho dos documentos com configurações padrão.

Por padrão, todos os documentos são indexados pelo Banco de Dados de Documentos automaticamente. Porém, caso deseje ajustar a sobrecarga de índice, você pode optar por remover determinados documentos da indexação no momento da inserção ou substituição de um documento, conforme descrito em [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md). Você pode configurar uma coleção do Banco de Dados de Documentos para excluir todos os documentos na coleção para não serem indexados. Você também pode configurar uma coleção do Banco de Dados de Documentos para indexar seletivamente somente determinadas propriedades ou caminhos com caracteres curinga dos seus documentos JSON, conforme descrito em [Configurando a política de indexação de uma coleção](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). A exclusão de propriedades ou documentos também melhora a taxa de transferência de gravação – o que significa que você consumirá menos unidades de solicitação.
 
## Próximas etapas
Para obter instruções sobre como monitorar os níveis de desempenho no Portal do Azure, veja [Monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).

Para obter mais informações sobre como escolher os níveis de desempenho para as coleções, consulte [Níveis de desempenho no Banco de Dados de Documentos](documentdb-performance-levels.md).
 

<!---HONumber=AcomDC_0128_2016-->