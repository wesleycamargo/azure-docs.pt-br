<properties
	pageTitle="Criar um aplicativo de HBase usando Maven e Java e implantar o HDInsight baseado em Linux| Microsoft Azure"
	description="Saiba como usar o Apache Maven para compilar um aplicativo do Apache HBase baseado em Java e depois implantá-lo no HDInsight baseada em Linux na nuvem do Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/14/2016"
	ms.author="larryfr"/>

#Usar o Maven para compilar aplicativos Java que usam o HBase com o HDInsight baseado em Linux (Hadoop)

Saiba como criar e compilar um aplicativo [HBase no Apache](http://hbase.apache.org/) em Java usando o Apache Maven. Em seguida, use o aplicativo com um cluster HDInsight baseados em Linux.

O [Maven](http://maven.apache.org/) é uma ferramenta de software para compreensão e gerenciamento de projetos que permite a você compilar software, documentação e relatórios para projetos Java. Neste artigo, você aprenderá como usá-lo para criar um aplicativo Java básico que cria, consulta e exclui uma tabela HBase em um cluster HDInsight baseado em Linux.

> [AZURE.NOTE] As etapas neste documento pressupõem que você está usando um cluster HDInsight baseado em Linux. Para saber mais sobre como usar um cluster HDInsight baseado em Windows, veja [Usar o Maven para compilar aplicativos Java que usam o HBase com o HDInsight baseado em Windows](hdinsight-hbase-build-java-maven.md)

##Requisitos

* [Plataforma Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou superior

* [Maven](http://maven.apache.org/)

* [Um cluster HDInsight do Azure baseado em Linux com HBase](../hdinsight-hbase-get-started-linux.md#create-hbase-cluster)

    > [AZURE.NOTE] As etapas neste documento foram testadas com as versões 3.2, 3.3 e 3.4 do cluster HDInsight. Os valores padrão fornecidos nos exemplos destinam-se um cluster HDInsight 3.4.

* **Familiaridade com o SSH e SCP**. Para obter mais informações sobre como usar SSH e SCP com o HDInsight, consulte o seguinte:

    * **Clientes Linux, Unix ou OS X**: consultem [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X ou Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Clientes Windows**: consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##Criar o projeto

1. Por meio da linha de comando em seu ambiente de desenvolvimento, mude os diretórios para o local em que você deseja criar o projeto, por exemplo, `cd code/hdinsight`.

2. Use o comando __mvn__, que é instalado com o Maven, para gerar o scaffolding para o projeto.

		mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Isso cria um novo diretório dentro do diretório atual, com o nome especificado pelo parâmetro __artifactID__ (**hbaseapp** neste exemplo). Esse diretório conterá os itens a seguir:

	* __pom.xml__: o [POM](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) (Modelo de Objeto de Projeto) contém informações e detalhes de configuração usados para compilar o projeto.

	* __src__: o diretório que contém o diretório __main\\java\\com\\microsoft\\examples__, no qual você criará o autor do aplicativo.

3. Exclua o arquivo __src\\test\\java\\com\\microsoft\\examples\\apptest.java__, já que ele não será utilizado neste exemplo.

##Atualize o modelo do objeto do projeto

1. Edite o arquivo __pom.xml__ e adicione o código a seguir na seção `<dependencies>`:

		<dependency>
      	  <groupId>org.apache.hbase</groupId>
          <artifactId>hbase-client</artifactId>
          <version>1.1.2</version>
        </dependency>

	Isso informa ao Maven que o projeto exige o __hbase-client__ versão __1.1.2__. No momento da compilação, ele será baixado do repositório padrão do Maven. Você pode usar a [Pesquisa de repositório central do Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hbase%7Chbase-client%7C0.98.4-hadoop2%7Cjar) para saber mais sobre essa dependência.

    > [AZURE.IMPORTANT] O número da versão deve corresponder à versão do HBase fornecida com o cluster HDInsight. Use a tabela a seguir para localizar o número da versão correta.

    | Versão do cluster HDInsight | Versão do HBase a ser usada |
    | ----- | ----- |
    | 3\.2 | 0\.98.4-hadoop2 |
    | 3\.3 e 3.4 | 1\.1.2 |

    Para saber mais sobre as versões e os componentes do HDInsight, confira [Quais são os diferentes componentes do Hadoop disponíveis com o HDInsight?](hdinsight-component-versioning.md).

2. Se estiver usando um cluster HDInsight 3.3 ou 3.4, você também deverá adicionar o seguinte à seção `<dependencies>`:

        <dependency>
            <groupId>org.apache.phoenix</groupId>
            <artifactId>phoenix-core</artifactId>
            <version>4.4.0-HBase-1.1</version>
        </dependency>
    
    Isso carregará os componentes phoenix-core, necessários com o Hbase versão 1.1.x.

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
          	    <source>1.7</source>
          	    <target>1.7</target>
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

	> [AZURE.NOTE] Você também pode definir valores de configuração via código. Veja os comentários no exemplo __CreateTable__ abaixo sobre como fazer isso.

	Isso também configura o [Plug-in compilador do Maven](http://maven.apache.org/plugins/maven-compiler-plugin/) e o [Plug-in de tonalidade do Maven](http://maven.apache.org/plugins/maven-shade-plugin/). O plug-in compilador é usado para compilar a topologia. O plug-in de tonalidade é usado para evitar a duplicação de licenças no pacote JAR que é criado pelo Maven. O motivo do uso desse recurso é que os arquivos de licença duplicados causam um erro no momento da execução no cluster do HDInsight. Utilizar o plug-in de tonalidade do Maven com a implementação `ApacheLicenseResourceTransformer` evita esse erro.

	O plug-in de tonalidade do Maven também produzirá um uber jar (ou fat jar), que contém todas as dependências exigidas pelo aplicativo.

3. Salve o arquivo __pom.xml__.

4. Crie um novo diretório chamado __conf__ no diretório __hbaseapp__. Isso será usado para armazenar informações de configuração para se conectar ao HBase.

5. Use o seguinte comando para copiar a configuração do HBase do servidor do HDInsight para o diretório __conf__. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

		scp USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml

	> [AZURE.NOTE] Se você tiver usado uma senha para sua conta SSH, a inserção da senha poderá ser solicitada. Se você usou uma chave SSH com a conta, você precisará usar o `-i` parâmetro para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
	>
	> `scp -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml`

##Criar o aplicativo

1. Vá ao diretório __hbaseapp/src/main/java/com/microsoft/examples__ e renomeie o arquivo app.java como __CreateTable.java__.

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
            // in code instead of an hbase-site.xml file
            //
            // config.set("hbase.zookeeper.quorum",
            //            "zookeepernode0,zookeepernode1,zookeepernode2");
            //config.set("hbase.zookeeper.property.clientPort", "2181");
            //config.set("hbase.cluster.distributed", "true");
            //
            //NOTE: Actual zookeeper host names can be found using Ambari:
            //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"
            
            //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
            config.set("zookeeper.znode.parent","/hbase-unsecure");

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

4. No diretório __hbaseapp/src/main/java/com/microsoft/examples__, crie um arquivo chamado __SearchByEmail.java__. Use o seguinte como conteúdo deste arquivo:

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

6. No diretório __hbaseapp/src/main/hava/com/microsoft/examples__, crie um novo arquivo chamado __DeleteTable.java__. Use o seguinte como conteúdo deste arquivo:

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

2. Use o diretório __hbaseapp__, use o comando a seguir para compilar um arquivo JAR que contém o aplicativo:

		mvn clean package

	Isso limpará quaisquer artefatos de compilações anteriores, baixará quaisquer dependências que ainda não tiverem sido instaladas e, então, compilará o aplicativo e criará seu pacote.

3. Assim que o comando for concluído, o diretório __hbaseapp/target__ conterá um arquivo chamado __hbaseapp-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE] O arquivo __hbaseapp-1.0-SNAPSHOT.jar__ é um uberjar (algumas vezes chamado de fatjar), que contém todas as dependências exigidas para executar o aplicativo.

##Carregue o arquivo JAR e inicie um trabalho

1. Use o comando a seguir para carregar o arquivo jar para o cluster do HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

		scp ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

	Isso carregará o arquivo para o diretório inicial para sua conta de usuário do SSH.

	> [AZURE.NOTE] Se você usou uma chave SSH com a conta, você pode precisar inserir a senha. Se você usou uma chave SSH com a conta, você precisará usar o `-i` parâmetro para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
	>
	> `scp -i ~/.ssh/id_rsa ./target/hbaseapp-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

2. Use o SSH para conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	> [AZURE.NOTE] Se você tiver usado uma senha para sua conta SSH, a inserção da senha poderá ser solicitada. Se você usou uma chave SSH com a conta, você precisará usar o `-i` parâmetro para especificar o caminho para o arquivo de chave. O exemplo a seguir carregará a chave privada de `~/.ssh/id_rsa`:
	>
	> `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Uma vez conectado, use o seguinte para criar uma nova tabela do HBase usando o aplicativo Java:

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable

	Isso criará uma nova tabela do HBase denominada __pessoas__, e vai preenchê-la com dados.

4. Em seguida, use o seguinte para procurar os endereços de email armazenados na tabela:

		hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com

	Você deve receber os seguintes resultados:

		Franklin Holtz - ID: 2
		Franklin Holtz - franklin@contoso.com - ID: 2
		Rae Schroeder - ID: 4
		Rae Schroeder - rae@contoso.com - ID: 4
		Gabriela Ingram - ID: 6
		Gabriela Ingram - gabriela@contoso.com - ID: 6

##Excluir a tabela

Após ter terminado o exemplo, use o comando a seguir na sessão do Azure PowerShell para excluir a tabela __pessoas__ utilizada neste exemplo:

	hadoop jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable

<!---HONumber=AcomDC_0914_2016-->