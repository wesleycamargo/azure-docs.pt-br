<properties
	pageTitle="Saídas de transformação de dados: opções de armazenamento e análise | Microsoft Azure"
	description="Saiba como direcionar as saídas de transformação de dados do Stream Analytics para opções de armazenamento de dados. Além disso, use o Power BI para os resultados da análise."
	keywords="transformação de dados, resultados da análise, opções de armazenamento de dados"
	services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
	documentationCenter="" 
	authors="jeffstokes72"
	manager="paulettm" 
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="07/27/2016"
	ms.author="jeffstok"/>

# Direcionar as saídas de transformação de dados do Stream Analytics para ferramentas de análise de opções de armazenamento de dados

Ao criar um trabalho do Stream Analytics, considere como a saída do trabalho de transformação de dados será consumida. Como você exibirá os resultados do trabalho do Stream Analytics? Quais ferramentas você usará para mostrar os resultados da análise de dados? É obrigatório ter uma opção de armazenamento de dados?

Para poder habilitar vários padrões de aplicativo, o Stream Analytics do Azure disponibiliza opções diferentes de armazenamento de saída e de exibição dos resultados da análise. Isso facilita a exibição da saída do trabalho e proporciona flexibilidade no consumo e armazenamento da saída do trabalho para data warehouse e outras finalidades. Qualquer saída configurada no trabalho deve existir antes do trabalho ser iniciado e dos eventos começarem a fluir. Por exemplo, se você usar o armazenamento de Blobs como uma saída, o trabalho não criará uma conta de armazenamento automaticamente. Ele precisa ser criado pelo usuário antes do trabalho ASA ser iniciado.

## Repositório Azure Data Lake

O Stream Analytics dá suporte ao [Repositório Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). Esse armazenamento permite que você armazene dados de qualquer tamanho, tipo e velocidade de ingestão para análises operacionais e exploratórias. No momento, há suporte para a criação e configuração das saídas do Repositório Data Lake apenas no Portal Clássico do Azure. Além disso, o Stream Analytics deve estar autorizado a acessar o Repositório Data Lake. Detalhes sobre a autorização e como se inscrever para a Preview do Repositório Data Lake (se necessário) são discutidos no [artigo sobre as saídas do Data Lake](stream-analytics-data-lake-output.md).

A tabela abaixo lista os nomes de propriedade e sua descrição para a criação de uma saída do Repositório Data Lake.

<table>
<tbody>
<tr>
<td><B>NOME DA PROPRIEDADE</B></td>
<td><B>DESCRIÇÃO</B></td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Repositório Data Lake.</td>
</tr>
<tr>
<td>Conta do Repositório Data Lake</td>
<td>O nome da conta de armazenamento para o qual você está enviando a saída Você verá uma lista suspensa de contas do Repositório Data Lake às quais o usuário conectado ao portal tem acesso.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [<I>opcional</I>]</td>
<td>O caminho do arquivo usado para gravar seus arquivos na Conta do Repositório Data Lake especificada. <BR>{data}, {hora}<BR>Exemplo 1: pasta1/logs/{data}/{hora}<BR>Exemplo 2: pasta1/logs/{data}</td>
</tr>
<tr>
<td>Formato de data [<I>opcional</I>]</td>
<td>Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH.</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato for CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável somente para serialização JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## Banco de dados SQL

Um [banco de dados SQL do Azure](https://azure.microsoft.com/services/sql-database/) pode ser usado como saída para os dados que sejam relacionais por natureza ou para aplicativos que dependam de o conteúdo ser hospedado em um banco de dados relacional. Os trabalhos do Stream Analytics gravarão em uma tabela existente em um banco de dados SQL do Azure. Observe que o esquema da tabela deve corresponder exatamente aos campos e seus tipos sendo a saída do seu trabalho. Um [Azure SQL Data Warehouse](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) também pode ser especificado como uma saída por meio da opção de saída do Banco de Dados SQL (esse é um recurso na fase de visualização). A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de dados SQL.

| Nome da Propriedade | Descrição |
|---------------|-------------|
| Alias de saída | Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse banco de dados. |
| Banco de dados | O nome do banco de dados para o qual você está enviando a saída |
| Nome do Servidor | Nome do servidor de banco de dados SQL do Azure |
| Nome de Usuário | O nome de usuário que tem acesso de gravação no banco de dados |
| Senha | A senha para se conectar ao banco de dados |
| Tabela | O nome da tabela em que a saída será gravada. O nome da tabela diferencia maiúsculas de minúsculas e o esquema da tabela deve corresponder exatamente ao número de campos e seus tipos sendo gerados por sua saída de trabalho. |

> [AZURE.NOTE] Atualmente, há suporte para a oferta do Banco de Dados SQL uma saída do trabalho do Stream Analytics. No entanto, não há suporte para a execução de uma Máquina Virtual do Azure que executa o SQL Server com um banco de dados anexado. Isso está sujeito à mudança em versões futuras.

## Armazenamento de blob

O armazenamento de Blob oferece uma solução econômica e escalonável para armazenar grandes quantidades de dados não estruturados na nuvem. Para obter uma introdução sobre o Armazenamento de blob do Azure e seu uso, confira a documentação em [Como usar blobs](../storage/storage-dotnet-how-to-use-blobs.md).

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída de banco de blob.

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Alias de saída</td>
<td>Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de blob.</td>
</tr>
<tr>
<td>Conta de armazenamento</td>
<td>O nome da conta de armazenamento para o qual você está enviando a saída</td>
</tr>
<tr>
<td>Chave da conta de armazenamento</td>
<td>A chave secreta associada à conta de armazenamento.</td>
</tr>
<tr>
<td>Contêiner de armazenamento</td>
<td>Os contêineres fornecem um agrupamento lógico de blobs armazenados no serviço Blob do Microsoft Azure. Quando você carrega um blob no serviço Blob, você deve especificar um contêiner para aquele blob.</td>
</tr>
<tr>
<td>Padrão de prefixo do caminho [opcional]</td>
<td>O caminho do arquivo usado para gravar seus blobs no contêiner especificado.<BR>No caminho, você pode optar por usar uma ou mais instâncias das duas variáveis a seguir para especificar a frequência com a qual os blobs são gravados:<BR>{data}, {hora}<BR>Exemplo 1: cluster1/logs /{data}/{hora}<BR>Exemplo 2: cluster1/logs/{data}</td>
</tr>
<tr>
<td>Formato de data [opcional]</td>
<td>Se o token de data for usado no caminho do prefixo, você pode selecionar o formato de data na qual os arquivos são organizados. Exemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [opcional]</td>
<td>Se o token de hora for usado no caminho do prefixo, você pode selecionar o formato de hora na qual os arquivos são organizados. Atualmente, o único valor aceito é HH.</td>
</tr>
<tr>
<td>Formato de serialização do evento</td>
<td>Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro.</td>
</tr>
<tr>
<td>Codificação</td>
<td>Se o formato for CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical.</td>
</tr>
<tr>
<td>Formatar</td>
<td>Aplicável somente para serialização JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## Hub de evento

Os [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/) são um ingestor de eventos altamente escalonável de publicação/assinatura. Ele pode coletar milhões de eventos por segundo. Uma utilização de um Hub de Eventos como saída é quando a saída de um trabalho do Stream Analytics será a entrada de outro trabalho de streaming.

Há alguns parâmetros que são necessários para configurar fluxos de dados de Hub de Eventos como uma saída.

| Nome da Propriedade | Descrição |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse Hub de eventos. |
| Namespace do Barramento de Serviço | Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. |
| Hub de evento | O nome da sua saída de Hub de eventos |
| Nome da política do Hub de Eventos. | A política de acesso compartilhada, que pode ser criada na guia Configurar o Hub de Eventos. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do Hub de eventos | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Coluna de chave de partição [opcional] | Esta coluna contém a chave de partição para saída do Hub de Eventos. |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente para o tipo JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON. |

## Power BI

O [Power BI](https://powerbi.microsoft.com/) pode ser usado como saída de um trabalho do Stream Analytics para fornecer uma experiência rica de visualização dos resultados da análise. Essa funcionalidade pode ser usada para painéis operacionais, geração de relatórios e relatórios orientados por métricas.

### Autorizar uma conta do Power BI

1.	Quando o Power BI é selecionado como uma saída no portal de Gerenciamento do Azure, você será solicitado a autorizar um usuário existente do Power BI ou criar uma nova conta do Power BI.

    ![Autorizar usuário do Power BI](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)

2.	Crie uma nova conta se você não ainda tiver uma e, em seguida, clique em Autorizar agora. Uma tela como essa será apresentada.

    ![Conta do Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)

3.	Nesta etapa, forneça a conta corporativa ou de estudante para autorizar a saída do Power BI. Se você não se inscreveu ainda no Power BI, escolha a opção Inscreva-se agora. A conta de trabalho ou escolar usada para o Power BI poderia ser diferente da conta de assinatura do Azure na qual você está conectado.

### Configurar as propriedades de saída do Power BI

Uma vez que a conta do Power BI foi autenticada, você pode configurar as propriedades de saída do Power BI. A tabela a seguir é a lista de nomes de propriedade e sua descrição para configurar a saída do Power BI.

| Nome da Propriedade | Descrição |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para essa saída do Power BI. |
| Nome do conjunto de dados | Forneça um nome de conjunto de dados que você deseja que seja usada para a saída do Power BI |
| Nome da tabela | Forneça um nome de tabela sob o conjunto de dados da saída do Power BI. Atualmente, a saída do Power BI de trabalhos do Stream Analytics só podem ter uma tabela em um conjunto de dados. |
| Nome do grupo | Para habilitar a compartilhar dados com outros usuários do Power BI, grave os dados em grupos. Você pode selecionar grupos dentro de sua conta do Power BI ou escolher "Meu espaço de trabalho" se você não deseja gravar em um grupo. Atualizar um grupo existente requer a renovação da autenticação do Power BI. |

Para um guia passo a passo de configuração de uma saída do Power BI e do painel, consulte o artigo [Power BI e Stream Analytics do Azure](stream-analytics-power-bi-dashboard.md).

> [AZURE.NOTE] Não crie explicitamente o conjunto de dados e a tabela no painel do Power BI. O conjunto de dados e a tabela serão preenchidos automaticamente quando o trabalho é iniciado e o trabalho começa a produzir a saída no Power BI. Observe que se a consulta de trabalho não gerar resultados, o conjunto de dados e a tabela não serão criados. Tenha em mente também que se o Power BI já tiver um conjunto de dados e uma tabela com o mesmo nome fornecido no trabalho do Stream Analytics, os dados existentes serão substituídos.

### Renovar a autorização do Power BI

Você precisará autenticar novamente sua conta do Power BI caso sua senha tenha sido alterada depois de seu trabalho ser criado ou autenticado pela última vez. Se a MFA (Multi-Factor Authentication) estiver configurada no locatário do AAD (Azure Active Directory) também será necessário renovar a autorização do Power BI a cada 2 semanas. Um sintoma desse problema é nenhuma saída de trabalho e um "erro de Autenticar usuário" nos Logs de Operação:

  ![Erro de token de atualização do Power BI](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)

Para resolver esse problema, pare seu trabalho em execução e vá para a saída do Power BI. Clique no link "Renovar autorização" e reinicie o trabalho a partir da Hora da Última Interrupção para evitar a perda de dados.

  ![Autorização de renovação do Power BI](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)

## Armazenamento de tabela

[Armazenamento de Tabelas do Azure](../storage/storage-introduction.md) oferece armazenamento altamente disponível e altamente escalonável, para que um aplicativo possa ser dimensionado automaticamente para atender à demanda dos usuários. O Armazenamento de tabela é um repositório de chave/atributo NoSQL da Microsoft que pode ser utilizado por alguém com dados estruturados, com menos restrições no esquema. O armazenamento de Tabela do Azure pode ser usado para armazenar dados de persistência e para recuperação eficiente.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da tabela.

| Nome da Propriedade | Descrição |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para esse armazenamento de tabela. |
| Conta de armazenamento | O nome da conta de armazenamento para o qual você está enviando a saída |
| Chave da conta de armazenamento | A chave de acesso associada à conta de armazenamento. |
| Nome da tabela | O nome da tabela. A tabela será criada se ainda não existir. |
| Chave de partição | O nome da coluna de saída que contém a chave da partição. A chave de partição é um identificador exclusivo para a partição em uma determinada tabela que forma a primeira parte da chave primária da entidade. É um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. |
| Chave de linha | O nome da coluna de saída que contém a chave de linha. A chave de linha é um identificador exclusivo para uma entidade em uma determinada partição. Ela forma a segunda parte da chave primária da entidade. A chave de linha é um valor de cadeia de caracteres que pode ter até 1 KB em tamanho. |
| Tamanho do lote | É o número de registros para uma operação em lote. Normalmente, o padrão é suficiente para a maioria dos trabalhos; consulte a [especificação da Operação em Lote de Tabela](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) para obter mais detalhes sobre como modificar essa configuração. |

## Filas de barramento de serviço

[Filas do barramento de serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) oferecem entrega de mensagem do tipo PEPS (primeiro a entrar, primeiro a sair) para um ou mais consumidores concorrentes. Normalmente, espera-se que as mensagens sejam recebidas e processadas pelos receptores na ordem cronológica em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da fila.

| Nome da Propriedade | Descrição |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para essa fila de Barramento de Serviço. |
| Namespace do Barramento de Serviço | Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. |
| Nome da fila | O nome da fila do Barramento de Serviço. |
| Nome da política da fila | Ao criar uma fila, você também pode criar políticas de acesso compartilhado na guia Configurar fila. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política de fila | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Para CSV e JSON, UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical. |
| Formatar | Aplicável somente para o tipo JSON. Uma linha separada especifica que a saída será formatada com cada objeto JSON separado por uma nova linha. Matriz especifica que a saída será formatada como uma matriz de objetos JSON. |

## Tópicos do Service Bus

Enquanto as Filas do Barramento de Serviço fornecem um método de comunicação de um-para-um do remetente para o receptor, os [Tópicos sobre o Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh367516.aspx) fornecem uma forma de comunicação de um-para-muitos.

A tabela a seguir lista os nomes de propriedade e sua descrição para a criação de uma saída da tabela.

| Nome da Propriedade | Descrição |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de saída | Esse é um nome amigável utilizado em consultas para direcionar a saída da consulta para essa tópico de Barramento de Serviço. |
| Namespace do Barramento de Serviço | Um namespace Barramento de Serviço é um contêiner para um conjunto de entidades de mensagens. Ao criar um novo Hub de Eventos, você também criou um namespace Barramento de Serviço. |
| Nome do tópico | Tópicos são entidades de envio de mensagens, semelhantes a filas e hubs de eventos. Eles são projetados para coletar fluxos de eventos de alguns dispositivos e serviços diferentes. Quando um tópico é criado, ele também recebe um nome específico. As mensagens enviadas para um tópico não estarão disponíveis a menos que uma assinatura seja criada, portanto, certifique-se de que há uma ou mais assinaturas sob o tópico. |
| Nome da política de tópico | Ao criar um tópico, você também pode criar políticas de acesso compartilhado na guia Configurar tópico. Cada política de acesso compartilhado terá um nome, as permissões definidas por você e as chaves de acesso. |
| Chave de política do tópico | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço |
| Formato de serialização do evento | Formato de serialização para dados de saída. Há suporte para JSON, CSV e Avro. |
| Codificação | Se o formato for CSV ou JSON, uma codificação deve ser especificada. UTF-8 é o único formato de codificação com suporte no momento. |
| Delimitador | Aplicável somente à serialização de CSV. O Stream Analytics é compatível com vários delimitadores comuns para serialização de dados no formato CSV. Os valores suportados são vírgula, ponto e vírgula, espaço, tab e barra vertical. |

## Banco de Dados de Documentos

[Banco de Dados de Documentos do Azure](https://azure.microsoft.com/services/documentdb/) é um serviço de Banco de Dados de Documentos NoSQL totalmente gerenciado, que oferece transações e consulta de dados sem esquema, desempenho previsível e confiável e rápido desenvolvimento.

A tabela abaixo lista os nomes de propriedade e sua descrição para a criação de uma saída do Banco de Dados de Documentos.

<table>
<tbody>
<tr>
<td>NOME DA PROPRIEDADE</td>
<td>DESCRIÇÃO</td>
</tr>
<tr>
<td>Nome da conta</td>
<td>O nome da conta do Banco de Dados de Documentos. Ele também pode ser o ponto de extremidade da conta.</td>
</tr>
<tr>
<td>Chave de conta</td>
<td>A chave de acesso compartilhado para a conta do Banco de Dados de Documentos.</td>
</tr>
<tr>
<td>Banco de dados</td>
<td>O nome do banco de dados do Banco de Dados de Documentos.</td>
</tr>
<tr>
<td>Padrão de nome da coleção</td>
<td>O padrão de nome da coleção para as coleções a ser usado. O formato de nome da coleção pode ser construído com o token {partição} opcional, em que as partições começam em 0.<BR>Por exemplo Veja abaixo as entradas válidas:<BR>MyCollection{partição}<BR>MyCollection<BR>Observe que as coleções devem existir antes do início do trabalho do Stream Analytics e que elas não serão criadas automaticamente.</td>
</tr>
<tr>
<td>Chave de partição</td>
<td>O nome do campo nos eventos de saída usado para especificar a chave para o particionamento de saída em várias coleções.</td>
</tr>
<tr>
<td>ID do documento</td>
<td>O nome do campo em eventos de saída usado para especificar a chave primária na qual se baseiam as operações de inserção ou atualização.</td>
</tr>
</tbody>
</table>


## Obter ajuda
Para obter mais assistência, experimente nosso [Fórum do Stream Analytics do Azure](https://social.msdn.microsoft.com/Forums/pt-BR/home?forum=AzureStreamAnalytics)

## Próximas etapas
Você foi apresentado ao Stream Analytics, um serviço gerenciado para análise de streaming em dados da Internet das coisas. Para saber mais sobre esse serviço, consulte:

- [Introdução ao uso do Stream Analytics do Azure](stream-analytics-get-started.md)
- [Dimensionar trabalhos do Stream Analytics do Azure](stream-analytics-scale-jobs.md)
- [Referência de Linguagem de Consulta do Stream Analytics do Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referência da API REST do Gerenciamento do Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!---HONumber=AcomDC_0727_2016-->