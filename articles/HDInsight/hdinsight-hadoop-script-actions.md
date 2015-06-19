<properties 
	pageTitle="Desenvolvimento de Ação de Script com o HDInsight | Microsoft Azure" 
	description="Saiba como personalizar os clusters do Hadoop com a Ação de Script." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Desenvolvimento de Ação de Script com o HDInsight 

A Ação de Script oferece funcionalidade de HDInsight do Azure que é usada para instalar software adicional em execução em um cluster do Hadoop ou para alterar a configuração de aplicativos instalados em um cluster. As Ações de Script são scripts executados em nós de cluster quando clusters HDInsight são implantados e são executados quando os nós no cluster concluírem a configuração do HDInsight. A Ação de script é executado sob privilégios de conta de administrador do sistema e concede direitos de acesso completo a nós do cluster. Cada cluster pode ser fornecido com uma lista de ações de script que serão executados na ordem em que forem especificados.

A Ação de Script pode ser implantada no PowerShell do Azure ou usando o SDK .NET do HDInsight. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize].



## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de scripts

Ao desenvolver um script personalizado para um cluster HDInsight, há várias práticas recomendadas para se considerar:

* [Verificar a versão do Hadoop](#bPS1)
* [Fornecer links estáveis para recursos de script](#bPS2)
* [Certifique-se de que o script de personalização do cluster seja idempotente](#bPS3)
* [Instalar componentes personalizados no local ideal ](#bPS4)
* [Garantir alta disponibilidade da arquitetura de cluster](#bPS5)
* [Configurar os componentes personalizados para usar armazenamento de Blob do Azure](#bPS6)

### <a name="bPS1"></a>Verificar a versão do Hadoop
Somente o HDInsight versão 3.1 (Hadoop 2.4) e acima suportam o uso da Ação de Script para instalar componentes personalizados em um cluster. Em seu script personalizado, você deve usar o método auxiliar **Get-HDIHadoopVersion** para verificar a versão do Hadoop antes de prosseguir com a execução de outras tarefas no script.


### <a name="bPS2"></a>Fornecer links estáveis para recursos de script 
Os usuários devem ter certeza de que todos os scripts e outros artefatos usados na personalização de um cluster permaneçam disponíveis durante a vida útil do cluster e que as versões desses arquivos não sejam alterados durante este período. Esses recursos serão necessários se a nova geração de imagens de nós no cluster for necessária. A prática recomendada é baixar e arquivar tudo em uma conta de armazenamento que o usuário controle. Essa pode ser a conta de armazenamento padrão ou qualquer uma das contas de armazenamento adicionais especificadas no momento da implantação de um cluster personalizado. Em nossos exemplos fornecidos de cluster personalizado de Spark e R, por exemplo, fizemos uma cópia local dos recursos nessa conta de armazenamento: https://hdiconfigactions.blob.core.windows.net/.


### <a name="bPS3"></a>Certifique-se de que o script de personalização do cluster seja idempotente
Você deve esperar que os nós de um cluster HDInsight sejam recriados durante o ciclo de vida do cluster. O script de personalização do cluster é executado sempre que um cluster for recriado. Esse script deve ser projetado para ser personalizado para ser idempotente no sentido de que, após a nova geração de imagens, o script deve garantir que o cluster retorne para o mesmo estado em que se encontrava após o script ser executado pela primeira vez quando o cluster foi inicialmente criado. Por exemplo, se um script personalizado instalou um aplicativo em D:\\AppLocation em sua primeira execução, em cada execução subsequente, ao ser recriado, o script deve verificar se o aplicativo existe no local D:\\AppLocation antes de prosseguir com outras etapas no script.


### <a name="bPS4"></a>Instalar componentes personalizados no local ideal 
Quando nós de cluster serão recriados, a unidade de recurso C:\\ e a unidade do sistema D:\\ podem ser reformatadas, resultando na perda de dados e aplicativos que estavam instalados nessas unidades. Isso também pode ocorrer se um nó de VM do Azure que é parte do cluster falhar e for substituído por um novo nó. Você pode instalar componentes na unidade D:\\ ou no local C:\\apps no cluster. Todos os outros locais na unidade C:\\ são reservados. Especifique o local onde bibliotecas ou aplicativos serão instalados no script de personalização do cluster.


### <a name="bPS5"></a>Garantir alta disponibilidade da arquitetura de cluster

HDInsight tem uma arquitetura ativa-passiva para alta disponibilidade, na qual um nó principal está no modo ativo (onde os serviços do HDInsight estão em execução) e outro nó principal está em modo de espera (em que os serviços do HDInsight não estão em execução). Os nós alternam entre os modos ativo e passivo se serviços HDInsight são interrompidos. Se uma ação de script é usada para instalar os serviços em ambos os nós principais para alta disponibilidade, observe que o mecanismo de failover do HDInsight não poderá realizar automaticamente o failover desses serviços instalados pelo usuário. Portanto, os serviços instalados pelo usuário nos nós principais do HDInsight, dos quais se espera alta disponibilidade, devem ter seu próprio mecanismo de failover se estiverem em modo ativo-passivo, ou então devem estar no modo ativo-ativo.

Um comando de Ação de Script do HDInsight é executado em ambos os nós principais quando a função do nó principal é especificada como um valor no parâmetro *ClusterRoleCollection* (documentado a seguir na seção [Como executar uma Ação de Script](#runScriptAction)). Então, quando você cria um script personalizado, verifique se o script está ciente dessa configuração. Você não deve encontrar problemas onde os mesmos serviços estiverem instalados e iniciados em ambos os nós principais, e esses serviços acabarem competindo entre si. Além disso, esteja ciente de que dados serão perdidos durante a recriação, então o software instalado usando as Ações de Script deve ser resiliente a tais eventos. Os aplicativos devem ser projetados para trabalhar com dados altamente disponíveis que são distribuídos através de vários nós. Observe que até 1/5 dos nós em um cluster pode ser uma nova imagem ao mesmo tempo.


### <a name="bPS6"></a>Configurar os componentes personalizados para usar armazenamento de Blob do Azure
Os componentes personalizados que você instala em nós de cluster podem ter uma configuração padrão para usar o armazenamento HDFS (Sistema de Arquivos Distribuído do Hadoop). Você deve alterar a configuração para usar, em vez disso, o armazenamento de Blob do Azure. Em uma recriação de cluster, o sistema de arquivos HDFS é formatado e você pode perder todos os dados armazenados ali. Usar o armazenamento de Blob do Azure em seu lugar assegura que seus dados serão mantidos.

## <a name="helpermethods"></a>Métodos auxiliares para scripts personalizados 

A Ação de Script fornece os seguintes métodos auxiliares que podem ser usados durante a gravação de scripts personalizados.

Método auxiliar | Descrição
-------------- | -----------
**Save-HDIFile** | Baixe um arquivo do URI (identificador de recurso uniforme) especificado para um local no disco local que está associado com o nó da VM do Azure atribuído ao cluster.
**Expand-HDIZippedFile** | Descompacte um arquivo compactado por zip.
**Invoke-HDICmdScript** | Execute um script por meio de cmd.exe.
**Write-HDILog** | Grave a saída do script personalizado usado para uma ação de script.
**Get-Services** | Obtenha uma lista de serviços em execução no computador em que o script é executado.
**Get-Service** | Com o nome de serviço específico como entrada, obtenha informações detalhadas para um serviço específico (nome do serviço, ID de processo, estado, etc.) no computador em que o script é executado.
**Get-HDIServices** | Obtenha uma lista de serviços do HDInsight em execução na máquina em que o script é executado.
**Get-HDIService** | Com o nome de serviço do HDInsight específico como entrada, obtenha informações detalhadas para um serviço específico (nome do serviço, ID de processo, estado, etc.) no computador em que o script é executado.
**Get-ServicesRunning** | Obtenha uma lista de serviços em execução no computador no qual o script é executado.
**Get-ServiceRunning** | Verifique se um serviço específico (por nome) está em execução no computador onde o script é executado.
**Get-HDIServicesRunning** | Obtenha uma lista de serviços do HDInsight em execução na máquina em que o script é executado.
**Get-HDIServiceRunning** | Verifique se um serviço do HDInsight específico (por nome) está em execução no computador onde o script é executado.
**Get-HDIHadoopVersion** | Obter a versão do Hadoop instalado no computador onde o script é executado.
**Test-IsHDIHeadNode** | Verifique se o computador onde o script é executado é um nó principal.
**Test-IsActiveHDIHeadNode** | Verifique se o computador onde o script é executado é um nó principal ativo.
**Test-IsHDIDataNode** | Verifique se o computador onde o script é executado é um nó de dados.
**Edit-HDIConfigFile** | Edite arquivos de configuração hive-site.xml, core-site.xml, hdfs-site.xml, mapred-site.xml ou yarn-site.xml.

## <a name="commonusage"></a>Padrões comuns de uso

Esta seção fornece orientações sobre como implementar alguns dos padrões comuns de uso que você pode encontrar ao escrever seu próprio script personalizado.

### Configurando variáveis de ambiente

Com frequência no desenvolvimento de ação de script, você sentirá a necessidade de definir variáveis de ambiente. Por exemplo, um cenário mais provável é quando você baixar um binário de um site externo, instalá-lo no cluster e adicionar o local de onde ele foi instalado à sua variável de ambiente “PATH”. O trecho a seguir mostra como definir variáveis de ambiente no script personalizado.

	Write-HDILog "Starting environment variable setting at: $(Get-Date)";
	[Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Essa instrução define a variável de ambiente **MDS_RUNNER_CUSTOM_CLUSTER** para o valor “true” e também define o escopo dessa variável como todo o computador. Às vezes é importante que as variáveis de ambiente sejam definidas no escopo apropriado – computador ou usuário. Consulte [aqui](https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx "aqui") para obter mais informações sobre como definir variáveis de ambiente.

### Acesso a locais onde estão armazenados os scripts personalizados

Scripts usados para personalizar um cluster precisam estar na conta de armazenamento padrão para o cluster ou então em um contêiner público somente leitura em qualquer outra conta de armazenamento. Se o seu script acessa recursos localizados em outro lugar, estes precisam acessíveis publicamente (pelo menos somente leitura público). Por exemplo, você pode desejar acessar um arquivo e salvá-lo usando o comando SaveFile-HDI.

	Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

### Lançar exceção para implantação de cluster malsucedida

Se você quiser ser notificado com precisão sobre o fato de que a personalização do cluster não obteve êxito conforme o esperado, é importante lançar uma exceção e não realizar o provisionamento do cluster. Por exemplo, você talvez queira processar um arquivo caso ele exista e lidar com o caso de erro quando o arquivo não existe. Isso garante que o script seja encerrado normalmente e que o estado correto do cluster seja conhecido. O trecho a seguir fornece um exemplo de como fazer isso:

	If(Test-Path($SomePath)) {
		#Process file in some way
	} else {
		# File does not exist; handle error case
		# Print error message
	exit
	}

## <a name="deployScript"></a>Lista de verificação para implantação de uma ação de script
Aqui estão as etapas que utilizamos ao se preparar para implantar esses scripts:

1. Coloque os arquivos que contêm os scripts personalizados em um local que seja acessível pelos nós de cluster durante a implantação. Esse local pode ser qualquer uma das contas de armazenamento padrão ou adicionais especificadas no momento da implantação de cluster, ou qualquer outro contêiner de armazenamento acessível publicamente.
2. Adicione as verificações em scripts para certificar-se de que elas são executadas de forma idempotencial, para que o script possa ser executado várias vezes no mesmo nó.
3. Use o cmdlet **Write-Output** do Azure PowerShell para gravar em STDOUT, bem como em STDERR. Não use **Write-Host**.
4. Use uma pasta de arquivo temporário, como $env:TEMP, para manter o arquivo baixado usado pelos scripts e, em seguida, limpá-los depois dos scripts serem executados.
5. Instale o software personalizado apenas em D:\\ ou C:\\apps. Outros locais na unidade C: não devem ser usados pois estão reservados. Observe que instalar os arquivos na unidade C: fora da pasta C:\\apps pode resultar em falhas de instalação durante as recriações do nó.
6. Caso as configurações de nível de sistema operacional ou arquivos de configuração de serviço do Hadoop sejam alterados, talvez você deseje reiniciar os serviços do HDInsight para que eles possam pegar qualquer configuração de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts.


## <a name="runScriptAction"></a>Como executar uma Ação de Script

Você pode usar Ações de Script para personalizar os clusters HDInsight usando o Portal do Azure, o Azure PowerShell ou o SDK do .NET do HDInsight. Para obter instruções, consulte [Como usar a Ação de Script](../hdinsight-hadoop-customize-cluster/#howto).


## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes em um cluster HDInsight. Os scripts de exemplo e instruções sobre como usá-los estão disponíveis nos links abaixo:

- [Instalar e usar o Spark em clusters HDInsight][hdinsight-install-spark]
- [Instalar e usar R em clusters Hadoop do HDInsight][hdinsight-r-scripts]
- [Instalar e usar o Solr em clusters HDInsight](../hdinsight-hadoop-solr-install)
- [Instalar e usar o Giraph em clusters HDInsight](../hdinsight-hadoop-giraph-install)  

> [AZURE.NOTE]Os scripts de exemplo funcionam apenas com o cluster HDInsight versão 3.1 ou posterior. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](../hdinsight-component-versioning/).

## <a name="testScript"></a>Como testar seu script personalizado com o emulador do HDInsight

Uma maneira direta de testar um script personalizado antes de usá-lo no comando de Ação de Script HDInsight é executá-lo no emulador do HDInsight. Você pode instalar o emulador do HDInsight localmente ou em uma VM Windows Server 2012 R2 de IaaS (infraestrutura como um serviço) do Azure, ou ainda em um computador local; então, observe se o script funciona corretamente. Observe que a VM Windows Server 2012 R2 é a mesma VM que usa o HDInsight para seus nós.

Esta seção descreve o procedimento para usar o Emulador do HDInsight localmente para fins de teste, mas o procedimento para usar uma VM é semelhante.

**Instalar o Emulador do HDInsight** - Para executar Ações de Script localmente, você precisa ter o Emulador do HDInsight instalado. Para obter instruções sobre como instalá-lo, consulte [Introdução ao Emulador do HDInsight](../hdinsight-get-started-emulator/).

**Definir a política de execução de PowerShell do Azure** - abra o Microsoft Azure PowerShell e execute (como administrador) o comando a seguir para definir a política de execução para a *LocalMachine* e para ser *Irrestrita*:
 
	Set-ExecutionPolicy Unrestricted –Scope LocalMachine

Precisamos que essa política seja irrestrita, já que os scripts não são assinados.

**Baixe a Ação de Script** que você deseja executar em um destino local. Os scripts de exemplo a seguir estão disponíveis para download nos seguintes locais:

* **Spark**. https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1
* **R**. https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1
* **Solr**. https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1
* **Giraph**. https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

**Executar a ação de script** - Abra um novo PowerShell do Azure no modo de administrador e execute o script de instalação do Spark ou R por meio da localização local onde eles foram salvos.

**Exemplos de uso** Quando você estiver usando os clusters Spark e R, arquivos de dados necessários podem não estar presentes no Emulador do HDInsight. Portanto, você precisará carregar arquivos .txt relevantes que contenham dados para um caminho no HDFS e, em seguida, usar esse caminho para acessar os dados. Por exemplo:

	val file = sc.textFile("/example/data/gutenberg/davinci.txt")


Observe que em alguns casos, um script personalizado, na verdade, pode depender dos componentes do HDInsight, para detectar se determinados serviços do Hadoop estão funcionando. Nesse caso, você precisará testar seus scripts personalizados, implantando-os em um cluster HDInsight real.


## <a name="debugScript"></a>Como depurar seu script personalizado

Os logs de erros de script são armazenados juntamente com outra saída, na conta de armazenamento padrão especificada para o cluster na sua criação. Os logs são armazenados em uma tabela de nome *u<\\fragmento-do-nome-do-cluster><\\carimbo-de-data-e-hora>setuplog*. Eles são logs agregados com registros de todos os nós (nós principais e de trabalho) nos quais o script é executado no cluster.

Você pode também pode realizar acesso remoto aos nós de cluster para ver ambos STDOUT e o STDERR para scripts personalizados. Os logs em cada nó são específicos apenas para esse nó, e são registrados em **C:\\HDInsightLogs\\DeploymentAgent.log**. Esses arquivos de log registram todas as saídas por meio do script personalizado. Um exemplo de trecho do log para uma Ação de Script de Spark deve ser assim:

	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...

	Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;
	
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;
	...
	
	Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; 
	Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.; 
	Version : 2.1.0.0; 
	ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692; 
	AzureVMName : HEADNODE0; 
	IsException : False; 
	ExceptionType : ; 
	ExceptionMessage : ; 
	InnerExceptionType : ; 
	InnerExceptionMessage : ; 
	Exception : ;

 
Nesse log, é claro que a ação de script de Spark foi executada na VM denominada HEADNODE0 e que nenhuma exceção foi lançada durante a execução.

Se ocorre uma falha na execução, a saída que o descreve também estará contida nesse arquivo de log. As informações fornecidas nesses logs devem ser úteis ao depurar problemas de script que possam surgir.


## <a name="seeAlso"></a>Consulte também

[Personalizar os clusters HDInsight usando a Ação de Script][hdinsight-cluster-customize]


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--HONumber=54--> 