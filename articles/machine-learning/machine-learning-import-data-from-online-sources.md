<properties
	pageTitle="Importar dados para o Estúdio de Aprendizado de Máquina de fontes de dados online | Microsoft Azure"
	description="Como importar os dados de treinamento para o Estúdio de Aprendizado de Máquina do Azure de diversas fontes online."
	keywords="importar dados, formato de dados, tipos de dados, fontes de dados, dados de treinamento"
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/19/2016"
	ms.author="bradsev;garye" />


# Importar os dados para o Estúdio do Aprendizado de Máquina do Azure de diferentes fontes de dados online com o módulo Importar Dados

Este artigo descreve o suporte para a importação de dados online de diversas fontes e as informações necessárias para a transmissão de dados dessas fontes para um experimento de Aprendizado de Máquina do Azure.

> [AZURE.NOTE] Este artigo fornece informações gerais sobre o módulo [Import Data][import-data] (Importar Dados). Para obter informações mais detalhadas sobre os tipos de dados que você pode acessar, formatos, parâmetros e respostas a perguntas comuns, consulte o tópico de referência do módulo [Importar Dados][import-data].

<!-- -->

[AZURE.INCLUDE [import-data-into-aml-studio-selector](../../includes/machine-learning-import-data-into-aml-studio.md)]

## Introdução

Você pode acessar dados do Estúdio de Aprendizado de Máquina do Azure de uma das várias fontes de dados online enquanto seu experimento é executado usando o módulo [Import Data][import-data] (Importar Dados):

- Uma URL da Web usando HTTP
- Hadoop usando HiveQL
- Armazenamento do blob do Azure
- Tabela do Azure
- Banco de dados SQL do Azure ou SQL Server na VM do Azure
- Banco de dados local do SQL Server
- Um provedor de feed de dados, atualmente OData
 
O fluxo de trabalho para a condução de experiências no Estúdio de Aprendizado de Máquina do Azure consiste em arrastar e soltar componentes na tela. Para acessar fontes de dados online, adicione o módulo [Import Data][import-data] (Importar Dados) ao seu experimento, escolha a **Fonte de dados** e forneça os parâmetros necessários para acessar os dados. As fontes de dados online com suporte são detalhadas na tabela a seguir. Esta tabela também resume os formatos de arquivo e os parâmetros com suporte usados para acessar os dados.

Observe que como os dados deste treinamento são acessados enquanto seu experimento está em execução, eles só estarão disponíveis nesse experimento. Por comparação, os dados que foram armazenados em um módulo do conjunto de dados estarão disponíveis para todos os experimentos em seu espaço de trabalho.

> [AZURE.IMPORTANT] Atualmente, os módulos [Import Data][import-data] (Importar Dados) e [Export Data][export-data] (Exportar dados) podem ler e gravar dados somente no armazenamento do Azure criado usando o modelo de implantação Clássico. Em outras palavras, o novo tipo de conta de Armazenamento de Blobs do Azure que oferece uma camada de acesso de armazenamento dinâmica ou uma camada de acesso de armazenamento estática ainda não é compatível.

> De modo geral, as contas de armazenamento do Azure que você possa ter criado antes de essa opção se tornar disponível não deverão ser afetadas. Se precisar criar uma nova conta, escolha **Clássico** para o modelo de implantação, ou use o Resource Manager, e para **Tipo de conta**, escolha **Uso geral** em vez de **Armazenamento de Blobs**.

> Para saber mais, confira [Armazenamento de Blobs do Azure: camadas de armazenamento dinâmica e estática](../storage/storage-blob-storage-tiers.md).



## Fontes de dados online com suporte
O módulo **Import Data** (Importar Dados) do Aprendizado de Máquina do Azure é compatível com as seguintes fontes de dados:

Fonte de dados | Descrição | Parâmetros |
---|---|---|
URL da Web via HTTP |Lê dados nos formatos de valores separados por vírgula (CSV), de valores separados por tabulação (TSV), de arquivo de relação de atributo (ARFF) e de Máquinas de Vetores de Suporte (SVM-light), de qualquer URL da Web que use HTTP | <b>URL</b>: especifica o nome completo do arquivo, incluindo a URL do site e o nome do arquivo com qualquer extensão. <br/><br/><b>Formato de dados</b>: especifica um dos formatos de dados com suporte: CSV, TSV, ARFF ou SVM-light. Se os dados tiverem uma linha de cabeçalho, serão usados para atribuir nomes de coluna.|
Hadoop/HDFS|Lê dados do armazenamento distribuído no Hadoop. Você especifica os dados desejados usando o HiveQL, uma linguagem de consulta do tipo SQL. O HiveQL também pode ser usado para agregar dados e executar a filtragem de dados antes da adição dos dados ao Estúdio de Aprendizado de Máquina.|<b>Consulta de banco de dados do Hive</b>: especifica a consulta do Hive usada para gerar os dados.<br/><br/><b>URI do servidor HCatalog </b> : especifica o nome do cluster usando o formato *&lt;nome do cluster&gt;.azurehdinsight.net.*<br/><br/><b>Nome de conta de usuário do Hadoop</b>: especifica o nome de conta de usuário do Hadoop usada para configurar o cluster.<br/><br/><b>Senha de conta de usuário do Hadoop</b> : especifica as credenciais usadas no provisionamento do cluster. Para saber mais, consulte [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-provision-clusters.md).<br/><br/><b>Local dos dados de saída</b>: especifica se os dados são armazenados em um sistema de arquivos distribuído (HDFS) do Hadoop ou no Azure. <br/><ul>Se você armazenar dados de saída no HDFS, especifique o URI do servidor HDFS. (Use o nome do cluster HDInsight sem o prefixo HTTPS://). <br/><br/>Se você armazenar os dados de saída no Azure, deverá especificar o nome da conta de armazenamento do Azure, a chave de acesso do Armazenamento e o nome do contêiner do Armazenamento.</ul>|
Banco de dados SQL |Lê os dados armazenados em um banco de dados SQL do Azure ou em um banco de dados do SQL Server em execução em uma máquina virtual do Azure. | <b>Nome do servidor de banco de dados</b>: especifica o nome do servidor no qual o banco de dados está em execução.<br/><ul>No caso do Banco de Dados SQL do Azure, insira o nome do servidor gerado. Geralmente, tem o formato *&lt;identificador\_gerado&gt;.database.windows.net.* <br/><br/>No caso de um SQL Server hospedado em uma máquina Virtual do Azure, insira *tcp:&lt;Nome DNS da Máquina Virtual&gt;, 1433*</ul><br/><b>Nome do banco de dados </b>: especifica o nome do banco de dados no servidor. <br/><br/><b>Nome de conta de usuário do servidor</b>: especifica um nome de usuário de uma conta com permissões de acesso para o banco de dados. <br/><br/><b>Senha de conta de usuário do servidor</b>: especifica a senha da conta de usuário.<br/><br/><b>Aceitar qualquer certificado do servidor</b>: use essa opção (menos segura) se quiser ignorar a revisão do certificado do site antes de ler os dados.<br/><br/><b>Consulta de banco de dados</b>: insira uma instrução SQL que descreva os dados que você deseja ler.|
Banco de dados SQL local |Lê dados armazenados em um banco de dados SQL local. | <b>Gateway de dados</b>: especifica o nome do Gateway de Gerenciamento de Dados instalado em um computador em que ele pode acessar o banco de dados do SQL Server. Para obter informações sobre como configurar o gateway, consulte [Executar análises avançadas com o Azure Machine Learning usando os dados de um banco de dados do SQL Server local](machine-learning-use-data-from-an-on-premises-sql-server.md).<br/><br/><b>Nome do servidor de banco de dados</b>: especifica o nome do servidor no qual o banco de dados está em execução.<br/><br/><b>Nome do banco de dados </b>: especifica o nome do banco de dados no servidor. <br/><br/><b>Nome de conta de usuário do servidor</b>: especifica um nome de usuário de uma conta com permissões de acesso para o banco de dados. <br/><br/><b>Nome de usuário e senha</b>: clique em <b>Inserir valores</b> para inserir suas credenciais de banco de dados. Você pode usar a Autenticação Integrada do Windows ou a Autenticação do SQL Server dependendo de como o SQL Server local está configurado.<br/><br/><b>Consulta de banco de dados</b>: insira uma instrução SQL que descreva os dados que você quer ler.|
Tabela do Azure|Lê os dados do serviço Tabela no Armazenamento do Azure.<br/><br/>Caso você leia grandes quantidades de dados com pouca frequência, use o serviço Tabela do Azure. Ele fornece uma solução de armazenamento flexível, não relacional (NoSQL), massivamente escalonável, barata e de alta disponibilidade.| As opções em **Import Data** (Importar Dados) variam de acordo com o que você está acessando: informações públicas ou uma conta de armazenamento privada que exija credenciais de logon. Isso é determinado pelo <b>Tipo de Autenticação</b>, que pode ter um valor "PublicOrSAS" ou "Account", cada um deles com seu próprio conjunto de parâmetros. <br/><br/><b>URI de Assinatura de Acesso Público ou Compartilhado (SAS)</b>: os parâmetros são:<br/><br/><ul><b>URI da Tabela</b>: especifica a URL Pública ou do SAS da tabela.<br/><br/><b>Especifica as linhas a serem examinadas em busca de nomes de propriedade</b>: os valores são <i>TopN</i> para examinar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela. <br/><br/>Se os dados forem homogêneos e previsíveis, é recomendável que você selecione *TopN* e insira um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam de acordo com a profundidade e a posição da tabela, escolha a opção *ScanAll* para examinar todas as linhas. Isso garante a integridade de sua propriedade resultante e da conversão de metadados.<br/><br/></ul><b>Conta de Armazenamento Privada</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: especifica o nome da conta que contém a tabela a ser lida.<br/><br/><b>Chave de conta</b>: especifica a chave de armazenamento associada à conta.<br/><br/><b>Nome da tabela</b> : especifica o nome da tabela que contém os dados a serem lidos.<br/><br/><b>Linhas a serem examinadas em busca de nomes de propriedade</b>: os valores são <i>TopN</i> para examinar o número especificado de linhas ou <i>ScanAll</i> para obter todas as linhas na tabela.<br/><br/>Se os dados forem homogêneos e previsíveis, recomendamos que você selecione *TopN* e insira um número para N. Para tabelas grandes, isso pode resultar em tempos de leitura mais rápidos.<br/><br/>Se os dados forem estruturados com conjuntos de propriedades que variam de acordo com a profundidade e a posição da tabela, escolha a opção *ScanAll* para examinar todas as linhas. Isso garante a integridade da propriedade resultante e da conversão de metadados.<br/><br/>|
Armazenamento do Blobs do Azure | Lê os dados armazenados no serviço Blob no Armazenamento do Azure, incluindo imagens, texto não estruturado ou dados binários.<br/><br/>Você pode usar o serviço Blob para expor dados publicamente ou para armazenar dados de aplicativo de forma privada. Você pode acessar seus dados de qualquer lugar usando as conexões HTTP ou HTTPS. | As opções no módulo **Import Data** (Importar Dados) variam de acordo com o que você está acessando: informações públicas ou uma conta de armazenamento privada que exija credenciais de logon. Isso é determinado pelo <b>Tipo de Autenticação</b> que pode ter um valor "PublicOrSAS" ou "Account".<br/><br/><b>URI da Assinatura de Acesso Público ou Compartilhado (SAS)</b>: os parâmetros são:<br/><br/><ul><b>URI</b>: especifica a URL Pública ou SAS do blob de armazenamento.<br/><br/><b>Formato de Arquivo</b>: especifica o formato dos dados no serviço Blob. Os formatos com suporte são CSV, TSV e ARFF.<br/><br/></ul><b>Conta de Armazenamento Privada</b>: os parâmetros são: <br/><br/><ul><b>Nome da conta</b>: especifica o nome da conta que contém o blob que você deseja ler.<br/><br/><b>Chave de conta</b>: especifica a chave de armazenamento associada à conta.<br/><br/><b>Caminho para o contêiner, diretório ou blob </b> : especifica o nome do blob que contém os dados a serem lidos.<br/><br/><b>Formato de arquivo de blob</b>: especifica o formato dos dados no serviço de blob. Os formatos de dados com suporte são CSV, TSV, ARFF, CSV com uma codificação especificada e o Excel. <br/><br/><ul>Se o formato for CSV ou TSV, indique se o arquivo contém uma linha de cabeçalho.<br/><br/>Você pode usar a opção do Excel para ler dados de pastas de trabalho do Excel. Na opção <i>formato de dados do Excel</i>, indique se os dados estão em um intervalo de planilha do Excel ou em uma tabela do Excel. Na opção <i>planilha do Excel ou tabela inserida </i>, especifique o nome da planilha ou da tabela que você deseja ler.</ul><br/>|
Provedor de feed de dados | Lê dados de um provedor de feeds com suporte. Atualmente, somente o formato OData (Open Data Protocol) tem suporte. | <b>Tipo de conteúdo de dados</b>: especifica o formato OData.<br/><br/><b>URL de Origem</b>: especifica a URL completa para o feed de dados. <br/>Por exemplo, a URL a seguir lê do banco de dados de exemplo Northwind: http://services.odata.org/northwind/northwind.svc/|


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[export-data]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C/

<!---HONumber=AcomDC_0921_2016-->