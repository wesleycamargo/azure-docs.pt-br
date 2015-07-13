<properties
   pageTitle="Usar o Stream Analytics do Azure com o SQL Data Warehouse | Microsoft Azure"
   description="Dicas para usar o Stream Analytics do Azure com o Azure SQL Data Warehouse para desenvolver as soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/22/2015"
   ms.author="sahajs"/>

# Usar o Stream Analytics do Azure com o SQL Data Warehouse

A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender o básico lendo [Introdução Stream Analytics do Azure][]. Em seguida, você pode aprender como criar uma solução de ponta a ponta com o Stream Analytics usando o [Tutorial de introdução][].

Neste artigo, você aprenderá como usar o banco de dados do SQL Data Warehouse do Azure como um coletor de saída seus trabalhos do Stream Analytics.

## Pré-requisitos

Primeiro, execute as seguintes etapas no [Tutorial de introdução][].

1. Criar uma entrada de Hub de eventos
2. Configurar e iniciar o aplicativo gerador de evento
3. Provisionar um trabalho de análise de fluxo
4. Especifique a entrada e a consulta do trabalho

Em seguida, crie um banco de dados do SQL Data Warehouse do Azure

## Especifique a saída do trabalho: banco de dados do SQL Data Warehouse do Azure

### Etapa 1
No trabalho de Stream Analytics, clique em **SAÍDA** na parte superior da página e depois em **ADICIONAR SAÍDA**.

### Etapa 2
Selecione o Banco de Dados SQL e clique em Avançar. ![][Add Output]

### Etapa 3
Insira os seguintes valores na próxima página - Alias de saída: insira um nome amigável para essa saída de trabalho. - Assinatura: - se o banco de dados do Data Warehouse do SQL estiver na mesma assinatura que o trabalho de análise de fluxo, selecione Usar banco de dados SQL da assinatura atual. - Se o banco de dados estiver em uma assinatura diferente, selecione Usar Banco de Dados SQL de Outra Assinatura. - Banco de dados: especifique o nome de um banco de dados de destino. - Nome do servidor: especifique o nome do servidor do banco de dados que você especificou. Você pode usar o Portal do Azure para encontrar isso.

![][Server Name]

- Nome de usuário: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
- Senha: forneça a senha da conta de usuário especificada.
- Tabela: especifique o nome da tabela de destino no banco de dados.

![][Add Database]

### Etapa 4
Clique no botão de verificação para adicionar essa saída e para verificar se o Stream Analytics pode se conectar com êxito ao banco de dados.

![][Test Connection]

Quando a conexão com o banco de dados tiver êxito, você verá uma notificação na parte inferior do portal. Você pode clicar em Testar Conexão na parte inferior para testar a conexão com o banco de dados.




## Próximas etapas
Para obter uma visão geral da integração, consulte [Visão geral da integração do SQL Data Warehouse][]. Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL Data Warehouse][].

<!--Image references-->
[Add Output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[Server Name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[Add Database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[Test Connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->
[Introdução Stream Analytics do Azure]: ./stream-analytics-introduction/
[Tutorial de introdução]: ./articles/stream-analytics-get-started/
[Visão geral de desenvolvimento do SQL Data Warehouse]: ./sql-data-warehouse-overview-develop/
[Visão geral da integração do SQL Data Warehouse]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=July15_HO1-->