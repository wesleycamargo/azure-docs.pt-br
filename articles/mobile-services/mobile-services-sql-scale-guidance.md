<properties 
	pageTitle="Escalar serviços móveis com suporte pelo Banco de Dados SQL do Azure - Serviços Móveis do Azure" 
	description="Saiba como diagnosticar e corrigir problemas de escalabilidade em seus serviços móveis com o apoio de banco de dados SQL" 
	services="mobile-services" 
	documentationCenter="" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="donnam;ricksal"/>

# Escalar serviços móveis suportados pelo Banco de Dados SQL do Azure

Os Serviços Móveis do Azure facilitam a introdução e compilação de um aplicativo que se conecta a um back-end hospedado em nuvem e armazena dados em um banco de dados SQL. À medida que seu aplicativo cresce, escalonar suas instâncias de serviços será tão simples quanto ajustar as configurações de escala no portal para adicionar mais capacidade computacional e de rede. Entretanto, escalonar o banco de dados SQL que suporta seu serviço exige planejamento e monitoramento proativo, uma vez que o serviço recebe mais carga. Este documento explica um é um conjunto de práticas recomendadas para assegurar um excelente desempenho contínuo de seus serviços móveis suportados por SQL.

Este tópico apresenta e explica as seguintes seções básicas:

1. [Diagnosticando problemas](#Diagnosing)
2. [Indexação](#Indexing)
3. [Design de esquema](#Schema)
4. [Design de consulta](#Query)
5. [Arquitetura de serviço](#Architecture)
6. [Solução de problemas avançada](#Advanced)

<a name="Diagnosing"></a>
## Diagnosticando problemas

Se você suspeitar de que seu serviço móvel está com problemas de carga, o primeiro lugar a verificar é a guia **Painel** de seu serviço no [Portal de Gerenciamento do Azure][]. Algumas das coisas a verificar são se:

- As medidas de uso, incluindo **Chamadas de API** e **Dispositivos Ativos**, não estão acima da cota
- O status do **Monitoramento de ponto de extremidade** indica que o serviço está apto (somente disponível se o serviço estiver usando a camada padrão e se o Monitoramento de ponto de extremidade estiver habilitado) 

Se alguma das opções acima não for verdadeira, considere ajustar suas configurações de escala na guia *Escala*. Se isso não resolver seu problema, é possível continuar e investigar se o Banco de Dados SQL do Azure pode ser a fonte do problema. As próximas seções abrangem abordagens diferentes para diagnosticar o que está dando errado.

### Escolhendo a camada certa do Banco de Dados SQL 

É importante entender as diferentes camadas de banco de dados que você tem a sua disposição para garantir que você escolha a camada certa para atender às necessidades de seu aplicativo. O Banco de Dados SQL do Azure oferece duas edições de banco de dados diferentes com camadas diferentes:

- Web e Business Edition (descontinuadas)
- Edições Básica, Padrão e Premium 

Embora as edições Web e Business sejam totalmente suportadas, elas serão desativadas até 24 de abril de 2015, conforme abordado em [Perguntas frequentes da desativação das Edições Web e Business](http://msdn.microsoft.com/library/azure/dn741330.aspx). Encorajamos os clientes novos para começar a usar a Basic, a Standard e a Premium Edition em preparação para essa alteração. Esta nova edição fornece uma variedade de novas camadas e recursos de monitoramento que facilitam a compreensão e solução de problemas de desempenho do banco de dados. Todos os novos serviços móveis são criados usando a nova edição.

Para converter um Serviço Móvel usando a Web e a Business Edition para uma entre a Basic, a Standard e a Premium Edition, siga essas etapas.

1. Inicie o [Portal de Gerenciamento do Azure][].
2. Selecione **+NOVO** na barra de ferramentas e escolha **Serviços de Dados**, **Banco de Dados SQL**, **Criação Rápida**.
3. Insira um nome de banco de dados e selecione **Novo servidor de Banco de Dados SQL** no campo **Servidor**. Isso criará um servidor que esteja usando as novas Edições Basic, Standard e Premium. 
4. Preencha o restante dos campos e selecione **Criar Banco de Dados SQL**. Isso criará um banco de dados de 100 MB usando uma camada Basic.
5. Configure seu serviço móvel para usar o banco de dados que você acabou de criar. Navegue até a guia **Configurar** para o serviço e selecione **Alterar o Banco de Dados** na barra de ferramentas. Na tela seguinte, selecione **Usar um banco de dados SQL existente** no campo **Banco de Dados SQL** e selecione **Avançar**. Na tela seguinte, certifique-se de escolher o banco de dados criado na etapa 5 e selecione **OK**.

Aqui estão algumas recomendações sobre como selecionar a camada certa para seu banco de dados:

- **Basic** – use no momento do desenvolvimento ou para pequenos serviços de produção em que você espera somente fazer uma única consulta ao banco de dados de cada vez
- **Standard** – use para serviços de produção em que você espera várias consultas simultâneas ao banco de dados
- **Premium** – use para serviços de produção em grande escala com muitas consultas simultâneas, carga em alto pico e baixa latência esperada em toda consulta.

Para obter mais informações sobre quando usar cada camada, consulte [Razões para usar as novas camadas de serviço](http://msdn.microsoft.com/library/azure/dn369873.aspx#Reasons)

### Analisando as métricas do banco de dados

Quando estiver familiarizado com as diferentes camadas do banco de dados, podemos explorar as métricas de desempenho do banco de dados para nos ajudar a entender sobre como escalar dentro e entre as camadas.

1. Inicie o [Portal de Gerenciamento do Azure][].
2. Na guia Serviços Móveis, selecione o serviço com o qual deseja trabalhar.
3. Selecione a guia **Configurar**.
4. Selecione o nome **Banco de Dados SQL** na seção **Configurações do Banco de Dados**. Com isso, você irá até a guia Banco de Dados SQL do Azure no portal.
5. Navegue até a guia **Monitorar**
6. Verifique se as métricas relevantes são exibidas usando o botão **Adicionar Métricas**. Inclua o seguinte
    - *Porcentagem de CPU* (disponível apenas nas camadas Basic/Standard/Premium)
    - *Porcentagem de Leituras de Dados Físicos* (disponível apenas nas camadas Basic/Standard/Premium) 
    - *Porcentagem de Gravação de Log* (disponível apenas nas camadas Basic/Standard/Premium)
    - *Armazenamento* 
7. Inspecione as métricas pela janela de tempo quando seu serviço estiver passando por problemas. 

    ![Portal de Gerenciamento do Azure – Métricas do Banco de Dados SQL][PortalSqlMetrics]

Se qualquer métrica exceder os 80% de utilização por um longo período, isso pode indicar um problema de desempenho. Para obter informações mais detalhadas sobre como entender a utilização do banco de dados, consulte [Entendendo o uso dos recursos](http://msdn.microsoft.com/library/azure/dn369873.aspx#Resource).

Se as métricas indicarem que seu banco de dados está com alta utilização, considere **expandi-lo a uma camada de serviço mais elevada** como uma primeira etapa de redução. Para resolver os problemas imediatamente, considere usar a guia **Escala** de seu banco de dados para expandi-lo. Isso resulta em um aumento na sua conta. ![Portal de Gerenciamento do Azure – Dimensionamento do Banco de Dados SQL][PortalSqlScale]

Assim que possível, considere essas etapas adicionais de redução:

- **Ajuste seu banco de dados.** Você pode reduzir a utilização do banco de dados com frequência, e evitar precisar expandi-lo a uma camada mais elevada ao otimizar o seu banco de dados. 
- **Considere sua arquitetura de serviço.** Com frequência, sua carga de serviço não é distribuída uniformemente ao logo do tempo, mas contém "picos" de alta demanda. Em vez de escalar banco de dados verticalmente para manipular os picos e deixá-lo com pouca utilização durante os períodos de baixa demanda, frequentemente é possível ajustar a arquitetura de serviço para evitar esses picos ou manipulá-los sem o risco de ocorrências de banco de dados.

As seções restantes deste documento contêm uma orientação personalizada para ajudá-lo a implementar essas reduções.


### Configurando alertas

Muitas vezes, é interessante configurar alertas para as principais métricas do banco de dados como uma etapa proativa para garantir que você tenha tempo suficiente para reagir no caso de exaustão de recursos.

1. Navegue até a guia **Monitoramento** do banco de dados para o qual deseja configurar alertas
2. Verifique se as métricas relevantes são exibidas conforme descrito na seção anterior
3. Selecione a métrica para a qual você deseja definir um alerta e selecione **Adicionar regra** ![Portal de Gerenciamento do Azure – Alerta do SQL][PortalSqlAddAlert]
4. Forneça um nome e descrição para o alerta ![Portal de Gerenciamento do Azure - Nome e descrição de Alerta do SQL][PortalSqlAddAlert2]
5. Especifique o valor a ser usado como o limite do alerta. Considere usar **80%** para permitir algum tempo de reação. Não deixe também de especificar um endereço de email que você monitore ativamente. ![Portal de Gerenciamento do Azure - Limite e email de alerta do SQL][PortalSqlAddAlert3]

Para obter mais informações sobre como diagnosticar problemas de SQL, consulte [Diagnósticos avançados](#AdvancedDiagnosing) na parte inferior deste documento.

<a name="Indexing"></a>
## Indexação

Ao começar a perceber problemas de desempenho de consulta, a primeira coisa a investigar é o design de seus índices. Os índices são importantes, pois afetam diretamente o modo como o mecanismo de SQL executa uma consulta.

Por exemplo, se frequentemente você precisa pesquisar um elemento por um determinado campo, considere adicionar um índice para essa coluna. Caso contrário, o mecanismo de SQL será forçado a executar uma verificação de tabela e ler cada registro físico (ou pelo menos a coluna da consulta) e os registros podem estar substancialmente espalhados no disco.

Portanto, se você realiza as instruções WHERE ou JOIN em colunas específicas com frequência, não se esqueça de indexá-las. Consulte a seção [Criando índices](#CreatingIndexes) para obter mais informações.

Se os índices forem muito grandes e a verificação da tabela, ruim, isso significa que você deve indexar cada coluna em sua tabela somente por segurança? A resposta rápida é, "provavelmente não". Os índices ocupam espaço e têm sobrecarga deles próprios: todas as vezes que algo é inserido em uma tabela, as estruturas de índice para cada uma das colunas indexadas precisam ser atualizadas. Veja abaixo as orientações sobre como escolher seus índices de coluna.

### Orientações de design de índice

Conforme mencionado acima, nem sempre é melhor adicionar mais índices a uma tabela, já que os próprios índices podem ser dispendiosos, tanto em termos de desempenho quanto de sobrecarga de armazenamento.

#### Considerações de consulta

- Considere adicionar índices nas colunas que sejam frequentemente usadas nos predicados (por exemplo, cláusulas com WHERE) e condições de junção, enquanto faz o balanceamento das considerações do banco de dados abaixo.
- Grave consultas que inserem ou modificam o maior número de linhas possível em uma única instrução, em vez de usar diversas consultas para atualizar as mesmas linhas. Quando houver apenas uma instrução, o mecanismo do banco de dados pode otimizar melhor a forma como ele mantém os índices.
	
#### Considerações do banco de dados

Um grande número de índices em uma tabela afeta o desempenho das instruções INSERIR, ATUALIZAR, EXCLUIR e MESCLAR, já que todos os índices devem ser ajustados adequadamente como dados nas alterações da tabela.

- Para tabelas **intensamente atualizadas**, evite a indexação de colunas intensamente atualizadas.
- Para tabelas que **não são frequentemente atualizadas**, mas têm grandes volumes de dados, use diversos índices. Isso pode melhorar o desempenho das consultas que não modificam dados (como as instruções SELECIONAR), já que o otimizador de consultas terá mais opções para encontrar o melhor método de acesso.

A indexação de pequenas tabelas pode não ser otimizada, pois o otimizador de consulta leva mais tempo para percorrer a pesquisa do índice para dados em vez de executar uma simples verificação de tabela. Portanto, os índices nunca devem ser usados nas tabelas pequenas, mas devem ser mantidos como dados nas alterações de tabela.


<a name="CreatingIndexes"></a>
### Criando índices

#### Back-end do JavaScript

Para configurar um índice para uma coluna no back-end do JavaScript, faça o seguinte:

1. Abra seu serviço móvel no [Portal de Gerenciamento do Azure][].
2. Clique na guia **Dados**.
3. Selecione a tabela que deseja modificar.
4. Clique na guia **Colunas**.
5. Selecione a coluna. Na barra de comandos, clique em **Configurar Índice**:

	![Portal de Serviços Móveis – Configurar Índice][SetIndexJavaScriptPortal]

Também é possível remover índices dessa exibição.

#### Back-end do .NET

Para definir um índice no Entity Framework, use o atributo `[Index]` nos campos que deseja indexar. Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

		[Index]
        public bool Complete { get; set; }
    }
		 
Para obter mais informações sobre os índices, consulte [Anotações de índice no Entity Framework][]. Para obter dicas adicionais sobre como otimizar os índices, consulte [Indexação avançada](#AdvancedIndexing) na parte inferior deste documento.

<a name="Schema"></a>
## Design de esquema

Aqui estão alguns problemas que você deve tomar conhecimento ao escolher os tipos de dados para seus objetos, os quais, por sua vez, convertem-se ao esquema de seu banco de dados SQL. Ajustar o esquema pode, frequentemente, trazer melhorias significativas de desempenho, já que a SQL possui maneiras otimizadas e personalizadas de manipular a indexação e o armazenamento para diferentes tipos de dados:

- **Use a coluna de ID fornecida**. Todas as tabelas de serviço móvel vêm com uma coluna de ID padrão configurada como a chave primária e tem seu índice configurado nela. Não há necessidade de criar uma coluna de ID adicional.
- **Use os tipos de dados corretos em seu modelo.** Se você sabe que uma determina propriedade de seu modelo será numérica ou booliana, não deixe de defini-la dessa forma em seu modelo, em vez de defini-la como uma cadeia de caracteres. No back-end do JavaScript, use literais como `true` em vez de `"true"` e `5` em vez de `"5"`. No back-end do .NET, use os tipos `int` e `bool` ao declarar as propriedades de seu modelo. Isso permite que a SQL crie o esquema correto para esses tipos, o que torna a consulta mais eficiente.  

<a name="Query"></a>
## Design de consulta

Aqui estão algumas orientações a serem consideradas ao consultar o banco de dados:

- **Sempre execute operações de junção no banco de dados.** Você precisará combinar os registros de duas ou mais tabelas com frequência, sendo que os registros sendo combinados compartilham um campo comum (também conhecido como uma *junção*). Essa operação pode não ser eficiente se for executada incorretamente, pois envolve mover todas as entidades para baixo de ambas as tabelas e, em seguida, iterar por meio delas. Esse tipo de operação é melhor se deixada para o próprio banco de dados, mas às vezes pode ser facilmente executada de forma errada no cliente ou no código do serviço móvel.
    - Não execute junções no código de seu aplicativo
    - Não execute junções no código de seu serviço móvel. Ao usar o back-end do JavaScript, saiba que o [objeto de tabela](http://msdn.microsoft.com/library/windowsazure/jj554210.aspx) não manipula junções. Não se esqueça de usar o [objeto mssql](http://msdn.microsoft.com/library/windowsazure/jj554212.aspx) diretamente para garantir que a junção ocorra no banco de dados. Para obter mais informações, consulte [Juntar tabelas relacionais](mobile-services-how-to-use-server-scripts.md#joins). Se estiver usando o back-end do .NET e consultando com LINQ, as junções serão automaticamente manipuladas no nível do banco de dados pelo Entity Framework.
- **Implementar paginação.** Às vezes, consultar o banco de dados pode resultar em um grande número de registros retornado ao cliente. Para minimizar o tamanho e a latência das operações, considere implementar a paginação.
    - Por padrão, seu serviço móvel limitará quaisquer consultas de entrada para uma página de tamanho 50 e você pode solicitar manualmente até 1.000 registros. Para obter mais informações, consulte "Retornar dados em páginas" para [Windows Store](mobile-services-windows-dotnet-how-to-use-client-library.md#paging), [iOS](mobile-services-ios-how-to-use-client-library.md#paging), [Android](mobile-services-android-how-to-use-client-library.md#paging), [HTML/JavaScript](mobile-services-html-how-to-use-client-library/#paging) e [Xamarin](partner-xamarin-mobile-services-how-to-use-client-library.md#paging).
    - Não existe um tamanho de página padrão para as consultas feitas de seu código de serviço móvel. Se o seu aplicativo não implementar a paginação, ou como uma medida defensiva, considere aplicar limites padrão em suas consultas. No back-end do JavaScript, use o operador **take** no [objeto de consulta](http://msdn.microsoft.com/library/azure/jj613353.aspx). Se estiver usando o back-end do .NET, considere usar o [método Take](http://msdn.microsoft.com/library/vstudio/bb503062(v=vs.110).aspx) como parte de sua consulta LINQ.  

Para obter mais informações sobre como melhorar o design da consulta, inclusive como analisar os planos de consulta, consulte [Design de consulta avançada](#AdvancedQuery) na parte inferior deste documento.

<a name="Architecture"></a>
## Arquitetura de serviço

Imagine um cenário em que você esteja prestes a enviar uma notificação por push para todos os seus clientes para verificarem um novo conteúdo no aplicativo. Conforme tocam na notificação, o aplicativo é iniciado, o que possivelmente dispara uma chamada para seu serviço móvel e uma execução de consulta no banco de dados SQL. Como milhões de clientes em potencial executam essa ação no período de apenas alguns minutos, isso gerará um aumento de carga de SQL, o que pode ser de uma magnitude superior à carga de estado constante de seu aplicativo. Isso pode ser resolvido ao expandir seu aplicativo para uma camada de SQL mais elevada durante o pico e, em seguida, reduzi-lo novamente, no entanto, essa solução exige uma intervenção manual e gera um custo elevado. Fazer pequenos ajustes em sua arquitetura de serviço móvel com frequência pode equilibrar significativamente a unidade de clientes de carga para seu banco de dados SQL e eliminar picos problemáticos na demanda. Essas modificações podem ser implementadas facilmente com mínimo impacto na experiência de seu cliente. Estes são alguns exemplos:

- **Espalhar a carga ao longo do tempo.** Se você controlar a duração de determinados eventos (por exemplo, uma notificação transmitida por push), os quais se espera gerar um pico na demanda, e a duração desses eventos não for crítica, considere espalhá-los ao longo do tempo. No exemplo acima, talvez seja aceitável que seus clientes de aplicativo sejam notificados de um novo conteúdo do aplicativo em massa ao longo de um dia em vez de quase simultaneamente. Considere separar os clientes em grupos que permitirão uma entrega escalonada em cada lote. Se estiver usando Hubs de Notificação, aplicar uma marca adicional para acompanhar o lote e entregar uma notificação por push para essa marca é uma maneira fácil de implementar essa estratégia. Para obter mais informações sobre as marcas, consulte [Usar Hubs de Notificação para enviar notícias de última hora](../notification-hubs-windows-store-dotnet-send-breaking-news.md).
- **Use o Armazenamento de Blob e de Tabela quando necessário.** Frequentemente, o conteúdo que os clientes verão durante o pico é razoavelmente estático e não precisa ser armazenado em um banco de dados SQL, já que seja pouco provável que você precise de capacidades de consulta relacionais nesse conteúdo. Nesse caso, considere armazenar o conteúdo no Armazenamento de Blob ou de Tabela. É possível acessar os blobs públicos diretamente no Armazenamento de Blob do dispositivo. Para acessar blobs de uma forma segura ou usar o Armazenamento de Tabela, será necessário passar pela API Personalizada dos Serviços Móveis para proteger sua chave de acesso do armazenamento. Para obter mais informações, consulte [Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md).
- **Usar um cache em memória**. Outra alternativa é armazenar dados, os quais poderiam ser normalmente acessados durante um pico de tráfego, em um cache em memória como o [Cache do Azure](http://azure.microsoft.com/services/cache/). Isso significa que as solicitações de entrada poderiam buscar as informações necessárias na memória, em vez de consultar repetidamente o banco de dados.

<a name="Advanced"></a>
## Solução de problemas avançada
Essa seção abrange algumas das tarefas de diagnóstico mais avançadas, as quais podem ser úteis se as etapas anteriores não tratarem completamente do problema.

### Pré-requisitos
Para executar algumas das tarefas de diagnóstico nesta seção, você precisa acessar uma ferramenta de gerenciamento de bancos de dados SQL como o **SQL Server Management Studio** ou a funcionalidade interna de gerenciamento do **Portal de Gerenciamento do Azure**.

O SQL Server Management Studio é um aplicativo gratuito do Windows que oferece os recursos mais avançados. Se você não tiver acesso a um computador Windows (por exemplo, se você estiver usando um Mac), considere provisionar uma Máquina Virtual no Azure, conforme mostrado em [Criar uma máquina virtual executando o Windows Server](../virtual-machines-windows-tutorial.md) e, em seguida, conecte-se remotamente a ela. Se você pretender usar a MV com a finalidade principal de executar o SQL Server Management Studio, uma instância **A0 Básica** (anteriormente chamada de "Extra Pequena") deve ser suficiente.

O Portal de Gerenciamento do Azure oferece uma experiência de gerenciamento interna que é mais limitada, mas está disponível sem a necessidade de uma instalação local.

As etapas a seguir explicam como obter informações de conexão para o banco de dados SQL que dá suporte a seu serviço móvel para que, em seguida, você use qualquer uma das duas ferramentas para se conectar a ele. Você pode escolher a ferramenta que preferir.

#### Obter informações de conexão SQL 
1. Inicie o [Portal de Gerenciamento do Azure][].
2. Na guia Serviços Móveis, selecione o serviço com o qual deseja trabalhar.
3. Selecione a guia **Configurar**.
4. Selecione o nome **Banco de Dados SQL** na seção **Configurações do Banco de Dados**. Com isso, você navegará até a guia Banco de Dados SQL do Azure no portal.
5. Selecione **Configurar as regras de firewall do Azure para esse endereço IP**.
6. Anote o endereço do servidor na seção **Conectar ao seu banco de dados**, por exemplo: *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio
1. Navegue até [Edições do SQL Server – Express](http://www.microsoft.com/server-cloud/products/sql-server-editions/sql-server-express.aspx)
2. Localize a seção **SQL Server Management Studio** e selecione o botão **Baixar** abaixo.
3. Conclua as etapas de configuração até que possa executar com êxito o aplicativo:

    ![SQL Server Management Studio][SSMS]

4. Na caixa de diálogo **Conectar ao Servidor**, insira os seguintes valores
    - Nome do servidor: *endereço do servidor obtido anteriormente*
    - Autenticação: *autenticação do SQL Server*
    - Logon: *logon escolhido ao criar o servidor*
    - Senha: *senha escolhida ao criar o servidor*
5. Agora você deve estar conectado.

#### Portal de Gerenciamento do Banco de Dados SQL
1. Na guia do Banco de Dados SQL do Azure de seu banco de dados, selecione o botão **Gerenciar** 
2. Configure a conexão com os seguintes valores
    - Servidor: *deve ser predefinido com o valor correto*
    - Banco de Dados: *deixe em branco*
    - Nome de usuário: *logon escolhido ao criar o servidor*
    - Senha: *senha escolhida ao criar o servidor*
3. Agora você deve estar conectado.

    ![Portal de Gerenciamento do Azure – Banco de Dados SQL][PortalSqlManagement]

<a name="AdvancedDiagnosing" />
### Diagnósticos avançados

Várias tarefas de diagnóstico podem ser facilmente concluídas da maneira correta no **Portal de Gerenciamento do Azure**, no entanto, algumas tarefas avançadas de diagnóstico somente são possíveis no **SSQL Server Management Studio** ou no **Portal de Gerenciamento do Banco de Dados SQL**. Aproveitaremos as exibições de gerenciamento dinâmico, um conjunto de exibições preenchidas automaticamente com informações de diagnóstico sobre seu banco de dados. Esta seção fornece um conjunto de consultas que pode ser executado nessas exibições para examinar diversas métricas. Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)][].

Após concluir as etapas na seção anterior para se conectar ao banco de dados no SQL Server Management Studio, selecione seu banco de dados no **Pesquisador de Objetos**. Expanda as **Exibições** e as **Exibições do Sistema** mostrarão uma lista de exibições de gerenciamento. Para executar as consultas abaixo, selecione **Nova Consulta**, enquanto estiver com seu banco de dados selecionado no **Pesquisador de Objetos**, cole a consulta e selecione **Executar**.

![SQL Server Management Studio – exibições de gerenciamento dinâmicas][SSMSDMVs]

Como alternativa, se estiver usando o Portal de Gerenciamento do Banco de Dados SQL, primeiro selecione seu banco de dados e escolha **Nova Consulta**.

![Portal de Gerenciamento do Banco de Dados SQL – nova consulta][PortalSqlManagementNewQuery]

Para executar qualquer uma das consultas abaixo, cole-as na janela e selecione **Executar**.

![Portal de Gerenciamento do Banco de Dados SQL – executar consulta][PortalSqlManagementRunQuery]

#### Métricas avançadas

O portal de gerenciamento disponibiliza determinadas métricas para leitura se você estiver usando as camadas Basic, Standard e Premium. No entanto, se estiver usando as camadas Web e Business, apenas a métrica Armazenamento estará disponível no portal. Felizmente, é fácil obter essas e outras métricas usando a exibição de gerenciamento **[sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)**, independentemente de qual camada você está usando. Considere a seguinte consulta:

    SELECT TOP 10 * 
    FROM sys.resource_stats 
    WHERE database_name = 'todoitem_db' 
    ORDER BY start_time DESC

> [AZURE.NOTE]Execute essa consulta no banco de dados **principal** de seu servidor, uma vez que a exibição **sys.resource_stats** está presente apenas nesse banco de dados.

O resultado contem a seguinte métrica útil: CPU (% de limite de camada), Armazenamento (megabytes), Leituras de Dados Físicos (% de limite de camada), Gravações de Log (% de limite de camada), Memória (% de limite de camada), Contagem de Trabalhador, Contagem de Sessão, etc.

#### Eventos de conectividade de SQL

A exibição **[sys.event_log](http://msdn.microsoft.com/library/azure/jj819229.aspx)** contém os detalhes dos eventos relacionados à conectividade.

    select * from sys.event_log 
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [AZURE.NOTE]Execute essa consulta no banco de dados **mestre** de seu servidor, uma vez que a exibição **sys.event_log** está presente apenas nesse banco de dados.

<a name="AdvancedIndexing" />
### Indexação avançada

Uma tabela ou exibição pode conter os seguintes tipos de índices:

- **Clusterizado**. Um índice clusterizado especifica como os registros são fisicamente armazenados em disco. Deve haver apenas um índice clusterizado por tabela, uma vez que as próprias linhas de dados podem ser classificadas em apenas uma ordem.

- **Não clusterizado**. Índices não clusterizados são armazenados em separado das linhas de dados e são usados para realizar uma pesquisa baseada no valor do índice. Todos os índices não clusterizados em uma tabela usam valores-chave do índice clusterizado como chave de pesquisa.

Para fornecer uma analogia ao mundo real: considere um livro ou uma técnica manual. O conteúdo de cada página é um registro, o número da página é o índice clusterizado e o índice de tópicos na parte final do livro é um índice não clusterizado. Cada entrada no índice de tópicos aponta para o índice clusterizado, o número da página.

> [AZURE.NOTE]Por padrão, o back-end do JavaScript dos Serviços Móveis do Azure configura **_createdAt** como o índice clusterizado. Se você remover essa coluna ou se desejar um índice clusterizado diferente, procure seguir as [orientações de design de índice clusterizado](#ClusteredIndexes) abaixo. No back-end do .NET, a classe `EntityData` define `CreatedAt` como um índice clusterizado usando a anotação `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>
#### Orientações de design de índices clusterizados

Todas as tabelas devem possuir um índice clusterizado na coluna (ou nas colunas, no caso de uma chave composta) com as seguintes propriedades:

- Estreita – usa um pequeno tipo de banco de dados ou é uma [chave composta][Primary and Foreign Key Constraints] de um pequeno número de colunas estreitas
- Exclusiva ou em grande parte exclusiva
- Estática – o valor não é alterado com frequência
- Em crescimento constante 
- (Opcional) Largura fixa
- (Opcional) Não nula

O motivo pelo qual existe a propriedade **estreita** é que todos os outros índices em uma tabela usam os valores-chave do índice clusterizado como chaves de pesquisa. No exemplo de um índice de tópico na parte posterior de um livro, o índice clusterizado é um número de página e é um número pequeno. Se o título do capítulo estiver incluído no índice clusterizado, o índice de tópicos seria por si só muito mais longo, já que o valor-chave também o seria (nome do capítulo, número de página).

A chave deve ser **estática** e **em crescimento constante** para evitar a necessidade de manter o local físico dos registros (o que significa mover os registros fisicamente ou possivelmente fragmentar o armazenamento ao dividir as páginas no local onde os registros são armazenados).

O índice clusterizado será mais valioso para consultas que realizam o seguinte:

- Retornam um intervalo de valores usando operadores como ENTRE, >, >=, < e <=. 
	- Após a linha com o primeiro valor ser localizada usando o índice clusterizado, as linhas com os valores clusterizados subsequentes são garantidas como sendo fisicamente adjacentes. 
- Use as cláusulas JUNTAR; normalmente existem colunas de chave estrangeira.
- Use as cláusulas CLASSIFICAR POR ou AGRUPAR POR.
	- Um índice nas colunas especificadas na cláusula CLASSIFICAR POR ou AGRUPAR POR pode remover a necessidade do Mecanismo de Banco de Dados de classificar os dados, uma vez que as linhas já estão classificadas. Isso melhora o desempenho da consulta.

#### Criando índices clusterizados no Entity Framework

Para configurar o índice clusterizado no back-end do .NET usando Entity Framework, configure a propriedade `IsClustered` da anotação. Por exemplo, essa é a definição de `CreatedAt` em `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

	[Index(IsClustered = true)]
	[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
	[TableColumnAttribute(TableColumnType.CreatedAt)]
	public DateTimeOffset? CreatedAt { get; set; }

#### Criando índices no esquema do banco de dados

Para o back-end do JavaScript, é possível apenas modificar o índice clusterizado de uma tabela alterando o esquema do banco de dados diretamente, ser no SQL Server Management Studio ou no Portal de Banco de Dados SQL do Azure.

As orientações a seguir descrevem como configurar um índice em cluster ou sem cluster modificando o esquema de banco de dados diretamente:

- [Criando e modificando restrições de CHAVE PRIMÁRIA][]
- [Criar índices não clusterizados][]
- [Criar índices clusterizados][]
- [Criar índices exclusivos][]

#### Encontrar os principais índices N ausentes 
Você pode escrever consultas SQL em exibições de gerenciamento dinâmico que lhe fornecem informações mais detalhadas sobre o uso de recursos de consultas individuais ou heurística sobre quais índices adicionar. A consulta a seguir determina quais os 10 índices ausentes que gerariam o aperfeiçoamento cumulativo antecipado mais alto, em ordem decrescente, para consultas de usuário.

    SELECT TOP 10 *
    FROM sys.dm_db_missing_index_group_stats
    ORDER BY avg_total_user_cost * avg_user_impact * (user_seeks + user_scans)
    DESC;

A consulta de exemplo a seguir executa uma junção entre estas tabelas para obter uma lista de colunas que devem fazer parte de cada índice ausente e calcula uma "vantagem de índice" para determinar se o índice fornecido deve ser considerado:

    SELECT * from 
    (
        SELECT 
        (user_seeks+user_scans) * avg_total_user_cost * (avg_user_impact * 0.01) AS index_advantage, migs.*
        FROM sys.dm_db_missing_index_group_stats migs
    ) AS migs_adv,
      sys.dm_db_missing_index_groups mig,
      sys.dm_db_missing_index_details mid
    WHERE
      migs_adv.group_handle = mig.index_group_handle and
      mig.index_handle = mid.index_handle
      AND migs_adv.index_advantage > 10
    ORDER BY migs_adv.index_advantage DESC;

Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico][] e [Exibições de gerenciamento dinâmico de índice ausente](sys-missing-index-stats).

<a name="AdvancedQuery" />
### Design de consulta avançada 

Frequentemente, é difícil diagnosticar quais consultas são as mais dispendiosas para o banco de dados.

#### Localizando as principais consultas N

O exemplo a seguir retorna informações sobre as cinco principais consultas classificadas pelo tempo médio de CPU. Este exemplo agrega as consultas de acordo com sua hash de consulta, para que as consultas logicamente equivalentes sejam agrupadas em função de seu consumo de recursos cumulativos.

	SELECT TOP 5 query_stats.query_hash AS "Query Hash", 
	    SUM(query_stats.total_worker_time) / SUM(query_stats.execution_count) AS "Avg CPU Time",
	    MIN(query_stats.statement_text) AS "Statement Text"
	FROM 
	    (SELECT QS.*, 
	    SUBSTRING(ST.text, (QS.statement_start_offset/2) + 1,
	    ((CASE statement_end_offset 
	        WHEN -1 THEN DATALENGTH(st.text)
	        ELSE QS.statement_end_offset END 
	            - QS.statement_start_offset)/2) + 1) AS statement_text
	     FROM sys.dm_exec_query_stats AS QS
	     CROSS APPLY sys.dm_exec_sql_text(QS.sql_handle) as ST) as query_stats
	GROUP BY query_stats.query_hash
	ORDER BY 2 DESC;

Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)][]. Além de executar a consulta, o **Portal de Gerenciamento do Banco de Dados SQL** fornece um bom atalho para ver esses dados, ao selecionar **Resumo** para seu banco de dados e, em seguida, **Desempenho da Consulta**:

![Portal de Gerenciamento do Banco de Dados SQL – desempenho da consulta][PortalSqlManagementQueryPerformance]

#### Analisando o plano de consulta

Quando tiver identificado as consultas dispendiosas ou se estiver prestes a implementar o código usando novas consultas e desejar investigar seu desempenho, a ferramenta oferece um ótimo suporte para análise do **plano de consulta**. O plano de consulta permite a você ver quais operações assumem a compilação do tempo de CPU e os recursos de ES quando uma determinada consulta SQL é executada. Para analisar o plano de consulta no **SQL Server Management Studio**, use os botões da barra de ferramentas em destaque.

![SQL Server Management Studio – plano de consulta][SSMSQueryPlan]

Para analisar o plano de consulta no **Portal de Gerenciamento do Banco de Dados SQL**, use os botões da barra de ferramentas em destaque.

![Portal de Gerenciamento do Banco de Dados SQL – plano de consulta][PortalSqlManagementQueryPlan]

## Consulte também

- [Documentação do Banco de dados SQL do Azure][]
- [Desempenho e escalonamento do Banco de Dados SQL do Azure][]
- [Solucionando problemas do Banco de Dados SQL do Azure][]

### Indexação

- [Fundamentos de índice][]
- [Orientações de design de índices gerais][]
- [Orientações de design de índices exclusivos][]
- [Orientações de design de índices clusterizados][]
- [Restrições de chave primária e estrangeira][]
- [Quanto custa essa chave?][]

### Entity Framework
- [Considerações de desempenho do Entity Framework 5][]
- [Anotações de dados do primeiro código][]

<!-- IMAGES -->
[SSMS]: ./media/mobile-services-sql-scale-guidance/1.png
[PortalSqlManagement]: ./media/mobile-services-sql-scale-guidance/2.png
[PortalSqlMetrics]: ./media/mobile-services-sql-scale-guidance/3.png
[PortalSqlScale]: ./media/mobile-services-sql-scale-guidance/4.png
[PortalSqlAddAlert]: ./media/mobile-services-sql-scale-guidance/5.png
[PortalSqlAddAlert2]: ./media/mobile-services-sql-scale-guidance/6.png
[PortalSqlAddAlert3]: ./media/mobile-services-sql-scale-guidance/7.png
[SetIndexJavaScriptPortal]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
[SSMSDMVs]: ./media/mobile-services-sql-scale-guidance/8.png
[PortalSqlManagementNewQuery]: ./media/mobile-services-sql-scale-guidance/9.png
[PortalSqlManagementRunQuery]: ./media/mobile-services-sql-scale-guidance/10.png
[PortalSqlManagementQueryPerformance]: ./media/mobile-services-sql-scale-guidance/11.png
[SSMSQueryPlan]: ./media/mobile-services-sql-scale-guidance/12.png
[PortalSqlManagementQueryPlan]: ./media/mobile-services-sql-scale-guidance/13.png

<!-- LINKS -->

[Portal de Gerenciamento do Azure]: http://manage.windowsazure.com

[Documentação do Banco de dados SQL do Azure]: http://azure.microsoft.com/documentation/services/sql-database/
[Managing SQL Database using SQL Server Management Studio]: http://go.microsoft.com/fwlink/p/?linkid=309723&clcid=0x409
[Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
[Desempenho e escalonamento do Banco de Dados SQL do Azure]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
[Solucionando problemas do Banco de Dados SQL do Azure]: http://msdn.microsoft.com/library/azure/ee730906.aspx

<!-- MSDN -->
[Criando e modificando restrições de CHAVE PRIMÁRIA]: http://technet.microsoft.com/library/ms181043(v=sql.105).aspx
[Criar índices clusterizados]: http://technet.microsoft.com/library/ms186342(v=sql.120).aspx
[Criar índices exclusivos]: http://technet.microsoft.com/library/ms187019.aspx
[Criar índices não clusterizados]: http://technet.microsoft.com/library/ms189280.aspx

[Primary and Foreign Key Constraints]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Restrições de chave primária e estrangeira]: http://msdn.microsoft.com/library/ms179610(v=sql.120).aspx
[Fundamentos de índice]: http://technet.microsoft.com/library/ms190457(v=sql.105).aspx
[Orientações de design de índices gerais]: http://technet.microsoft.com/library/ms191195(v=sql.105).aspx
[Orientações de design de índices exclusivos]: http://technet.microsoft.com/library/ms187019(v=sql.105).aspx
[Orientações de design de índices clusterizados]: http://technet.microsoft.com/library/ms190639(v=sql.105).aspx

[sys-missing-index-stats]: http://technet.microsoft.com/library/ms345421.aspx

<!-- EF -->
[Considerações de desempenho do Entity Framework 5]: http://msdn.microsoft.com/data/hh949853
[Anotações de dados do primeiro código]: http://msdn.microsoft.com/data/jj591583.aspx
[Anotações de índice no Entity Framework]: http://msdn.microsoft.com/data/jj591583.aspx#Index

<!-- BLOG LINKS -->
[Quanto custa essa chave?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/

<!--HONumber=54--> 