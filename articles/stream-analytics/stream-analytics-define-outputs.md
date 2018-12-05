---
title: Entender as saídas do Azure Stream Analytics
description: Este artigo descreve as opções de saída de dados disponíveis no Azure Stream Analytics, incluindo o Power BI para resultados da análise.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: 869941781643d3486506b5a3caed4006019fb3b7
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2018
ms.locfileid: "52310025"
---
# <a name="understand-outputs-from-azure-stream-analytics"></a>Entender as saídas do Azure Stream Analytics
Este artigo descreve os diferentes tipos de saídas disponíveis para um trabalho do Azure Stream Analytics. As saídas permitem armazenar e salvar os resultados do trabalho do Stream Analytics. Usando os dados de saída, você pode fazer mais análise de negócios e data warehouse de seus dados. 

Quando você criar sua consulta do Stream Analytics, consulte o nome de saída usando a [cláusula INTO](https://msdn.microsoft.com/azure/stream-analytics/reference/into-azure-stream-analytics). Você pode usar uma única saída por trabalho ou várias saídas por trabalho de streaming se você precisar fornecer várias cláusulas INTO na consulta.

Para criar, editar e testar as saídas de trabalho do Stream Analytics, você pode usar o [Porta do Azure](stream-analytics-quick-create-portal.md#configure-job-output), o [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), o [.Net API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), o [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) e o [Visual Studio](stream-analytics-quick-create-vs.md).

Alguns tipos de saídas dão suporte a [particionamento](#partitioning) e os [tamanhos de lote de saída](#output-batch-size) variam para otimizar a taxa de transferência.


## <a name="azure-data-lake-store"></a>Repositório Azure Data Lake
O Stream Analytics dá suporte ao [Repositório Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). O Repositório Azure Data Lake é um repositório em hiper-escala corporativo para cargas de trabalho de análise de big data. O Repositório Data Lake permite que você armazene dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. O Stream Analytics deve estar autorizado a acessar o Data Lake Store.

Saída do repositório Azure Data Lake do Stream Analytics não está disponível no Azure na China (21Vianet) e regiões do Azure Alemanha (T-Systems internacional).

### <a name="authorize-an-azure-data-lake-store-account"></a>Autorizar uma conta do Azure Data Lake Store

1. Quando o Data Lake Store é selecionado como uma saída no portal do Azure, você é solicitado a autorizar uma conexão com um Data Lake Store existente.  

   ![Autorizar o Repositório Data Lake](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

2. Se você já tiver acesso ao Data Lake Store, selecione **Autorizar agora** e uma página será exibida indicando o **Redirecionando para autorização**. Depois que a autorização for bem-sucedida, você verá a página que permite que você configure a saída do Data Lake Store.

3. Uma vez que a conta do Repositório Data Lake foi autenticada, você pode configurar as propriedades de saída do Repositório Data Lake. A tabela a seguir é a lista de nomes de propriedade e sua descrição para configurar a saída do Repositório Data Lake.

   ![Autorizar o Repositório Data Lake](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

| Nome da propriedade | DESCRIÇÃO | 
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Data Lake Store. | 
| Nome da conta | O nome da conta do Data Lake Store para a qual você está enviando a saída. Você verá uma lista suspensa de contas do Data Lake Store que estão disponíveis na sua assinatura. |
| Padrão de prefixo de caminho | O caminho do arquivo usado para gravar seus arquivos na Conta do Repositório Data Lake especificada. Você pode especificar uma ou mais instâncias de variáveis de {data} e {hora}.</br><ul><li>Exemplo 1: pasta1/logs/{data}/{hora}</li><li>Exemplo 2: pasta1/logs/{data}</li></ul><br>O registro de data e hora da estrutura de pastas criada segue o UTC e não o horário local.</br><br>Se o padrão de caminho do arquivo não contiver uma "/" à direita, o último padrão no caminho do arquivo é tratado como um prefixo de nome de arquivo. </br></br>Novos arquivos são criados nessas circunstâncias:<ul><li>Alteração no esquema de saída</li><li>Reinicialização interna ou externa de um trabalho.</li></ul> |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
|Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.| 
| Codificação | Se estiver usando formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.|
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical.|
| Formatar | Aplicável somente para serialização JSON. Uma linha separada especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. A matriz especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, já que não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado.|

### <a name="renew-data-lake-store-authorization"></a>Renovar autorização do Data Lake Store
Você precisa autenticar novamente sua conta do Data Lake Store caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez. Se você não autenticar novamente, o trabalho não produzirá resultados e mostrará um erro indicando a necessidade de uma nova autorização nos Logs de operação. Atualmente, há uma limitação em que o token de autenticação deve ser atualizado manualmente a cada 90 dias para todos os trabalhos com saída do Repositório Data Lake. 

Para renovar a autorização, **Pare** seu trabalho > vá para sua saída do Data Lake Store > clique no link **Renovar autorização** e por um curto período uma página será exibida indicando **Redirecionando para autorização...**. A página será fechada automaticamente e, se for bem-sucedida, indicará que a **Autorização foi renovada com êxito**. Em seguida, você precisa clicar em **Salvar** na parte inferior da página e poderá continuar reiniciando seu trabalho da **última vez em que foi interrompido** para evitar perda de dados.

![Autorizar o Repositório Data Lake](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  

## <a name="sql-database"></a>Banco de dados SQL
[banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) pode ser usado como saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam de o conteúdo ser hospedado em um banco de dados relacional. Os trabalhos do Stream Analytics gravam em uma tabela existente em um banco de dados SQL do Azure.  O esquema da tabela deve corresponder exatamente aos campos e seus tipos sendo a saída do seu trabalho. Um [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) também pode ser especificado como uma saída por meio da opção de saída do Banco de Dados SQL. Para saber mais sobre as maneiras de melhorar a taxa de transferência de gravação, consulte o artigo [Stream Analytics com o Banco de dados SQL do Azure como saída](stream-analytics-sql-output-perf.md). A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de dados SQL.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados para o qual você está enviando a saída. |
| Nome do servidor | Nome do servidor de Banco de Dados SQL do Azure. |
| Nome de Usuário | O nome de usuário, que tem acesso para gravar no banco de dados. O Stream Analytics dá suporte apenas a autenticação do SQL. |
| Senha | A senha para se conectar ao banco de dados. |
| Tabela | O nome da tabela em que a saída é gravada. O nome da tabela diferencia maiúsculas de minúsculas e o esquema da tabela deve corresponder exatamente ao número de campos e seus tipos sendo gerados por sua saída de trabalho. |

> [!NOTE]
> Atualmente, há suporte para a oferta do Banco de Dados SQL uma saída do trabalho do Stream Analytics. No entanto, não há suporte para a execução de uma Máquina Virtual do Azure que executa o SQL Server com um banco de dados anexado. Isso está sujeito à mudança em versões futuras.
> 

## <a name="blob-storage"></a>Armazenamento de blob
O armazenamento de Blob oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem.  Para obter uma introdução sobre o Armazenamento de blob do Azure e seu uso, confira a documentação em [Como usar blobs](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de blob.

| Nome da propriedade       | DESCRIÇÃO                                                                      |
| ------------------- | ---------------------------------------------------------------------------------|
| Alias de saída        | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob. |
| Conta de armazenamento     | O nome da conta de armazenamento para o qual você está enviando a saída               |
| Chave da conta de armazenamento | A chave secreta associada à conta de armazenamento.                              |
| Contêiner de armazenamento   | Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob. |
| Padrão de caminho | Opcional. O padrão do caminho do arquivo usado para gravar seus blobs no contêiner especificado. <br /><br /> No padrão de caminho, você pode optar por usar uma ou mais instâncias das duas variáveis de hora e data para especificar a frequência com a qual os blobs são gravados: <br /> {data}, {hora} <br /><br />Se você usar esse [link de portal do Azure](https://portal.azure.com/?microsoft_azure_streamanalytics_bloboutputpathpartitioning=true&Microsoft_Azure_StreamAnalytics_bloboutputcontainerpartitioning=true) para acessar a visualização de particionamento de blobs personalizada, poderá especificar um nome de {campo} personalizado dos dados de seu evento para os blobs de partição. O nome do campo é alfanumérico e pode incluir espaços, hífens e sublinhados. Restrições em campos personalizados incluem o seguinte: <ul><li>Insensibilidade a maiúsculas e minúsculas (não é possível diferenciar entre "ID" da coluna e "id" da coluna)</li><li>Campos aninhados não são permitidos (em vez disso, use um alias na consulta de trabalho “nivelar” o campo)</li><li>As expressões não podem ser usadas como um nome de campo.</li></ul> <br /><br /> A visualização também permite o uso de configurações de especificador de formato personalizado de data/hora no caminho. Os formatos personalizados de data e hora devem ser especificados um de cada vez, entre a palavra-chave {datetime:\<specifier>}. As entradas permitidas para o \<specifier> são aaaa, MM, M, dd, d, HH, H, mm, m, ss ou s. A palavra-chave {datetime:\<specifier>} pode ser usada várias vezes no caminho para formar as configurações personalizadas de data/hora. <br /><br />Exemplos: <ul><li>Exemplo 1: cluster1/logs /{data}/{hora}</li><li>Exemplo 2: cluster1/logs/{data}</li><li>Exemplo 3 (versão prévia): cluster1/{client_id}/{data}/{hora}</li><li>Exemplo 4 (versão prévia): cluster1/{datetime:ss}/{myField} em que a consulta é: SELECIONAR data.myField COMO myField DA Entrada;</li><li>Exemplo 5 (versão prévia): cluster1/year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}</ul><br /><br />O registro de data e hora da estrutura de pastas criada segue o UTC e não o horário local.<br /><br/>A nomenclatura de arquivo segue a seguinte convenção: <br /><br />{Padrão de prefixo de caminho}/schemaHashcode_Guid_Number.extension<br /><br />Exemplo de arquivos de saída:<ul><li>Myoutput/20170901/00/45434_gguid_1.csv</li>  <li>Myoutput/20170901/01/45434_gguid_1.csv</li></ul> <br /><br /> Para obter mais informações sobre essa visualização, visite [Padrões de caminho personalizados de DateTime para saída de armazenamento de blob do Azure Stream Analytics (Visualizar)](stream-analytics-custom-path-patterns-blob-storage-output.md). |
| Formato de data | Opcional. Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD |
| Formato de hora | Opcional. Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH. |
| Formato de serialização do evento | Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação    | Se estiver usando formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador   | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar      | Aplicável somente para serialização JSON. Uma linha separada especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. A matriz especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, já que não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado. |

Ao usar o Armazenamento Blobs como saída, um novo arquivo é criado no blob nos seguintes casos:

* Se o arquivo atual excede o número máximo permitido de blocos (atualmente 50.000). O número máximo permitido de blocos pode ser alcançado sem atingir o tamanho máximo permitido do blob. Por exemplo, se a taxa de saída for alta, você pode ver mais bytes por bloco, e o tamanho do arquivo é maior. Se a taxa de saída for baixa, cada bloco tem menos dados e o tamanho do arquivo é menor.
* Se houver uma alteração de esquema na saída, e o formato de saída requer esquema fixo (CSV e Avro).  
* Se um trabalho for reiniciado externamente por um usuário, parando e iniciando-o, ou internamente para manutenção do sistema ou recuperação de erro.  
* Se a consulta for totalmente particionada, um novo arquivo será criado para cada partição de saída.  
* Se um arquivo ou um contêiner da conta de armazenamento for excluído pelo usuário.  
* Se a saída for tempo particionado usando o padrão de prefixo do caminho, um novo blob será usado quando a consulta se mover para a próxima hora.
* Se a saída for particionada por um campo personalizado, um novo blob será criado por chave de partição se ele não existir.
* Se a saída for particionada por um campo personalizado, onde a cardinalidade da chave de partição excede 8000, um novo blob poderá ser criado por chave de partição.

## <a name="event-hub"></a>Hub de evento
Os serviço de [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Uma utilização de um Hub de Eventos como saída é quando a saída de um trabalho do Stream Analytics torna-se a entrada de outro trabalho de streaming.

Há alguns parâmetros que são necessários para configurar fluxos de dados de Hub de Eventos como uma saída.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída | Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Hub de Eventos. |
| Namespace do Hub de Eventos |Um namespace do Event Hub é um contêiner para um conjunto de entidades de mensagens. Quando você criou um novo Hub de Eventos, também criou um namespace do Hub de Eventos. |
| Nome do Hub de Eventos | O nome da sua saída de Hub de Eventos. |
| Nome da política do Hub de Eventos | A política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do Hub de Eventos | A chave de Acesso Compartilhado usada para autenticar o acesso ao namespace do Hub de Eventos. |
| Coluna de chave de partição [opcional] | Esta coluna contém a chave de partição para saída do Hub de Eventos. |
| Formato de serialização do evento | Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente para serialização JSON. Uma linha separada especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. A matriz especifica que a saída é formatada como uma matriz de objetos JSON. Essa matriz é fechada somente quando o trabalho for interrompido ou o Stream Analytics tiver passado para a próxima janela de tempo. Em geral, é preferível usar JSON separado por linha, já que não exige nenhuma manipulação especial enquanto o arquivo de saída ainda estiver sendo gravado. |

## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/) pode ser usado como saída de um trabalho do Stream Analytics para fornecer uma experiência rica de visualização dos resultados da análise. Essa funcionalidade pode ser usada para painéis operacionais, geração de relatórios e relatórios orientados por métricas.

A saída do Power BI do Stream Analytics atualmente não está disponível nas regiões Azure China (21Vianet) e Azure Germany (T-Systems International).

### <a name="authorize-a-power-bi-account"></a>Autorizar uma conta do Power BI
1. Quando o Power BI é selecionado como uma saída no portal do Azure, você é solicitado a autorizar um usuário existente do Power BI ou criar uma nova conta do Power BI.  
   
   ![Autorizar usuário do Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2. Crie uma nova conta se você não ainda tiver uma e, em seguida, clique em Autorizar agora.  A página a seguir é mostrada:
   
   ![Conta do Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3. Nesta etapa, forneça a conta corporativa ou de estudante para autorizar a saída do Power BI. Se você não se inscreveu ainda no Power BI, escolha a opção Inscreva-se agora. A conta corporativa ou de estudante usada para o Power BI poderia ser diferente da conta de assinatura do Azure na qual você está conectado.

### <a name="configure-the-power-bi-output-properties"></a>Configurar as propriedades de saída do Power BI
Uma vez que a conta do Power BI foi autenticada, você pode configurar as propriedades de saída do Power BI. A tabela a seguir é a lista de nomes de propriedade e sua descrição para configurar a saída do Power BI.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para essa saída do Power BI. |
| Agrupar o workspace |Para permitir o compartilhamento de dados com outros usuários do Power BI, você pode selecionar grupos dentro de sua conta do Power BI ou escolher "Meu workspace" se não quiser gravar em um grupo.  Atualizar um grupo existente requer a renovação da autenticação do Power BI. |
| Nome do conjunto de dados |Forneça um nome de conjunto de dados que você deseja que seja usada para a saída do Power BI |
| Nome da tabela |Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |

Para obter um passo a passo de configuração de uma saída e de um painel do Power BI, consulte o artigo [Azure Stream Analytics e Power BI](stream-analytics-power-bi-dashboard.md).

> [!NOTE]
> Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e a tabela são preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a produzir a saída no Power BI. Observe que se a consulta do trabalho não gerar resultados, o conjunto de dados e a tabela não são criados. Observe também que se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes são substituídos.
> 

### <a name="schema-creation"></a>Criação de Esquema
O Stream Analytics do Azure cria um conjunto de dados do Power BI e uma tabela em nome do usuário, se eles ainda não existirem. Em todos os outros casos, a tabela é atualizada com novos valores. Atualmente, há uma limitação de que apenas uma tabela pode existir dentro de um conjunto de dados.

### <a name="data-type-conversion-from-stream-analytics-to-power-bi"></a>Conversão do tipo de dados do Stream Analytics para Power BI
O Stream Analytics do Azure atualiza o modelo de dados dinamicamente no tempo de execução se o esquema de saída mudar. Alterações de nome de coluna, alterações de tipo de coluna e a adição ou remoção de colunas são controladas.

Esta tabela abrange as conversões de tipo de dados dos [Tipos de dados do Stream Analytics](https://msdn.microsoft.com/library/azure/dn835065.aspx) para Power BIs [Tipos de EDM (Modelo de Dados de Entidade)](https://powerbi.microsoft.com/documentation/powerbi-developer-walkthrough-push-data/) se um conjunto de dados do POWER BI e uma tabela não existirem.

Do Stream Analytics | Para o Power BI
-----|-----|------------
bigint | Int64
nvarchar(max) | Cadeia de caracteres
Datetime | DateTime
flutuante | Duplo
Matriz de registro | Tipo de cadeia de caracteres, valor Constante "IRecord" ou "IArray"

### <a name="schema-update"></a>Atualização do Esquema
o Stream Analytics infere o esquema do modelo de dados com base no primeiro conjunto de eventos na saída. Posteriormente, se necessário, o esquema do modelo de dados é atualizado para acomodar os eventos de entrada que não se encaixam no esquema original.

A consulta `SELECT *` deve ser evitada para impedir que o esquema dinâmico atualize as linhas. Além das possíveis implicações de desempenho, ela também pode resultar na incerteza do tempo necessário para obter os resultados. Os campos exatos que precisam ser mostradas no painel do Power BI devem ser selecionados. Além disso, os valores de dados devem estar em conformidade com o tipo de dados escolhido.


Anterior/Atual | Int64 | Cadeia de caracteres | DateTime | Duplo
-----------------|-------|--------|----------|-------
Int64 | Int64 | Cadeia de caracteres | Cadeia de caracteres | Duplo
Duplo | Duplo | Cadeia de caracteres | Cadeia de caracteres | Duplo
Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres | Cadeia de caracteres |  | Cadeia de caracteres | 
DateTime | Cadeia de caracteres | Cadeia de caracteres |  DateTime | Cadeia de caracteres


### <a name="renew-power-bi-authorization"></a>Renovar a autorização do Power BI
Se a senha da conta Power BI for alterada depois que o trabalho do Stream Analytics foi criado ou autenticado pela última vez, você precisa autenticar novamente o Stream Analytics. Se a MFA (Autenticação Multifator) estiver configurada no locatário do AAD (Azure Active Directory), também será necessário renovar a autorização do Power BI a cada duas semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "erro de Autenticar usuário" nos Logs de Operação:

  ![Erro de token de atualização do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Para resolver esse problema, pare seu trabalho em execução e vá para a saída do Power BI.  Selecione o link **Renovar autorização** e reinicie o trabalho da **Hora da Última Interrupção** para evitar a perda de dados.

  ![Autorização de renovação do Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Armazenamento de Tabelas
O [armazenamento de Tabelas do Azure](../storage/common/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser escalado automaticamente para atender à demanda dos usuários. O Armazenamento de Tabelas é um repositório de chave/atributo NoSQL da Microsoft, que pode ser aproveitado para dados estruturados com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da tabela.

| Nome da Propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabelas. |
| Conta de armazenamento |O nome da conta de armazenamento para o qual você está enviando a saída |
| Chave da conta de armazenamento |A chave de acesso associada à conta de armazenamento. |
| Nome da tabela |O nome da tabela. A tabela será criada, se ainda não existir. |
| Chave de partição |O nome da coluna de saída que contém a chave da partição. A chave de partição é um identificador exclusivo para a partição em uma determinada tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. |
| Chave de linha |O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade em uma determinada partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. |
| Tamanho do lote |É o número de registros para uma operação em lote. O padrão (100) é suficiente para a maioria dos trabalhos. Consulte a [Especificação da operação de lote da tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar essa configuração. |
 
## <a name="service-bus-queues"></a>Filas de barramento de serviço
[Filas do barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecem entrega de mensagem do tipo PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes. Normalmente, espera-se que as mensagens sejam recebidas e processadas pelos receptores na ordem cronológica em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da fila.

| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para essa Fila de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila |O nome da fila do Barramento de Serviço. |
| Nome da política de fila |Ao criar uma fila, você também pode criar políticas de acesso compartilhado na guia Configurar fila. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de fila |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Formato de serialização do evento |Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação |Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar |Aplicável somente para o tipo JSON. Uma linha separada especifica que a saída é formatada com cada objeto JSON separado por uma nova linha. A matriz especifica que a saída é formatada como uma matriz de objetos JSON. |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="service-bus-topics"></a>Tópicos do Service Bus
Enquanto as Filas do Barramento de Serviço fornecem um método de comunicação de um-para-um do remetente para o receptor, os [Tópicos sobre o Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de comunicação de um-para-muitos.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da tabela.

| Nome da propriedade | DESCRIÇÃO |
| --- | --- |
| Alias de saída |Um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Tópico de Barramento de Serviço. |
| Namespace do Barramento de Serviço |Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico |Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estão disponíveis a menos que uma assinatura seja criada, portanto, certifique-se de que há uma ou mais assinaturas sob o tópico |
| Nome da política de tópico |Ao criar um tópico, você também pode criar políticas de acesso compartilhado na guia Configurar tópico. Cada política de acesso compartilhado tem o nome, as permissões que você definiu e as chaves de acesso |
| Chave de política de tópico |A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Formato de serialização do evento |Formato de serialização para dados de saída.  Há suporte para JSON, CSV e Avro. |
| Codificação |Se estiver usando formato CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador |Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores com suporte são vírgula, ponto e vírgula, espaço, tab e barra vertical. |

O número de partições baseia-se [no tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
O [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) é um serviço de banco de dados multimodelo distribuído globalmente que oferece dimensionamento elástico ilimitado em todo o mundo, consulta avançada e indexação automática em modelos de dados independentes de esquemas, baixa latência garantida e SLAs abrangentes líderes do setor. Para saber mais sobre opções de coleta do Cosmos DB para Stream Analytics, consulte o artigo [Stream Analytics com o Cosmos DB como saída](stream-analytics-documentdb-output.md).

A saída do Azure Cosmos DB do Stream Analytics não está disponível atualmente nas regiões Azure China (21Vianet) e Azure Germany (T-Systems International).

> [!Note]
> Neste momento, o Azure Stream Analytics oferece suporte apenas à conexão ao CosmosDB usando a **API do SQL**.
> Ainda não há suporte para outras APIs do Azure Cosmos DB. Se você apontar o Azure Stream Analytics para as contas do Azure Cosmos DB criado com as outras APIs, talvez os dados não sejam armazenados corretamente. 

A tabela a seguir descreve as propriedades para a criação de uma saída do Azure Cosmos DB.
| Nome da propriedade | Descrição |
| --- | --- |
| Alias de saída | Um alias para se referir a essa saída na sua consulta do Stream Analytics. |
| Coletor | Cosmos DB |
| Importar opção | Escolha "Selecionar Cosmos DB de sua assinatura" ou "Fornecer configurações do Cosmos DB manualmente".
| Id de conta | O nome ou o URI do ponto de extremidade da conta do Cosmos DB. |
| Chave de conta | A chave de acesso compartilhado da conta do Cosmos DB. |
| Banco de dados | O nome do banco de dados do Cosmos DB. |
| Padrão de nome da coleção | O nome da coleção ou seu padrão para as coleções a ser usado. <br/>O formato de nome da coleção pode ser construído com o token {partição} opcional, em que as partições começam em 0. Dois exemplos:  <br/>1.  _MyCollection_ – uma coleção denominada “MyCollection” deve existir.  <br/>2. _MyCollection {partition}_ – com base na coluna de particionamento. <br/>As coleções de coluna de particionamento devem existir – "MyCollection0”, “MyCollection1”, “MyCollection2” e assim por diante. |
| Chave de partição | Opcional. Isso só será necessário se você estiver usando um token {partition} no seu padrão de nome de coleção.<br/> A chave de partição é o nome do campo nos eventos de saída usado para especificar a chave para a saída do particionamento em várias coleções.<br/> Para uma saída de coleção única, nenhuma coluna de saída arbitrária pode ser usada. Por exemplo, PartitionId. |
| ID do documento |Opcional. O nome do campo em eventos de saída usado para especificar a chave primária que serve de base para as operações de inserção ou atualização.  

## <a name="azure-functions"></a>Funções do Azure
O Azure Functions é um serviço de computação sem servidor que lhe permite executar código sob demanda sem a necessidade de provisionar explicitamente ou gerenciar a infraestrutura. Ele permite que você implemente código que é disparado por eventos que ocorrem no Azure ou por serviços de terceiros.  Essa capacidade do Azure Functions para responder a gatilhos torna uma saída natural para um Azure Stream Analytics. Este adaptador de saída permite aos usuários se conectar o Stream Analytics ao Azure Functions e executar um script ou trecho de código em resposta a vários eventos.

A saída das Funções do Azure do Stream Analytics não está disponível atualmente nas regiões Azure China (21Vianet) e Azure Germany (T-Systems International).

O Azure Stream Analytics chama o Azure Functions por meio de gatilhos de HTTP. O novo Adaptador de saída do Azure Functions está disponível com as seguintes propriedades configuráveis:

| Nome da propriedade | Descrição |
| --- | --- |
| Aplicativo de funções |Nome de seu aplicativo do Azure Functions |
| Função |Nome da função em seu aplicativo do Azure Functions |
| Chave |Se você quiser usar um Azure Function de outra assinatura, você pode fazer isso fornecendo a chave para acessar sua função |
| Tamanho máximo do lote |Essa propriedade pode ser usada para definir o tamanho máximo para cada lote de saída que é enviado para o Azure Function. Por padrão, esse valor é 256 KB |
| Contagem máxima do lote  |Como o nome indica, essa propriedade permite que você especifique o número máximo de eventos em cada lote que serão enviados ao Azure Functions. O valor da contagem máxima de lote padrão é de 100 |

Quando o Azure Stream Analytics recebe a exceção 413 (Entidade de Solicitação http muito grande) da função do Azure, ele reduz o tamanho dos lotes que envia para o Azure Functions. Em seu código de função do Azure, use essa exceção para certificar-se de que o Azure Stream Analytics não envie lotes muito grandes. Além disso, certifique-se de que os valores de contagem e tamanho máximo do lote utilizados na função sejam consistentes com os valores inseridos no portal do Stream Analytics. 

Além disso, em uma situação em que não há nenhum evento caindo em uma janela de tempo, nenhuma saída é gerada. Como resultado, a função computeResult não é chamada. Esse comportamento é consistente com as funções de agregação em janelas internas.

## <a name="partitioning"></a>Particionamento

A tabela a seguir resume o suporte de partição e o número de gravadores de saída para cada tipo de saída:

| Tipo de saída | Suporte ao particionamento | Chave de partição  | Número de gravadores de saída | 
| --- | --- | --- | --- |
| Repositório Azure Data Lake | SIM | Use tokens de {data} e {hora} no padrão de prefixo de caminho. Escolha o formato de data, como DD/MM/AAAA, MM/DD/AAAA, MM-DD-AAAA. HH é usado para o formato de hora. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). | 
| Banco de Dados SQL do Azure | SIM | Baseado na cláusula PARTITION BY na consulta | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). Para saber mais sobre como obter melhor desempenho de rendimento de gravação ao carregar dados no Banco de Dados do SQL Azure, visite a [saída do Azure Stream Analytics para o Banco de Dados SQL do Azure](stream-analytics-sql-output-perf.md). | 
| Armazenamento de Blobs do Azure | SIM | Use os tokens de {data} e {hora} dos seus campos de evento no padrão do caminho. Escolha o formato de data, como DD/MM/AAAA, MM/DD/AAAA, MM-DD-AAAA. HH é usado para o formato de hora. Como parte da [versão prévia](https://aka.ms/ASApreview1), a saída de blob pode ser particionada por um atributo de evento personalizado único {fieldname} ou {datetime:\<specifier>}. | Segue o particionamento de entrada para [consultas totalmente paralelizáveis](stream-analytics-scale-jobs.md). | 
| Hub de Eventos do Azure | SIM | SIM | Varia dependendo do alinhamento da partição.</br> Quando a chave de partição de saída do Hub de Eventos é alinhada de forma igual com a etapa de consulta (anterior) de upstream, o número de gravadores é o mesmo que o de partições do Hub de Eventos de saída. Cada gravador usa [a classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender?view=azure-dotnet) do EventHub para enviar eventos para a partição específica. </br> Quando a chave de partição de saída do Hub de Eventos não é alinhada com a etapa de consulta (anterior) de upstream, o número de gravadores é o mesmo que o de partições nessa etapa anterior. Cada gravador usa a [classe SendBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync?view=azure-dotnet) do EventHubClient para enviar eventos para todas as partições de saída. |
| Power BI | Não  | Nenhum | Não aplicável. | 
| Armazenamento da tabela do Azure | SIM | Qualquer coluna de saída.  | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). | 
| Tópico do Barramento de Serviço do Azure | SIM | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições no tópico de saída.  |
| Fila do Barramento de Serviço do Azure | SIM | Escolhido automaticamente. O número de partições baseia-se no [tamanho e SKU do Barramento de Serviço](../service-bus-messaging/service-bus-partitioning.md). Chave de partição é um valor inteiro exclusivo para cada partição.| Mesmo que o número de partições na fila de saída. |
| Azure Cosmos DB | SIM | Use o token {partition} no padrão de nome de coleção. valor de {partition} baseia-se na cláusula PARTITION BY na consulta. | Segue o particionamento de entrada para as [consultas totalmente paralelizadas](stream-analytics-scale-jobs.md). |
| Funções do Azure | Não  | Nenhum | Não aplicável. | 

Se o seu adaptador de saída não estiver particionado, a falta de dados em uma partição de entrada causará um atraso até a quantidade de tempo de chegada tardia.  Nesses casos, a saída é mesclada em um único gravador, o que pode causar gargalos no pipeline. Para saber mais sobre a política de chegada tardia, visite [Considerações sobre a ordem de evento do Azure Stream Analytics](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Tamanho do lote de saída
O Azure Stream Analytics usa lotes de tamanho variável para processar eventos e gravar em saídas. Normalmente, o mecanismo do Stream Analytics não grava uma mensagem por vez e usa lotes para maior eficiência. Quando a entrada e a taxa de eventos de saída é alta, ele usa lotes maiores. Quando a taxa de saída é baixa, ele usa lotes menores para manter a latência baixa. 

A tabela a seguir explica algumas considerações para envio em lote de saída:

| Tipo de saída | Tamanho máximo de mensagem | Otimização de tamanho de lote |
| :--- | :--- | :--- | 
| Repositório Azure Data Lake | Consulte [limites de armazenamento do Data Lake](../azure-subscription-service-limits.md#data-lake-store-limits) | Até 4 MB por operação de gravação |
| Banco de Dados SQL do Azure | Máximo de 10.000 linhas por inserção em massa única</br>Mínimo de 100 linhas por inserção em massa única </br>Consulte também [limites SQL do Azure](../sql-database/sql-database-resource-limits.md) |  Cada lote é inicialmente inserido em massa com o tamanho máximo de lote e pode ser dividido em lotes pela metade (até o tamanho mínimo de lote) com base em com base em erros do SQL com nova tentativa. |
| Armazenamento de Blobs do Azure | Consulte os [limites de Armazenamento do Microsoft Azure](../azure-subscription-service-limits.md#storage-limits) | O tamanho máximo do bloco do Blob é de 4 MB</br>A contagem máxima do bloco do Blob é 50000 |
| Hub de Eventos do Azure   | 256 KB por mensagem </br>Consulte também [limites de Hubs de Eventos](../event-hubs/event-hubs-quotas.md) |    Quando o particionamento de entrada e saída não está alinhado, cada evento é individualmente compactado em um EventData e enviado em um lote com o tamanho máximo de mensagem (1 MB para o SKU Premium). </br></br>  Quando o particionamento de entrada e saída está alinhado, vários eventos são compactados em um único EventData com até o tamanho máximo da mensagem e enviados.    |
| Power BI | Consulte [limites da API Rest do Power BI](https://msdn.microsoft.com/library/dn950053.aspx) |
| Armazenamento da tabela do Azure | Consulte os [limites de Armazenamento do Microsoft Azure](../azure-subscription-service-limits.md#storage-limits) | O padrão é 100 entidades por transação única e pode ser configurado como um valor menor, conforme necessário. |
| Fila do Barramento de Serviço do Azure   | 256 KB por mensagem</br> Consulte também [Limites do Barramento de Serviço do Microsoft Azure](../service-bus-messaging/service-bus-quotas.md) | Evento único por mensagem |
| Tópico do Barramento de Serviço do Azure | 256 KB por mensagem</br> Consulte também [Limites do Barramento de Serviço do Microsoft Azure](../service-bus-messaging/service-bus-quotas.md) | Evento único por mensagem |
| Azure Cosmos DB   | Consulte [Limites do Azure Cosmos DB](../azure-subscription-service-limits.md#azure-cosmos-db-limits) | O tamanho do lote e a frequência de gravação são ajustados dinamicamente com base nas respostas do CosmosDB. </br> Sem limitações predeterminadas do Stream Analytics. |
| Funções do Azure   | | Tamanho de lote padrão é 256 KB. </br> A contagem de eventos padrão por lote é 100. </br> O tamanho do lote é configurável e pode ser aumentado ou diminuído nas [opções de saída](#azure-functions) do Stream Analytics. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]

> [Início Rápido: criar um trabalho do Stream Analytics usando o Portal do Azure](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
