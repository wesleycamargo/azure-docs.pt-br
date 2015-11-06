<properties
	pageTitle="Criar um aplicativo de HBase usando Maven e Java e implantar o HDInsight baseado em Windows | Microsoft Azure"
	description="Saiba como usar o Apache Maven para compilar um aplicativo do Apache HBase baseado em Java e depois implantá-lo no cluster HDInsight do Azure baseado no Windows."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/22/2015"
	ms.author="larryfr"/>

#Utilizar o Maven para criar aplicativos Java que usam o HBase com o HDInsight (Hadoop)

Saiba como criar e compilar um aplicativo [HBase no Apache](http://hbase.apache.org/) em Java usando o Apache Maven. Então, use o aplicativo com o HDInsight do Azure (Hadoop).

O [Maven](http://maven.apache.org/) é uma ferramenta de software para compreensão e gerenciamento de projetos que permite a você compilar software, documentação e relatórios para projetos Java. Neste artigo, você aprenderá como usá-lo para criar um aplicativo Java básico que cria, consulta e exclui uma tabela HBase em um cluster Azure HDInsight.

##Requisitos

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou superior

* [Maven](http://maven.apache.org/)

* [Um cluster Azure HDInsight com HBase](hdinsight-hbase-get-started.md#create-hbase-cluster)

##Criar o projeto

1. Por meio da linha de comando em seu ambiente de desenvolvimento, mude os diretórios para o local em que você deseja criar o projeto, por exemplo, `cd code\hdinsight`.

2. Use o comando __mvn__, que é instalado com o Maven, para gerar o scaffolding para o projeto.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Isso cria um novo diretório dentro do diretório atual, com o nome especificado pelo parâmetro __artifactID__ (**hbaseapp** neste exemplo). Esse diretório conterá os itens a seguir:

	* __pom.xml__: o [POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) (Modelo de Objeto de Projeto) contém informações e detalhes de configuração usados para compilar o projeto.

	* __src__: o diretório que contém o diretório __main\\java\\com\\microsoft\\examples__, no qual você criará seu aplicativo.

3. Exclua o arquivo __src\\test\\java\\com\\microsoft\\examples\\apptest.java__, já que ele não será utilizado neste exemplo.

##Atualize o modelo do objeto do projeto

1. Edite o arquivo __pom.xml__ e adicione o código a seguir na seção `<dependencies>`:

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

	Isso informa ao Maven que o projeto exige o __cliente hbase__ versão __0.98.4-hadoop2__. No momento da compilação, ele será baixado do repositório padrão do Maven. Você pode usar a [Pesquisa de repositório central do Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre essa dependência.

2. Adicione o código a seguir ao arquivo __pom.xml__. Isso precisa estar dentro das marcas `<project>...</project>` no arquivo; por exemplo, entre `</dependencies>` e `</project>`.

		<build>
		  <sourceDirectory>src</sourceDirectory>
		  <resources>
	        <resource>
	          <directory>${basedir}/conf</directory>
	          <filtering>false</filtering>
	          <includes>
	            <include>hbase-site.xml</include>
	          </includes>
	        </resource>
	      </resources>
		  <plugins>
		    <plugin>
        	  <groupId>org.apache.maven.plugins</groupId>
        	  <artifactId>maven-compiler-plugin</artifactId>
						<version>3.3</version>
        	  <configuration>
          	    <source>1.6</source>
          	    <target>1.6</target>
        	  </configuration>
      		</plugin>
		    <plugin>
		      <groupId>org.apache.maven.plugins</groupId>
		      <artifactId>maven-shade-plugin</artifactId>
		      <version>2.3</version>
		      <configuration>
		        <transformers>
		          <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
	              </transformer>
	            </transformers>
		      </configuration>
		      <executions>
		        <execution>
		          <phase>package</phase>
		          <goals>
		            <goal>shade</goal>
		          </goals>
		        </execution>
		      </executions>
		    </plugin>
		  </plugins>
		</build>

	Isso configura um recurso (__conf\\hbase-site.xml__), que contém informações de configuração para o HBase.

	> [AZURE.NOTE]Você também pode definir valores de configuração via código. Veja os comentários no exemplo __CreateTable__ abaixo sobre como fazer isso.

	Isso também configura o [Plug-in compilador do Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e o [Plug-in de tonalidade do Maven](http://maven.apache.org/plugins/maven-shade-plugin/). O plug-in compilador é usado para compilar a topologia. O plug-in de tonalidade é usado para evitar a duplicação de licenças no pacote JAR que é criado pelo Maven. O motivo do uso desse recurso é que os arquivos de licença duplicados causam um erro no momento da execução no cluster do HDInsight. Utilizar o plug-in de tonalidade do Maven com a implementação `ApacheLicenseResourceTransformer` evita esse erro.

	O plug-in de tonalidade do Maven também produzirá um uber jar (ou fat jar), que contém todas as dependências exigidas pelo aplicativo.

3. Salve o arquivo __pom.xml__.

4. Crie um novo diretório chamado __conf__ no diretório __hbaseapp__. No diretório __conf__, crie um novo arquivo chamado __hbase-site.xml__, usando o informado a seguir como conteúdo:

		<?xml version="1.0"?>
		<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
		<!--
		/**
		 * Copyright 2010 The Apache Software Foundation
		 *
		 * Licensed to the Apache Software Foundation (ASF) under one
		 * or more contributor license agreements.  See the NOTICE file
		 * distributed with this work for additional information
		 * regarding copyright ownership.  The ASF licenses this file
		 * to you under the Apache License, Version 2.0 (the
		 * "License"); you may not use this file except in compliance
		 * with the License.  You may obtain a copy of the License at
		 *
		 *     http://www.apache.org/licenses/LICENSE-2.0
		 *
		 * Unless required by applicable law or agreed to in writing, software
		 * distributed under the License is distributed on an "AS IS" BASIS,
		 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		 * See the License for the specific language governing permissions and
		 * limitations under the License.
		 */
		-->
		<configuration>
		  <property>
		    <name>hbase.cluster.distributed</name>
		    <value>true</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.quorum</name>
		    <value>zookeeper0,zookeeper1,zookeeper2</value>
		  </property>
		  <property>
		    <name>hbase.zookeeper.property.clientPort</name>
		    <value>2181</value>
		  </property>
		</configuration>

	Esse arquivo será usado para carregar a configuração HBase para um cluster HDInsight.

	> [AZURE.NOTE]Esse é um arquivo hbase-site.xml absolutamente mínimo, contendo as configurações estritamente mínimas para o cluster HDInsight. Para um versão completa do arquivo de configuração hbase-site.xml usado pelo HDInsight, consulte [Gerenciar clusters Hadoop no HDInsight usando o portal do Azure](hdinsight-administer-use-management-portal.md#rdp). O arquivo hbase-site.xml está no diretório C:\\apps\\dist\\hbase-<número de versão>-hadoop2\\conf. A parte do caminho para o arquivo referente ao número de versão será modificada conforme o HBase é atualizado no cluster.

3. Salve o arquivo __hbase-site.xml__.

##Criar o aplicativo

1. Vá ao diretório __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__ e renomeie o arquivo app.java como __CreateTable.java__.

2. Abra o arquivo __CreateTable.java__ e substitua o conteúdo existente pelo exibido a seguir:

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;
		import org.apache.hadoop.hbase.HTableDescriptor;
		import org.apache.hadoop.hbase.TableName;
		import org.apache.hadoop.hbase.HColumnDescriptor;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Put;
		import org.apache.hadoop.hbase.util.Bytes;

		public class CreateTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Example of setting zookeeper values for HDInsight
			//   in code instead of an hbase-site.xml file
			//
		    // config.set("hbase.zookeeper.quorum",
		    //            "zookeepernode0,zookeepernode1,zookeepernode2");
		    //config.set("hbase.zookeeper.property.clientPort", "2181");
		    //config.set("hbase.cluster.distributed", "true");

		    // create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);

		    // create the table...
		    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
		    // ... with two column families
		    tableDescriptor.addFamily(new HColumnDescriptor("name"));
		    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
		    admin.createTable(tableDescriptor);

		    // define some people
		    String[][] people = {
		        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
		        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
		        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
		        { "4", "Rae", "Schroeder", "rae@contoso.com" },
		        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
		        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

		    HTable table = new HTable(config, "people");

		    // Add each person to the table
		    //   Use the `name` column family for the name
		    //   Use the `contactinfo` column family for the email
		    for (int i = 0; i< people.length; i++) {
		      Put person = new Put(Bytes.toBytes(people[i][0]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
		      person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
		      person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
		      table.put(person);
		    }
		    // flush commits and close the table
		    table.flushCommits();
		    table.close();
		  }
		}

	Essa é a classe __CreateTable__, que criará uma tabela chamada __pessoas__ e a preencherá com alguns usuários pré-definidos.

3. Salve o arquivo __CreateTable.java__.

4. No diretório __hbaseapp\\src\\main\\java\\com\\microsoft\\examples__, crie um arquivo chamado __SearchByEmail.java__. Use o seguinte como conteúdo deste arquivo:

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HTable;
		import org.apache.hadoop.hbase.client.Scan;
		import org.apache.hadoop.hbase.client.ResultScanner;
		import org.apache.hadoop.hbase.client.Result;
		import org.apache.hadoop.hbase.filter.RegexStringComparator;
		import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
		import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
		import org.apache.hadoop.hbase.util.Bytes;
		import org.apache.hadoop.util.GenericOptionsParser;

		public class SearchByEmail {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Use GenericOptionsParser to get only the parameters to the class
		    // and not all the parameters passed (when using WebHCat for example)
		    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
		    if (otherArgs.length != 1) {
		      System.out.println("usage: [regular expression]");
		      System.exit(-1);
		    }

			// Open the table
		    HTable table = new HTable(config, "people");

			// Define the family and qualifiers to be used
		    byte[] contactFamily = Bytes.toBytes("contactinfo");
		    byte[] emailQualifier = Bytes.toBytes("email");
		    byte[] nameFamily = Bytes.toBytes("name");
		    byte[] firstNameQualifier = Bytes.toBytes("first");
		    byte[] lastNameQualifier = Bytes.toBytes("last");

			// Create a new regex filter
		    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
			// Attach the regex filter to a filter
			//   for the email column
		    SingleColumnValueFilter filter = new SingleColumnValueFilter(
		      contactFamily,
		      emailQualifier,
		      CompareOp.EQUAL,
		      emailFilter
		    );

			// Create a scan and set the filter
		    Scan scan = new Scan();
		    scan.setFilter(filter);

			// Get the results
		    ResultScanner results = table.getScanner(scan);
			// Iterate over results and print  values
		    for (Result result : results ) {
		      String id = new String(result.getRow());
		      byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
		      String firstName = new String(firstNameObj);
		      byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
		      String lastName = new String(lastNameObj);
		      System.out.println(firstName + " " + lastName + " - ID: " + id);
			  byte[] emailObj = result.getValue(contactFamily, emailQualifier);
		      String email = new String(emailObj);
			  System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
		    }
		    results.close();
			table.close();
		  }
		}

	A classe __SearchByEmail__ pode ser usada para pesquisar por linhas, por endereço de email. Já que ele utiliza um filtro de expressão comum, você pode fornecer uma cadeia de caracteres ou então uma expressão comum ao utilizar a classe.

5. Salve o arquivo __SearchByEmail.java__.

6. No diretório __hbaseapp\\src\\main\\hava\\com\\microsoft\\examples__, crie um novo arquivo chamado __DeleteTable.java__. Use o seguinte como conteúdo deste arquivo:

		package com.microsoft.examples;
		import java.io.IOException;

		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.hbase.HBaseConfiguration;
		import org.apache.hadoop.hbase.client.HBaseAdmin;

		public class DeleteTable {
		  public static void main(String[] args) throws IOException {
		    Configuration config = HBaseConfiguration.create();

		    // Create an admin object using the config
		    HBaseAdmin admin = new HBaseAdmin(config);

		    // Disable, and then delete the table
		    admin.disableTable("people");
		    admin.deleteTable("people");
		  }
		}

	Essa classe é para limpar esse exemplo desabilitando e eliminando a tabela criada pela classe __CreateTable__.

7. Salve o arquivo __DeleteTable.java__.

##Compilar e criar o pacote do aplicativo

1. Abra um prompt de comando e mude os diretórios para o diretório __hbaseapp__.

2. Use o comando a seguir para compilar um arquivo JAR contendo o aplicativo:

		mvn clean package

	Isso limpará quaisquer artefatos de compilações anteriores, baixará quaisquer dependências que ainda não tiverem sido instaladas e, então, compilará o aplicativo e criará seu pacote.

3. Assim que o comando for concluído, o diretório __hbaseapp\\target__ conterá um arquivo chamado __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE]O arquivo __hbaseapp-1.0-SNAPSHOT.jar__ é um uberjar (algumas vezes chamado de fatjar), que contém todas as dependências exigidas para executar o aplicativo.

##Carregar o arquivo JAR e iniciar um trabalho

> [AZURE.NOTE]Há muitos modos de carregar um arquivo em seu cluster HDInsight, conforme descrito em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md). As etapas a seguir usam o [PowerShell do Azure](../install-configure-powershell.md).

1. Após instalar e configurar o PowerShell do Azure, crie um novo arquivo chamado __hbase-runner.psm1__. Use o seguinte como conteúdo deste arquivo:

        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.CreateTable"
        -clusterName "MyHDInsightCluster"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "contoso.com"
        
        .EXAMPLE
        Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
        -clusterName "MyHDInsightCluster"
        -emailRegex "^r" -showErr
        #>
        
        function Start-HBaseExample {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
        #The class to run
        [Parameter(Mandatory = $true)]
        [String]$className,
        
        #The name of the HDInsight cluster
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        
        #Only used when using SearchByEmail
        [Parameter(Mandatory = $false)]
        [String]$emailRegex,
        
        #Use if you want to see stderr output
        [Parameter(Mandatory = $false)]
        [Switch]$showErr
        )
        
        Set-StrictMode -Version 3
        
        # Is the Azure module installed?
        FindAzure
        
        # Get the login for the HDInsight cluster
        $creds = Get-Credential
        
        # Get storage information
        $storage = GetStorage -clusterName $clusterName
        
        # The JAR
        $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"
        
        # The job definition
        $jobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile $jarFile `
            -ClassName $className `
            -Arguments $emailRegex
        
        # Get the job output
        $job = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $jobDefinition `
            -HttpCredential $creds
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        if($showErr)
        {
        Write-Host "STDERR"
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
                    -DisplayOutputType StandardError
        }
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
                    -Clustername $clusterName `
                    -JobId $job.JobId `
                    -DefaultContainer $storage.container `
                    -DefaultStorageAccountName $storage.storageAccountName `
                    -DefaultStorageAccountKey $storage.storageAccountKey `
                    -HttpCredential $creds
        }
        
        <#
        .SYNOPSIS
        Copies a file to the primary storage of an HDInsight cluster.
        .DESCRIPTION
        Copies a file from a local directory to the blob container for
        the HDInsight cluster.
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        .EXAMPLE
        Add-HDInsightFile -localPath "C:\temp\data.txt"
        -destinationPath "example/data/data.txt"
        -ClusterName "MyHDInsightCluster"
        -Container "MyContainer"
        #>
        
        function Add-HDInsightFile {
            [CmdletBinding(SupportsShouldProcess = $true)]
            param(
                #The path to the local file.
                [Parameter(Mandatory = $true)]
                [String]$localPath,
                
                #The destination path and file name, relative to the root of the container.
                [Parameter(Mandatory = $true)]
                [String]$destinationPath,
                
                #The name of the HDInsight cluster
                [Parameter(Mandatory = $true)]
                [String]$clusterName,
                
                #If specified, overwrites existing files without prompting
                [Parameter(Mandatory = $false)]
                [Switch]$force
            )
            
            Set-StrictMode -Version 3
            
            # Is the Azure module installed?
            FindAzure
            
            # Get authentication for the cluster
            $creds=Get-Credential
            
            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }
            
            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName
            
            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath `
                -Blob $destinationPath `
                -force:$force `
                -Container $storage.container `
                -Context $storage.context
        }
        
        function FindAzure {
            # Is there an active Azure subscription?
            $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Login-AzureRmAccount cmdlet to login to your subscription."
            }
        }
        
        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureRmHDInsightCluster -ClusterName $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            
            # Get storage information
            $resourceGroup = $hdi.ResourceGroup
            $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
            $container=$hdi.DefaultStorageContainer
            $storageAccountKey=Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup `
                | %{ $_.Key1 }
            # Get the resource group, in case we need that
            $return.resourceGroup = $resourceGroup
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $container
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccount = $storageAccountName
            $return.storageAccountKey = $storageAccountKey
            
            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

	Esse arquivo contém dois módulos:

	* __Add-HDInsightFile__ - utilizado para carregar arquivos no HDInsight

	* __Start-HBaseExample__ - utilizado para executar as classes criadas anteriormente

2. Salve o arquivo __hbase-runner.psm1__.

3. Abra uma nova janela do PowerShell do Azure, altere os diretórios para o diretório __hbaseapp__ e, então, execute o comando a seguir.

		PS C:\ Import-Module c:\path\to\hbase-runner.psm1

	Altere o caminho para o local do arquivo __hbase-runner.psm1__ criado anteriormente. Isso registrará o módulo para essa sessão do PowerShell do Azure.

2. Utilize o comando a seguir para carregar o __hbaseapp-1.0-SNAPSHOT.jar__ para seu cluster HDInsight.

		Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

	Substitua __hdinsightclustername__ pelo nome do seu cluster HDInsight. O comando carregará então o __hbaseapp-1.0-SNAPSHOT.jar__ para o local __example/jars__ no armazenamento primário de seu cluster HDInsight.

3. Após os arquivos terem sido carregados, use o seguinte código para criar uma nova tabela utilizando o __hbaseapp__:

		Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

	Substitua __hdinsightclustername__ pelo nome do seu cluster HDInsight.

	Esse comando cria uma nova tabela chamada __pessoas__ em seu cluster HDInsight. Esse comando não mostra nenhuma saída na janela do console.

2. Para procurar por entradas na tabela, use o comando a seguir:

		Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

	Substitua __hdinsightclustername__ pelo nome do seu cluster HDInsight.

	Isso utilizará a classe **SearchByEmail** para procurar por quaisquer linhas em que a família da coluna __contactinformation__ e a coluna __email__ contenham a cadeia __contoso.com__. Você deve receber os seguintes resultados:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

	Usar __fabrikam.com__ para o valor `-emailRegex` retornará como resultado os usuários que têm __fabrikam.com__ no campo de email. Já que essa pesquisa é implementada usando um filtro baseado em expressões comuns, você também pode inserir expressões comuns como __^r__, que retornarão como resultado entradas nas quais o email começa pela letra “r”.

##Excluir a tabela

Após ter terminado o exemplo, use o comando a seguir na sessão do Azure PowerShell para excluir a tabela __pessoas__ utilizada neste exemplo:

	Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Substitua __hdinsightclustername__ pelo nome do seu cluster HDInsight.

##Solucionar problemas

###Nenhum resultado ou resultados inesperados ao usar o Start-HBaseExample

Utilize o parâmetro `-showErr` para exibir o erro padrão (STDERR) produzido durante a execução do trabalho.

<!----HONumber=Oct15_HO4-->