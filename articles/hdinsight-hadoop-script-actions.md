<properties linkid="customize HDInsight cluster" urlDisplayName="Use Script Actions in HDInsight to customize Hadoop clusters" pageTitle="Desenvolvimento de Ação de Script com o HDInsight| Azure" metaKeywords ="" description="Saiba como personalizar os clusters de Hadoop com a ação de script." metaCanonical="" services="hdinsight" documentationCenter="" title="Script Action Development with HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="bradsev" />

# Desenvolvimento (visualização) de Ação de Script com o HDInsight 

A visualização de Ação de Script fornece a funcionalidade de HDInsight do Azure que é usada para instalar software adicional ou para alterar a configuração de aplicativos executados em um cluster Hadoop usando scripts do PowerShell. Ações de Script são scripts executados em nós de cluster quando clusters HDInsight são implantados e são executados quando os nós no cluster concluirem a configuração do HDInsight. A ação de script é executado sob privilégios de conta de administrador do sistema e concede direitos de acesso completo a nós do cluster. Cada cluster pode ser fornecido com uma lista de ações de script para executar que serão executados na ordem em que forem especificados.

Ação de Script pode ser implantada no PowerShell do Azure ou usando o SDK .NET do HDInsight.  Para obter mais informações, consulte [Personalizar cluster HDInsight usando a ação de script][hdinsight-cluster-customize].


## Neste artigo

- [Práticas recomendadas para o desenvolvimento de scripts](#bestPracticeScripting)
- [Lista de verificação para implantação de uma Ação de Script](#deployScript)
- [Como testar o script personalizado](#testScript)
- [Como executar uma Ação de Script](#runScriptAction)
- [Como depurar seu script personalizado](#debugScript)
- [Exemplos de script personalizado](#sampleScripts) 
- [Confira também](#seeAlso)


## <a name="bestPracticeScripting"></a>Práticas recomendadas para o desenvolvimento de scripts

Ao desenvolver um script personalizado para um cluster HDInsight, há várias práticas recomendadas para se considerar:

### Fornecer estável links para recursos de script 
Os usuários devem ter certeza de que todos os scripts e outros artefatos usados na personalização de um cluster permaneçam disponíveis durante a vida útil do cluster e que as versões desses arquivos não sejam alterados durante este período. Esses recursos serão necessário se a nova geração de imagens de nós no cluster for solicitada. A prática recomendada é fazer o download e arquivar tudo em uma conta de armazenamento que o usuário controle. O que pode ser a conta de armazenamento padrão ou qualquer uma das contas de armazenamento adicionais especificados na implantação do cluster.
Em nossos exemplos fornecidos de personalização de Spark e cluster, por exemplo, fizemos uma cópia local dos recursos nessa conta de armazenamento: https://hdiconfigactions.blob.core.windows.net/.

### O script de personalização do cluster deve ser idempotentes
Você deve esperar que os nós de um cluster HDInsight sejam recriados durante o ciclo de vida do cluster. O script de personalização do cluster é executado sempre que um cluster for recriado. Esse script deve ser projetado para ser personalizado para ser idempotente no sentido de que, após a nova geração de imagens, o script deve garantir que o cluster retorne para o mesmo estado que se encontrava depois que o script foi executado pela primeira vez quando o cluster foi criado. Por exemplo, se um script personalizado instalou um aplicativo em D:\AppLocation em sua primeira execução, em cada execução subsequente, ao ser recriado, o script deve verificar se o aplicativo existe no local D:\AppLocation antes de prosseguir com outras etapas no script.

###O local onde os componentes personalizados são instalados 
Quando os nós de cluster são recriados, C:\ (unidade de recurso) e D:\ (unidade do sistema) podem ser reformatados resultando na perda de dados e de plicativos que estão instalados nessas unidades. Isso também pode ocorrer se um nó de VM do Azure que é parte do cluster falhar e for substituído por um novo nó. Você pode instalar componentes na unidade D: / ou no local c:\apps no cluster. Todos os outros locais na unidade C:\ são reservados para arquivos do sistema. O local onde você deseja instalar aplicativos ou bibliotecas é especificado no script de personalização do cluster. 


###Arquitetura de cluster
Para garantir a alta disponibilidade, o HDInsight tem, por padrão, dois headnodes: um no modo ativo (onde os serviços HDInsight estão em execução) e o outro no modo de espera (onde os serviços HDInsight não estão em execução). Eles alternarão o modo se os serviços HDInsight forem interrompidos. O comando de ação de script do HDInsight é executado em ambas as headnodes quando a função headnode é especificada no parâmetro *ClusterRoleCollection* (documentado acima). Então quando você cria seus scripts personalizados, verifique se o script está ciente dessa configuração. Por exemplo, você não deve executar problemas onde os mesmos serviços estiverem instalados e iniciados em ambos os headnodes e acabam competindo entre si. Além disso, os dados serão perdidos durante a nova imagem e então o software instalado usando as Ações de Script deve ser resiliente a tais eventos. Os aplicativos devem ser projetados para trabalhar com dados altamente disponíveis que são distribuídos através de vários nós e, portanto, ser capaz de recuperar todos os dados. Observe que até 1/5 dos nós em um cluster pode ser uma nova imagem ao mesmo tempo.

## <a name="deployScript"></a>Lista de verificação para implantação de uma Ação de Script
Aqui estão as etapas que utilizamos ao se preparar para implantar esses scripts:

1. Coloque os arquivos que contêm os scripts personalizados em um local que seja acessível pelos nós de cluster durante a implantação.
2. Adicione as verificações em scripts para certificar-se de que elas são executadas de forma idempotencial, para que o script possa ser executado várias vezes no mesmo nó.
3. Use a função de Write-Output do Powershell para mprimir em STDOUT, bem como para STDERR.
4. Use uma pasta de arquivo temporário, como $env:TEMP para manter o arquivo baixado usado pelos scripts e, em seguida, limpá-los depois dos scripts serem executados.
5. Instale o software personalizado apenas nos seguintes locais: D:, C:/apps. Outros locais na unidade C: não devem ser usados pois estão reservados para uso do sistema. Observe que instalar os arquivos na unidade C: fora da pasta C:/apps pode resultar em falhas de instalação durante a nova imagem do nó.
6. No caso de configurações de nível de sistema operacional ou arquivos de configuração de serviço do Hadoop serem alterados, talvez você deseje reiniciar os serviços do HDInsight para que eles possam pegar qualquer configuração de nível de sistema operacional, como as variáveis de ambiente definidas nos scripts.

## <a name="testScript"></a>Como testar o script personalizado

Uma maneira direta de testar um script personalizado antes de usá-lo no comando Ação do Script do HDInsight é executá-lo manualmente em uma máquina virtual do Windows Server 2012 R2 do Azure IaaS ou em um computador local e observe se ele funciona corretamente. O Windows Server 2012 R2 é a mesma VM que o HDInsight usa para os seus nós. 

Em alguns casos, um script personalizado, na verdade, pode depender dos componentes do HDInsight, para detectar se determinados serviços do Hadoop estão funcionando. Nesse caso, você precisará testar seus scripts personalizados, implantando-os em um cluster HDInsight real.

## <a name="runScriptAction"></a>Como executar uma Ação de Script

Esta seção mostra como usar os comandos do PowerShell do HDInsight para executar uma única Ação de Script e várias Ações de Script. Para usar esses comandos, você deve ter o PowerShell instalado e configurado. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

Use o seguinte comando do PowerShell para executar uma única Ação de Script ao implantar um cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name TestScriptAction -Uri http://test.com/test.ps1 -Parameters test -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Use o seguinte comando do PowerShell para executar várias Ações de Script ao implantar um cluster HDInsight:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name TestScriptAction1 -Uri http://test.com/test1.ps1 -Parameters test1 -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name TestScriptAction2 -Uri http://test.com/test2.ps1 -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config


As entradas para o comando de ação de script do HDInsight **AzureHDInsightScriptAction Add** estão descritas nas tabelas a seguir:


<table border="1">
<tr><th>-Config</th><td>O objeto de configuração em que a informações da ação de script é adicionada.</td></tr>
<tr><td>Aliases</td><td>Nenhum</td></tr>
<tr><td>Obrigatório?</td><td>Verdadeiro</td></tr>
<tr><td>Position?</td><td>0</td></tr>
<tr><td>Valores padrão</td><td>Nenhum</td></tr>
<tr><td>Aceita entrada do Pipeline?</td><td>Verdadeiro (por valor)</td></tr>
<tr><td>Aceitar caracteres curinga?</td><td>False</td></tr>
</table>


<table border="1">
<tr><th>-Name</th><td>Nome da ação de script.</td></tr>
<tr><td>Aliases</td><td>Nenhum</td></tr>
<tr><td>Obrigatório?</td><td>Verdadeiro</td></tr>
<tr><td>Position?</td><td>1</td></tr>
<tr><td>Valores padrão</td><td>Nenhum</td></tr>
<tr><td>Aceita entrada do Pipeline?</td><td>False</td></tr>
<tr><td>Aceitar caracteres curinga?</td><td>False</td></tr>
</table>


<table border="1">
<tr><th>-ClusterRoleCollection</th><td>A lista de nós de cluster no qual o script será executado.</td></tr>
<tr><td>Aliases</td><td>Nenhum</td></tr>
<tr><td>Obrigatório?</td><td>Verdadeiro</td></tr>
<tr><td>Position?</td><td>2</td></tr>
<tr><td>Valores padrão</td><td>Nenhum</td></tr>
<tr><td>Aceita entrada do Pipeline?</td><td>False</td></tr>
<tr><td>Aceitar caracteres curinga?</td><td>False</td></tr>
</table>


<table border="1">
<tr><th>-Uri</th><td>O URI para o script que será executado.</td></tr>
<tr><td>Aliases</td><td>Nenhum</td></tr>
<tr><td>Obrigatório?</td><td>Verdadeiro</td></tr>
<tr><td>Position?</td><td>3</td></tr>
<tr><td>Valores padrão</td><td>Nenhum</td></tr>
<tr><td>Aceita entrada do Pipeline?</td><td>False</td></tr>
<tr><td>Aceitar caracteres curinga?</td><td>False</td></tr>
</table>


<table border="1">
<tr><th>-Parameters</th><td>Os parâmetros de entrada para o script que será executado.</td></tr>
<tr><td>Aliases</td><td>Nenhum</td></tr>
<tr><td>Obrigatório?</td><td>False</td></tr>
<tr><td>Position?</td><td>4</td></tr>
<tr><td>Valores padrão</td><td>Nenhum</td></tr>
<tr><td>Aceita entrada do Pipeline?</td><td>False</td></tr>
<tr><td>Aceitar caracteres curinga?</td><td>False</td></tr>
</table>


## <a name="debugScript"></a>Como depurar seu script personalizado

Os logs de erros de script são armazenados juntamente com outra saída, na conta de armazenamento padrão especificada para o cluster na sua criação. Os logs são armazenados em uma tabela com o nome *u<\cluster-name-fragment><\time-stamp>setuplog*. Eles são logs agregados com registros de todos os nós (nós headnode e de trabalho) no qual o script é executado no cluster.

O STDOUT e STDERR de scripts personalizados são registrados em C:\HDInsightLogs\DeploymentAgent.log no nó onde os scripts personalizados foram executados. Um exemplo de trecho do log para uma Ação de Script de Spark deve ser assim:

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

**Iniciando a instalação de Spark no: 04/09/2014 21:46:02 feito com a instalação do Spark em: 09/04/2014 21:46:38;**

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

 
Nesse log, é claro que a ação de script de Spark foi executada na VM denominada HEADNODE0 e nenhuma exceção foi lançada durante a execução. Além disso, a parte em **negrito** é o STDOUT para esta ação de script. 

Se ocorre uma falha na execução, a saída que o descreve também estará contida nesse arquivo de log. As informações fornecidas nesses logs devem ser útil ao depurar problemas de script que possam surgir.

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar Spark e R no HDInsight. Os scripts de exemplo para Spark e R estão localizados em:

* https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv01/spark-installer-v01.ps1
* https://hdiconfigactions.blob.core.windows.net/rconfigactionv01/r-installer-v01.ps1

Os tópicos que descrevem como usar esses scripts para instalar Spark e R em clusters HDInsight são fornecidos aqui:

- [Instalar e usar o Spark 1.0 em clusters HDInsight (visualização)][hdinsight-install-spark]
- [Instalar e usar R em clusters Hadoop do HDInsight (visualização)][hdinsight-r-scripts]

> [WACOM.NOTE] O script de exemplo funciona apenas com o cluster HDInsight versão 3.1. Para obter mais informações sobre as versões do cluster HDInsight, consulte [Versões do cluster HDInsight](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/).

## <a name="seeAlso"></a>Consulte também

[Personalizar os clusters HDInsight usando a Ação de Script (visualização)][hdinsight-cluster-customize] 


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
[powershell-install-configure]: ../install-configure-powershell/

<!--HONumber=35.2-->
