<properties 
	pageTitle="Notas de versão: Emulador do Microsoft HDInsight para o Azure | Microsoft Azure" 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>




# Notas de versão: Emulador do Microsoft HDInsight para o Azure 



> [AZURE.NOTE]A maneira mais fácil de verificar o número de versão é abrir Adicionar/Remover Programas e localizar a entrada **Emulador do Microsoft HDInsight para o Azure** (para a versão 1.0.0.0 ou posterior) ou **Microsoft HDInsight Developer Preview** (para versões anteriores à 1.0.0.0).

## Versão 2.0.0.0, lançada em 29 de agosto de 2014

* Essa versão atualiza o Emulador do HDInsight visando o mesmo conjunto de projetos do Hadoop que está atualmente ativo no serviço na versão 3.1.    

* Como nas versões de visualização desse produto, esta versão também se destina a cenários de desenvolvedor e, portanto, dá suporte a apenas implantações de nó único.

### O que há de novo? 
 
* [Versões de componentes do Hadoop atualizadas](hdinsight-component-versioning.md) correspondentes à versão 3.1 do serviço. Isso inclui suporte ao Hive 0.13 e Tez.

## Versão 1.0.0.0, lançada em 28 de outubro de 2013

* Esta é a versão disponível do Emulador do Microsoft HDInsight para Azure, anteriormente conhecida como Microsoft HDInsight Developer Preview. 

* Como nas versões de visualização desse produto, esta versão também se destina a cenários de desenvolvedor e, portanto, dá suporte a apenas implantações de nó único.

### O que há de novo? 
 
* Foram adicionados scripts para simplificar as configurações de todos os serviços do Apache Hadoop para início automático ou manual. O início continua sendo automático por padrão, mas agora todos os serviços podem ser alterados por meio dos scripts set-onebox-manualstart.cmd ou set-onebox-autostart.cmd, instalados em C:\\Hadoop. 

* O número de dependências de instalações necessárias foram reduzidas significativamente, permitindo instalações mais rápidas.

* Esta versão inclui uma correção de bug no comando usado para executar os exemplos de Pig no script RunSamples.ps1 instalado na pasta GettingStarted.

* Esta versão contém uma atualização para HDP (Hortonworks Data Platform) versão 1.1 que corresponde aos serviços do HDP disponíveis com o cluster Azure HDInsight versão 1.6.

## Versão 0.11.0.0, lançada em 30 de setembro de 2013

### O que há de novo? 
		
* O painel do HDInsight foi removido. 

* Esta versão contém uma atualização para o Hortonworks Data Platform Developer Preview que corresponde ao Hortonworks Data Platform Preview no Azure HDInsight.

## Versão 0.10.0.0, lançada em 9 de agosto de 2013

### O que há de novo? 
	
* Esta versão contém uma atualização para o Hortonworks Data Platform Developer Preview que corresponde ao Hortonworks Data Platform Preview no Azure HDInsight.

## Versão 0.8.0.0, lançada em 7 de junho de 2013

### O que há de novo? 

* Esta versão contém uma atualização para o Hortonworks Data Platform Developer Preview que corresponde ao Hortonworks Data Platform Preview no Azure HDInsight.

## Versão 0.6.0.0, lançada em 13 de maio de 2013

### O que há de novo? 

* O Hive Server 2 agora está sendo instalado. Isso é necessário para a versão 0.9.2.0 do Microsoft ODBC Driver para Hive, que foi lançado ao mesmo tempo que essa atualização. 

* Todos os serviços estão configurados para inicialização automática – você não precisa mais começar tudo de novo após reinicializar o computador.

## Versão 0.4.0.0, lançada em 25 de março de 2013

### O que há de novo? 

* Nova versão do Microsoft HDInsight Developer Preview, bem como do Hortonworks Data Platform para Windows Developer Preview. 

* Inclui o Apache Hadoop, o Hive, o Pig, o Sqoop, o Oozie, o HCatalog e o Templeton.

* Novo painel HDInsight com os seguintes recursos:
 
	* Conexão a vários clusters, inclusive à instalação local e aos clusters em execução remota por meio do serviço Azure HDInsight. Para obter mais informações sobre o serviço HDInsight, consulte [http://azure.microsoft.com/pt-br/documentation/services/hdinsight/](http://azure.microsoft.com/documentation/services/hdinsight/).
 
	* Configure o armazenamento de Blob do Azure no cluster local. Consulte as instruções detalhadas abaixo.

	* Crie e edite consultas do Hive no novo console interativo do Hive.

	* Exiba e baixe o histórico e os resultados dos trabalhos.

### Notas de versão 

* Os pontos de extremidade da API REST em uma instalação local do HDInsight e o serviço Azure HDInsight são acessados por meio de números de porta diferentes para os mesmos serviços: 

	Local: Oozie - http://localhost:11000/oozie/v1/admin/status Templeton - http://localhost:50111/templeton/v1/status ODBC - usar porta 10000 na configuração de DSN ou cadeia de conexão

	Serviço HDInsight: Oozie - http://ServerFQDN:563/oozie/v1/admin/status Templeton - http://ServerFQDN:563/templeton/v1/status ODBC - usar porta 563 na configuração de DSN ou cadeia de conexão


* Configurando o armazenamento de Blob do Azure no cluster local:

	No painel, você verá um cluster padrão local chamado "local (hdfs)". Se quiser usar o armazenamento de Blob do Azure como o armazenamento de sua instalação local, faça o seguinte:

	1. Adicione a marca da conta no core-site.xml localizado em C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf:       

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

	3. Acesse qualquer arquivo nessa conta usando o URI completo: asv://{container}@{account}/{path} (ou asvs:// se quiser usar HTTPS para acessar os dados). Exemplo:
	 
	 		hadoop fs -lsr 
			asvs://MyHadoopOnAzureContainerName@MyHadoopOnAzureAccountName/example/data/

	4. Exclua o cluster local registrado atualmente e registre-o novamente com as novas credenciais do armazenamento de Blob do Azure.


## Versão 0.3.0.0, lançada em 13 de dezembro de 2012 

* O site do painel foi alterado para autenticação anônima, em vez de usar as credenciais do Windows. Isso elimina o problema da solicitação de logon mencionada nas notas de versão da versão anterior. 

* Alguns bugs do Sqoop na exportação e em alguns tipos de importação foram corrigidos.

### Problemas da versão 

* O console do JavaScript falha ao carregar. Consulte as notas de versão da versão 0.2.0.0 para obter detalhes. 
* A linha de comando do Sqoop exibirá avisos, como mostrado abaixo. Isso será corrigido em uma atualização futura e pode ser ignorado com segurança. 
	
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

## Versão 0.2.0.0, lançada em 3 de dezembro de 2012

* Introdução de controle de versão semântico para Windows Installer 

* Correções de vários bugs de instalação relatados nos fóruns do MSDN, principalmente acerca da instalação do painel do HDInsight

* Itens adicionados ao menu **Iniciar** para maior detectabilidade

* Correção para entrada multilinhas do console do Hive

* Atualizações secundárias do conteúdo da Introdução

### Problemas da versão 

* O console do JavaScript falha ao carregar. 

	* Em algumas instalações, o console do JavaScript falhará com um erro HTTP 404 exibido na página. Para contornar esse problema, vá diretamente para http://localhost:8080 para usar o console. 

* A navegação até o painel do HDInsight gera uma solicitação de logon.

	* Recebemos relatos de uma caixa de diálogo de logon que está sendo gerada na navegação até o painel do HDInsight. Nesse caso, forneça as informações de logon de seu usuário atual e você conseguirá navegar até o painel. 


## Versão 1.0.0.0, lançada em 23 de outubro de 2012

* Versão inicial 

### Problemas da versão 

* Console do Hive 

	* Se houver uma newline no comando do Hive enviado, ocorrerá um erro de sintaxe. Remova as newlines e a consulta deverá ser executada conforme o esperado. 



## Problemas conhecidos gerais

* Expiração da senha do usuário do Hadoop 

	A senha do usuário do Hadoop pode expirar, dependendo das políticas de Active Directory enviadas por push para o computador. O script do Windows PowerShell a seguir definirá que a senha não expire e pode ser executado em um prompt de comando administrativo:

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


* Diretório temporário
	
	O arquivo haddop.tmp.dir aponta para o local errado. em vez de apontar para C:\\hadoop\\hdfs, aponta para c:\\hdfs. Esse bug será corrigido na próxima atualização de bits do HDP.

* Restrições do sistema operacional

	O servidor HDInsight deve ser instalado em um sistema operacional de 64 bits.

* Resultados da pesquisa do WebPI (Web Platform Installer)

	O HDInsight não pode ser encontrado nos resultados da pesquisa WebPI. Isso normalmente é devido a uma restrição do sistema operacional O HDInsight requer um sistema operacional de 64 bits com as seguintes versões mínimas: Windows 7 Service Pack 1, Windows Server 2008 R2 Service Pack 1, Windows 8 ou Windows Server 2012.

* Documentação do prompt de comando administrativo

	* Para executar comandos como **hadoop mradmin** ou **hadoop defadmin**, você deve executar como usuário do Hadoop. 

	* Para criar facilmente um shell que execute com esse usuário, abra um prompt de comando do Hadoop e execute o seguinte:
	 
	 		start-hadoopadminshell

	* Isso abrirá um novo shell de comando, que será executado com privilégios de administrador do Hadoop.

##<a name="nextsteps"></a> Próximas etapas

- [Introdução ao Emulador do HDInsight][hdinsight-hadoop-emulator-get-started]




[hdinsight-hadoop-emulator-get-started]: ../hdinsight-get-started-emulator.md


<!--HONumber=54--> 