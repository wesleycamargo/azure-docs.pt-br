---
title: Usar o SDK Java do Data Lake Store para desenvolver aplicativos | Microsoft Docs
description: "Usar o SDK do Java do Repositório Azure Data Lake para desenvolver aplicativos"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 255e8def5179e544a4fb919774f3b18148566898


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Introdução ao Repositório Azure Data Lake usando o Java
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI do Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Saiba como usar o SDK Java para o Azure Data Lake Store a fim de executar operações básicas, como criar pastas, carregar e baixar arquivos de dados etc. Para obter mais informações sobre o Data Lake, veja [Azure Data Lake Store](data-lake-store-overview.md).

Você pode acessar os documentos de API do Java SDK do Azure Data Lake Store em [Documentos de API Java do Azure Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Pré-requisitos
* Java Development Kit (JDK 7 ou superior, usando Java versão 1.7 ou superior)
* Conta do Azure Data Lake Store. Siga as instruções em [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Este tutorial usa o Maven para compilação e dependências de projeto. Embora seja possível compilar sem usar um sistema de compilação como Maven ou Gradle, com esses sistemas é muito mais fácil gerenciar dependências.
* (Opcional) E IDE como [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou semelhante.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Como faço para me autenticar usando o Azure Active Directory?
Neste tutorial, usamos um segredo do cliente de aplicativo do Azure AD para recuperar um token do Azure Active Directory (autenticação de serviço a serviço). Podemos usar esse token para criar um objeto de cliente do Data Lake Store para executar operações de arquivo e operações de diretório. Para obter instruções sobre como autenticar com o Azure Data Lake Store usando o segredo do cliente, podemos executar as seguintes etapas de alto nível:

1. Criar um aplicativo Web do Azure AD
2. Recupere a ID do cliente, o segredo do cliente e o ponto de extremidade de token para o aplicativo Web do Azure AD.
3. Configure o acesso ao aplicativo Web do Azure AD no arquivo/pasta do Data Lake Store que você deseja acessar por meio do aplicativo Java que está criando.

Para obter instruções sobre como executar essas etapas, confira [Criar um aplicativo do Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

O Azure Active Directory fornece outras opções, bem como a recuperação de um token. Você pode escolher vários mecanismos de autenticação diferentes de acordo com seu cenário; por exemplo, um aplicativo em execução em um navegador, um aplicativo distribuído como um aplicativo da área de trabalho ou um aplicativo de servidor em execução no local ou em uma máquina virtual do Azure. Você também pode escolher diferentes tipos de credenciais, como senhas, certificados, autenticação de dois fatores etc. Além disso, o Azure Active Directory permite sincronizar os usuários do Active Directory locais com a nuvem. Para obter detalhes, confira [Cenários de autenticação do Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Criar um aplicativo Java
O exemplo de código disponível [no GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) explica o processo de criação de arquivos no armazenamento, concatenação de arquivos, download de um arquivo e exclusão de alguns arquivos no armazenamento. Esta seção do artigo explica as principais partes do código.

1. Crie um projeto do Maven usando [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) na linha de comando ou usando um IDE. Para obter instruções sobre como criar um projeto Java usando IntelliJ, veja [aqui](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Para obter instruções sobre como criar um projeto usando o Eclipse, veja [aqui](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Adicione as dependências a seguir para o arquivo **pom.xml** do Maven. Adicione o seguinte trecho de texto entre a marca **\</version>** e a marca **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    A primeira dependência é usar o SDK do Data Lake Store (`azure-datalake-store`) do repositório do maven. A segunda dependência (`slf4j-nop`) é especificar qual estrutura de registros a ser usada para o aplicativo. O SDK do Data Lake Store usa a fachada de registro em log [slf4j](http://www.slf4j.org/), que permite que você escolha entre uma série de estruturas de log populares, como log4j, registro em log Java, logback etc. ou nenhum log. Para este exemplo, desabilitaremos o registro em log, por isso, usaremos a associação **slf4j nop**. Para usar outras opções de log em seu aplicativo, veja [aqui](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Adicionar o código do aplicativo
Há três partes principais para o código.

1. Obter o token do Azure Active Directory
2. Use o token para criar um cliente do Data Lake Store.
3. Use o cliente do Data Lake Store para executar operações.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Etapa 1: obter um token do Azure Active Directory.
O SDK do Data Lake Store fornece métodos convenientes que permitem obter os tokens de segurança necessários para se comunicar com a conta do Data Lake Store. No entanto, o SDK não exige que apenas esses métodos sejam usados. Você pode usar qualquer outro meio de obter o token, como usar o [SDK do Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) ou seu próprio código personalizado.

Para usar o SDK do Data Lake Store para obter o token para o aplicativo Web do Active Directory que você criou anteriormente, use os métodos estáticos na classe `AzureADAuthenticator`. Substitua **FILL-IN-HERE** pelos valores reais para o aplicativo Web do Azure Active Directory.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Etapa 2: criar um objeto de cliente (ADLStoreClient) do Azure Data Lake Store
Criar um objeto [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) requer que você especifique o nome da conta do Data Lake Store e o token do Azure Active Directory gerado na última etapa. Observe que o nome da conta do Data Lake Store deve ser um nome de domínio totalmente qualificado. Por exemplo, substitua **FILL-IN-HERE** por algo como **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Etapa 3: usar o ADLStoreClient para executar operações de arquivo e diretório
O código a seguir contém trechos de código de exemplo de algumas operações comuns. Você pode examinar os [documentos de API do SDK Javado Data Lake Store](https://azure.github.io/azure-data-lake-store-java/javadoc/) completos do objeto **ADLStoreClient** para ver outras operações.

Observe que os arquivos são lidos e gravados usando fluxos Java padrão. Isso significa que você pode definir qualquer um dos fluxos Java sobre os fluxos do Data Lake Store para tirar proveito da funcionalidade padrão do Java (por exemplo, impressão de fluxos de saída formatada ou qualquer um dos fluxos de compactação ou criptografia para funcionalidade adicional etc.).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Etapa 4: compile e execute o aplicativo
1. Para executar por meio de um IDE, localize e pressione o botão **Executar**. Para executar por meio do Maven, use [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. Para produzir um jar autônomo que você pode executar na linha de comando, compile o jar com todas as dependências incluídas, usando o [plug-in de assembly do Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). O pom.xml no [código de exemplo no github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) tem um exemplo de como fazer isso.

## <a name="next-steps"></a>Próximas etapas
* [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)
* [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO2-->


