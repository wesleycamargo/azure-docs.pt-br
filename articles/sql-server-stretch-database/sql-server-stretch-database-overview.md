<properties
	pageTitle="Visão geral do Stretch Database | Microsoft Azure"
	description="Saiba como o Stretch Database migra seus dados históricos de forma transparente e segura para a nuvem do Microsoft Azure."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Visão geral do Stretch Database

O Stretch Database migra seus dados históricos de forma transparente e segura para a nuvem do Microsoft Azure.

Se você deseja começar com o Stretch Database imediatamente, consulte [Introdução executando Habilitar Banco de Dados para o assistente Stretch](sql-server-stretch-database-wizard.md).

## Quais são os benefícios do Stretch Database?
O Stretch Database oferece os seguintes benefícios:

**Fornece disponibilidade econômica para dados frios** O Stretch torna os dados frios e ativos de forma dinâmica do SQL Server para o Microsoft Azure usando o SQL Server Stretch Database. Ao contrário do armazenamento comum de dados frio, seus dados estão sempre online e disponíveis para consulta. Você pode fornecer uma retenção de dados mais longa, sem ir à falência, para tabelas grandes como o Histórico de Pedidos do Cliente. Beneficie-se do baixo custo do Azure em vez de expandir um armazenamento local caro. Escolha o tipo de preço e defina as configurações no Portal do Azure para manter controle sobre a velocidade de acesso aos dados e o preço. Escale vertical ou horizontalmente conforme o necessário. Visite a página [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) para obter detalhes.

**Não exige alterações em aplicativos ou consultas** Acesse os dados do SQL Server diretamente, independentemente de serem locais ou alongados até a nuvem. Defina a política que determina onde os dados são armazenados, e o SQL Server tratará da movimentação dos dados em segundo plano. A tabela inteira está sempre online e passível de consulta. Além disso, o Stretch Database não exige mudanças nos aplicativos ou consultas existentes. O local dos dados é completamente transparente ao aplicativo.

**Simplifica a manutenção de dados locais** Reduza a manutenção e o armazenamento local de seus dados. Os backups para a parte de seus dados que está na nuvem são executados automaticamente. Os backups de seus dados locais são executados e concluídos com mais rapidez dentro da janela de manutenção. Suas necessidades de armazenamento local reduzem bastante. O Armazenamento do Azure pode ser 80% mais barato do que adicionar SSD local.

**Mantém seus dados seguros mesmo durante a migração** Aproveite a tranquilidade enquanto amplia, com segurança, os seus aplicativos mais importantes para a nuvem. Sempre Criptografado do SQL Server fornece criptografia para seus dados em movimento. RLS (Segurança de Nível de Linha) e outros recursos de segurança avançados do SQL Server também funcionam com o Stretch Database para proteger seus dados.

## Qual é a função do Stretch Database?
Depois que você habilita o Stretch Database para uma instância, um banco de dados e pelo menos uma tabela do SQL Server, ele começa a migrar silenciosamente os dados históricos para o Azure.

-   Se você armazenar dados históricos em uma tabela separada, poderá migrar toda a tabela.

-   Se a sua tabela contiver dados históricos e atuais, você poderá especificar um predicado de filtro para selecionar as linhas para migração.

O Stretch Database assegura que nenhum dado seja perdido se ocorrer uma falha durante a migração. Ele também tem uma lógica de repetição para lidar com problemas de conexão que podem ocorrer durante a migração. Um modo de exibição de gerenciamento dinâmico fornece o status da migração.

Você pode pausar a migração de dados para solucionar problemas no servidor local ou para maximizar a largura de banda de rede disponível.

Você não precisa alterar as consultas e aplicativos de cliente existentes. Você continua a ter um acesso contínuo aos dados locais e remotos, mesmo durante a migração dos dados. Há uma pequena quantidade de latência para consultas remotas, mas você enfrentará essa latência apenas ao consultar dados históricos.

![Visão geral do Stretch Database][StretchOverviewImage1]

## O Stretch Database serve para você?
Se você puder fazer as seguintes declarações, o Stretch Database poderá ajudar a atender às suas necessidades e a resolver seus problemas.

|Se você for um tomador de decisões|Se você for um DBA|
|------------------------------|-------------------|
|Preciso manter dados transacionais por um longo período.|O tamanho das minhas tabelas está saindo do controle.|
|Às vezes, eu preciso consultar os dados históricos.|Meus usuários dizem que querem acesso aos dados históricos, mas raramente os usam.|
|Tenho aplicativos, incluindo aplicativos mais antigos, que não quero atualizar.|Preciso comprar e adicionar mais armazenamento frequentemente.|
|Quero encontrar um jeito de economizar dinheiro com armazenamento.|Não consigo fazer backup ou restaurar essas tabelas grandes no SLA.|

## Quais tipos de bancos de dados e tabelas são candidatos ao Stretch Database?
O Stretch Database destina-se a bancos de dados transacionais com grandes quantidades de dados históricos, geralmente armazenados em uma pequena quantidade de tabelas. Essas tabelas podem conter mais de um bilhão de linhas.

Se você usar o recurso de tabela temporal do SQL Server 2016, use o Stretch Database para migrar toda ou parte da tabela de histórico associada para o armazenamento econômico no Azure. Para obter mais informações, confira [Gerenciar a retenção de dados históricos em Tabelas temporais com versão do sistema](https://msdn.microsoft.com/library/mt637341.aspx).

Use o Supervisor do Stretch Database, um recurso do Supervisor de Atualização do SQL Server 2016, para identificar os bancos de dados e tabelas candidatos ao Stretch Database. Para obter mais informações, confira [Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md). Para saber mais sobre os possíveis problemas de bloqueio, confira [Limitações de área de superfície e problemas de bloqueio do Stretch Database.](sql-server-stretch-database-limitations.md)

## <a name="FAQ"></a>Perguntas frequentes sobre o Stretch Database
**O Stretch Database funciona com &lt;Nome de recurso do SQL Server&gt;?**
-   Para obter uma lista dos recursos do SQL Server que desqualificam uma tabela para o Stretch, confira [Limitações de área de superfície e problemas de bloqueio do Stretch Database](sql-server-stretch-database-limitations.md).

-   Como opção, baixe o Supervisor de Atualização do SQL Server 2016 e execute o Supervisor do Stretch Database para identificar os bancos de dados e tabelas candidatos ao Stretch Database. Para obter mais informações, confira [Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md).

**Posso direcionar outra instância local do SQL Server para o Stretch Database?** Não. O Stretch Database não oferece suporte a outra instância local do SQL Server como o ponto de extremidade remoto.

**Posso desabilitar o Stretch e mover os dados migrados de volta para a tabela local?** Sim. Para obter mais informações, confira [Desabilitar o Stretch Database e retornar os dados remotos](sql-server-stretch-database-disable.md).

## Termos
**Banco de dados local**. O banco de dados local do SQL Server.

**Ponto de extremidade remoto**. O local no Microsoft Azure que contém dados remotos do banco de dados.

**Dados locais**. Os dados em um banco de dados com o Stretch Database habilitado não serão movidos para o Azure com base na configuração do Stretch Database das tabelas no banco de dados.

**Dados qualificados**. Os dados em um banco de dados com o Stretch Database habilitado e que ainda não foram movidos, mas que serão movidos para o Azure com base na configuração do Stretch Database das tabelas no banco de dados.

**Dados remotos**. Os dados em um banco de dados com o Stretch Database habilitado que já foram movidos para o Azure.

## Arquitetura
O Stretch Database aproveita os recursos do Microsoft Azure para descarregar o armazenamento de dados de arquivamento e o processamento de consultas.

Quando você habilita o Stretch Database em um banco de dados, ele cria uma definição servidor vinculado segura no SQL Server local. Essa definição de servidor vinculado tem o ponto de extremidade remoto como destino. Quando você habilita o Stretch Database em uma tabela no banco de dados, ele provisiona recursos remotos e começa a migrar dados qualificados, se a migração estiver habilitada.

As consultas realizadas em tabelas com o Stretch Database habilitado são automaticamente realizadas no banco de dados local e no ponto de extremidade remoto. O Stretch Database aproveita a capacidade de processamento do Azure para executar consultas em dados remotos reescrevendo a consulta. Você pode ver essa reconfiguração como um operador "remote query" no novo plano de consulta.

![Arquitetura do Stretch Database][StretchOverviewImage2]

## Segurança e permissões

### Como habilitar e desabilitar o Stretch Database para uma instância do SQL Server
Para começar a configuração dos bancos de dados para o Stretch Database, primeiro você deve alterar a opção de configuração no nível da instância "remote data archive" usando sp\_configure. Esta operação requer os privilégios sysadmin ou serveradmin. Com essa opção habilitada, você pode configurar os bancos de dados para o Stretch Database, migrar os dados e consultar os dados no ponto de extremidade remoto.

### Como habilitar e desabilitar o Stretch Database para um banco de dados ou tabela
Para configurar um banco de dados para o Stretch Database, você deve ter a permissão CONTROL DATABASE. Além disso, você precisa fornecer credenciais de administrador para o ponto de extremidade remoto.

Para configurar uma tabela para o Stretch Database, você deve ter o privilégio ALTER na tabela, e o banco de dados já deve estar configurado para o Stretch Database.

### Acesso de dados
Somente processos do sistema podem acessar a definição de servidor vinculado por trás do Stretch Database. Logons de usuário não podem emitir consultas através da definição de servidor vinculado para o ponto de extremidade remoto.

O Stretch Database não altera o modelo de permissões de um banco de dados existente. Logons de usuário podem consultar os dados em uma tabela com o Stretch Database habilitado por meio do banco de dados local. O banco de dados local executa verificações de permissão para quaisquer ações iniciadas pelo usuário da mesma maneira que faz como quaisquer outros objetos. Se você está autorizado a acessar a tabela com o Stretch Database habilitado, terá acesso a todo o conteúdo para o qual você está autorizado, independentemente de onde residem os dados fisicamente.

![Modelo de acesso a dados do Stretch Database][StretchOverviewImage3]

## Testar o Stretch Database.
**Teste o Stretch Database com o banco de dados de exemplo AdventureWorks.** Para obter o banco de dados de exemplo AdventureWorks, baixe pelo menos o arquivo de banco de dados e o arquivo de exemplos e scripts [aqui](https://www.microsoft.com/download/details.aspx?id=49502). Depois de restaurar o banco de dados de exemplo para uma instância do SQL Server 2016, descompacte o arquivo de exemplos e abra o arquivo Stretch DB Samples da pasta do Stretch DB. Execute os scripts neste arquivo para verificar o espaço usado por seus dados antes e depois de habilitar o Stretch Database, para acompanhar o andamento da migração de dados e para confirmar que você pode continuar a consultar os dados existentes e a inserir novos dados durante e após a migração.

## Próxima etapa
**Identificar os bancos de dados e tabelas que são candidatos ao Stretch Database.** Baixe o Supervisor de Atualização do SQL Server 2016 e execute o Supervisor do Stretch Database para identificar os bancos de dados e tabelas que são candidatos ao Stretch Database. O Supervisor do Banco de Dados de Stretch também identifica os problemas de bloqueio. Para obter mais informações, confira [Identificar bancos de dados e tabelas para o Stretch Database](sql-server-stretch-database-identify-databases.md).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0316_2016-->