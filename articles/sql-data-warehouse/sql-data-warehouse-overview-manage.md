<properties
   pageTitle="Ferramentas de gerenciamento para o SQL Data Warehouse | Microsoft Azure"
   description="Introdução às ferramentas de gerenciamento para o SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="HappyNicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/24/2015"
   ms.author="mausher;nicw;barbkess;JRJ@BigBangData.co.uk;"/>

# Ferramentas de gerenciamento para o SQL Data Warehouse
Este tópico explora e compara as ferramentas e opções de gerenciamento do SQL Data Warehouse para que você possa escolher a ferramenta certa de acordo com suas necessidades. Escolher a ferramenta certa depende de quantos bancos de dados você gerencia, da tarefa e da frequência de execução de uma tarefa.

## Portal do Azure
O [Portal do Azure][] é um portal de gerenciamento com base na Web no qual você pode criar, atualizar e excluir bancos de dados e monitorar recursos do banco de dados. Essa ferramenta será excelente se você estiver começando a usar o Azure, se estiver gerenciando uma pequena quantidade de bancos de dados de data warehouse ou se precisar fazer alguma coisa rapidamente.

O portal contém métricas que abrangem as configurações histórias e atuais de DWU de desempenho, a quantidade de armazenamento que está sendo usado, as conexões SQL bem-sucedidas e com falha e um conjunto de visualizações e dados que permitem uma compreensão das consultas em execução em sua instância e os detalhes das consultas.

## SQL Server Data Tools no Visual Studio	
[SQL Server Data Tools][] (SSDT) no Visual Studio é uma ferramenta cliente que executa em seu computador e permite a conexão, gerenciamento e desenvolvimento do banco de dados na nuvem. Se você for um desenvolvedor de aplicativos familiarizado com o Visual Studio ou com outros ambientes de desenvolvimento integrados (IDEs), tente usar o SSDT no Visual Studio.

O SSDT inclui o SQL Server Explorer, que permite a visualização, conexão e execução de scripts em bancos de dados do SQL Data Warehouse. Para conectar-se rapidamente ao SQL Data Warehouse, você pode simplesmente clicar no botão **Abrir no Visual Studio** na barra de comandos ao exibir os detalhes do banco de dados no Portal do Azure.

Você pode baixar a versão mais recente do [SQL Server Data Tools][] (SSDT) que inclui suporte para o SQL Data Warehouse.

## Ferramentas da linha de comando
Uma opção é usar as ferramentas de linha de comando do PowerShell ou sqlcmd para gerenciar o SQL Data Warehouse e automatizar as implantações de recursos do Azure. Recomendamos essas ferramentas para gerenciar uma grande quantidade de servidores lógicos e implantar alterações de recursos em um ambiente de produção, pois as tarefas necessárias podem ser incluídas em script e automatizadas.

## Próximas etapas
Para começar a usar essas ferramentas, vá até o tópico [conexão][].

<!--Image references-->

<!--Article references-->
[conexão]: sql-data-warehouse-develop-connections.md

<!--MSDN references-->
[SQL Server Data Tools]: https://msdn.microsoft.com/pt-br/library/mt204009.aspx

<!--Other web references-->
[Portal do Azure]: http://portal.azure.com/

<!---HONumber=August15_HO6-->