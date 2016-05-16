<properties
   pageTitle="Gerenciar bancos de dados no Azure SQL Data Warehouse | Microsoft Azure"
   description="Visão geral do gerenciamento de bancos de dados do SQL Data Warehouse. Inclui ferramentas de gerenciamento, DWUs e escalar o desempenho horizontalmente, solucionar problemas de desempenho de consulta, estabelecer políticas de segurança e restaurar um banco de dados contra dados corrompidos ou de uma interrupção regional."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/03/2016"
   ms.author="barbkess;sonyama;"/>

# Gerenciar bancos de dados no SQL Data Warehouse do Azure

O SQL Data Warehouse automatiza muitos aspectos do gerenciamento de seus bancos de dados. Por exemplo, para aumentar o desempenho, basta ajustar para o nível certo de recursos de computação e pagar por eles, para então deixar o SQL Data Warehouse fazer todo o trabalho de escalar horizontalmente e de reverter essa escala.

Sem dúvida, você desejará monitorar sua carga de trabalho para identificar suas necessidades de desempenho, bem como solucionar problemas em consultas de execução longa. Você também precisará executar algumas tarefas de segurança para gerenciar permissões para usuários e logons.

Esta visão geral aborda esses aspectos do gerenciamento do SQL Data Warehouse.

- Ferramentas de gerenciamento
- Computação de escala
- Pausar e retomar
- Práticas recomendadas de desempenho
- Monitoramento de consulta
- Segurança
- Backup e restauração

## Ferramentas de gerenciamento

Você pode usar uma variedade de ferramentas para gerenciar bancos de dados no SQL Data Warehouse. Ao gerenciar bancos de dados, você desenvolverá preferências de ferramenta para cada tipo de tarefa, que você precisa executar.

### Portal do Azure
O [Portal do Azure][] é um portal com base na Web no qual você pode criar, atualizar e excluir bancos de dados e monitorar recursos do banco de dados. Essa ferramenta será excelente se você estiver começando a usar o Azure, se estiver gerenciando uma pequena quantidade de bancos de dados de data warehouse ou se precisar fazer alguma coisa rapidamente.

Para começar a usar o portal do Azure, consulte [Criar um SQL Data Warehouse (Portal do Azure)][].

### SQL Server Data Tools no Visual Studio
O [SSDT][] (SQL Server Data Tools) no Visual Studio permite que você se conecte, gerencie e desenvolva seus bancos de dados. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou com outros ambientes de desenvolvimento integrados (IDEs), tente usar o SSDT no Visual Studio.

O SSDT inclui o Pesquisador de Objetos do SQL Server, que permite a visualização, conexão e execução de scripts em bancos de dados do SQL Data Warehouse. Para conectar-se rapidamente ao SQL Data Warehouse, você pode simplesmente clicar no botão **Abrir no Visual Studio** na barra de comandos ao exibir os detalhes do banco de dados no Portal Clássico do Azure.

Para uma introdução ao SSDT no Visual Studio, consulte [Conectar-se ao Azure SQL Data Warehouse com o Visual Studio][].

### Ferramentas da linha de comando
Ferramentas de linha de comando são ideais para automatizar suas cargas de trabalho. PowerShell e sqlcmd são duas formas incríveis de automatizar os processos. Recomendamos essas ferramentas para gerenciar uma grande quantidade de servidores lógicos e implantar alterações de recursos em um ambiente de produção, pois as tarefas necessárias podem ser incluídas em script e automatizadas.

### Exibições de gerenciamento dinâmico 

DMVs são a base do gerenciamento de SQL Data Warehouse. Quase todas as informações que aparecem no portal dependem de DMVs. Para ver uma lista de DMVs do SQL Data Warehouse, consulte [Exibições do sistema do SQL Data Warehouse][].

Para começar, consulte [Conectar e consultar com sqlcmd][], e [Criar um banco de dados (PowerShell)][].

## Computação de escala

No SQL Data Warehouse você pode, com rapidez, escalar horizontalmente o desempenho ou reverter esse processo, aumentando ou diminuindo os recursos de computação de CPU, memória e largura de banda de E/S. Para dimensionar o desempenho, tudo o que você precisa fazer é ajustar o número de DWUs (unidades de data warehouse) que o SQL Data Warehouse aloca para seu banco de dados. O SQL Data Warehouse faz rapidamente a alteração e trata de todas as alterações subjacentes de hardware e software.

Para saber mais sobre o dimensionamento de DWUs, consulte [Dimensionar o desempenho][].

##  Pausar e retomar

Para economizar custos, é possível pausar e retomar os recursos de computação sob demanda. Por exemplo, se você não usar banco de dados durante a noite e nos finais de semana, você poderá pausá-lo durante esses períodos e retomá-lo durante o dia. Você não será cobrado por DWUs enquanto o banco de dados estiver em pausa.

Para obter mais informações, consulte [Pausar a computação][] e [Retomar a computação][].

## Práticas recomendadas de desempenho

Ao ser apresentado a uma nova tecnologia, descobrir as dicas e truques que funcionam melhor desde o início pode economizar muito tempo. Você encontrará as práticas recomendadas ao longo de muitos tópicos.

Para ver várias um resumo das considerações mais importantes no desenvolvimento de sua carga de trabalho, consulte [Práticas recomendadas do SQL Data Warehouse][].

## Monitoramento de consulta

Às vezes, uma consulta está sendo executada por muito tempo, mas você não tem certeza de qual é o culpada. O SQL Data Warehouse tem DMVs (exibições de gerenciamento dinâmico) que você pode usar para descobrir qual consulta está demorando tempo demais.

Para consultas de execução longa, consulte [Monitorar sua carga de trabalho usando DMVs][].

## Segurança

Para manter um sistema seguro, você deve estar alerta e protegê-lo contra todo tipo de acesso não autorizado. Um sistema de segurança precisa verificar se as regras de firewall estão em vigor de modo que apenas endereços IP autorizados possam se conectar. Ele precisa de autenticação adequada das credenciais do usuário. Depois que um usuário se conectou ao banco de dados, o usuário só deve ter permissões para executar um número mínimo de ações. Para proteger dados, você pode usar criptografia. Também é importante ter auditoria e acompanhamento para que você possa retraçar eventos em caso de qualquer atividade suspeita.

Para saber mais sobre gerenciamento de segurança, vá até a [Visão geral de segurança][].

## Backup e restauração

Há duas maneiras de recuperar um banco de dados. Se você tiver alguns dados corrompidos no banco de dados ou cometer um erro, você poderá restaurar um instantâneo de banco de dados. Se houver uma interrupção regional ou um desastre que torne uma das regiões não disponíveis, você poderá recriar o banco de dados em outra região.

O SQL Data Warehouse faz automaticamente o backup do seu banco de dados em intervalos regulares. Para o agendamento de backup de dados e a política de retenção, consulte [Alta confiabilidade][].

### Armazenamento com redundância geográfica

Uma vez que o SQL Data Warehouse separa computação e armazenamento, todos os seus dados são gravados diretamente no RA-GRS (Armazenamento do Azure com redundância geográfica). O armazenamento com redundância geográfica replica seus dados para uma região secundária a centenas de quilômetros da região primária. Em ambas as regiões, primária e secundária, seus dados são replicados três vezes cada um, entre domínios de falha e domínios de atualização separados. Isso garante a durabilidade dos dados, mesmo no caso de uma interrupção regional completa ou um desastre que cause indisponibilidade das regiões. Para saber mais sobre o Armazenamento com Redundância Geográfica com Acesso de Leitura, leia [Opções de redundância de armazenamento do Azure][].

### Restauração do banco de dados

A restauração do banco de dados foi desenvolvida para restaurar seu banco de dados para um determinado ponto anterior. O serviço do SQL Data Warehouse protege todos os bancos de dados com instantâneos automáticos de armazenamento pelo menos a cada oito horas e os mantêm por sete dias para fornecer a você um conjunto distinto de pontos de restauração. Esses backups são armazenados no Armazenamento do Azure RA-GRS e, portanto, com redundância geográfica por padrão. Os recursos de backup e restauração automáticos são gratuitos, além de não terem nenhum custo e não exigirem administração para proteger os bancos de dados contra corrupção ou exclusão acidentais.

Para saber mais sobre a restauração de banco de dados, vá até [Restaurar do instantâneo][].

### Restauração geográfica

A Restauração Geográfica foi desenvolvida para recuperar o banco de dados no caso em que ele se torna indisponível devido a um evento de interrupção. Você pode contatar o suporte para restaurar um banco de dados usando um backup com redundância geográfica de modo a criar um novo banco de dados em qualquer região do Azure. Como o backup tem redundância geográfica, ele pode ser usado para recuperar um banco de dados mesmo que este esteja inacessível devido a uma interrupção. O recurso de Restauração Geográfica não tem custos adicionais.

Para usar a restauração geográfica, vá até [Restauração geográfica do instantâneo][].

## Próximas etapas
Usar bons princípios de design de banco de dados tornará mais fácil gerenciar seus bancos de dados no SQL Data Warehouse. Para saber mais, vá até a [Visão geral de desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Opções de redundância de armazenamento do Azure]: storage-redundancy.md#read-access-geo-redundant-storage
[Criar um SQL Data Warehouse (Portal do Azure)]: sql-data-warehouse-get-started-provision.md
[Criar um banco de dados (PowerShell)]: sql-data-warehouse-get-started-provision-powershell
[connection]: sql-data-warehouse-develop-connections.md
[Conectar-se ao Azure SQL Data Warehouse com o Visual Studio]: sql-data-warehouse-get-started-connect.md
[Conectar e consultar com sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md
[Visão geral de desenvolvimento]: sql-data-warehouse-overview-development.md
[Restauração geográfica do instantâneo]: sql-data-warehouse-backup-and-geo-restore-from-snapshot.md
[Alta confiabilidade]: sql-data-warehouse-overview-expectations.md#high-reliability
[Monitorar sua carga de trabalho usando DMVs]: sql-data-warehouse-manage-monitor.md
[Pausar a computação]: sql-data-warehouse-overview-scalability.md#pause-compute-bk
[Restaurar do instantâneo]: sql-data-warehouse-backup-and-restore-from-snapshot.md
[Retomar a computação]: sql-data-warehouse-overview-scalability.md#resume-compute-performance-bk
[Dimensionar o desempenho]: sql-data-warehouse-overview-scalability.md#scale-performance-bk
[Visão geral de segurança]: sql-data-warehouse-overview-security.md
[Práticas recomendadas do SQL Data Warehouse]: sql-data-warehouse-best-practices.md
[Exibições do sistema do SQL Data Warehouse]: sql-data-warehouse-reference-tsql-system-views.md

<!--MSDN references-->
[SSDT]: https://msdn.microsoft.com/library/mt204009.aspx

<!--Other web references-->
[Portal do Azure]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->