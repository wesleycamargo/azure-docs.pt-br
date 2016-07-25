<properties
   pageTitle="Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure | Microsoft Azure" 
   description="Neste artigo você aprenderá sobre os requisitos e padrões de arquitetura de dados comuns que aplicativos de banco de dados de multilocatários em execução em um ambiente de nuvem precisam considerar e as várias compensações associadas a esses padrões. Ele também explica como o serviço de banco de dados SQL do Azure com seus pools de banco de dados elástico e ferramentas elásticas ajudam a atender esses requisitos de uma maneira sem comprometimento."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure

Neste artigo você aprenderá sobre os requisitos e padrões de arquitetura comuns de dados de aplicativos de banco de dados software como serviço (SaaS) multilocatários em execução em um ambiente de nuvem precisa considerar e várias vantagens e desvantagens associadas a esses padrões. Ele também explica como o serviço de banco de dados SQL do Azure com seus pools de banco de dados elástico e ferramentas elásticas ajudam a atender esses requisitos de uma maneira sem comprometimento.

## Introdução

Tendo trabalhado com fornecedores de aplicativos SaaS ao longo dos últimos anos, vemos os desenvolvedores frequentemente fazerem escolhas em relação a seus melhores interesses a longo prazo durante a criação de modelos de locatário para as camadas de dados de seus aplicativos de multilocatários. Pelo menos inicialmente, a facilidade de desenvolvimento e o provedor de nuvem de menor custo são percebidos como mais importantes do que o isolamento de locatário ou a escalabilidade dos aplicativos. Muitas vezes, isso leva a problemas de satisfação do cliente e correções de curso caras em um momento posterior no tempo.

No contexto deste artigo, um aplicativo multilocatário é um aplicativo hospedado em um ambiente de nuvem e fornece o mesmo conjunto de serviços para vários locatários (centenas ou milhares) que não compartilham ou veem os dados uns dos outros. Uma categoria arquetípica de serviço como essa é um aplicativo de SaaS que fornece serviços para seus locatários em um ambiente de nuvem hospedado.

## Aplicativos multilocatários

Os aplicativos multilocatários são um exemplo proeminente de um tipo de aplicativo cujos dados e cargas de trabalho podem ser particionados com facilidade. Por exemplo, com aplicativos multilocatários, dados e carga de trabalho podem ser particionados normalmente em limites de locatário já que a maioria das solicitações estão dentro dos limites de um locatário. Essa propriedade é inerente aos dados e à carga de trabalho e ela favorece os padrões de aplicativos discutidos no restante do artigo.

Podemos encontrar tais aplicativos distribuídos em todo o espectro de aplicativos baseados em nuvem incluindo:

- aplicativos de banco de dados de ISV, fazendo a transição para a nuvem como aplicativos SaaS
- Aplicativos SaaS compilador para a nuvem do zero
- Usuário final/consumidor direto voltado para aplicativos
- Aplicativos empresariais voltados para funcionários

Aplicativos SaaS de nuvem e aplicativos SaaS enraizados em aplicativos de banco de dados do ISV normalmente resultam em aplicativos de multilocatário. Esses aplicativos SaaS entregam um aplicativo de software especializado, como um serviço para seus locatários. Os locatários têm acesso ao serviço de aplicativo e a propriedade completa dos dados associados armazenados como parte do aplicativo. Mas para tirar proveito dos benefícios de SaaS, seus locatários devem ceder um nível de controle sobre seus próprios dados, confiando no fornecedor SaaS para mantê-lo seguro e isolado de outros dados de locatário. Exemplos típicos são MyOB, SnelStart, Salesforce e etc. Todos esses aplicativos permitem particionamento em limites de locatário e, portanto, dão suporte aos padrões de aplicativos discutidos nas seções a seguir deste artigo.

Os aplicativos que fornecem um serviço direto a consumidores ou funcionários dentro de uma organização (também conhecidos como usuários, em vez de locatários) formam a outra categoria no espectro de aplicativos de multilocatários. Os clientes assinam o serviço e não possuem os dados coletados e armazenados pelo provedor de serviços. Provedores de serviços têm requisitos menos rigorosos para manter os dados de seus clientes isolados uns dos outros, diferente de normas de privacidade determinadas pelo governo. Exemplos típicos são provedores de conteúdo de mídia como Netflix, Spotify, Xbox live, etc. Outros exemplos de aplicativos facilmente particionáveis encontram-se em aplicativos de escala da Internet voltados para clientes ou aplicativos IoT (Internet das Coisas) em que cada cliente ou dispositivo pode servir como uma partição e limites de partição podem ser estabelecidos entre diferentes usuários ou dispositivos.

Podemos, no entanto, também reconhecer que nem todos os aplicativos são particionados facilmente com uma única propriedade, como o locatário, cliente, usuário ou dispositivo. Pense, por exemplo, em aplicativos complexos de ERP (Planejamento de Recursos Empresariais) com produtos, pedidos, clientes. Eles normalmente têm um esquema complexo com milhares de tabelas altamente interconectadas. Nenhuma estratégia de partição única se aplicará a todas as tabelas e funcionará em toda a carga de trabalho. Portanto, podemos explicitamente excluí-las da nossa discussão no restante deste artigo.

Os padrões de design de multilocatário que exploramos nas seções a seguir se aplicam a todos os padrões de aplicativo acima que têm cargas de trabalho e dados facilmente particionáveis. No entanto, para facilitar a apresentação, simplesmente chamamos todos eles como aplicativos SaaS multilocatários.

## Compensações do design de aplicativo multilocatário

Para os desenvolvedores que criam aplicativos de multilocatários na nuvem, as seguintes dimensões são abrangidas:

-	***Isolamento de locatários***: os desenvolvedores precisam assegurar que nenhum locatário obtém acesso indesejado aos dados de outros locatários. Esse requisito de isolamento se estende a outras propriedades, como proteção de vizinhos barulhentos, a capacidade de restaurar dados de um determinado locatário, personalizações específicas de locatário e etc.
-	***Custo de recursos de nuvem***: o aplicativo de SaaS precisa ter custo competitivo. Com base nisso, os desenvolvedores de aplicativos SaaS otimizam aplicativos para reduzir o custo em seu uso de recursos de nuvem (computação, armazenamento e etc.), ao projetar seus aplicativos de multilocatários.
-	***Facilidade de DevOps***: os provedores de aplicativos de multilocatários precisam criar uma proteção de isolamento, manter seus aplicativos, o esquema de banco de dados, monitorar a integridade e solucionar problemas de seus locatários. A complexidade de desenvolvimento de aplicativos e a operação se traduz diretamente em custos adicionais e a satisfação de locatário inferior.
-	***Escalabilidade***: adicionar incrementalmente mais locatários é fundamental para uma operação bem-sucedida de SaaS bem como adicionar mais capacidade para locatários individuais que exigem mais recursos.

Há compensações entre essas dimensões. A oferta de nuvem de menor custo pode não necessariamente oferecer também a experiência de desenvolvimento mais conveniente. Vemos com frequência os desenvolvedores fazerem escolhas mal informadas no espaço identificado pelas quatro dimensões acima.

Para aplicativos SaaS de multilocatários provisionando para outras empresas, o isolamento de locatários geralmente é um requisito fundamental. Vemos com frequência os desenvolvedores sendo tentados para as vantagens percebidas em relação a simplicidade e o custo sobre isolamento de locatários e escalabilidade. Essa compensação pode se provar complexa e cara conforme o serviço cresce e requisitos de isolamento de locatário se tornam mais importantes e precisam ser manipulados com a camada de aplicativo.

Para aplicativos de multilocatários fornecendo um serviço voltado para o cliente direto para usuários finais, o isolamento de locatários pode se tornar uma prioridade menor que a otimização de custo de recursos de nuvem.

Um padrão de desenvolvimento popular é agrupar vários locatários em um ou alguns bancos de dados. Os benefícios (percebidos) nessa abordagem é diminuir o custo por pagar apenas alguns bancos de dados e simplificar trabalhando apenas com o menor número de banco de dados possível. Para aplicativos SaaS de multilocatários, ao longo do tempo essa decisão tem desvantagens significativas com relação ao isolamento e escalabilidade de locatário. Se for necessário o isolamento de locatários, esforços adicionais serão exigidos para proteger os dados de locatário no armazenamento compartilhado contra acesso não autorizado ou vizinhos barulhentos e ele pode melhorar significativamente os esforços de desenvolvimento de aplicativos e os custos de manutenção de isolamento. Da mesma forma, ao adicionar novos locatários, escalar adequadamente a camada de dados desse aplicativo normalmente requer experiência em desenvolvimento para redistribuir dados de locatário entre bancos de dados.

## Modelos de dados de multilocatário 

Práticas de design comuns para a colocação de dados do locatário seguem esses três modelos distintos.


  ![modelos de dados de multilocatário](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png) Figura 1: práticas comuns de design para modelos de dados de multilocatário
  
1.	***Banco de dados por locatário***: essa abordagem coloca cada locatário em seu próprio banco de dados. Todos os dados específicos de locatário estão restritos ao seu banco de dados e isolados de outros locatários e seus dados.
2.	***Banco de dados fragmentados compartilhados***: essa abordagem usa vários bancos de dados, com vários locatários compartilhando um banco de dados. Por exemplo, um conjunto distinto de locatários são atribuídos a cada banco de dados usando uma estratégia de particionamento, como hash, intervalo ou lista de particionamento. Essa estratégia de distribuição de dados é muitas vezes conhecida como fragmentação.
3.	***Banco de dados único compartilhado***: essa abordagem usa um banco de dados único, às vezes, grande que contém dados para todos os locatários sem ambiguidade com uma coluna de ID de locatário.
  
> [AZURE.NOTE] Às vezes, locatários diferentes também são colocados em esquemas de banco de dados diferentes em que o nome do esquema é usado para resolver a ambiguidade entre locatários diferentes. Isso não é uma abordagem recomendada pois geralmente requer o uso de SQL dinâmico e ele não pode fazer uso efetivo do cache do plano. Portanto, o restante deste artigo se concentra na abordagem de tabela compartilhada nessa categoria.
 
## Caracterização de modelos de dados de multilocatário populares

Ao avaliar o uso desses modelos de dados de multilocatário é importante enquadrá-los em termos de compensações do design do aplicativo, discutidos na seção anterior.

-	***Isolamento***: o nível de isolamento entre locatários como uma medida de quanto isolamento de locatário um modelo de dados alcança e
-	***Custo de recursos de nuvem***: a quantidade de compartilhamento de recursos que ocorre entre locatários para otimizar o custo de recursos de nuvem. Um recurso pode ser definido como o custo de computação e armazenamento.
-	***Custo de DevOps***: facilidade de desenvolvimento de aplicativos, implantação e gerenciamento reduz o custo geral de operação de SaaS.

Usando essas dimensões, podemos caracterizar os modelos de dados de multilocatário descritos anteriormente e seu uso do banco de dados com o espaço de quadrante, conforme mostrado na Figura 2 abaixo. O nível de isolamento de locatários e a quantidade de compartilhamento de recursos descrevem as dimensões de eixo Y e X do espaço. A seta diagonal grande no meio indica os custos de DevOps.

![padrões populares](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figura 2: caracterização de modelos de dados de multilocatário populares

O quadrante inferior direito da Figura 2 acima mostra o uso de um banco de dados compartilhado único potencialmente grande junto com a tabela compartilhada (ou esquema separado) como um dos padrões de aplicativo. Ele fornece compartilhamento de recursos excelente porque todos os locatários estão usando os mesmos recursos de banco de dados (CPU, memória e E/S) em um banco de dados individual. No entanto, o isolamento de locatários é limitado. Etapas adicionais devem ser tomadas para proteger locatários uns dos outros na camada de aplicativo que pode aumentar significativamente os custos de DevOps no desenvolvimento e gerenciamento de aplicativo. Além disso, a escalabilidade é limitada pela escala do hardware usado para hospedar o banco de dados.

A seção intermediária ilustra vários locatários fragmentados em vários bancos de dados diferentes (unidades de escala de hardware normalmente diferentes) com cada banco de dados hospedando um subconjunto de locatários que soluciona o problema de escalabilidade do padrão anterior. Se a nova capacidade for necessária para mais locatários, ela poderá facilmente ser colocada em novos bancos de dados alocados para novas unidades de escala de hardware. No entanto, a quantidade de compartilhamento de recursos é reduzida, pois somente locatários colocados nas mesmas unidades de escala compartilham seus recursos. Além disso, a abordagem fornece poucas melhorias ao isolamento de locatários pois ainda vários locatários são colocados nos mesmos locais sem sejam protegidos automaticamente contra ações uns dos outros. A complexidade do aplicativo permanece alta. Essa abordagem é muitas vezes conhecida como "fragmentação".

O quadrante superior esquerdo da Figura 2 acima é a terceira abordagem. Ela coloca cada locatário em seu próprio banco de dados. Essa abordagem tem propriedades de isolamento locatário excelentes, mas permite pouco compartilhamento de recursos quando cada banco de dados fornece seus próprios recursos dedicados. Essa abordagem é ótima caso todos os locatários estejam mostrando cargas de trabalho previsíveis. No entanto, assim que as cargas de trabalho de locatário se tornarem menos previsíveis (mais comum para SaaS), o compartilhamento de recursos não poderá ser otimizado fazendo com que o provedor realize provisionamento excessivo para atender às demandas ou diminua os recursos, resultando em custos mais altos ou menor satisfação do locatário. Como resultado, um grau maior de compartilhamento de recursos entre locatários se torna desejável para tornar a solução mais econômica. Aumentar o número de bancos de dados também aumenta o custo de DevOps na implantação de aplicativos e manutenção. Voltaremos a essas preocupações na próxima seção, mas é importante lembrar que esse método fornece o isolamento melhor e mais fácil para locatários em comparação com os outros.

A escolha do cliente do padrão de design também é influenciada por esses fatores adicionais:

-	***Propriedade dos dados de locatário***: se locatários retêm a propriedade dos dados que favorece o banco de dados único por padrão de locatário
-	***Escala***: se o aplicativo for voltado para centenas de milhares ou milhões de locatários, ele favorecerá abordagens de compartilhamento de banco de dados como fragmentação, mas os requisitos de isolamento ainda representarão desafios.
-	***Modelo de negócios e valor***: por receita de locatário; se ela for muito pequena (menos de um dólar), os requisitos de isolamento se tornarão menos essenciais e o banco de dados compartilhado fará sentido. Se for alguns dólares ou mais, o banco de dados por locatário se tornará viável e o custo de operações de desenvolvimento de aplicativo poderá ser reduzido.

Considerando as compensações acima, conforme mostrado na Figura 2 acima, um modelo multilocatário ideal seria necessário incorporar ótimas propriedades de isolamento de locatário com compartilhamento de recursos entre os locatários excelente. Por exemplo, um modelo que se ajusta quadrante superior direito.

## Suporte de multilocatários com o banco de dados SQL do Azure

O banco de dados SQL do Azure dá suporte a todos os padrões de aplicativo de multilocatário descritos na Figura 2. Com os pools de banco de dados elástico, ele agora também dá suporte a um novo padrão de aplicativo que combina benefícios de isolação e de compartilhamento de recursos excelentes da abordagem de banco de dados por locatário (quadrante superior direito em verde na Figura 3 abaixo). As ferramentas de banco de dados elástico e os recursos no banco de dados SQL do Azure ajudam com custo do DevOps no desenvolvimento e operação de um aplicativo com muitos bancos de dados (mostrados pela área sombreada na Figura 3 abaixo). Essas ferramentas ajudam a criar e gerenciar aplicativos adotando qualquer um dos padrões de vários bancos de dados. As subseções a seguir discutem os recursos de banco de dados SQL e como o banco de dados SQL ajuda a esses modelos de multilocatários em mais detalhes.

![padrão do BD SQL](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figura 3: padrões de aplicativo de multilocatário com o banco de dados SQL do Azure

## Modelo de banco de dados por locatário com ferramentas e pools elásticos

O banco de dados SQL do Azure fornece "pools de banco de dados elástico" para melhor suporte à abordagem de 'banco de dados por locatário' combinando o isolamento de locatários com compartilhamento de recursos entre bancos de dados de locatário. Ele foi projetado como uma solução de camada de dados para provedores de SaaS criarem aplicativos de multilocatários. Quando combinado com ofertas de ferramentas de banco de dados elástico, multilocação é interna e a carga de compartilhamento entre locatários de recursos é movida do aplicativo para a camada de serviço do banco de dados. A complexidade do gerenciamento/consulta em grande escala em bancos de dados é simplificada com trabalhos elásticos, consulta, transações e com a biblioteca de cliente do banco de dados elástico.

| Requisitos do Aplicativo | Recursos de Banco de Dados SQL |
| ------------------------ | ------------------------- |
| Isolamento de locatários e compartilhamento de recursos | [Pools de banco de dados elástico:](sql-database-elastic-pool.md) esse recurso permite alocar um pool de recursos SQLDB e compartilhar esses recursos em vários bancos de dados. Além disso, os bancos de dados individuais podem retirar a quantidade de recursos do pool necessária para acomodar picos de demanda de capacidade devido a alterações nas cargas de trabalho de locatário e o próprio pool elástico pode ser aumentado ou reduzido conforme necessário. Pools elásticos também fornecem facilidade de gerenciamento, monitoramento e solução de problemas no nível do pool. |
| Facilidade de DevOps em bancos de dados | [Pools de banco de dados elástico:](sql-database-elastic-pool.md) conforme listado acima.|
||[Consulta elástica:](sql-database-elastic-query-horizontal-partitioning.md) fornece capacidade de consultar em bancos de dados relatórios ou análises entre locatários.|
||[Trabalhos elásticos:](sql-database-elastic-jobs-overview.md) fornece a capacidade de empacotar e implantar confiavelmente as operações de manutenção de banco de dados ou alterações de esquema do banco de dados para vários bancos de dados.|
||[Transações elásticas:](sql-database-elastic-transactions-overview.md) fornece a capacidade de processar alterações em vários bancos de dados de uma maneira atômica e isolada. Isso é necessário quando os aplicativos precisam de garantias de "tudo ou nada" sobre várias operações de banco de dados. |
||[Biblioteca de cliente do banco de dados elástico:](sql-database-elastic-database-client-library.md) esse recurso permite gerenciar distribuições de dados e o mapeamento de locatários para bancos de dados. |
||||

## Modelos compartilhados

Conforme descrito anteriormente, para a maioria dos provedores SaaS abordagens de “modelo compartilhado” podem apresentar problemas com problemas de isolamento do locatário, bem como as complexidades de desenvolvimento e manutenção do aplicativo. No entanto, para aplicativos de multilocatário fornecerem um serviço diretamente para consumidores finais, os requisitos de isolamento de locatário podem não ser uma prioridade tão alta como o desejo de otimizar o custo. Eles poderão empacotar locatários em um ou mais bancos de dados em uma densidade bastante alta para reduzir os custos. Os modelos de banco de dados compartilhados usando um banco de dados individual ou vários bancos de dados fragmentados podem resultar em uma eficiência adicional no compartilhamento de recursos e em redução do custo geral. O banco de dados SQL do Azure fornece alguns recursos que ajudam os clientes a criar isolamento de segurança e gerenciamento em uma redução horizontal da camada de dados.

| Requisitos do Aplicativo | Recursos de Banco de Dados SQL |
| ------------------------ | ------------------------- |
| Recursos de isolamento de segurança | [Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Esquema de banco de dados](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilidade de DevOps em bancos de dados | [Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Trabalhos elásticos](sql-database-elastic-jobs-overview.md) |
|| [Transações elásticas](sql-database-elastic-transactions-overview.md) |
|| [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) |
|| [Divisão/mesclagem do banco de dados elástico](sql-database-elastic-scale-overview-split-and-merge.md) |
||||

## Conclusões

Requisitos de isolamento de locatário são muito importantes para a maioria dos aplicativos SaaS de multilocatários. A melhor opção para fornecer isolamento é voltada fortemente para a abordagem "banco de dados por locatário". As outras duas abordagens exigem investimentos de camada de aplicativo complexos e uma equipe de desenvolvimento especializada para fornecer isolamento. Isso pode aumentar significativamente os custos e riscos. Se os requisitos de isolamento não forem considerados no início do desenvolvimento de serviços, retrocedê-los poderá ser ainda mais oneroso do que os primeiros dois modelos. As principais desvantagens associadas ao modelo "banco de dados por locatário" estão relacionadas aos custos de recursos de nuvem maiores devido à redução de compartilhamento e à manutenção e gerenciamento de um grande número de bancos de dados. Os desenvolvedores de aplicativos SaaS muitas vezes tem dificuldades em fazer essas compensações.

Enquanto essas compensações podem ser grandes barreiras para a maioria dos provedores de serviço de banco de dados de nuvem, o serviço de banco de dados SQL do Azure elimina essas barreiras com o "pool de banco de dados elástico" e os "recursos de banco de dados elástico". Os desenvolvedores de SaaS podem combinar as características de isolamento do modelo de banco de dados por locatário ao otimizar o compartilhamento de recursos e melhor gerenciamento de um grande número de bancos de dados com pools elásticos e ferramentas associadas.

Para provedores de aplicativos de multilocatários que não têm requisitos de isolamento de locatário e são capazes de empacotar locatários em banco de dados em uma densidade muito alta para reduzir custos, os modelos de dados “compartilhados” podem resultar em alguma eficiência adicional no compartilhamento de recursos e na redução do custo geral. Ferramentas de banco de dados elástico do banco de dados SQL do Azure, bibliotecas de fragmentação e recursos de segurança ajudam ps provedores SaaS na criação e gerenciamento desses aplicativos de multilocatários.

## Próximas etapas

Para ver um aplicativo de exemplo que demonstra a biblioteca de cliente, confira [Introdução às ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-get-started.md).

Para obter um aplicativo de exemplo que fornece uma solução para um cenário de SaaS (Software como solução) que utiliza Pools Elásticos para fornecer um back-end de banco de dados econômico e escalonável para um aplicativo SaaS, veja [Painel Personalizado de Pool Elástico para Saas](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard).

Para converter os bancos de dados existentes para usar as ferramentas, veja [Migrar bancos de dados existentes para escala horizontal](sql-database-elastic-convert-to-use-elastic-tools.md).

Para criar um novo pool, confira o [tutorial Criar pool elástico](sql-database-elastic-pool-create-portal.md).

Para monitorar e gerenciar um pool de Banco de Dados Elástico, confira [Monitorar e gerenciar um pool de Banco de Dados Elástico](sql-database-elastic-pool-manage-portal.md).

## Recursos adicionais

- [O que é um Pool de Banco de Dados Elástico do Azure?](sql-database-elastic-pool.md)
- [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
- [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Autenticação em aplicativos multilocatários usando o Azure AD e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Aplicativo Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)
- [Inícios Rápidos da Solução](sql-database-solution-quick-starts.md)

## Perguntas e solicitações de recursos

Em caso de dúvidas, entre em contato conosco pelo fórum [Banco de Dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e, para solicitações de recursos, adicione-as ao [Fórum de comentários do SQL Database](https://feedback.azure.com/forums/217321-sql-database/).









	

<!---HONumber=AcomDC_0713_2016-->