---
title: 'Depurar Hadoop no HDInsight: exibir logs e interpretar mensagens de erro | Microsoft Docs'
description: Saiba quais são as mensagens de erro que você pode receber ao administrar o HDInsight usando o PowerShell, e as etapas que podem ser executadas para recuperação.
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: mumian
documentationcenter: ''

ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
ms.author: jgao

---
# Analisar logs do HDInsight
Cada cluster Hadoop no Azure HDInsight tem uma conta de armazenamento do Azure usada como o sistema de arquivos padrão. Essa conta de armazenamento é conhecida como a Conta de armazenamento padrão. O cluster usa o Armazenamento de Tabelas do Azure e o Armazenamento de Blobs na conta de armazenamento padrão para armazenar seus logs. Para descobrir a conta de armazenamento padrão para o cluster, consulte [Gerenciar clusters do Hadoop no HDInsight](hdinsight-administer-use-management-portal.md#find-the-default-storage-account). Os logs são mantidos na Conta de armazenamento mesmo após a exclusão do cluster.

## Logs gravados em Tabelas do Azure
Os logs gravados em Tabelas do Azure fornecem informações sobre o que está acontecendo com um cluster HDInsight.

Quando você cria um cluster HDInsight, seis tabelas são criadas automaticamente para clusters baseados em Linux no Armazenamento de Tabelas padrão:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Três tabelas são criadas para clusters baseados no Windows:

* setuplog: log de eventos/exceções encontrados no provisionamento/configuração de clusters do HDInsight.
* hadoopinstalllog: log de eventos/exceções encontrados durante a instalação do Hadoop no cluster. Essa tabela pode ser útil na depuração de problemas relacionados aos clusters criados com parâmetros personalizados.
* hadoopservicelog: log de eventos/exceções registrados por todos os serviços do Hadoop. Essa tabela pode ser útil na depuração de problemas relacionados a falhas de trabalho em clusters do HDInsight.

Os nomes de arquivo da tabela são **u<NomeDoCluster>DDMonYYYYatHHMMSSsss<NomeDaTabela>**.

Essas tabelas contêm os seguintes campos:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Mensagem
* N
* PreciseTimeStamp
* Função
* RowIndex
* Locatário
* TIMESTAMP
* TraceLevel

### Ferramentas para acessar os logs
Há muitas ferramentas disponíveis para acesso dos dados nessas tabelas:

* Visual Studio
* Gerenciador de Armazenamento do Azure
* Power Query para Excel

#### Usar o Power Query para Excel
O Power Query pode ser instalado do site [www.microsoft.com/pt-BR/download/details.aspx?id=39379](http://www.microsoft.com/pt-BR/download/details.aspx?id=39379). Consulte a página de download para saber os requisitos do sistema

**Para usar o Power Query para abrir e analisar o log do serviço**

1. Abra o **Microsoft Excel**.
2. No menu **Power Query**, clique em **Do Azure** e clique em **Do Armazenamento de Tabelas do Microsoft Azure**.
   
    ![HDInsight Hadoop Excel PowerQuery abrir o Armazenamento de Tabelas do Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Digite o nome da conta de armazenamento. Pode ser o nome curto ou o FQDN.
4. Insira a chave da conta de armazenamento. Você verá uma lista de tabelas:
   
    ![Logs do HDInsight Hadoop armazenados no Armazenamento de Tabelas do Azure](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Clique com o botão direito do mouse na tabela hadoopservicelog no painel **Navegador** e escolha **Editar**. Você deverá ver quatro colunas. Opcionalmente, exclua as colunas **Chave de Partição**, **Chave de Linha** e **Carimbo de Data/Hora** selecionando-as e clicando em **Remover Colunas** nas opções na faixa de opções.
6. Clique no ícone de expansão na coluna Conteúdo para escolher as colunas que você deseja importar para a planilha do Excel. Para esta demonstração, escolhi TraceLevel e ComponentName: elas podem me dar algumas informações básicas sobre quais componentes apresentaram problemas.
   
    ![Logs do HDInsight Hadoop escolher colunas](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Clique em **OK** para importar os dados.
8. Escolha as colunas **TraceLevel**, Função e **ComponentName** e clique no controle **Agrupar por** na faixa de opções.
9. Clique em **OK** na caixa de diálogo Agrupar por
10. Clique em **Aplicar e Fechar**.

Agora você pode usar o Excel para filtrar e classificar conforme o necessário. Obviamente, convém incluir outras colunas (por exemplo, Mensagem) para analisar problemas quando eles ocorrerem. Porém, a seleção e agrupamento das colunas descritas acima fornece uma imagem decente do que está acontecendo com os serviços do Hadoop. A mesma ideia pode ser aplicada às tabelas setuplog e hadoopinstalllog.

#### Usar o Virtual Studio
**Para usar o Virtual Studio**

1. Abra o Visual Studio.
2. No menu **Exibir**, clique em **Cloud Explorer**. Ou simplesmente clique em **CTRL+\\, CTRL+X**.
3. No **Cloud Explorer**, escolha **Tipos de Recurso**. A outra opção disponível é **Grupos de Recursos**.
4. Expanda **Contas de Armazenamento**, a conta de armazenamento padrão para o cluster e **Tabelas**.
5. Clique duas vezes em **hadoopservicelog**.
6. Adicione um filtro. Por exemplo:
   
        TraceLevel eq 'ERROR'
   
    ![Logs do HDInsight Hadoop escolher colunas](./media/hdinsight-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Para saber mais sobre como construir filtros, consulte [Construir cadeias de caracteres de filtro para o Designer de Tabela](../vs-azure-tools-table-designer-construct-filter-strings.md).

## Logs gravados no Armazenamento de Blobs do Azure
[Os logs gravados em Tabelas do Azure](#log-written-to-azure-tables) fornecem informações sobre o que está acontecendo com um cluster HDInsight. No entanto, essas tabelas não fornecem logs no nível da tarefa, que podem ser úteis para a análise mais detalhada dos problemas, quando eles ocorrerem. Para fornecer esse nível de detalhes, os clusters HDInsight são configurados para gravar logs de tarefas em sua conta de Armazenamento de Blobs para qualquer trabalho enviado por meio do Templeton. Na prática, isso significa os trabalhos enviados usando os cmdlets do Microsoft Azure PowerShell ou as APIs de envio de trabalho do .NET, não os trabalhos enviados por meio de acesso ao cluster por RDP/linha de comando.

Para exibir os logs, confira [Acessar os logs de aplicativo YARN no HDInsight baseado em Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md).

Para saber mais sobre os logs de aplicativos, confira [Como simplificar o gerenciamento de logs do usuário e o acesso no YARN](http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).

## Exibir logs de trabalho e integridade do cluster
### Acessar a interface de usuário do Hadoop
No Portal do Azure, clique em um nome de cluster HDInsight para abrir a folha do cluster. Na folha do cluster, clique em **Painel**.

![Iniciar painel do cluster](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard.png)

Quando solicitado, insira as credenciais de administrador do cluster. No Console de Consulta que se abre, clique em **IU do Hadoop**.

![Iniciar a interface do usuário do Hadoop](./media/hdinsight-debug-jobs/hdi-debug-launch-dashboard-hadoop-ui.png)

### Acessar a interface do usuário do Yarn
No Portal do Azure, clique em um nome de cluster HDInsight para abrir a folha do cluster. Na folha do cluster, clique em **Painel**. Quando solicitado, insira as credenciais de administrador do cluster. No Console de Consulta que se abre, clique em **IU DO YARN**.

Você pode usar a interface do usuário do YARN para fazer o seguinte:

* **Obter o status do cluster**. No painel esquerdo, expanda **Cluster** e clique em **Sobre**. Isso apresenta detalhes de status do cluster como memória alocada total, núcleos usados, o estado do gerenciador de recursos de cluster, versão do cluster, etc.
  
    ![Iniciar painel do cluster](./media/hdinsight-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Obtenha o status do nó**. No painel esquerdo, expanda **Cluster** e clique em **Nós**. Isso lista todos os nós no cluster, o endereço HTTP de cada nó, os recursos alocados para cada nó, etc.
* **Monitore o status do trabalho**. No painel esquerdo, expanda **Cluster**, e, em seguida, clique em **aplicativos** para listar todos os trabalhos no cluster. Se você quiser examinar os trabalhos em um estado específico (como novo, enviado, em execução, etc.), clique no link apropriado em **Aplicativos**. Você pode seguir clicando no nome do trabalho para saber mais detalhes sobre ele, como saída, logs, etc.

### Acessar a interface do usuário do HBase
No Portal do Azure, clique em um nome de cluster HBase do HDInsight para abrir a folha do cluster. Na folha do cluster, clique em **Painel**. Quando solicitado, insira as credenciais de administrador do cluster. No Console de Consulta aberto, clique em **IU do HBase**.

## Códigos de erro do HDInsight
As mensagens de erro detalhadas nesta seção são fornecidas para ajudar os usuários do Hadoop no Azure HDInsight a entenderem possíveis condições de erro que podem encontrar ao administrar o serviço usando o Azure PowerShell e para avisá-los sobre as etapas que podem ser executadas para se recuperar do erro.

Algumas dessas mensagens de erro também podem ser vistas no Portal do Azure quando ele for usado para gerenciar clusters HDInsight. Mas outras mensagens de erro que você pode encontrar são menos granulares devido às restrições nas ações corretivas possíveis neste contexto. Outras mensagens de erro são fornecidas nos contextos onde a atenuação é óbvia.

### <a id="AtleastOneSqlMetastoreMustBeProvided"></a>AtleastOneSqlMetastoreMustBeProvided
* **Descrição**: forneça os detalhes do Banco de Dados SQL do Azure para pelo menos um componente para usar as configurações personalizadas para as metastores do Hive e do Oozie.
* **Atenuação**: o usuário precisa fornecer um metastore válido do SQL Azure e repetir a solicitação.

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Descrição**: não foi possível criar o cluster na região *nameOfYourRegion*. Use uma região do HDInsight válida e repita a solicitação.
* **Atenuação**: o cliente deve criar a região do cluster que atualmente dá suporte a ele: Sudeste da Ásia, Europa Ocidental, Norte da Europa, Leste dos EUA ou Oeste dos EUA.

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Descrição**: o servidor não pôde localizar o registro do cluster solicitado.
* **Atenuação**: repita a operação.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Descrição**: o nome DNS do cluster *yourDnsName* é inválido. O nome deve começar e terminar com caracteres alfanuméricos e pode conter apenas o caractere especial '-'
* **Atenuação**: verifique se você usou um nome DNS válido para seu cluster que comece e termine com alfanuméricos e que não contenha caracteres especiais além do traço '-' e, em seguida, repita a operação.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Descrição**: o nome do Cluster *yourClusterName* não está disponível. Escolha outro nome.
* **Atenuação**: o usuário deve especificar um clustername exclusivo e que não exista e repetir. Se o usuário estiver usando o Portal, a interface do usuário irá notificá-lo se um nome de cluster já estiver sendo usado durante as etapas de criação.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Descrição**: a senha do cluster é inválida. A senha deve ter, pelo menos, 10 caracteres e deve conter, no mínimo, um número, uma letra maiúscula, uma letra minúscula e um caractere especial sem espaços e não pode contar o nome do usuário.
* **Atenuação**: forneça uma senha válida do cluster e repita a operação.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Description**: o nome de usuário do cluster é inválido. Verifique se o nome do usuário não contém caracteres especiais ou espaços.
* **Atenuação**: forneça um nome de usuário válido e repita a operação.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Descrição**: o nome DNS do cluster *yourDnsClusterName é inválido*. O nome deve começar e terminar com caracteres alfanuméricos e pode conter apenas o caractere especial '-'
* **Atenuação**: forneça um nome de usuário DNS do cluster válido e repita a operação.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Descrição**: o nome do contêiner no URI *yourcontainerURI* e o nome DNS *yourDnsName* no corpo da solicitação devem ser o mesmo.
* **Atenuação**: verifique se o nome do contêiner e o seu nome DNS são os mesmos e repita a operação.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Descrição**: a configuração do cluster é inválida. Não é possível encontrar nenhuma definição de nó de dados no tamanho do nó.
* **Atenuação**: repita a operação.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Descrição**: falha na exclusão da implantação do cluster
* **Atenuação**: repita a operação de exclusão.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Descrição**: erro de configuração de serviço. Informações de mapeamento de DNS necessárias não encontradas.
* **Atenuação**: exclua e crie um novo cluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Descrição**: tentativa de criação de contêiner de cluster duplicado. Existe um registro para *nameOfYourContainer* mas as Etags não coincidem.
* **Atenuação**: forneça um nome exclusivo para o contêiner e repita a operação de criação.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Descrição**: o serviço hospedado *nameOfYourHostedService* já contém um cluster. Um serviço hospedado não pode conter vários clusters
* **Atenuação**: hospede o cluster em outro serviço hospedado.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Descrição**: o servidor não pôde atualizar o estado da implantação do cluster.
* **Atenuação**: repita a operação. Se isso acontecer várias vezes, entre em contato com o CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Descrição**: o cluster *yourClusterName* foi excluído como parte da manutenção. Recrie o cluster.
* **Atenuação**: recrie o cluster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Descrição**: a configuração do cluster é inválida. Configuração de nó de cabeçalho necessária não encontrada nos tamanhos de nós.
* **Atenuação**: repita a operação.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Descrição**: não é possível criar o serviço hospedado *nameOfYourHostedService*. Tente novamente a solicitação.
* **Atenuação**: repita a solicitação.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Descrição**: o serviço hospedado *nameOfYourHostedService* já contém uma implantação em produção. Um serviço hospedado não pode conter várias implantações em produção. Tente novamente a solicitação com um nome de cluster diferente.
* **Atenuação**: use outro nome de cluster e repita a solicitação.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Descrição**: o serviço hospedado *nameOfYourHostedService* do cluster não pôde ser encontrado.
* **Atenuação**: se o cluster estiver em estado de erro, exclua-o e tente novamente.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Descrição**: o serviço hospedado *nameOfYourHostedService* não tem uma implantação associada.
* **Atenuação**: se o cluster estiver em estado de erro, exclua-o e tente novamente.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Descrição**: a SubscriptionId *yourSubscriptionId* não tem núcleos restantes para criar o cluster *yourClusterName*. Obrigatório: *resourcesRequired*, disponível: *resourcesAvailable*.
* **Atenuação**: libere recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Descrição**: a ID de assinatura *yourSubscriptionId* não tem cota para um novo HostedService para criar o cluster *yourClusterName*.
* **Atenuação**: libere recursos em sua assinatura ou aumente os recursos disponíveis para a assinatura e tente criar o cluster novamente.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Descrição**: o servidor encontrou um erro interno. Tente novamente a solicitação.
* **Atenuação**: repita a solicitação.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Descrição**: o local de armazenamento do Azure *dataRegionName* não é um local válido. Verifique se a região está correta e tente novamente a solicitação.
* **Atenuação**: selecione um local de armazenamento que dê suporte ao HDInsight, verifique se o cluster está colocalizado e repita a operação.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Descrição**: o tamanho da VM é inválido para os nós de dados. Somente o tamanho 'VM Grande' tem suporte para todos os nós de dados.
* **Atenuação**: especifique o tamanho de nó com suporte para o nó de dados e repita a operação.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Descrição**: o tamanho da VM é inválido para o nó de cabeçalho. Apenas o tamanho 'VM Extragrande' tem suporte para o nó de cabeçalho.
* **Atenuação**: especifique o tamanho de nó com suporte para o nó de cabeçalho e repita a operação.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Descrição**: a ID de assinatura de *yourSubscriptionId* que está sendo usada não tem permissões suficientes para executar a operação de exclusão para o cluster *yourClusterName*.
* **Atenuação**: se o cluster estiver em estado de erro, remova-o e tente novamente.

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Descrição**: o nome do contêiner de blob da conta de armazenamento externa *yourContainerName* é inválido. Verifique se o nome começa com uma letra e contém somente letras minúsculas, números e hífen.
* **Atenuação**: especifique um nome de contêiner de blob da conta de armazenamento válido e repita a operação.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Descrição**: a configuração da conta de armazenamento externa *yourStorageAccountName* é necessária para que os detalhes da chave secreta sejam definidos.
* **Atenuação**: especifique uma chave secreta válida para a conta de armazenamento e repita a operação.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Descrição**: a versão do cabeçalho *yourVersionHeader* não está no formato válido de aaaa-mm-dd.
* **Atenuação**: especifique um formato válido para a versão do cabeçalho e repita a solicitação.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Descrição**: a configuração do cluster é inválida. Encontrada mais de uma configuração de nó de cabeçalho.
* **Atenuação**: edite a configuração para que apenas um nó de cabeçalho seja especificado.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Descrição**: a operação não pôde ser concluída dentro do tempo permitido ou do número máximo de tentativas possível. Tente novamente a solicitação.
* **Atenuação**: repita a solicitação.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Descrição**: o parâmetro *yourParameterName* não pode ser nulo ou vazio.
* **Atenuação**: especifique um valor válido para o parâmetro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Descrição**: uma ou mais das entradas se solicitação de criação de cluster não são válidas. Verifique se os valores de entrada estão corretos e tente novamente a solicitação.
* **Atenuação**: verifique se os valores de entrada estão corretos e repita a solicitação.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Descrição**: a capacidade da região não está disponível para a região *yourRegionName* e para a ID da assinatura *yourSubscriptionId*.
* **Atenuação**: especifique uma região que dê suporte a clusters HDInsight. As regiões com suporte público: Sudeste da Ásia, Europa Ocidental, Norte da Europa, Leste dos EUA ou oeste dos EUA.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Descrição**: a conta de armazenamento *yourStorageAccountName* está na região *currentRegionName*. Deveria ser igual à região do cluster *yourClusterRegionName*.
* **Atenuação**: especifique uma conta de armazenamento na mesma região em que seu cluster está ou, se os seus dados já estiverem na conta de armazenamento, crie um novo cluster na mesma região da conta de armazenamento existente. Se você estiver usando o Portal, a interface do usuário irá notificá-lo sobre esse problema com antecedência.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Descrição**: a ID da assinatura *yourSubscriptionId* fornecida não está ativa.
* **Atenuação**: reative sua assinatura ou obtenha uma nova assinatura válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Descrição**: a ID da assinatura *yourSubscriptionId* não pôde ser encontrada.
* **Atenuação**: verifique se sua ID de assinatura é válida e repita a operação.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Descrição**: não é possível resolver o *yourDnsUrl* do DNS. Verifique se foi fornecida a URL totalmente qualificada do ponto de extremidade do blob.
* **Atenuação**: forneça uma URL de blob válida. A URL DEVE ser totalmente válida, incluindo começar com *http://* e terminar com *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Descrição**: não é possível verificar a localização do recurso *yourDnsUrl*. Verifique se foi fornecida a URL totalmente qualificada do ponto de extremidade do blob.
* **Atenuação**: forneça uma URL de blob válida. A URL DEVE ser totalmente válida, incluindo começar com *http://* e terminar com *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Descrição**: a capacidade da versão não está disponível para a versão *specifiedVersion* e para a ID da assinatura *yourSubscriptionId*.
* **Atenuação**: escolha uma versão que esteja disponível e repita a operação.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Descrição**: não há suporte para a versão *specifiedVersion*.
* **Atenuação**: escolha uma versão que tenha suporte e repita a operação.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Descrição**: a versão *specifiedVersion* não está disponível na região do Azure *specifiedRegion*.
* **Atenuação**: escolha uma versão que tenha suporte na região especificada e repita a operação.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Descrição**: a configuração do cluster é inválida. Configuração da conta WASB necessária não encontrada em contas externas.
* **Atenuação**: verifique se a conta existe e está corretamente especificada na configuração e repita a operação.

## Próximas etapas
* [Usar os modos de exibição do Ambari para depurar trabalhos do Tez no HDInsight](hdinsight-debug-ambari-tez-view.md)
* [Habilitar despejos heap para serviços do Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Gerenciar clusters HDInsight usando a interface de usuário do Ambari Web](hdinsight-hadoop-manage-ambari.md)

<!---HONumber=AcomDC_0914_2016-->