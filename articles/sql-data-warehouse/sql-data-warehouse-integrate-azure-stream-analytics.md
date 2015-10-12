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
   ms.date="09/30/2015"
   ms.author="sahajs;twounder"/>

# Usar o Stream Analytics do Azure com o SQL Data Warehouse

A Stream Analytics do Azure é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos ao longo do fluxo de dados na nuvem. Você pode aprender as noções básicas lendo [Introdução ao Stream Analytics do Azure][]. Depois, você pode saber como criar uma solução de ponta a ponta com o Stream Analytics seguindo o tutorial [Introdução ao uso do Stream Analytics do Azure][].

Neste artigo, você aprenderá como usar o banco de dados do SQL Data Warehouse do Azure como um coletor de saída seus trabalhos do Stream Analytics.

## Pré-requisitos

Primeiro, realize as etapas a seguir no tutorial [Introdução ao uso do Stream Analytics do Azure][].

1. Criar uma entrada de Hub de eventos
2. Configurar e iniciar o aplicativo gerador de evento
3. Provisionar um trabalho de análise de fluxo
4. Especifique a entrada e a consulta do trabalho

Em seguida, crie um banco de dados do SQL Data Warehouse do Azure

## Especifique a saída do trabalho: banco de dados do SQL Data Warehouse do Azure

### Etapa 1

No trabalho de Stream Analytics, clique em **SAÍDA** na parte superior da página e depois em **ADICIONAR SAÍDA**.

### Etapa 2

Selecione o Banco de Dados SQL e clique em Avançar.

![][add-output]

### Etapa 3
Insira os seguintes valores na próxima página:

- *Alias de saída*: insira um nome amigável para essa saída de trabalho.
- *Assinatura*:
	- se o seu banco de dados do SQL Data Warehouse estiver na mesma assinatura que o trabalho do Stream Analytics, selecione Usar Banco de Dados SQL da Assinatura Atual.
	- Se o seu banco de dados estiver em uma assinatura diferente, selecione Usar Banco de Dados SQL de Outra Assinatura.
- *Banco de dados*: especifique o nome de um banco de dados de destino.
- *Nome do servidor*: especifique o nome do servidor do banco de dados que você acabou de especificar. Você pode usar o Portal do Azure para encontrar isso.

![][server-name]

- *Nome de usuário*: especifique o nome de usuário de uma conta que tenha permissões de gravação para o banco de dados.
- *Senha*: forneça a senha da conta de usuário especificada.
- *Tabela*: especifique o nome da tabela de destino no banco de dados.

![][add-database]

### Etapa 4

Clique no botão de verificação para adicionar essa saída e para verificar se o Stream Analytics pode se conectar com êxito ao banco de dados.

![][test-connection]

Quando a conexão com o banco de dados tiver êxito, você verá uma notificação na parte inferior do portal. Você pode clicar em Testar Conexão na parte inferior para testar a conexão com o banco de dados.

## Próximas etapas

Para obter uma visão geral da integração, consulte [Visão geral de integração do SQL Data Warehouse][].

Para obter mais dicas de desenvolvimento, confira [Visão geral sobre o desenvolvimento no SQL Data Warehouse][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introdução ao Stream Analytics do Azure]: stream-analytics-introductiond.md
[Introdução ao uso do Stream Analytics do Azure]: stream-analytics-get-started.md
[Visão geral sobre o desenvolvimento no SQL Data Warehouse]: sql-data-warehouse-overview-develop.md
[Visão geral de integração do SQL Data Warehouse]: sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

<!---HONumber=Oct15_HO1-->