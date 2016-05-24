<properties
   pageTitle="Usar o SDK do Java do Repositório Data Lake para desenvolver aplicativos | Azure"
   description="Usar o SDK do Java do Repositório Azure Data Lake para desenvolver aplicativos"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Introdução ao Repositório Azure Data Lake usando o Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Saiba como usar o SDK do Java do Repositório Azure Data Lake para criar uma conta do Azure Data Lake e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta etc. Para obter mais informações sobre o Data Lake, veja [Repositório Azure Data Lake](data-lake-store-overview.md).

## SDK do Java do Repositório Azure Data Lake

Os links a seguir fornecem o local de download do SDK do Java para Repositório Data Lake e a referência do SDK do Java. Para este tutorial, não é necessário baixar o SDK ou seguir o documento de referência. Esses links servem somente para informá-lo.

* O código-fonte do SDK do Java para Repositório Data Lake está disponível no [GitHub](https://github.com/Azure/azure-sdk-for-java).
* A referência do SDK do Java para Repositório Data Lake está disponível em [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Pré-requisitos

* Java Development Kit (JDK) 8 (usando o Java versão 1.8).
* IntelliJ ou outro ambiente de desenvolvimento Java adequado. Isto é opcional, mas recomendado. As instruções abaixo usam o IntelliJ.
* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
* **Criar um aplicativo do Azure Active Directory**. Há duas maneiras de autenticar usando o Azure Active Directory - **interativa** e **não interativa**. Existem pré-requisitos diferentes com base na forma como você deseja autenticar.
	* **Para a autenticação interativa** (usada neste artigo) - no Azure Active Directory, você precisa criar um **aplicativo Cliente Nativo**. Depois de criar o aplicativo, recupere os seguintes valores relacionados ao aplicativo.
		- Obter a **ID do cliente** e o **URI de redirecionamento** do aplicativo
		- Definir permissões delegadas

	* **Para a autenticação não interativa** (usada neste artigo) - no Azure Active Directory, você precisa criar um **aplicativo Web**. Depois de criar o aplicativo, recupere os seguintes valores relacionados ao aplicativo.
		- Obter a **ID do cliente**, o **segredo do cliente** e o **URI de redirecionamento** do aplicativo
		- Definir permissões delegadas
		- Atribua o aplicativo do Azure Active Directory a uma função. A função pode estar no nível do escopo no qual você deseja conceder permissão ao aplicativo do Azure Active Directory. Por exemplo, você pode atribuir o aplicativo no nível da assinatura ou no nível de um grupo de recursos. 

	Veja [Criar aplicativo do Active Directory e a entidade de serviço usando o portal](../resource-group-create-service-principal-portal.md) para obter instruções sobre como recuperar esses valores, definir as permissões e atribuir funções.

## Como faço para me autenticar usando o Azure Active Directory?

O trecho de código a seguir fornece o código para a autenticação **não interativa**, onde o aplicativo fornece suas próprias credenciais.

Você precisará conceder permissão ao seu aplicativo para criar recursos no Azure para este tutorial funcionar. É **altamente recomendável** que você forneça a esse aplicativo somente permissões de Colaborador para um grupo de recursos novo, não usado e vazio em sua assinatura do Azure para este tutorial.

## Criar um aplicativo Java

1. Abra o IntelliJ e crie um novo projeto Java usando o modelo **Aplicativo da Linha de Comando**. Conclua o Assistente para criar o projeto.

2. Clique com o botão direito do mouse no projeto no lado esquerdo da tela e clique em **Adicionar Suporte da Estrutura**. Escolha **Maven** e clique em **OK**.

3. Abra o arquivo **"pom.xml"** recém-criado e adicione o seguinte trecho de texto entre as marcas **</version>** e **</project>**:

    >[AZURE.NOTE] Esta etapa é temporária até que o SDK do Repositório Azure Data Lake esteja disponível no Maven. Este artigo será atualizado quando o SDK estiver disponível no Maven. Todas as futuras atualizações para esse SDK estarão disponíveis por meio do Maven.

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. Vá para **Arquivo**, **Configurações** e **Compilação, Execução e Implantação**. Expanda as **Ferramentas de Compilação**, **Maven** e expanda **Importação**. Marque a caixa de seleção **Importar projetos Maven automaticamente**. Clique em **Aplicar** e clique em **OK**.

5. No painel esquerdo, navegue até **src**, **principal**, **java**, **< nome do pacote >**, abra **Main.java** e substitua o bloco de código existente pelo código a seguir. Além disso, forneça os valores para os parâmetros chamados no trecho de código, como **localFolderPath**, **\_adlsAccountName**, **\_resourceGroupName** e substitua os espaços reservados por** CLIENT-ID**,** CLIENT-SECRET**, **TENANT-ID** e **SUBSCRIPTION-ID**.

    Esse código explica o processo para criar uma conta do Repositório Data Lake, criar pastas no repositório, concatenar arquivos, baixar um arquivo e, por fim, excluir a conta.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
                _resourceGroupName = "<RESOURCE-GROUP-NAME>";
                _location = "East US 2";
        
                _tenantId = "<TENANT-ID>";
                _subId =  "<SUBSCRIPTION-ID>";
                _clientId = "<CLIENT-ID>";
        
                _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
        
                String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
        
                // Authenticate
                ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
                SetupClients(creds);
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
            }
        
            // Helper function to show status and wait for user input
            public static void WaitForNewline(String reason, String nextAction)
            {
                if (nextAction == null)
                    nextAction = "";
                if (!nextAction.isEmpty())
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                    System.out.println(nextAction);
                }
                else
                {
                    System.out.println(reason + "\r\nPress ENTER to continue...");
                    try{System.in.read();}
                    catch(Exception e){}
                }
            }
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
                PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
        
                String fileContents = "";
                if (stream != null) {
                    Writer writer = new StringWriter();
        
                    char[] buffer = new char[1024];
                    try {
                        Reader reader = new BufferedReader(
                                new InputStreamReader(stream, "UTF-8"));
                        int n;
                        while ((n = reader.read(buffer)) != -1) {
                            writer.write(buffer, 0, n);
                        }
                    } finally {
                        stream.close();
                    }
                    fileContents =  writer.toString();
                }
        
                pWriter.println(fileContents);
                pWriter.close();
            }
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Execute o aplicativo. Siga os prompts para executar e concluir o aplicativo.

## Próximas etapas

- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->