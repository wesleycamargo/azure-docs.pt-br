#Baixar o SDK do Azure para Java

##Bibliotecas de cliente do Azure para Java - Download Manual

As bibliotecas do Azure para Java são distribuídas sob a ][licença]do[Apache License, Versão 2.0. Clique [aqui][zip-download] para obter um arquivo ZIP das bibliotecas e de todas as dependências.  Isso é disponibilizado pela Microsoft Open Technologies, Inc. Consulte os arquivos license.txt e ThirdPartyNotices.txt dentro do ZIP para obter a licença e outras informações.

##Bibliotecas do Azure para Java - Maven

Se o seu projeto já estiver configurado para usar o Maven para compilação, adicione a seguinte dependência ao seu arquivo pom.xml.

	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-compute</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-network</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-sql</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-storage</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-management-websites</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-media</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-servicebus</artifactId>
	    <version>n.n.n</version>
	</dependency>
	<dependency>
	    <groupId>com.microsoft.azure</groupId>
	    <artifactId>azure-serviceruntime</artifactId>
	    <version>n.n.n</version>
	</dependency>



Dentro do elemento `<version>`, substitua *n.n.n* por um número de versão válido, o qual pode ser obtido por meio do [Azure Libraries Repository on Maven](http://go.microsoft.com/fwlink/?LinkID=286274).

[licença]: http://www.apache.org/licenses/LICENSE-2.0.html
[zip-download]:  http://go.microsoft.com/fwlink/?LinkId=253887
