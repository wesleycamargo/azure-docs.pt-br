<properties 
	pageTitle="Notas de versão: Emulador do Microsoft HDInsight para Azure | Azure" 
	description="Obtenha as últimas informações sobre as versões mais recentes do Emulador Hadoop do HDInsight." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/03/2014" 
	ms.author="jgao"/>




# Notas de versão: Emulador do Microsoft HDInsight para Azure 



> [AZURE.NOTE] 
> A maneira mais fácil de verificar o número da versão é procurar em Adicionar ou Remover Programas na entrada do "Emulador do Microsoft HDInsight para o Azure" (para a versão 1.0.0.0 ou maior) ou no "Microsoft HDInsight Developer Preview" (para versões inferiores à 1.0.0.0). 

## v2.0.0.0, liberado em 29/8/2014

* Essa versão atualiza o Emulador do HDInsight visando o mesmo conjunto de projetos do Hadoop que está atualmente ativo no serviço na versão 3.1.    

* Como com as versões de visualização desse produto, esta versão continua a ser destinada para cenários de desenvolvedor e só oferece suporte a implantações de nó único. 

### O que há de novo? 
 
* [Versões de componentes do Hadoop atualizadas](http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-component-versioning/) correspondentes à versão 3.1 do serviço.  Isso inclui suporte ao Hive 0.13 e Tez.

## v1.0.0.0, lançada em 28/10/2013

* Esta é a versão disponível do Emulador do Microsoft HDInsight para Azure, anteriormente conhecida como Microsoft HDInsight Developer Preview. 

* Como com as versões de visualização desse produto, esta versão continua a ser destinada para cenários de desenvolvedor e só oferece suporte a implantações de nó único. 

### O que há de novo? 
 
* Foram adicionados scripts para simplificar as configurações de todos os serviços do Apache Hadoop para início automático ou manual. O padrão, como antes, ainda será automático, mas todos os serviços agora podem ser alterados usando os scripts set-onebox-manualstart.cmd ou set-onebox-autostart.cmd que estão instalados no C:\Hadoop. 

* O número de dependências de instalações necessárias foram reduzidas significativamente, permitindo instalações mais rápidas. 

* Correção de bug no comando usado para executar os exemplos de Pig no script RunSamples.ps1 instalado na pasta GettingStarted. 

* Essa versão contém uma atualização para o Hortonworks Data Platform versão 1.1 que corresponde aos serviços disponíveis do Hortonworks Data Platform com a versão 1,6 do cluster Azure HDInsight. 

## v0.11.0.0, lançada em 30/09/2013

### O que há de novo? 
		
* O painel do HDInsight foi removido. 

* Esta versão contém uma atualização para o Hortonworks Data Platform Developer Preview que corresponde ao Hortonworks Data Platform Preview no Azure HDInsight. 

## v0.10.0.0, lançada em 09/08/2013

### O que há de novo? 
	
* Esta versão contém uma atualização para o Hortonworks Data Platform Developer Preview que corresponde ao Hortonworks Data Platform Preview no Azure HDInsight.

## v0.8.0.0, lançada em 07/06/2013

### O que há de novo? 

* Esta versão contém uma atualização para o Hortonworks Data Platform Developer Preview que corresponde ao Hortonworks Data Platform Preview no Azure HDInsight.

## v0.6.0.0, lançada em 13/05/2013

### O que há de novo? 

* O Hive Server 2 agora está sendo instalado. Isso é necessário para a nova versão 0.9.2.0 do Microsoft ODBC Driver para o Hive que foi lançado ao mesmo tempo que essa atualização. 

* Todos os serviços estão configurados para inicialização automática, portanto, não há mais necessidade de iniciar tudo de novo após a reinicialização do computador. 

## v0.4.0.0, lançada em 25/03/2013

### O que há de novo? 

* Nova versão do Microsoft HDInsight Developer Preview, bem como do Hortonworks Data Platform para Windows Developer Preview. 

* Inclui o Apache Hadoop, o Hive, o Pig, o Sqoop, o Oozie, o HCatalog e o Templeton. 

* Novo Painel do Microsoft HDInsight com os seguintes recursos: 
 
* Conecte-se a vários clusters, incluindo a instalação local, bem como aos executados remotamente usando o Serviço do Azure HDInsight. 
 
* Para obter mais informações sobre o Serviço do HDInsight, consulte [http://azure.microsoft.com/pt-br/documentation/services/hdinsight/](http://azure.microsoft.com/pt-br/documentation/services/hdinsight/).

* Configure o WASB no cluster local

* Consulte as instruções detalhadas abaixo.

* Crie e edite consultas do Hive no novo console interativo do Hive.

* Exiba e baixe o histórico e os resultados dos trabalhos.

### Notas de versão 

Números de porta 

* Os pontos de extremidade da API REST em uma instalação local do HDInsight e o Serviço do Azure HDInsight são acessados por números de porta diferentes para os mesmos serviços: 

	Local: 
	Oozie: http://localhost:11000/oozie/v1/admin/status 
	Templeton: http://localhost:50111/templeton/v1/status 
	ODBC: use a porta 10000 na configuração do DNS ou na cadeia de conexão. 

	Serviço do HDInsight: 
	Oozie: http://ServerFQDN:563/oozie/v1/admin/status 
	Templeton: http://ServerFQDN:563/templeton/v1/status 
	ODBC: use a porta 563 na configuração do DNS ou na cadeia de conexão. 


* Configurar o ASV no cluster local: 

	No painel, você verá um cluster padrão local chamado "local (hdfs)". Se você desejar o ASV como seu armazenamento para a sua instalação local, faça o seguinte: 

	1. Adicione a marca da conta no core-site.xml localizado em C:\Hadoop\hadoop-1.1.0-SNAPSHOT\conf:       

			<property>
        		<name>fs.azure.account.key.{AccountName}</name>
        		<value>{Key}</value>
     		</property>

			<property>
        		<name>fs.default.name</name>
       			<!-- cluster variant -->
       	 		<value>asv://ASVContainerName@ASVAccountName</value>
        		<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
        		<final>true</final>
      		</property>

     		<property>
        		<name>dfs.namenode.rpc-address</name>
        		<value>hdfs://localhost:8020</value>
        		<description>A base for other temporary directories.</description>
      		</property>
      
		Exemplo:       

			<property>
    			<name>fs.azure.account.key.MyHadoopOnAzureAccountName</name>
				<value>8T45df897/a5fSox1yMjYkX66CriiGgA5zptSXpPdG4o1Qw2mnbbWQN1E9i/i7kihk6FWNhvLlqe02NXEVw6rP==</value>
    		</property>

			<property>
				<name>fs.default.name</name>
				<!-- cluster variant -->
				<value>asv://MyASVContainer@MyASVAccount</value>
				<description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
				<final>true</final>
			</property>
        

	2. Abra o shell de comando do Hadoop na sua área de trabalho no modo elevado e execute o seguinte comando:
	 
	 		%HADOOP_NODE%\stop-onebox.cmd && %HADOOP_NODE%\start-onebox.cmd

	3. Access any file on that account using the full URI: asv://{container}@{account}/{path} (or asvs:// if you want to use HTTPS for accessing the data). Exemplo:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Exclua o cluster local registrado atualmente e registre-o novamente com as novas credenciais do ASV. 


## v0.3.0.0, lançada em 13/12/2012 

* O site do painel foi alterado para autenticação anônima em vez de usar as credenciais do Windows. Isso elimina o problema com o prompt de logon mencionado nas notas da versão anterior. 

* Corrigidos alguns bugs do Sqoop com exportação e alguns tipos de importação. 

### Problemas da versão 

* O console do JavaScript falha ao carregar, consulte as notas da versão 0.2.0.0 para obter detalhes. 
* A linha de comando do Sqoop exibirá avisos conforme mostrado abaixo. Isso será corrigido em uma atualização futura e pode ser ignorado com segurança. 
	
		c:\Hadoop\sqoop-1.4.2\bin>sqoop version 
		Setting HBASE_HOME to 
		Warning: HBASE_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\hbase-0.94.2] does not exist HBase imports will fail. 
		Please set HBASE_HOME to the root of your HBase installation. 
		Setting ZOOKEEPER_HOME to 
		Warning: ZOOKEEPER_HOME [c:\hadoop\hadoop-1.1.0-SNAPSHOT\zookeeper-3.4.3] does not exist 
		Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation. 
		Sqoop 1.4.2 
		git commit id 3befda0a456124684768348bd652b0542b002895 
		Compiled by  on Thu 11/29/2012- 3:26:26.10

## v0.2.0.0, lançada em 03/12/2012

* Introdução ao controle de versão semântico para o MSI 

* Correções para vários bugs de instalação relatados nos fóruns do MSDN, principalmente em relação à instalação do Painel do HDInsight 

* Itens adicionados ao menu Iniciar para maior detectabilidade 

* Correção para entrada de várias linhas no console do Hive 

* Atualizações secundárias para conteúdo de introdução 

### Problemas da versão 

* O console do JavaScript falha ao carregar 

	* Em algumas instalações, o console do JavaScript falhará com um erro HTTP 404 exibido na página. Para resolver o problema, navegue diretamente até http://localhost:8080 para usar o console. 

* Navegar para o painel do HDInsight gera um prompt de logon 

	* Tivemos alguns relatos de uma caixa de diálogo de logon sendo gerada ao navegar para o Painel do HDInsight. Nesse caso, você pode fornecer as informações de logon para seu usuário atual e poderá navegar até o painel de controle. 


## v1.0.0.0, lançada em 23/10/2012

* Versão inicial 

### Problemas da versão 

* Console do Hive 

	* Se uma newline for incluída no comando do Hive enviado, você receberá um "erro de sintaxe". Remova as newlines e a consulta deverá ser executada conforme o esperado. 



## Problemas gerais conhecidos

* Expiração da senha do usuário do Hadoop 

	A senha do usuário do Hadoop pode expirar, de acordo com suas políticas do AD enviadas por push para o computador. O script do PowerShell a seguir definirá a senha para não expirar e pode ser executado de um prompt de comando administrativo. 	

		$username = "hadoop"
		$ADS_UF_DONT_EXPIRE_PASSWD = 0x10000 # (65536, from ADS_USER_FLAG_ENUM enumeration)

		$computer = [ADSI]("WinNT://$ENV:COMPUTERNAME,computer")
		$users = $computer.psbase.children | where { $_.psbase.schemaclassname -eq "User" }

		foreach($user in $users)
		{
			if($user.Name -eq $username)
			{
				$user.UserFlags = $ADS_UF_DONT_EXPIRE_PASSWD
        		$user.SetInfo()
 
        		$user.PasswordExpired = 0
        		$user.SetInfo()
 
        		Write-Host "$username user maintenance completed. "
    		}
		}


* Diretório Temp> 
	
	O hadoop.tmp.dir aponta para o local errado: em vez de apontar para C:\hadoop\hdfs, aponta para c:\hdfs. Esse bug será corrigido na próxima atualização de bits do HDP. 

* Restrições do sistema operacional 

	O Servidor do HDInsight deve ser instalado em um sistema operacional de 64 bits 

* O HDInsight não pode ser encontrado nos resultados da pesquisa WebPI. 

	Isso normalmente é devido a uma restrição do sistema operacional. O HDInsight requer um sistema operacional de 64 bits com uma versão mínima do Windows 7 Service Pack 1, o Windows Server 2008 R2 Service Pack1, o Windows 8 ou o Windows Server 2012.

* Documentação do prompt de comando administrativo 

	*  Para executar comandos, como o **hadoop mradmin** ou o **hadoop defadmin**, você deve executar como o usuário do hadoop. 

	* Para criar facilmente um shell em execução como usuário, abra um prompt de comando do Hadoop e execute o seguinte: 
	 
	 		start-hadoopadminshell

	* Isso abrirá um novo shell de comando em execução com privilégios de administrador do Hadoop 

##<a name="nextsteps"></a> Próximas etapas

- [Introdução ao emulador do HDInsight][hdinsight-get-started-emulator]


[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/

<!--HONumber=42-->
