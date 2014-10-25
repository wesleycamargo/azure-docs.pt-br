<properties title="Build an HBase application using Maven" pageTitle="Build an HBase application using Maven" description="Learn how to use Apache Maven to build a Java-based Apache HBase application, then deploy it to Azure HDInsight" metaKeywords="Maven hbase hadoop, hbase hadoop, maven java hbase, maven java hbase hadoop, maven java hadoop, hbase hdinsight, hbase java hdinsight, maven hdinsight, maven java hdinsight, hadoop database, hdinsight database" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="larryfr"></tags>

## Utilizar o Maven para criar aplicativos Java que usam o HBase com o HDInsight (Hadoop)

Saiba como criar e compilar um aplicativo [HBase no Apache][HBase no Apache] em Java usando o Maven no Apache. Então, use o aplicativo com o HDInsight do Azure (Hadoop).

[Maven][Maven] é uma ferramenta de software para compreensão e gerenciamento de projetos que permite a você compilar software, documentação e relatórios para projetos Java. Neste artigo, você aprenderá como usá-lo para criar um aplicativo Java básico que cria, consulta e exclui uma tabela HBase em um cluster Azure HDInsight.

## Requisitos

-   [Plataforma Java JDK][Plataforma Java JDK] 7 ou superior

-   [Maven][Maven]

-   [Um cluster Azure HDInsight com HBase][Um cluster Azure HDInsight com HBase]

## Criar o projeto

1.  Por meio da linha de comando em seu ambiente de desenvolvimento, mude os diretórios para o local em que você deseja criar o projeto. Por exemplo, `cd code\hdinsight`

2.  Use o comando **mvn**, que é instalado com o Maven, para gerar o scaffolding para o projeto.

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    Isso criará um novo diretório dentro do diretório atual, com o nome especificado pelo parâmetro **artifactID** (**hbaseapp** neste exemplo). Esse diretório conterá os itens a seguir.

    -   **pom.xml** - o Modelo de Objeto de Projeto ([POM][POM]) contém informações e detalhes de configuração usados para criar o projeto

    -   **src** - o diretório que contém o diretório **main\\java\\com\\microsoft\\examples**, onde você criará seu aplicativo.

3.  Exclua o arquivo **src\\test\\java\\com\\microsoft\\examples\\apptest.java**, já que ele não será utilizado neste exemplo.

## Atualize o modelo do objeto do projeto

1.  Edite o arquivo **pom.xml** e adicione o seguinte dentro da seção `<dependencies>`.

        <dependency>
          <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>0.98.4-hadoop2</version>
        </dependency>

    Isso informa ao Maven que o projeto exige o **cliente hbase** versão **0.98.4-hadoop2**. No momento da compilação, ele será baixado do repositório padrão do Maven. Você pode usar a [pesquisa de repositório do Maven][pesquisa de repositório do Maven] para exibir mais informações sobre essa dependência.

2.  Adicione o conteúdo a seguir ao arquivo **pom.xml**. Isso precisa estar dentro das tags `<project>...</project>` no arquivo; por exemplo, entre `</dependencies>` e `</project>`.

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

    Isso configura um recurso (**conf\\hbase-site.xml**), que contém informações de configuração para o HBase.

    > [WACOM.NOTE] Você também pode definir valores de configuração via código. Veja os comentários no exemplo **CreateTable** abaixo sobre como fazer isso.

    Isso também configura o [maven-shade-plugin][maven-shade-plugin], que é utilizado para evitar duplicação de licenças no JAR que é compilado pelo Maven. O motivo do uso desse recurso é que os arquivos de licença duplicados causam um erro no momento da execução no cluster do HDInsight. Utilizar o maven-shade-plugin com a implementação `ApacheLicenseResourceTransformer` evita esse erro.

    O maven-shade-plugin também produzirá um uberjar (ou fatjar), que contém todas as dependências exigidas pelo aplicativo.

3.  Salve o arquivo **pom.xml**.

4.  Crie um novo diretório chamado **conf** no diretório **hbaseapp**. No diretório **conf**, crie um novo arquivo chamado **hbase-site.xml** e use para ele o conteúdo a seguir.

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
            <name>hbase.cluster.distributed</name>
            <value>true</value>
          </property>
          <property>
            <name>hbase.zookeeper.quorum</name>
            <value>zookeepernode0:2181 zookeepernode1:2181 zookeepernode2:2181</value>
          </property>

        </configuration>

    Esse arquivo será usado para carregar a configuração HBase para um cluster HDInsight.

    > [WACOM.NOTE] Esse é um arquivo hbase-site.xml mínimo, contendo as configurações absolutamente mínimas para o cluster HDInsight. Para obter uma versão completa do arquivo de configuração hbase-site.xml usado pelo HDInsight, [acesse o cluster HDInsight via desktop remoto][acesse o cluster HDInsight via desktop remoto], o arquivo hbase-site.xml está localizado no diretório C:\\apps\\dist\\hbase-\<número da versão\>-hadoop2\\conf. A parte do caminho para o arquivo referente ao número de versão será modificada conforme o HBase é atualizado no cluster.

5.  Salve o arquivo **hbase-site.xml**.

## Criar o aplicativo

1.  Vá até o diretório **hbaseapp\\src\\com\\microsoft\\examples** e renomeie o arquivo app.java\_\_ como **CreateTable.java**.

2.  Abra o arquivo **CreateTable.java** e substitua o conteúdo existente pelo exibido a seguir.

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

    Essa é a classe **CreateTable**, que criará uma tabela chamada **pessoas** e a preencherá com alguns usuários pré-definidos.

3.  Salve o arquivo **CreateTable.java**.

4.  No diretório **hbaseapp\\com\\microsoft\\examples**, crie um novo arquivo chamado **SearchByEmail.java**. Use o conteúdo a seguir para este arquivo.

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

    A classe **SearchByEmail** pode ser usada para pesquisar por linhas, por endereço de email. Como utiliza um filtro de expressão comum, você pode fornecer uma cadeia de caracteres ou, então, uma expressão comum ao utilizar a classe.

5.  Salve o arquivo **SearchByEmail.java**.

6.  No diretório **hbaseapp\\com\\microsoft\\examples**, crie um novo arquivo chamado **DeleteTable.java**. Use o conteúdo a seguir para este arquivo.

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

    Essa classe é apenas para limpar esse exemplo primeiro desabilitando, depois eliminando a tabela criada pela classe **CreateTable**.

7.  Salve o arquivo **CreateTable.java**.

## Compilar e criar o pacote do aplicativo

1.  Abra um prompt de comando e mude os diretórios para o diretório **hbaseapp**.

2.  Use o comando a seguir para compilar um JAR contendo o aplicativo.

        mvn clean package

    Isso limpará quaisquer artefatos de compilações anteriores, baixará quaisquer dependências que ainda não tiverem sido instaladas e, então, compilará e criará o pacote do aplicativo.

3.  Assim que o comando for concluído, o diretório **hbaseapp\\target** conterá um arquivo chamado **hbaseapp-1.0-SNAPSHOT.jar**.

    > [WACOM.NOTE] O arquivo **hbaseapp-1.0-SNAPSHOT.jar** é um uberjar (algumas vezes chamado de um fatjar), que contém todas as dependências exigidas para executar o aplicativo.

## Carregue o JAR e inicie um trabalho

> [WACOM.NOTE] Há muitos modos de carregar um arquivo em seu cluster HDInsight, conforme descrito em [Carregar dados para trabalhos do Hadoop no HDInsight][Carregar dados para trabalhos do Hadoop no HDInsight]. As etapas abaixo utilizam o [PowerShell do Azure][PowerShell do Azure].

1.  Após instalar e configurar o [PowerShell do Azure][PowerShell do Azure], crie um novo arquivo chamado **hbase-runner.psm1**. Use o conteúdo a seguir para este arquivo.

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

            # The JAR
            $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

            # The job definition
            $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
              -JarFile $jarFile `
              -ClassName $className `
              -Arguments $emailRegex

            # Get the job output
            $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
            Write-Host "Wait for the job to complete ..." -ForegroundColor Green
            Wait-AzureHDInsightJob -Job $job
            if($showErr)
            {
                Write-Host "STDERR"
                Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
            }
            Write-Host "Display the standard output ..." -ForegroundColor Green
            Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
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

            # Does the local path exist?
            if (-not (Test-Path $localPath))
            {
                throw "Source path '$localPath' does not exist."
            }

            # Get the primary storage container
            $storage = GetStorage -clusterName $clusterName

            # Upload file to storage, overwriting existing files if -force was used.
            Set-AzureStorageBlobContent -File $localPath -Blob $destinationPath -force:$force `
                                        -Container $storage.container `
                                        -Context $storage.context
        }

        function FindAzure {
            # Is the Azure module installed?
            if (-not(Get-Module -ListAvailable Azure))
            {
                throw "Windows Azure PowerShell not found! For help, see http://www.windowsazure.com/pt-br/documentation/articles/install-configure-powershell/"
            }

            # Is there an active Azure subscription?
            $sub = Get-AzureSubscription -ErrorAction SilentlyContinue
            if(-not($sub))
            {
                throw "No active Azure subscription found! If you have a subscription, use the Add-AzureAccount or Import-PublishSettingsFile cmdlets to make the Azure account available to Windows PowerShell."
            }
        }

        function GetStorage {
            param(
                [Parameter(Mandatory = $true)]
                [String]$clusterName
            )
            $hdi = Get-AzureHDInsightCluster -name $clusterName
            # Does the cluster exist?
            if (!$hdi)
            {
                throw "HDInsight cluster '$clusterName' does not exist."
            }
            # Create a return object for context & container
            $return = @{}
            $storageAccounts = @{}
            # Get the primary storage account information
            $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
            $storageAccountKey = $hdi.DefaultStorageAccount.StorageAccountKey
            # Build the hash of storage account name/keys
            $storageAccounts.Add($hdi.DefaultStorageAccount.StorageAccountName, $storageAccountKey)
            foreach($account in $hdi.StorageAccounts)
            {
                $storageAccounts.Add($account.StorageAccountName, $account.StorageAccountKey)
            }
            # Get the storage context, as we can't depend
            # on using the default storage context
            $return.context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
            # Get the container, so we know where to
            # find/store blobs
            $return.container = $hdi.DefaultStorageAccount.StorageContainerName
            # Return storage accounts to support finding all accounts for
            # a cluster
            $return.storageAccounts = $storageAccounts

            return $return
        }
        # Only export the verb-phrase things
        export-modulemember *-*

    Esse arquivo contém dois módulos:

    -   **Add-HDInsightFile** - utilizado para carregar arquivos no HDInsight

    -   **Start-HBaseExample** - utilizado para executar as classes criadas anteriormente

2.  Salve o arquivo **hbase-runner.psm1**.

3.  Abra uma nova janela do PowerShell do Azure, altere os diretórios para o diretório **hbaseapp** e, então, execute o comando a seguir.

        PS C:\ Import-Module c:\path\to\hbase-runner.psm1

    Altere o caminho para o local do arquivo **hbase-runner.psm1** criado anteriormente. Isso registrará o módulo para essa sessão do PowerShell.

4.  Utilize o comando a seguir para carregar o **hbaseapp-1.0-SNAPSHOT.jar** para seu cluster HDInsight.

        Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName hdinsightclustername

    Substitua **hdinsightclustername** pelo nome do seu cluster HDInsight. O comando carregará, então, o **hbaseapp-1.0-SNAPSHOT.jar** para o local **example/jars** no armazenamento primário para seu cluster do HDInsight.

5.  Após os arquivos terem sido carregados, use o seguinte para criar uma nova tabela utilizando o **hbaseapp**

        Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName hdinsightclustername

    Substitua **hdinsightclustername** pelo nome do seu cluster HDInsight.

    Esse comando criará, então, uma nova tabela chamada **pessoas** em seu cluster HDInsight.

6.  Para procurar por entradas na tabela, use o comando a seguir.

        Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName hdinsightclustername -emailRegex contoso.com

    Substitua **hdinsightclustername** pelo nome do seu cluster HDInsight.

    Isso utilizará a classe SearchByEmail para procurar por quaisquer linhas onde a família da coluna **contactinformation**, a coluna **email**, contenha a cadeia **contoso.com**. Você deve receber os seguintes resultados:

        Rae Schroeder - rae@contoso.com - ID: 032439814
        Franklin Holtz - franklin@contoso.com - ID: 229772110
        Gabriela Ingram - gabriela@contoso.com - ID: 655336201

    Usar **fabrikam.com** para o valor `-emailRegex` retornará como resultado os usuários que têm **fabrikam.com** no campo de email. Já que essa pesquisa é implementada usando um filtro baseado em expressões comuns, você também pode inserir expressões comuns como **^r**, que retornarão como resultado entradas nas quais o email começa pela letra 'r'.

## Excluir a tabela

Após ter terminado o exemplo, use o comando a seguir da sessão do PowerShell para excluir a tabela **pessoas** utilizada por este exemplo.

    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName hdinsightclustername

Substitua **hdinsightclustername** pelo nome do seu cluster HDInsight.

## Solucionar problemas

### Nenhum resultado ou resultados inesperados ao utilizar o **Start-HBaseExample**

Utilize o parâmetro `-showErr` para visualizar o STDERR produzido durante a execução do trabalho.

  [HBase no Apache]: http://hbase.apache.org/
  [Maven]: http://maven.apache.org/
  [Plataforma Java JDK]: http://www.oracle.com/technetwork/java/javase/downloads/index.html
  [Um cluster Azure HDInsight com HBase]: /pt-br/documentation/articles/hdinsight-hbase-get-started/#create-hbase-cluster
  [POM]: http://maven.apache.org/guides/introduction/introduction-to-the-pom.html
  [pesquisa de repositório do Maven]: http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar
  [maven-shade-plugin]: http://maven.apache.org/plugins/maven-shade-plugin/
  [acesse o cluster HDInsight via desktop remoto]: http://azure.microsoft.com/pt-br/documentation/articles/hdinsight-administer-use-management-portal/#rdp
  [Carregar dados para trabalhos do Hadoop no HDInsight]: /pt-br/documentation/articles/hdinsight-upload-data/
  [PowerShell do Azure]: /pt-br/documentation/articles/install-configure-powershell/
