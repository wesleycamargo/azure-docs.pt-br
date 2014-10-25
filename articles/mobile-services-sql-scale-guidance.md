<properties linkid="mobile-services-sql-scale-guidance" urlDisplayName="Scale mobile services backed by Azure SQL Database" pageTitle="Scale mobile services backed by Azure SQL Database - Azure Mobile Services" metaKeywords="" description="Learn how to diagnose and fix scalability issues in your mobile services backed by SQL Database" metaCanonical="" services="" documentationCenter="Mobile" title="Scale mobile services backed by Azure SQL Database" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# Escalar serviços móveis suportados pelo Banco de Dados SQL do Azure

Os Serviços Móveis do Azure facilitam a introdução e compilação de um aplicativo que se conecta a um back-end hospedado em nuvem e armazena dados em um banco de dados SQL. À medida que seu aplicativo cresce, escalonar suas instâncias de serviços será tão simples quanto ajustar as configurações de escala no portal para adicionar mais capacidade computacional e de rede. Entretanto, escalonar o banco de dados SQL que suporta seu serviço exige planejamento e monitoramento proativo, uma vez que o serviço recebe mais carga. Este documento explica um é um conjunto de práticas recomendadas para assegurar um excelente desempenho contínuo de seus serviços móveis suportados por SQL.

Este tópico apresenta e explica as seguintes seções básicas:

1.  [Diagnosticando problemas][Diagnosticando problemas]
2.  [Indexação][Indexação]
3.  [Design de esquema][Design de esquema]
4.  [Design de consulta][Design de consulta]
5.  [Arquitetura de serviço][Arquitetura de serviço]
6.  [Solução de problemas avançada][Solução de problemas avançada]

<a name="Diagnosing"></a>

## Diagnosticando problemas

Se você suspeitar de que seu serviço móvel está com problemas de carga, o primeiro lugar a verificar é a guia **Painel** de seu serviço no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure]. Algumas das coisas a verificar são se:

-   As medidas de uso, incluindo **Chamadas de API** e **Dispositivos Ativos**, não estão acima da cota
-   O status do **Monitoramento de ponto de extremidade** indica que o serviço está apto (somente disponível se o serviço estiver usando a camada padrão e se o Monitoramento de ponto de extremidade estiver habilitado)

Se alguma das opções acima não for verdadeira, considere ajustar suas configurações de escala na guia *Escala*. Se isso não resolver seu problema, é possível continuar e investigar se o Banco de Dados SQL do Azure pode ser a fonte do problema. As próximas seções abrangem abordagens diferentes para diagnosticar o que está dando errado.

### Escolhendo a camada certa do Banco de Dados SQL

É importante entender as diferentes camadas de banco de dados que você tem a sua disposição para garantir que você escolha a camada certa para atender às necessidades de seu aplicativo. O Banco de Dados SQL do Azure oferece duas edições de banco de dados diferentes com camadas diferentes:

-   Edições Web e Business
-   Edição Basic, Standard e Premium (atualmente em visualização)

Embora as edições Web e Business sejam totalmente suportadas, elas serão desativadas até 24 de abril de 2015, conforme abordado em [Perguntas frequentes da desativação das Edições Web e Business][Perguntas frequentes da desativação das Edições Web e Business]. Incentivamos novos clientes a começarem a usar a visualização Basic, Standard e Premium em preparação para essa mudança, caso os requisitos de seus aplicativos permitam isso.

#### Edições Web e Business

Esta é a edição padrão usada pelos Serviços Móveis no momento. Aqui estão algumas recomendações na seleção da camada de seu banco de dados:

-   **Banco de dados com 20 MB gratuitos** – use para fins de desenvolvimento apenas
-   **Web e Business** – use para desenvolvimento e serviços de produção. As duas camadas fornecem o mesmo nível de desempenho, no entanto, a camada Web suporta somente bancos de dados de até 5 GB. Para bancos de dados maiores, use a camada Business.

#### Edições Básica, Padrão e Premium

Esta nova edição fornece uma variedade de novas camadas e recursos de monitoramento que facilitam a compreensão e solução de problemas de desempenho do banco de dados. Para usar essa edição com seu serviço móvel:

1.  Navegue para a página [Recursos de Visualização][Recursos de Visualização] e inscreva-se em **Novas Camadas de Serviço para Bancos de Dados SQL**
2.  Após o recurso de visualização estar ativo, inicie o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
3.  Selecione **+NOVO** na barra de ferramentas e escolha **Serviços de Dados**, **Banco de Dados SQL**, **Criação Rápida**.
4.  Insira um nome de banco de dados e selecione **Novo servidor de Banco de Dados SQL** no campo **Servidor**. Isso criará um servidor que esteja usando as novas Edições Basic, Standard e Premium.
5.  Preencha o restante dos campos e selecione **Criar Banco de Dados SQL**. Isso criará um banco de dados de 100 MB usando uma camada Basic.
6.  Configure seu serviço móvel para usar o banco de dados que você acabou de criar
    -   Se estiver criando um novo serviço móvel, selecione **+NEW**, **Computação**, **Serviço Móvel**, **Criar**. Na tela seguinte, preencha os valores, selecione **Usar um banco de dados SQL existente** no campo **Banco de Dados** e **Avançar**. Na tela seguinte, certifique-se de escolher o banco de dados criado na etapa 5 e selecione **OK**.
    -   Se já tiver um serviço móvel selecionado, navegue até a guia **Configurar** desse serviço e selecione **Alterar Banco de Dados** na barra de ferramentas. Na tela seguinte, selecione **Usar um banco de dados SQL existente** no campo **Banco de Dados SQL** e selecione **Avançar**. Na tela seguinte, certifique-se de escolher o banco de dados criado na etapa 5 e selecione **OK**.

Aqui estão algumas recomendações sobre como selecionar a camada certa para seu banco de dados:

-   **Basic** – use no momento do desenvolvimento ou para pequenos serviços de produção em que você espera somente fazer uma única consulta ao banco de dados de cada vez
-   **Standard** – use para serviços de produção em que você espera várias consultas simultâneas ao banco de dados
-   **Premium** – use para serviços de produção em grande escala com muitas consultas simultâneas, carga em alto pico e baixa latência esperada em toda consulta.

Para obter mais informações sobre quando usar cada camada, consulte [Razões para usar as novas camadas de serviço][Razões para usar as novas camadas de serviço]

### Analisando as métricas do banco de dados

Quando estiver familiarizado com as diferentes camadas do banco de dados, podemos explorar as métricas de desempenho do banco de dados para nos ajudar a entender sobre como escalar dentro e entre as camadas.

1.  Inicie o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Na guia Serviços Móveis, selecione o serviço com o qual deseja trabalhar.
3.  Selecione a guia **Configurar**.
4.  Selecione o nome **Banco de Dados SQL** na seção **Configurações do Banco de Dados**. Com isso, você irá até a guia Banco de Dados SQL do Azure no portal.
5.  Navegue até a guia **Monitorar**
6.  Verifique se as métricas relevantes são exibidas usando o botão **Adicionar Métricas**. Inclua o seguinte
    -   *Porcentagem de CPU* (disponível apenas nas camadas Basic/Standard/Premium)
    -   *Porcentagem de Leituras de Dados Físicos* (disponível apenas nas camadas Basic/Standard/Premium)
    -   *Porcentagem de Gravação de Log* (disponível apenas nas camadas Basic/Standard/Premium)
    -   *Armazenamento*
7.  Inspecione as métricas pela janela de tempo quando seu serviço estiver passando por problemas.

    ![Portal de Gerenciamento do Azure – Métricas do Banco de Dados SQL][Portal de Gerenciamento do Azure – Métricas do Banco de Dados SQL]

Se qualquer métrica exceder os 80% de utilização por um longo período, isso pode indicar um problema de desempenho. Para obter informações mais detalhadas sobre como entender a utilização do banco de dados, consulte [Entendendo o uso dos recursos][Entendendo o uso dos recursos].

Se as métricas indicarem que seu banco de dados está com alta utilização, considere **expandi-lo a uma camada de serviço mais elevada** como uma primeira etapa de redução. Para resolver os problemas imediatamente, considere usar a guia **Escala** de seu banco de dados para expandi-lo. Isso resultará em um aumento na sua conta.
![Portal de Gerenciamento do Azure – Escala do Banco de Dados SQL][Portal de Gerenciamento do Azure – Escala do Banco de Dados SQL]

Assim que possível, considere essas etapas adicionais de redução:

-   **Ajuste seu banco de dados.**
     Você pode reduzir a utilização do banco de dados com frequência e evitar precisar expandi-lo a uma camada mais elevada ao otimizar o seu banco de dados.
-   **Considere sua arquitetura de serviço.**
     Geralmente, sua carga de serviço não é distribuída uniformemente ao logo do tempo, mas contém "picos" de alta demanda. Em vez de expandir o banco de dados para manipular os picos e deixá-lo com pouca utilização durante os períodos de baixa demanda, frequentemente é possível ajustar a arquitetura de serviço para evitar esses picos ou manipulá-los sem o risco de ocorrências de banco de dados.

As seções restantes deste documento contêm uma orientação personalizada para ajudá-lo a implementar essas reduções.

### Configurando alertas

Muitas vezes, é interessante configurar alertas para as principais métricas do banco de dados como uma etapa proativa para garantir que você tenha tempo suficiente para reagir no caso de exaustão de recursos.

1.  Navegue até a guia **Monitoramento** do banco de dados para o qual deseja configurar alertas
2.  Verifique se as métricas relevantes são exibidas conforme descrito na seção anterior
3.  Selecione a métrica para a qual deseja configurar um alerta e selecione **Adicionar Regra**
    ![Portal de Gerenciamento do Azure – Alerta SQL][Portal de Gerenciamento do Azure – Alerta SQL]
4.  Forneça um nome e uma descrição para o alerta
    ![Portal de Gerenciamento do Azure – Nome e Descrição do Alerta SQL][Portal de Gerenciamento do Azure – Nome e Descrição do Alerta SQL]
5.  Especifique o valor a ser usado como o limite do alerta. Considere usar **80%** para permitir algum tempo de reação. Lembre-se também de especificar um endereço de email que possa ser monitorado ativamente por você.
    ![Portal de Gerenciamento do Azure – Limite de Alerta SQL e Email][Portal de Gerenciamento do Azure – Limite de Alerta SQL e Email]

Para obter mais informações sobre como diagnosticar problemas de SQL, consulte [Diagnósticos avançados][Diagnósticos avançados] na parte inferior deste documento.

<a name="Indexing"></a>

## Indexação

Ao começar a perceber problemas de desempenho de consulta, a primeira coisa a investigar é o design de seus índices. Os índices são importantes, pois afetam diretamente o modo como o mecanismo de SQL executa uma consulta. 

Por exemplo, se frequentemente você precisa pesquisar um elemento por um determinado campo, considere adicionar um índice para essa coluna. Caso contrário, o mecanismo de SQL será forçado a executar uma verificação de tabela e ler cada registro físico (ou pelo menos a coluna da consulta) e os registros podem estar substancialmente espalhados no disco.

Portanto, se você realiza as instruções WHERE ou JOIN em colunas específicas com frequência, não se esqueça de indexá-las. Consulte a seção [Criando índices][Criando índices] para obter mais informações.

Se os índices forem muito grandes e a verificação da tabela, ruim, isso significa que você deve indexar cada coluna em sua tabela somente por segurança? A resposta rápida é, "provavelmente não". Os índices ocupam espaço e costumam se sobrecarregar: todas as vezes que algo é inserido em uma tabela, as estruturas de índice para cada uma das colunas indexadas precisam ser atualizadas. Veja abaixo as orientações sobre como escolher seus índices de coluna.

### Orientações de design de índice

Conforme mencionado acima, nem sempre é melhor adicionar mais índices a uma tabela, já que os próprios índices podem ser dispendiosos, tanto em termos de desempenho quanto de sobrecarga de armazenamento.

#### Considerações de consulta

-   Considere adicionar índices nas colunas que sejam frequentemente usadas nos predicados (por exemplo, cláusulas com WHERE) e condições de junção, enquanto faz o balanceamento das considerações do banco de dados abaixo.
-   Grave consultas que inserem ou modificam o maior número de linhas possível em uma única instrução, em vez de usar diversas consultas para atualizar as mesmas linhas. Quando houver apenas uma instrução, o mecanismo do banco de dados pode otimizar melhor a forma como ele mantém os índices.

#### Considerações do banco de dados

Um grande número de índices em uma tabela afeta o desempenho das instruções INSERIR, ATUALIZAR, EXCLUIR e MESCLAR, já que todos os índices devem ser ajustados adequadamente como dados nas alterações da tabela.

-   Para tabelas **intensamente atualizadas**, evite a indexação de colunas intensamente atualizadas.
-   Para tabelas que **não são frequentemente atualizadas**, mas têm grandes volumes de dados, use diversos índices. Isso pode melhorar o desempenho das consultas que não modificam dados (como as instruções SELECIONAR), já que o otimizador de consultas terá mais opções para encontrar o melhor método de acesso.

A indexação de pequenas tabelas pode não ser otimizada, pois o otimizador de consulta leva mais tempo para percorrer a pesquisa do índice para dados em vez de executar uma simples verificação de tabela. Portanto, os índices nunca devem ser usados nas tabelas pequenas, mas devem ser mantidos como dados nas alterações de tabela.

<a name="CreatingIndexes"></a>

### Criando índices

#### Back-end do JavaScript

Para configurar um índice para uma coluna no back-end do JavaScript, faça o seguinte:

1.  Abra seu serviço móvel no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Clique na guia **Dados**.
3.  Selecione a tabela que deseja modificar.
4.  Clique na guia **Colunas**.
5.  Selecione a coluna. Na barra de comandos, clique em **Configurar Índice**:

    ![Portal de Serviços Móveis – Configurar Índice][Portal de Serviços Móveis – Configurar Índice]

Também é possível remover índices dessa exibição.

#### Back-end do .NET

Para definir um índice no Entity Framework, use o atributo `[Index]` nos campos que deseja indexar. Por exemplo:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }

        [Index]
        public bool Complete { get; set; }
    }
         

Para obter mais informações sobre os índices, consulte [Anotações de índice no Entity Framework][Anotações de índice no Entity Framework]. Para obter dicas adicionais sobre como otimizar os índices, consulte [Indexação avançada][Indexação avançada] na parte inferior deste documento.

<a name="Schema"></a>

## Design de esquema

Aqui estão alguns problemas que você deve tomar conhecimento ao escolher os tipos de dados para seus objetos, os quais, por sua vez, convertem-se ao esquema de seu banco de dados SQL. Ajustar o esquema pode, frequentemente, trazer melhorias significativas de desempenho, já que a SQL possui maneiras otimizadas e personalizadas de manipular a indexação e o armazenamento para diferentes tipos de dados:

-   **Use a coluna de ID fornecida**. Todas as tabelas de serviço móvel vêm com uma coluna de ID padrão configurada como a chave primária e tem seu índice configurado nela. Não há necessidade de criar uma coluna de ID adicional.
-   **Use os tipos de dados corretos em seu modelo.** Se você sabe que uma determina propriedade de seu modelo será numérica ou booliana, não deixe de defini-la dessa forma em seu modelo, em vez de defini-la como uma cadeia de caracteres. No back-end do JavaScript, use literais como `true` em vez de `"true"` e `5` de `"5"`. No back-end do .NET, use os tipos `int` e `bool` ao declarar as propriedades de seu modelo. Isso permite que a SQL crie o esquema correto para esses tipos, o que torna a consulta mais eficiente.

<a name="Query"></a>

## Design de consulta

Aqui estão algumas orientações a serem consideradas ao consultar o banco de dados:

-   **Sempre execute operações de junção no banco de dados.** Você precisará combinar os registros de duas ou mais tabelas com frequência, sendo que os registros sendo combinados compartilham um campo comum (também conhecido como uma *junção*). Essa operação pode não ser eficiente se for executada incorretamente, pois envolve mover todas as entidades para baixo de ambas as tabelas e, em seguida, iterar por meio delas. Esse tipo de operação é melhor se deixada para o próprio banco de dados, mas às vezes pode ser facilmente executada de forma errada no cliente ou no código do serviço móvel.
    -   Não execute junções no código de seu aplicativo
    -   Não execute junções no código de seu serviço móvel. Ao usar o back-end do JavaScript, saiba que o [objeto de tabela][objeto de tabela] não manipula junções. Não se esqueça de usar o [objeto mssql][objeto mssql] diretamente para garantir que a junção ocorra no banco de dados. Para obter mais informações, consulte [Juntar tabelas relacionais][Juntar tabelas relacionais]. Se estiver usando o back-end do .NET e consultando com LINQ, as junções serão automaticamente manipuladas no nível do banco de dados pelo Entity Framework.
-   **Implementar paginação.** Às vezes, consultar o banco de dados pode resultar em um grande número de registros retornado ao cliente. Para minimizar o tamanho e a latência das operações, considere implementar a paginação.
    -   Por padrão, seu serviço móvel limitará quaisquer consultas de entrada para uma página de tamanho 50 e você pode solicitar manualmente até 1.000 registros. Para obter mais informações, consulte "Retornar dados em páginas" para [Windows Store][Windows Store], [iOS][iOS], [Android][Android], [HTML/JavaScript][HTML/JavaScript] e [Xamarin][Xamarin].
    -   Não existe um tamanho de página padrão para as consultas feitas de seu código de serviço móvel. Se o seu aplicativo não implementar a paginação, ou como uma medida defensiva, considere aplicar limites padrão em suas consultas. No back-end do JavaScript, use o operador **take** no [objeto de consulta][objeto de consulta]. Se estiver usando o back-end do .NET, considere usar o [método Take][método Take] como parte de sua consulta LINQ.

Para obter mais informações sobre como melhorar o design da consulta, inclusive como analisar os planos de consulta, consulte [Design de consulta avançada][Design de consulta avançada] na parte inferior deste documento.

<a name="Architecture"></a>

## Arquitetura de serviço

Imagine um cenário em que você esteja prestes a enviar uma notificação por push para todos os seus clientes para verificarem um novo conteúdo no aplicativo. Conforme tocam na notificação, o aplicativo é iniciado, o que possivelmente dispara uma chamada para seu serviço móvel e uma execução de consulta no banco de dados SQL. Como milhões de clientes em potencial executam essa ação no período de apenas alguns minutos, isso gerará um aumento de carga de SQL, o que pode ser de uma magnitude superior à carga de estado constante de seu aplicativo. Isso pode ser resolvido ao expandir seu aplicativo para uma camada de SQL mais elevada durante o pico e, em seguida, reduzi-lo novamente, no entanto, essa solução exige uma intervenção manual e gera um custo elevado. Fazer pequenos ajustes em sua arquitetura de serviço móvel com frequência pode equilibrar significativamente a unidade de clientes de carga para seu banco de dados SQL e eliminar picos problemáticos na demanda. Essas modificações podem ser implementadas facilmente com mínimo impacto na experiência de seu cliente. Estes são alguns exemplos:

-   **Espalhar a carga ao longo do tempo.** Se você controlar a duração de determinados eventos (por exemplo, uma notificação transmitida por push), os quais se espera gerar um pico na demanda, e a duração desses eventos não for crítica, considere espalhá-los ao longo do tempo. No exemplo acima, talvez seja aceitável que seus clientes de aplicativo sejam notificados de um novo conteúdo do aplicativo em massa ao longo de um dia em vez de quase simultaneamente. Considere separar os clientes em grupos que permitirão uma entrega escalonada em cada lote. Se estiver usando Hubs de Notificação, aplicar uma marca adicional para acompanhar o lote e entregar uma notificação por push para essa marca é uma maneira fácil de implementar essa estratégia. Para obter mais informações sobre as marcas, consulte [Usar Hubs de Notificação para enviar notícias de última hora][Usar Hubs de Notificação para enviar notícias de última hora].
-   **Use o Armazenamento de Blob e de Tabela quando necessário.** Frequentemente, o conteúdo que os clientes verão durante o pico é razoavelmente estático e não precisa ser armazenado em um banco de dados SQL, já que seja pouco provável que você precise de capacidades de consulta relacionais nesse conteúdo. Nesse caso, considere armazenar o conteúdo no Armazenamento de Blob ou de Tabela. É possível acessar os blobs públicos diretamente no Armazenamento de Blob do dispositivo. Para acessar blobs de uma forma segura ou usar o Armazenamento de Tabela, será necessário passar pela API Personalizada dos Serviços Móveis para proteger sua chave de acesso do armazenamento. Para obter mais informações, consulte [Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis][Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis].
-   **Usar um cache em memória**. Outra alternativa é armazenar dados, os quais poderiam ser normalmente acessados durante um pico de tráfego, em um cache em memória como o [Cache do Azure][Cache do Azure]. Isso significa que as solicitações de entrada poderiam buscar as informações necessárias na memória, em vez de consultar repetidamente o banco de dados.

<a name="Advanced"></a>

## Solução de problemas avançada

Essa seção abrange algumas das tarefas de diagnóstico mais avançadas, as quais podem ser úteis se as etapas anteriores não tratarem completamente do problema.

### Pré-requisitos

Para executar algumas das tarefas de diagnóstico nesta seção, você precisa acessar uma ferramenta de gerenciamento de bancos de dados SQL como o **SQL Server Management Studio** ou a funcionalidade interna de gerenciamento do **Portal de Gerenciamento do Azure**.

O SQL Server Management Studio é um aplicativo gratuito do Windows que oferece os recursos mais avançados. Se você não tiver acesso a um computador Windows (por exemplo, se você estiver usando um Mac), considere provisionar uma Máquina Virtual no Azure, conforme mostrado em [Criar uma máquina virtual executando o Windows Server][Criar uma máquina virtual executando o Windows Server] e, em seguida, conecte-se remotamente a ela. Se você pretender usar a MV com a finalidade principal de executar o SQL Server Management Studio, uma instância **A0 Básica** (anteriormente chamada de "Extra Pequena") deve ser suficiente.

O Portal de Gerenciamento do Azure oferece uma experiência de gerenciamento interna que é mais limitada, mas está disponível sem a necessidade de uma instalação local.

As etapas a seguir explicam como obter informações de conexão para o banco de dados SQL que suporta seu serviço móvel para que, em seguida, você use qualquer uma das duas ferramentas para se conectar a ele. Você pode escolher a ferramenta que preferir.

#### Obter informações de conexão SQL

1.  Inicie o [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure].
2.  Na guia Serviços Móveis, selecione o serviço com o qual deseja trabalhar.
3.  Selecione a guia **Configurar**.
4.  Selecione o nome **Banco de Dados SQL** na seção **Configurações do Banco de Dados**. Com isso, você irá até a guia Banco de Dados SQL do Azure no portal.
5.  Selecione **Configurar as regras de firewall do Microsoft Azure para esse endereço IP**.
6.  Anote o endereço do servidor na seção **Conectar ao seu banco de dados**, por exemplo: *mcml4otbb9.database.windows.net*.

#### SQL Server Management Studio

1.  Navegue até [Edições do SQL Server – Express][Edições do SQL Server – Express]
2.  Localize a seção **SQL Server Management Studio** e selecione o botão **Baixar** abaixo.
3.  Conclua as etapas de configuração até que possa executar com êxito o aplicativo:

    ![SQL Server Management Studio][SQL Server Management Studio]

4.  Na caixa de diálogo **Conectar ao Servidor**, insira os seguintes valores

    -   Nome do servidor: *endereço do servidor obtido anteriormente*
    -   Autenticação: *Autenticação do SQL Server*
    -   Logon: *logon escolhido ao criar o servidor*
    -   Senha: *senha escolhida ao criar o servidor*

5.  Agora você deve estar conectado.

#### Portal de Gerenciamento do Banco de Dados SQL

1.  Na guia do Banco de Dados SQL do Azure de seu banco de dados, selecione o botão **Gerenciar**
2.  Configure a conexão com os seguintes valores
    -   Servidor: *deve ser predefinido com o valor correto*
    -   Banco de dados: *deixe em branco*
    -   Nome de usuário: *logon escolhido ao criar o servidor*
    -   Senha: *senha escolhida ao criar o servidor*

3.  Agora você deve estar conectado.

    ![Portal de Gerenciamento do Azure – Banco de Dados SQL][Portal de Gerenciamento do Azure – Banco de Dados SQL]

<a name="AdvancedDiagnosing"></a>

### Diagnósticos avançados

Várias tarefas de diagnóstico podem ser facilmente concluídas da maneira correta no **Portal de Gerenciamento do Azure**, no entanto, algumas tarefas avançadas de diagnóstico somente são possíveis no **SSQL Server Management Studio** ou no **Portal de Gerenciamento do Banco de Dados SQL**. Aproveitaremos as exibições de gerenciamento dinâmico, um conjunto de exibições preenchidas automaticamente com informações de diagnóstico sobre seu banco de dados. Esta seção fornece um conjunto de consultas que pode ser executado nessas exibições para examinar diversas métricas. Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)][Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)].

Após concluir as etapas na seção anterior para se conectar ao banco de dados no SQL Server Management Studio, selecione seu banco de dados no **Pesquisador de Objetos**. Expanda as **Exibições** e as **Exibições do Sistema** mostrarão uma lista de exibições de gerenciamento. Para executar as consultas abaixo, selecione **Nova Consulta**, enquanto estiver com seu banco de dados selecionado no **Pesquisador de Objetos**, cole a consulta e selecione **Executar**.

![SQL Server Management Studio – exibições de gerenciamento dinâmicas][SQL Server Management Studio – exibições de gerenciamento dinâmicas]

Como alternativa, se estiver usando o Portal de Gerenciamento do Banco de Dados SQL, primeiro selecione seu banco de dados e escolha **Nova Consulta**.

![Portal de Gerenciamento do Banco de Dados SQL – nova consulta][Portal de Gerenciamento do Banco de Dados SQL – nova consulta]

Para executar qualquer uma das consultas abaixo, cole-as na janela e selecione **Executar**.

![Portal de Gerenciamento do Banco de Dados SQL – executar consulta][Portal de Gerenciamento do Banco de Dados SQL – executar consulta]

#### Métricas avançadas

O portal de gerenciamento disponibiliza determinadas métricas para leitura se você estiver usando as camadas Basic, Standard e Premium. No entanto, se estiver usando as camadas Web e Business, apenas a métrica Armazenamento estará disponível no portal. Felizmente, é fácil obter essas e outras métricas usando a exibição de gerenciamento **[sys.resource\_stats][sys.resource\_stats]**, independentemente de qual camada você está usando. Considere a seguinte consulta:

    SELECT TOP 10 * 
    FROM sys.resource_stats 
    WHERE database_name = 'todoitem_db' 
    ORDER BY start_time DESC

> [WACOM.NOTE]
> Execute essa consulta no banco de dados **principal** de seu servidor, uma vez que a exibição **sys.resource\_stats** somente está presente nesse banco de dados.

O resultado conterá as seguintes métricas úteis: CPU (% de limite de camada), Armazenamento (megabytes), Leituras de Dados Físicos (% de limite de camada), Gravações de Log (% de limite de camada), Memória (% de limite de camada), Contagem de Trabalhador, Contagem de Sessão, etc.

#### Eventos de conectividade de SQL

A exibição **[sys.event\_log][sys.event\_log]** contém os detalhes dos eventos relacionados à conectividade.

    select * from sys.event_log 
    where database_name = 'todoitem_db'
    and event_type like 'throttling%'
    order by start_time desc

> [WACOM.NOTE]
> Execute essa consulta no banco de dados **principal** de seu servidor, uma vez que a exibição **sys.event\_log** somente está presente nesse banco de dados.

<a name="AdvancedIndexing"></a>

### Indexação avançada

Uma tabela ou exibição pode conter os seguintes tipos de índices:

-   **Clusterizado**. Um índice clusterizado especifica como os registros são fisicamente armazenados em disco. Deve haver apenas um índice clusterizado por tabela, uma vez que as próprias linhas de dados podem ser classificadas em apenas uma ordem.

-   **Não clusterizado**. Índices não clusterizados são armazenados em separado das linhas de dados e são usados para realizar uma pesquisa baseada no valor do índice. Todos os índices não clusterizados em uma tabela usam valores-chave do índice clusterizado como chave de pesquisa.

Para fornecer uma analogia de mundo real: pense em um livro ou manual técnico. O conteúdo de cada página é um registro, o número da página é o índice clusterizado e o índice de tópicos na parte final do livro é um índice não clusterizado. Cada entrada no índice de tópicos aponta para o índice clusterizado, o número da página.

> [WACOM.NOTE]
> Por padrão, o back-end do JavaScript dos Serviços Móveis do Azure configura **\_createdAt** como o índice clusterizado. Se você remover essa coluna ou se desejar um índice clusterizado diferente, procure seguir as [orientações de design de índice clusterizado][orientações de design de índice clusterizado] abaixo. No back-end do .NET, a classe `EntityData` define `CreatedAt` como um índice clusterizado usando a anotação `[Index(IsClustered = true)]`.

<a name="ClusteredIndexes"></a>

#### Orientações de design de índices clusterizados

Todas as tabelas devem possuir um índice clusterizado na coluna (ou nas colunas, no caso de uma chave composta) com as seguintes propriedades:

-   Estreita – usa um pequeno tipo de banco de dados ou é uma [chave composta][chave composta] de um pequeno número de colunas estreitas
-   Exclusiva ou em grande parte exclusiva
-   Estática – o valor não é alterado com frequência
-   Em crescimento constante
-   (Opcional) Largura fixa
-   (Opcional) Não nula

O motivo pelo qual existe a propriedade **estreita** é que todos os outros índices em uma tabela usam os valores-chave do índice clusterizado como chaves de pesquisa. No exemplo de um índice de tópico na parte posterior de um livro, o índice clusterizado é um número de página e é um número pequeno. Se o título do capítulo estiver incluído no índice clusterizado, o índice de tópicos seria por si só muito mais longo, já que o valor-chave também o seria (nome do capítulo, número de página).

A chave deve ser **estática** e **em crescimento constante** para evitar a necessidade de manter o local físico dos registros (o que significa mover os registros fisicamente ou possivelmente fragmentar o armazenamento ao dividir as páginas no local onde os registros são armazenados).

O índice clusterizado será mais valioso para consultas que realizam o seguinte:

-   Retornam um intervalo de valores usando operadores como ENTRE, \>, \>=, \< e \<=.
    -   Após a linha com o primeiro valor ser localizada usando o índice clusterizado, as linhas com os valores clusterizados subsequentes são garantidas como sendo fisicamente adjacentes.
-   Use as cláusulas JUNTAR; normalmente existem colunas de chave estrangeira.
-   Use as cláusulas CLASSIFICAR POR ou AGRUPAR POR.
    -   Um índice nas colunas especificadas na cláusula CLASSIFICAR POR ou AGRUPAR POR pode remover a necessidade do Mecanismo de Banco de Dados de classificar os dados, uma vez que as linhas já estão classificadas. Isso melhora o desempenho da consulta.

#### Criando índices clusterizados no Entity Framework

Para configurar o índice clusterizado no back-end do .NET usando Entity Framework, configure a propriedade `IsClustered` da anotação. Por exemplo, essa é a definição de `CreatedAt` no `Microsoft.WindowsAzure.Mobile.Service.EntityData`:

    [Index(IsClustered = true)]
    [DatabaseGenerated(DatabaseGeneratedOption.Identity)]
    [TableColumnAttribute(TableColumnType.CreatedAt)]
    public DateTimeOffset? CreatedAt { get; set; }

#### Criando índices no esquema do banco de dados

Para o back-end do JavaScript, é possível apenas modificar o índice clusterizado de uma tabela alterando o esquema do banco de dados diretamente, ser no SQL Server Management Studio ou no Portal de Banco de Dados SQL do Azure.

As orientações a seguir descrevem como configurar um índice em cluster ou sem cluster modificando o esquema de banco de dados diretamente:

-   [Criando e modificando restrições de CHAVE PRIMÁRIA][Criando e modificando restrições de CHAVE PRIMÁRIA]
-   [Criar índices não clusterizados][Criar índices não clusterizados]
-   [Criar índices clusterizados][Criar índices clusterizados]
-   [Criar índices exclusivos][Criar índices exclusivos]

#### Encontrar os principais índices N ausentes

É possível gravar as consultas de SQL em exibições de gerenciamento dinâmico que lhe fornecerá informações mais detalhadas sobre o uso dos recursos das consultas individuais ou que lhe fornecerá a heurística sobre quais índices adicionar. A consulta a seguir determina quais 10 índices ausentes produziriam a mais alta melhoria cumulativa antecipada, em ordem decrescente, para consultas de usuários.

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

Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico][Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)] e [Exibições de gerenciamento dinâmico de índice ausente][Exibições de gerenciamento dinâmico de índice ausente].

<a name="AdvancedQuery"></a>

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

Para obter mais informações, consulte [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)][Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)]. Além de executar a consulta, o **Portal de Gerenciamento do Banco de Dados SQL** fornece um bom atalho para ver esses dados, ao selecionar **Resumo** para seu banco de dados e, em seguida, **Desempenho da Consulta**:

![Portal de Gerenciamento do Banco de Dados SQL – desempenho da consulta][Portal de Gerenciamento do Banco de Dados SQL – desempenho da consulta]

#### Analisando o plano de consulta

Quando tiver identificado as consultas dispendiosas ou se estiver prestes a implementar o código usando novas consultas e desejar investigar seu desempenho, a ferramenta oferece um ótimo suporte para análise do **plano de consulta**. O plano de consulta permite a você ver quais operações assumem a compilação do tempo de CPU e os recursos de ES quando uma determinada consulta SQL é executada. Para analisar o plano de consulta no **SQL Server Management Studio**, use os botões da barra de ferramentas em destaque.

![SQL Server Management Studio – plano de consulta][SQL Server Management Studio – plano de consulta]

Para analisar o plano de consulta no **Portal de Gerenciamento do Banco de Dados SQL**, use os botões da barra de ferramentas em destaque.

![Portal de Gerenciamento do Banco de Dados SQL – plano de consulta][Portal de Gerenciamento do Banco de Dados SQL – plano de consulta]

## Consulte também

-   [Documentação do Banco de dados SQL do Azure][Documentação do Banco de dados SQL do Azure]
-   [Desempenho e escalonamento do Banco de Dados SQL do Azure][Desempenho e escalonamento do Banco de Dados SQL do Azure]
-   [Solucionando problemas do Banco de Dados SQL do Azure][Solucionando problemas do Banco de Dados SQL do Azure]

### Indexação

-   [Fundamentos de índice][Fundamentos de índice]
-   [Orientações de design de índices gerais][Orientações de design de índices gerais]
-   [Orientações de design de índices exclusivos][Orientações de design de índices exclusivos]
-   [Orientações de design de índices clusterizados][Orientações de design de índices clusterizados]
-   [Restrições de chave primária e estrangeira][chave composta]
-   [Quanto essa chave custa?][Quanto essa chave custa?]

### Entity Framework

-   [Considerações de desempenho do Entity Framework 5][Considerações de desempenho do Entity Framework 5]
-   [Anotações de dados do primeiro código][Anotações de dados do primeiro código]

  [Diagnosticando problemas]: #Diagnosing
  [Indexação]: #Indexing
  [Design de esquema]: #Schema
  [Design de consulta]: #Query
  [Arquitetura de serviço]: #Architecture
  [Solução de problemas avançada]: #Advanced
  [Portal de Gerenciamento do Azure]: http://manage.windowsazure.com
  [Perguntas frequentes da desativação das Edições Web e Business]: http://msdn.microsoft.com/en-US/library/azure/dn741330.aspx
  [Recursos de Visualização]: https://account.windowsazure.com/previewfeatures
  [Razões para usar as novas camadas de serviço]: http://msdn.microsoft.com/en-US/library/azure/dn369873.aspx#Reasons
  [Portal de Gerenciamento do Azure – Métricas do Banco de Dados SQL]: ./media/mobile-services-sql-scale-guidance/3.png
  [Entendendo o uso dos recursos]: http://msdn.microsoft.com/en-US/library/azure/dn369873.aspx#Resource
  [Portal de Gerenciamento do Azure – Escala do Banco de Dados SQL]: ./media/mobile-services-sql-scale-guidance/4.png
  [Portal de Gerenciamento do Azure – Alerta SQL]: ./media/mobile-services-sql-scale-guidance/5.png
  [Portal de Gerenciamento do Azure – Nome e Descrição do Alerta SQL]: ./media/mobile-services-sql-scale-guidance/6.png
  [Portal de Gerenciamento do Azure – Limite de Alerta SQL e Email]: ./media/mobile-services-sql-scale-guidance/7.png
  [Diagnósticos avançados]: #AdvancedDiagnosing
  [Criando índices]: #CreatingIndexes
  [Portal de Serviços Móveis – Configurar Índice]: ./media/mobile-services-sql-scale-guidance/set-index-portal-ui.png
  [Anotações de índice no Entity Framework]: http://msdn.microsoft.com/en-us/data/jj591583.aspx#Index
  [Indexação avançada]: #AdvancedIndexing
  [objeto de tabela]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554210.aspx
  [objeto mssql]: http://msdn.microsoft.com/en-us/library/windowsazure/jj554212.aspx
  [Juntar tabelas relacionais]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-how-to-use-server-scripts/#joins
  [Windows Store]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/#paging
  [iOS]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-ios-how-to-use-client-library/#paging
  [Android]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-android-how-to-use-client-library/#paging
  [HTML/JavaScript]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-html-how-to-use-client-library/#paging
  [Xamarin]: http://azure.microsoft.com/pt-br/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/#paging
  [objeto de consulta]: http://msdn.microsoft.com/en-us/library/azure/jj613353.aspx
  [método Take]: http://msdn.microsoft.com/en-us/library/vstudio/bb503062(v=vs.110).aspx
  [Design de consulta avançada]: #AdvancedQuery
  [Usar Hubs de Notificação para enviar notícias de última hora]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Fazer upload de imagens no Armazenamento do Azure usando Serviços Móveis]: http://azure.microsoft.com/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/
  [Cache do Azure]: http://azure.microsoft.com/en-us/services/cache/
  [Criar uma máquina virtual executando o Windows Server]: http://azure.microsoft.com/pt-br/documentation/articles/virtual-machines-windows-tutorial/
  [Edições do SQL Server – Express]: http://www.microsoft.com/en-us/server-cloud/products/sql-server-editions/sql-server-express.aspx
  [SQL Server Management Studio]: ./media/mobile-services-sql-scale-guidance/1.png
  [Portal de Gerenciamento do Azure – Banco de Dados SQL]: ./media/mobile-services-sql-scale-guidance/2.png
  [Monitorando o Banco de Dados SQL usando a Exibição de Gerenciamento Dinâmico (a página pode estar em inglês)]: http://go.microsoft.com/fwlink/p/?linkid=309725&clcid=0x409
  [SQL Server Management Studio – exibições de gerenciamento dinâmicas]: ./media/mobile-services-sql-scale-guidance/8.png
  [Portal de Gerenciamento do Banco de Dados SQL – nova consulta]: ./media/mobile-services-sql-scale-guidance/9.png
  [Portal de Gerenciamento do Banco de Dados SQL – executar consulta]: ./media/mobile-services-sql-scale-guidance/10.png
  [sys.resource\_stats]: http://msdn.microsoft.com/en-us/library/dn269979.aspx
  [sys.event\_log]: http://msdn.microsoft.com/en-us/library/azure/jj819229.aspx
  [orientações de design de índice clusterizado]: #ClusteredIndexes
  [chave composta]: http://msdn.microsoft.com/en-us/library/ms179610(v=sql.120).aspx
  [Criando e modificando restrições de CHAVE PRIMÁRIA]: http://technet.microsoft.com/en-us/library/ms181043(v=sql.105).aspx
  [Criar índices não clusterizados]: http://technet.microsoft.com/en-us/library/ms189280.aspx
  [Criar índices clusterizados]: http://technet.microsoft.com/en-us/library/ms186342(v=sql.120).aspx
  [Criar índices exclusivos]: http://technet.microsoft.com/en-us/library/ms187019.aspx
  [Exibições de gerenciamento dinâmico de índice ausente]: sys-missing-index-stats
  [Portal de Gerenciamento do Banco de Dados SQL – desempenho da consulta]: ./media/mobile-services-sql-scale-guidance/11.png
  [SQL Server Management Studio – plano de consulta]: ./media/mobile-services-sql-scale-guidance/12.png
  [Portal de Gerenciamento do Banco de Dados SQL – plano de consulta]: ./media/mobile-services-sql-scale-guidance/13.png
  [Documentação do Banco de dados SQL do Azure]: http://azure.microsoft.com/pt-br/documentation/services/sql-database/
  [Desempenho e escalonamento do Banco de Dados SQL do Azure]: http://go.microsoft.com/fwlink/p/?linkid=397217&clcid=0x409
  [Solucionando problemas do Banco de Dados SQL do Azure]: http://msdn.microsoft.com/en-us/library/azure/ee730906.aspx
  [Fundamentos de índice]: http://technet.microsoft.com/en-us/library/ms190457(v=sql.105).aspx
  [Orientações de design de índices gerais]: http://technet.microsoft.com/en-us/library/ms191195(v=sql.105).aspx
  [Orientações de design de índices exclusivos]: http://technet.microsoft.com/en-us/library/ms187019(v=sql.105).aspx
  [Orientações de design de índices clusterizados]: http://technet.microsoft.com/en-us/library/ms190639(v=sql.105).aspx
  [Quanto essa chave custa?]: http://www.sqlskills.com/blogs/kimberly/how-much-does-that-key-cost-plus-sp_helpindex9/
  [Considerações de desempenho do Entity Framework 5]: http://msdn.microsoft.com/en-us/data/hh949853
  [Anotações de dados do primeiro código]: http://msdn.microsoft.com/en-us/data/jj591583.aspx
