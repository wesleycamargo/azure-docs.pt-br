# Baixar o SDK do Azure para Java

## Bibliotecas de cliente do Azure para Java - Download Manual

As bibliotecas do Azure para Java são distribuídas sob a [licença do Apache, versão 2,0][licença do Apache, versão 2,0]. Clique [aqui][aqui] para um arquivo ZIP das bibliotecas e de todas as dependências. Isso é disponibilizado pela Microsoft Open Technologies, Inc. Consulte os arquivos license.txt e ThirdPartyNotices.txt dentro do ZIP para obter a licença e outras informações.

## Bibliotecas do Azure para Java - Maven

Se o seu projeto já estiver configurado para usar o Maven para compilação, adicione a seguinte dependência ao seu arquivo pom.xml.

    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-management-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-media</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-servicebus</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.windowsazure</groupId>
        <artifactId>microsoft-azure-api-serviceruntime</artifactId>
        <version>n.n.n</version>
    </dependency>

Dentro do '`<version>`' elemento, substituir *n.n.n* com um número de versão válida, que pode ser obtido com o [Repositório de bibliotecas do Azure no Maven][Repositório de bibliotecas do Azure no Maven].

  [licença do Apache, versão 2,0]: http://www.apache.org/licenses/LICENSE-2.0.html
  [aqui]: http://go.microsoft.com/fwlink/?LinkId=253887
  [Repositório de bibliotecas do Azure no Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
