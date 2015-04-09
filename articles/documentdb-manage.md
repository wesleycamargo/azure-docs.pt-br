<properties 
	pageTitle="Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos | Azure" 
	description="Saiba como você pode dimensionar elasticamente o Banco de Dados de Documentos para atender às necessidades de capacidade do seu aplicativo." 
	services="documentdb" 
	authors="mimig1, johnfmacintyre" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/17/2015" 
	ms.author="mimig"/>

#Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos
O Banco de Dados de Documentos é um serviço de banco de dados NoSQL orientado a documentos, escalonável e totalmente gerenciado.  Com o Banco de Dados de Documentos, você não precisa alugar máquinas virtuais, implantar software, monitorar banco de dados nem se preocupar com recuperação de desastres. O Banco de Dados de Documentos é operado e continuamente monitorado por engenheiros da Microsoft para a melhor fornecer disponibilidade, desempenho e proteção aos dados do cliente.  

Você pode começar a usar o Banco de Dados de Documentos criando uma conta de banco de dados por meio do [Portal de Gerenciamento da Visualização do Microsoft Azure](https://portal.azure.com/). O Banco de Dados de Documentos é oferecido em UCs (unidades de capacidade) empilháveis de armazenamento com suporte de SSD (unidade de estado sólido) e taxa de transferência. Você pode dimensionar elasticamente o Banco de Dados de Documentos para atender às necessidades de desempenho e armazenamento do seu aplicativo. 

Cada unidade de capacidade vem com uma cota de coleções para armazenar dados de documentos, armazenamento de documentos provisionados e taxa de transferência provisionada como unidades de solicitação por segundo. Se os requisitos de capacidade do seu aplicativo mudarem, você pode expandir ou reduzir a quantidade de capacidade provisionada em sua conta de banco de dados. A capacidade provisionada em uma conta de banco de dados está disponível para todos os bancos de dados e coleções que existem ou são criadas dentro da conta.

> [AZURE.NOTE] Cada coleção pode ser dimensionada no armazenamento e na taxa de transferência até o [limite](documentdb-limits.md) máximo com suporte. A taxa de transferência será distribuída uniformemente para todas as coleções até o limite máximo de cada coleção.

Quando seu aplicativo ultrapassa os níveis de desempenho para uma ou várias coleções, as solicitações são limitadas por coleção. Isso significa que algumas solicitações do aplicativo podem ser bem-sucedidas enquanto outras são limitadas.

##<a name="DBaccount"></a>Conta de banco de dados e recursos administrativos
Como assinante do Azure, você pode provisionar uma ou mais contas do Banco de Dados de Documentos. Cada conta de banco de dados vem com uma cota de recursos administrativos incluindo bancos de dados, usuários e permissões. Esses recursos estão sujeitos a [cotas e limites](documentdb-limits.md). Se você precisar de recursos administrativos adicionais, entre em contato com o suporte.   

##<a name="DBstorage"></a> Bancos de dados com armazenamento de documentos ilimitado
Um único banco de dados do Banco de Dados de Documentos pode conter praticamente uma quantidade ilimitada de armazenamento de documentos particionado por coleções. As coleções formam os domínios de transação para os documentos contidos neles. Um banco de dados do Banco de Dados de Documentos é elástico por padrão, indo de alguns GB a, possivelmente, terabytes de armazenamento de documento com suporte de SSD e taxa de transferência provisionada. Diferentemente de um banco de dados RDBMS tradicional, um banco de dados do Banco de Dados de Documentos não é vinculado a uma única máquina.   

Com o Banco de Dados de Documentos, conforme a escala do seu aplicativo precisa crescer, você pode criar mais coleções ou bancos de dados, ou ambos. Na verdade, vários aplicativos primários na Microsoft utilizam o Banco de Dados de Documento na escala do consumidor ao criar bancos de dados do Banco de Dados de Documento extremamente grandes, cada um contendo milhares de coleções com terabytes de armazenamento de documentos. É possível expandir ou reduzir um banco de dados adicionando ou removendo coleções para atender os requisitos de escada do seu aplicativo. Você pode criar qualquer número de coleções dentro de um banco de dados sujeito à disponibilidade de oferta e à quantidade de unidades de capacidade compradas. O armazenamento com suporte de SSD e taxa de transferência provisionado a você pode ser dividido entre as coleções nos bancos de dados em sua conta de banco de dados. 

##<a name="DBCollections"></a>Coleções de banco de dados
Cada banco de dados do Banco de Dados de Documentos pode conter uma ou mais coleções. Uma coleção fornece o escopo para armazenamento de documentos e execução de consultas. Uma coleção também é um domínio de transação para todos os documentos contidos nele. Você pode criar qualquer número de coleções para atender aos requisitos de escala dos seus aplicativos. Para criar coleções, primeiro é preciso comprar uma ou mais CUs (unidades de capacidade). Cada unidade de capacidade inclui uma cota de coleções. Caso você atinja a cota de coleções para sua conta, você pode comprar unidades de capacidade adicionais.  

>[AZURE.NOTE] Cada coleção dá suporte ao armazenamento de até 10 GB de dados de documentos. 

##<a name="ProvStorage"></a>Armazenamento e taxa de transferência provisionados como unidades de capacidade
Você pode provisionar unidades empilháveis de armazenamento de documento com suporte a SSD e taxa de transferência em termos de CU (unidades de capacidade). Você pode dimensionar elasticamente o Banco de Dados de Documentos com desempenho previsível adquirindo mais unidades de capacidade, para atender às necessidades do seu aplicativo para escala de leitura, armazenamento e taxa de transferência.  
 
Cada CU vem com 3 coleções elásticas, armazenamento de documentos provisionados com suporte a SSD de 10 GB e 2000 RUs (unidades de solicitação) de taxa de transferência provisionada. O armazenamento provisionado e a capacidade de taxa de transferência associados a uma CU são distribuídos entre as coleções do Banco de Dados de Documentos criadas.   

##<a name="ProvThroughput"></a>Unidades de solicitação e operações de banco de dados
O Banco de Dados de Documentos proporciona um amplo conjunto de operações de banco de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e disparadores - todos operando em documentos dentro de uma coleção de banco de dados. O custo de processamento associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para realizar a operação. Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.

As unidades de solicitação são provisionadas para cada Conta de banco de dados com base no número de unidades de capacidade que você compra. Na Visualização do Banco de Dados de Documentos, um limite de unidades de solicitação é atribuído a cada coleção criada. A alocação é calculada por meio da alocação uniforme de todas as unidades de solicitação das unidades de capacidade adquiridas para o número de coleções criadas em uma conta. Por exemplo, para uma conta com duas unidades de capacidade adquiridas e quatro coleções criadas, a cada coleção serão atribuídas duas unidades de capacidade x 2.000 unidades de solicitação / quatro coleções para uma alocação de 1.000 unidades de solicitação por coleção. Quando o Banco de Dados de Documentos do Azure tiver disponibilidade geral, o serviço não espalhará mais unidades de solicitação entre coleções em uma conta. As unidades de solicitação serão adquiridas e atribuídas a coleções específicas com base nos níveis de desempenho. Para obter mais informações, consulte [Níveis de desempenho do Banco de Dados de Documentos](documentdb-performance-levels.md). 

>[AZURE.NOTE] Durante a Visualização do Banco de Dados de Documentos do Azure, não é possível alocar a cada coleção mais de 2.000 unidades de solicitação. 

O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Os aplicativos que excedem a taxa de unidades de solicitação provisionadas para sua conta serão limitados até que a taxa fique abaixo do nível para cada coleção. Se o seu aplicativo exigir um nível mais alto de taxa de transferência, você pode comprar unidades de capacidade adicionais.

Uma unidade de solicitação é uma medida normalizada de custo de processamento de solicitação. Uma única unidade de solicitação representa a capacidade de processamento necessária para ler um único documento JSON de 1 KB que consiste em 10 valores de propriedade exclusivos. O custo de unidade de solicitação pressupõe um nível de consistência definido como a "Sessão" padrão e todos os documentos indexados automaticamente. Uma solicitação para inserir, substituir ou excluir o mesmo documento consumirá mais processamento do serviço e, portanto, mais unidades de solicitação. Cada resposta de solicitação do serviço inclui um cabeçalho personalizado (x-ms-request-charge) que mede as unidades de solicitação consumidas para a solicitação. Esse cabeçalho também está acessível por meio dos SDKs. No SDK do .Net, RequestCharge é uma propriedade do objeto ResourceResponse.

Há vários fatores que afetam as unidades de solicitação consumidas para uma operação em relação a uma conta do Banco de Dados de Documentos. Esses fatores incluem:

- Tamanho do documento - à medida que os tamanhos de documento aumentam, as unidades consumidas para ler ou gravar os dados também aumentam.
- Contagem de propriedades - pressupondo a indexação padrão de todas as propriedades, as unidades consumidas para gravar um documento aumentarão conforme a contagem de propriedades aumentar
- Consistência de dados - ao serem usados níveis de consistência de dados Strong ou Bounded Staleness, unidades adicionais serão consumidas para ler documentos
- Propriedades indexadas - uma política de indexação em cada coleção determina quais propriedades são indexadas por padrão. Você pode reduzir o consumo de unidades de solicitação limitando o número de propriedades indexadas
- Indexação de documentos - por padrão, cada documento é indexado automaticamente. Você consumirá menos unidades de solicitação se optar por não indexar alguns de seus documentos

Consultas, procedimentos armazenados e gatilhos consumirão unidades de solicitação com base na complexidade das operações que estão sendo executadas. À medida que desenvolver seu aplicativo, inspecione o cabeçalho de solicitação de carga para entender melhor como cada operação está consumindo a capacidade de unidades de solicitação.

##<a name="Collections"></a>Coleções e taxa de transferência provisionada
A taxa de transferência provisionada para sua conta de banco de dados é alocada uniformemente em todas as coleções até o nível máximo de taxa de transferência (unidades de solicitação) para uma única coleção. Por exemplo, se você comprar uma única unidade de capacidade e criar uma única coleção, toda a taxa de transferência provisionada para a CU estará disponível para a coleção. Se uma coleção adicional for criada, a taxa de transferência provisionada será alocada uniformemente com cada coleção recebendo metade de toda a taxa de transferência provisionada.  

##<a name="Consistency"></a>Escolha do nível de consistência e da taxa de transferência
A escolha do nível de consistência padrão tem impacto na taxa de transferência e na latência.  Você pode definir o nível de consistência padrão programaticamente e através do portal do Azure. Você também pode substituir o nível de consistência com base em cada solicitação. Por padrão, o nível de consistência é o Session, que fornece leitura/gravação monotônica e lê suas garantias de gravação. A consistência Session é excelente para aplicativos centrados no usuário e fornece um equilíbrio ideal de compensações de consistência e desempenho.    

Para obter instruções sobre como alterar o nível de consistência no Portal de Gerenciamento de Visualização do Azure, consulte Como: gerenciar configurações de consistência do Banco de Dados de Documentos em [Como gerenciar uma conta do Banco de Dados de Documentos](documentdb-manage-account.md).

##<a name="IndexOverhead"></a>Armazenamento de documentos provisionado e sobrecarga de índice
Cada CU comprada para sua conta é provisionada com 10 GB de armazenamento de documentos com suporte de SSD. O armazenamento de documentos de 10 GB inclui os documentos e o armazenamento para o índice. Por padrão, uma coleção do Banco de Dados de Documentos está configurada para indexar automaticamente todos os documentos sem requerer explicitamente nenhum índice ou esquema secundário. Com base no uso da produção em aplicativos primários da escala do consumidor que usam o Banco de Dados de Documentos, a sobrecarga de índice típica fica entre 2-20%. A tecnologia de indexação usada pelo Banco de Dados de Documentos garante que, independentemente dos valores das propriedades, a sobrecarga de índice não excede mais que 80% do tamanho dos documentos com configurações padrão.  

Por padrão, todos os documentos são indexados pelo Banco de Dados de Documentos automaticamente. Porém, caso deseje ajustar a sobrecarga de índice, você pode optar por remover determinados documentos da indexação no momento da inserção ou substituição de um documento. Você pode configurar uma coleção do Banco de Dados de Documentos para excluir todos os documentos na coleção para não serem indexados. Você também pode configurar uma coleção do Banco de Dados de Documentos para indexar seletivamente somente determinadas propriedades ou caminhos com caracteres curinga dos seus documentos JSON.  A exclusão de propriedades ou documentos também melhora a taxa de transferência de gravação - o que significa que você consumirá menos unidades de solicitação.   
 
##<a name="NextSteps"></a>Próximas etapas
Para obter instruções sobre como gerenciar sua conta do Banco de Dados de Documentos no portal de Visualização do Azure, consulte Como: gerenciar configurações de consistência do Banco de Dados de Documentos em [Como gerenciar uma conta do Banco de Dados de Documentos](documentdb-manage-account.md).

Para obter instruções sobre como monitorar os níveis de desempenho do portal de Visualização do Azure, consulte [Monitorar uma conta do Banco de Dados de Documentos](documentdb-monitor-accounts.md).

<!--HONumber=49-->