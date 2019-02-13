---
title: Usar dados de referência de um Banco de Dados SQL para um trabalho do Azure Stream Analytics (versão prévia)
description: Este artigo descreve como usar um Banco de Dados SQL como entrada de dados de referência para um trabalho do Azure Stream Analytics no portal do Azure e no Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: d79e423dd617a498aee09cff6b774bbe90eeaa83
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701500"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job-preview"></a>Usar dados de referência de um Banco de Dados SQL para um trabalho do Azure Stream Analytics (versão prévia)

O Azure Stream Analytics dá suporte ao Banco de Dados SQL como uma fonte de entrada para dados de referência. É possível usar o Banco de Dados SQL como dados de referência para o trabalho do Stream Analytics no portal do Azure e no Visual Studio com as ferramentas do Stream Analytics. Este artigo monstra como realizar ambos os métodos.

## <a name="azure-portal"></a>Portal do Azure

Siga as etapas abaixo para adicionar o Banco de Dados SQL como uma fonte de entrada de referência usando o portal do Azure:

### <a name="portal-prerequisites"></a>Pré-requisitos do portal

1. Criar um trabalho do Stream Analytics.

2. Crie uma conta de armazenamento para ser usada pelo trabalho do Stream Analytics.

3. Crie o Banco de Dados SQL do Azure com um conjunto de dados a ser usado como dados de referência pelo trabalho do Stream Analytics.

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência do Banco de Dados SQL

1. No trabalho do Stream Analytics, selecione **Entradas** em **Topologia do trabalho**. Clique em **Adicionar referência de entrada** e escolha **Banco de Dados SQL**.

   ![Entrada de trabalho do Stream Analytics](./media/sql-reference-data/stream-analytics-inputs.png)

2. Preencha as Configurações de entrada do Stream Analytics. Escolha o nome do banco de dados, o nome do servidor, o nome de usuário e a senha. Se quiser que os dados de referência de entrada sejam atualizados periodicamente, escolha "On" para especificar a taxa de atualização em DD:HH:MM. Se tiver grandes conjuntos de dados com uma taxa de atualização curta, você poderá usar uma [consulta delta](sql-reference-data.md#delta-query).

   ![Configuração da referência do Banco de Dados SQL](./media/sql-reference-data/sql-input-config.png)

3. Teste a consulta de instantâneo no editor de consultas SQL. Para obter mais informações, confira o artigo sobre como [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](../sql-database/sql-database-connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Especificar a conta de armazenamento na configuração do trabalho

Navegue até **Configurações da conta de armazenamento** em **Configurar** e selecione **Adicionar conta de armazenamento**.

   ![Configurações de conta de armazenamento do Stream Analytics](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Iniciar o trabalho

Depois de configurar outras entradas, saídas e consultas, você poderá iniciar o trabalho do Stream Analytics.

## <a name="tools-for-visual-studio"></a>Ferramentas para Visual Studio

Siga as etapas abaixo para adicionar o Banco de Dados SQL do Azure como uma fonte de entrada de referência usando o Visual Studio:

### <a name="visual-studio-prerequisites"></a>Pré-requisitos do Visual Studio

1. Se estiver usando o Visual Studio 2017, atualize para a versão 15.8.2 ou superior. As versões 16.0 e superior não têm suporte neste momento.

2. [Instale as ferramentas do Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Há suporte para as seguintes versões do Visual Studio:

   * Visual Studio 2015
   * Visual Studio 2017

3. Familiarize-se com o início rápido das [Ferramentas do Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md).

4. Criar uma conta de armazenamento.

### <a name="create-a-sql-database-table"></a>Criar uma tabela do Banco de Dados SQL

Use o SQL Server Management Studio para criar uma tabela para armazenar os dados de referência. Saiba mais em [Projetar seu primeiro Banco de Dados SQL do Azure usando o SSMS](../sql-database/sql-database-design-first-database.md).

A tabela de exemplo usada a seguir foi criada com base nesta instrução:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Escolha sua assinatura

1. No Visual Studio, no menu **Exibir**, selecione **Gerenciador de Servidores**.

2. Clique com o botão direito do mouse em **Azure**, selecione **Conectar-se à assinatura do Microsoft Azure** e entre com a conta do Azure.

### <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Selecione **Arquivo > Novo Projeto**. 

2. Na lista de modelos à esquerda, selecione **Stream Analytics** e então selecione **Aplicativo do Stream Analytics do Azure**. 

3. Insira o **Nome**, o **Local** e o **Nome da solução** do projeto e selecione **OK**.

   ![Projeto do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definir a entrada de dados de referência do Banco de Dados SQL

1. Crie uma nova entrada.

   ![Entrada do New Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Clique duas vezes em **Input.json** no **Gerenciador de soluções**.

3. Preencha as **Configurações de entrada do Stream Analytics**. Escolha o nome do banco de dados, o nome do servidor, tipo de atualização e a taxa de atualização. Especifique a taxa de atualização no formato `DD:HH:MM`.

   ![Configuração de entrada do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Se escolher "Executar apenas uma vez" ou "Executar periodicamente", um arquivo CodeBehind do SQL chamado **[Alias de entrada].snapshot.sql** é gerado no projeto sob o nó de arquivo **Input.json**.

   ![Entrada do CodeBehind no Visual Studio](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Se escolher "Atualizar periodicamente com Delta", dois arquivos CodeBehind do SQL serão gerados: **[Alias de entrada].snapshot.sql** e **[Alias de entrada].delta.sql**.

   ![CodeBehind no Gerenciador de Soluções](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Abra o arquivo SQL no editor e grave a consulta SQL.

5. Se estiver usando o Visual Studio 2017 e tiver instalado as ferramentas do SQL Server Data, você poderá testar a consulta clicando em **Executar**. Uma janela de assistente será exibida para ajudá-lo a se conectar ao Banco de Dados SQL, e o resultado da consulta será exibido na parte inferior da janela.

### <a name="specify-storage-account"></a>Especificar a conta de armazenamento

Abra o **JobConfig.json** para especificar a conta de armazenamento para armazenar instantâneos de referência do SQL.

   ![Configuração de trabalho do Stream Analytics no Visual Studio](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testar localmente e implantar no Azure

Antes de implantar o trabalho no Azure, você poderá testar a lógica de consulta localmente em relação aos dados dinâmicos de entrada. Você encontra mais informações sobre esse recurso em [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md). Quando você terminar o teste, clique em **Enviar para o Azure**. Saiba mais sobre como começar trabalho no início rápido [Criar um Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="delta-query"></a>Consulta delta

Ao usar a consulta delta, são recomendadas [tabelas temporais no Banco de Dados SQL do Azure](../sql-database/sql-database-temporal-tables.md).

1. Crie a consulta de instantâneo. 

   Use o parâmetro **@snapshotTime** para instruir o tempo de execução do Stream Analytics para obter o conjunto de dados de referência da tabela temporal válida do Banco de Dados SQL na hora do sistema. Se não fornecer esse parâmetro, você corre o risco de obter um conjunto impreciso de dados de referência de base devido à defasagem de horário. Veja a seguir um exemplo de uma consulta completa de instantâneo:

   ![Consulta de instantâneo no Stream Analytics](./media/sql-reference-data/snapshot-query.png)
 
2. Crie a consulta delta. 
   
   Essa consulta recupera todas as linhas no Banco de Dados SQL que foram inseridas ou excluídas entre uma hora de início **@deltaStartTime** e uma hora de término **@deltaEndTime**. A consulta delta terá de retornar as mesmas colunas como a consulta de instantâneo, bem como a coluna **_operação_**. Essa coluna define se a linha é inserida ou excluída entre **@deltaStartTime** e **@deltaEndTime**. As linhas resultantes são sinalizadas como **1**, se os registros foram inseridos, ou **2** se excluídos. 

   Quanto aos registros que foram atualizados, a tabela temporal faz a contabilidade, capturando uma operação de inserção e exclusão. O tempo de execução do Stream Analytics aplicará os resultados da consulta delta para o instantâneo anterior a fim de manter os dados de referência atualizados. Veja a seguir um exemplo de consulta delta:

   ![Consulta delta do Stream Analytics](./media/sql-reference-data/delta-query.png)
 
  O tempo de execução do Stream Analytics pode executar periodicamente a consulta de instantâneo além da consulta delta para armazenar pontos de verificação.

## <a name="faqs"></a>Perguntas frequentes

**Haverá custos adicionais se eu usar a entrada de dados de referência do SQL no Azure Stream Analytics?**

Não há [custos por unidade de streaming](https://azure.microsoft.com/pricing/details/stream-analytics/) adicionais no trabalho do Stream Analytics. No entanto, é preciso que o trabalho do Stream Analytics tenha uma conta de armazenamento do Azure associada. O trabalho do Stream Analytics consulta o Banco de Dados SQL (durante o início do trabalho e o intervalo de atualização) para recuperar o conjunto de dados de referência e armazena esse instantâneo na conta de armazenamento. O armazenamento desses instantâneos acarretará cobranças adicionais, conforme detalhadas na [página de preços](https://azure.microsoft.compricing/details/storage/) da conta de armazenamento do Azure.

**Como saber se o instantâneo de dados de referência está sendo consultado pelo Banco de Dados SQL e está sendo usado no trabalho do Azure Stream Analytics?**

Há duas métricas filtradas pelo Nome lógico (nas Métricas do Portal do Azure) que você pode usar para monitorar a integridade dos dados de referência do Banco de Dados SQL de entrada.

   * InputEvents: esta métrica mede o número de registros carregados do conjunto de dados de referência de Banco de Dados SQL.
   * InputEventBytes: esta métrica mede o tamanho do instantâneo de dados de referência carregado na memória do trabalho do Stream Analytics. 

A combinação de ambas as métricas pode ser usada para inferir se o trabalho é a consulta do Banco de Dados SQL para buscar o conjunto de dados de referência e, então, carregá-lo na memória.

**É necessário um tipo especial de Banco de Dados SQL do Azure?**

O Azure Stream Analytics funciona com qualquer tipo de Banco de Dados SQL. No entanto, é importante entender que a taxa de atualização definida para sua entrada de dados de referência pode afetar sua carga de consulta. Para usar a opção de consulta delta, é recomendável usar tabelas temporais no Banco de Dados SQL do Azure.

**É possível obter uma entrada de exemplo da entrada de dados de referência do Banco de Dados SQL?**

Este recurso não está disponível.

**Por que o Azure Stream Analytics armazena instantâneos na conta de armazenamento do Azure?**

O Stream Analytics garante exatamente o processamento de eventos exatamente uma vez e pelo menos uma entrega de eventos. Nos casos em que problemas transitórios impactarem seu trabalho, será necessária um pequena reprodução para restaurar o estado. Para habilitar a reprodução, é necessário que esses instantâneos estejam armazenados em uma conta de Armazenamento do Azure. Confira mais informações sobre a reprodução de ponto de verificação em [Conceitos de ponto de verificação e de reprodução em trabalhos do Azure Stream Analytics](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Próximas etapas

* [Usar dados de referência para pesquisas no Stream Analytics](stream-analytics-use-reference-data.md)
* [Início Rápido: Criar um trabalho do Stream Analytics usando as ferramentas do Azure Stream Analytics para Visual Studio](stream-analytics-quick-create-vs.md)
* [Testar dados dinâmicos localmente usando as ferramentas do Azure Stream Analytics para Visual Studio (versão prévia)](stream-analytics-live-data-local-testing.md)
