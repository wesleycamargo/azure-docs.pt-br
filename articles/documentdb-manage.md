<properties 
	pageTitle="Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos | Azure" 
	description="Saiba como você pode dimensionar elasticamente o Banco de Dados de Documentos para atender às necessidades de armazenamento e desempenho do seu aplicativo." 
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
	ms.date="01/13/2015" 
	ms.author="mimig"/>

#Gerenciar a capacidade e o desempenho do Banco de Dados de Documentos
O Banco de Dados de Documentos é um serviço de banco de dados NoSQL orientado a documentos extremamente escalonável e totalmente gerenciado.  Com o Banco de Dados de Documentos, você não precisa alugar máquinas virtuais, implantar software, monitorar banco de dados nem se preocupar com recuperação de desastres. O Banco de Dados de Documentos é operado e continuamente monitorado por engenheiros da Microsoft para fornecer disponibilidade, desempenho e proteção a dados de classe internacional.  

Você pode começar a usar o Banco de Dados de Documentos criando um banco de dados através do portal do Microsoft Azure. O Banco de Dados de Documentos é oferecido em CUs (unidades de capacidade) empilháveis de armazenamento com suporte de SSD e taxa de transferência. Você pode dimensionar elasticamente o Banco de Dados de Documentos para atender às necessidades de desempenho e armazenamento do seu aplicativo. 

Cada unidade de capacidade vem com uma cota de coleções elásticas para armazenar dados de documentos, armazenamento de documentos provisionados e taxa de transferência provisionada como unidades de solicitação por segundo. Se os requisitos de capacidade do seu aplicativo mudarem, você pode expandir ou reduzir a quantidade de capacidade provisionada em sua conta de banco de dados. A capacidade provisionada em uma conta de banco de dados está disponível para todos os bancos de dados e coleções que existem ou são criadas dentro da Conta.  

#Conta de banco de dados e cota administrativa
Como assinante do Azure, você pode provisionar uma ou mais contas do Banco de Dados de Documentos. Cada conta de banco de dados vem com uma cota de recursos administrativos incluindo 100 bancos de dados, 500.000 usuários e 2.000.000 de permissões.   

#Bancos de dados com armazenamento de documentos ilimitado
Um único banco de dados do Banco de Dados de Documentos pode conter praticamente uma quantidade ilimitada de armazenamento de documentos particionado por coleções. As coleções formam os domínios de transação para os documentos contidos neles. Um banco de dados do Banco de Dados de Documentos é elástico por padrão, indo de alguns GB a, possivelmente, petabytes de armazenamento de documento com suporte de SSD e taxa de transferência provisionada. Diferentemente de um banco de dados RDBMS tradicional, um banco de dados do Banco de Dados de Documentos não é vinculado a um único computador.   

Com o Banco de Dados de Documentos, conforme a escala do seu aplicativo precisa crescer, você pode criar mais coleções ou bancos de dados, ou ambos. Na verdade, vários aplicativos primários na Microsoft utilizam o Banco de Dados de Documento na escala do consumidor ao criar bancos de dados do Banco de Dados de Documento extremamente grandes, cada um contendo milhares de coleções com terabytes de armazenamento de documentos. É possível expandir ou reduzir um banco de dados adicionando ou removendo coleções para atender os requisitos de escada do seu aplicativo. Você pode criar qualquer número de coleções dentro de um banco de dados sujeito à disponibilidade de oferta e à quantidade de unidades de capacidade compradas. O armazenamento com suporte de SSD e taxa de transferência provisionado a você pode ser dividido entre as coleções nos bancos de dados em sua conta de banco de dados. 

#Coleções elásticas
Cada banco de dados do Banco de Dados de Documentos pode conter uma ou mais coleções. Uma coleção fornece o escopo para armazenamento de documentos e execução de consultas. Uma coleção também é um domínio de transação para todos os documentos contidos nele. As coleções são elásticas e podem crescer e reduzir em termos de armazenamento e taxa de transferência. Você pode criar qualquer número de coleções para atender aos requisitos de escala dos seus aplicativos. Para criar coleções, primeiro é preciso comprar uma ou mais CUs (unidades de capacidade). Cada unidade de capacidade inclui uma cota de coleções. Caso você atinja a cota de coleções para sua conta, você pode comprar unidades de capacidade adicionais.  

>[AZURE.NOTE] Observe que na versão de teste as coleções podem crescer e reduzir em qualquer lugar entre 0-10 GB. 

#Armazenamento provisionado e taxa de transferência como unidades de capacidade
Você pode provisionar unidades empilháveis de armazenamento de documento com suporte a SSD e taxa de transferência em termos de CU (unidades de capacidade). Você pode dimensionar elasticamente o Banco de Dados de Documentos com desempenho previsível adquirindo mais unidades de capacidade, para atender às necessidades do seu aplicativo para escala de leitura, armazenamento e taxa de transferência.  
 
Cada CU vem com 3 coleções elásticas, armazenamento de documentos provisionados com suporte a SSD de 10 GB e 2000 RUs (unidades de solicitação) de taxa de transferência provisionada. O armazenamento provisionado e a capacidade de taxa de transferência associados a uma CU são distribuídos entre as coleções do Banco de Dados de Documentos criadas.   

#Taxa de transferência provisionada, unidades de solicitação e operações de bancos de dados
Diferentemente de um armazenamento chave-valor NoSQL que oferece operações GET e PUT simples, o Banco de Dados de Documentos permite um conjunto mais avançado de operações de bancos de dados, incluindo consultas relacionais e hierárquicas com UDFs, procedimentos armazenados e gatilhos - todos operando nos documentos em uma coleção de banco de dados. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação.  Em vez de pensar em e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para realizar várias operações de bancos de dados e atender a uma solicitação do aplicativo.   

As unidades de solicitação são provisionadas para cada Conta de banco de dados com base no número de unidades de capacidade que você compra. O consumo da unidade de solicitação é avaliado em termos de taxa por segundo. Os aplicativos que excedem a taxa de unidades de solicitação provisionadas para sua conta serão limitados até que a taxa fique abaixo do nível reservado para a Conta. Se o seu aplicativo exigir um nível mais alto de taxa de transferência, você poderá comprar unidades de capacidade adicionais.  

A tabela a seguir fornece uma lista de várias operações de banco de dados e as unidades de solicitação disponíveis por CU. A tabela ajuda você a planejar como uma operação de determinado banco de dados consome a taxa de transferência provisionada associada a uma CU.  Vamos supor que o tamanho do documento é de 1 KB consistindo em 10 valores de propriedade exclusivos com o nível de consistência padrão definido como "Session" e todos os documentos indexados automaticamente pelo Banco de Dados de Documentos. 

|Operações de banco de dados|Número de operações por segundo por CU|
|-------------------|--------------------------------------|
|Leitura de um único documento	|2000
|Inserção/substituição/exclusão de um único documento	|500
|Consulta de uma coleção com um predicado simples e retorno de um único documento	|1000
|Procedimento armazenado com 50 inserções de documentos	|20

Observe na tabela que as unidades de solicitação consumidas pelas operações de inserção/substituição/exclusão para documentos indexados automaticamente são aproximadamente 4x as de leitura de um documento. A tabela acima é apenas uma referência. Porém, na prática,  

-	O tamanho dos documentos pode variar (e não corresponder a 1 KB), 
-	Os documentos podem ter mais de 10 propriedades.
-	Você pode optar por definir o nível de consistência padrão como Strong, Bounded Staleness ou Eventual
-	Você pode optar por não indexar somente alguns dos documentos 
-	Você pode optar por indexar somente algumas das suas propriedades e não deixar o Banco de Dados de Documentos indexar tudo automaticamente
-	Suas consultas e procedimentos armazenados podem ser muito mais complexos.  

 Você pode adquirir as unidades de solicitação de determinada solicitação inspecionando o cabeçalho de resposta x-ms-request-charge da solicitação.  

#Coleções e taxa de transferência provisionada
A taxa de transferência provisionada para sua conta de banco de dados é alocada uniformemente em todas as coleções até o nível máximo de taxa de transferência (unidades de solicitação) para uma única coleção. Por exemplo, se você comprar uma única unidade de capacidade e criar uma única coleção, toda a taxa de transferência provisionada para a CU estará disponível para a coleção. Se uma coleção adicional for criada, a taxa de transferência provisionada será alocada uniformemente com cada coleção recebendo metade de toda a taxa de transferência provisionada.  

#Escolha do nível de consistência e da taxa de transferência
A escolha do nível de consistência padrão tem impacto na taxa de transferência e na latência.  Você pode definir o nível de consistência padrão programaticamente e através do portal do Azure. Você também pode substituir o nível de consistência com base em cada solicitação. Por padrão, o nível de consistência é o Session, que fornece leitura/gravação monotônica e lê suas garantias de gravação. A consistência Session é excelente para aplicativos centrados no usuário e fornece um equilíbrio ideal de compensações de consistência e desempenho.   

-	Os níveis de consistência Session e Eventual fornecem aproximadamente 2000 solicitações de leitura de documentos e 500 inserções/substituições/exclusões de documentos.
-	Os níveis de consistência Strong e Bounded Staleness fornecem aproximadamente 1200 solicitações de leitura de documentos e 500 inserções/substituições/exclusões de documentos. As inserções/substituições/exclusões com Bounded Staleness são significativamente menores em latência que no nível Strong.  

#Armazenamento de documentos provisionado e sobrecarga de índice
Cada CU comprada para sua conta é provisionada com 10 GB de armazenamento de documentos com suporte de SSD. O armazenamento de documentos de 10 GB inclui os documentos e o armazenamento para o índice. Por padrão, uma coleção do Banco de Dados de Documentos está configurada para indexar automaticamente todos os documentos sem requerer explicitamente nenhum índice ou esquema secundário. Com base no uso da produção em aplicativos primários da escala do consumidor que usam o Banco de Dados de Documentos, a sobrecarga de índice típica fica entre 2-20%. A tecnologia de indexação usada pelo Banco de Dados de Documentos garante que, independentemente dos valores das propriedades, a sobrecarga de índice não excede mais que 80% do tamanho dos documentos com configurações padrão.  

Por padrão, todos os documentos são indexados pelo Banco de Dados de Documentos automaticamente. Porém, caso deseje ajustar a sobrecarga de índice, você pode optar por remover determinados documentos da indexação no momento da inserção ou substituição de um documento. Você pode configurar uma coleção do Banco de Dados de Documentos para excluir todos os documentos na coleção para não serem indexados. Você também pode configurar uma coleção do Banco de Dados de Documentos para indexar seletivamente apenas determinadas propriedades ou caminhos com caracteres curinga de documentos JSON.  Excluir propriedades ou documentos também melhora a taxa de transferência de gravação - o que significa que você consumirá menos unidades de solicitação.   
 

<!--HONumber=46--> 
