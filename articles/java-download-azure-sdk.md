<properties 
	pageTitle="Baixar o SDK do Azure para Java" 
	description="Saiba como baixar o SDK do Azure para o Java, com um código de exemplo fornecido para projetos do Maven e as etapas básicas de instalação para o Kit de Ferramentas do Azure para o Eclipse." 
	services="" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="robmcm"/>

# Baixar o SDK do Azure para Java #

Este artigo contém instruções sobre como baixar e instalar as bibliotecas do Azure para Java.

**Observação:** as bibliotecas do Azure para Java são distribuídas sob a [licença do Apache, versão 2.0][license].

## Bibliotecas do Azure para Java - Download manual ##

Para instalar manualmente as bibliotecas do Azure para Java, clique em <http://go.microsoft.com/fwlink/?LinkId=690320> para baixar um arquivo ZIP que contém todas as bibliotecas e todas as dependências.

Depois de baixar o arquivo zip em seu computador, extraia seu conteúdo e use uma das seguintes opções para adicionar os arquivos JAR ao seu projeto:

* Importe os arquivos JAR para o seu projeto do Java no Eclipse.

* Configure o **Build Path** em seu projeto de Java no Eclipse para incluir o caminho para os arquivos JAR.

Para obter informações detalhadas sobre como configurar os caminhos de compilação no Eclipse, confira o artigo [Caminho de compilação do Java][] no site do Eclipse.

**Observação:** veja os arquivos license.txt e ThirdPartyNotices.txt dentro do ZIP para obter a licença e outras informações.

## Bibliotecas do Azure para Java - Compilando com o Maven ##

### Etapa 1 - Configurar seu projeto para usar o Maven para compilação ###

Para criar projetos do Maven no Eclipse que usam as bibliotecas do Azure para Java, siga as instruções no artigo [Introdução às bibliotecas de gerenciamento do Azure para Java][maven-getting-started].

### Etapa 2 - Definir as configurações do Maven com as dependências necessárias ###

Depois que o projeto foi configurado para usar o Maven para compilação, é possível adicionar as dependências necessárias ao arquivo pom.xml usando uma sintaxe como mostrada no exemplo a seguir. Observe que você não precisa adicionar cada dependência que é listada no exemplo a seguir; você precisa adicionar apenas as dependências específicas necessários para o seu projeto.

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>0.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>0.6.0</version>
    </dependency>

**Observação:** dentro de cada elemento `<version>` no exemplo anterior, substitua os números de versão neste exemplo por números de versão válidos, que podem ser obtidos nos [Repositórios das Bibliotecas do Azure no Maven][].

## Instalando o Kit de Ferramentas do Azure para Eclipse ##

Esta seção contém instruções básicas para instalar o Kit de Ferramentas do Azure para o Eclipse; para obter instruções detalhadas, veja [Instalando o Kit de Ferramentas do Azure para o Eclipse][].

### Pré-requisitos ###

1. Sistemas operacionais Windows listados no artigo [Novidades no Kit de Ferramentas do Azure para o Eclipse][].
1. Os sistemas operacionais Macintosh ou Linux listados no artigo [Novidades no Kit de Ferramentas do Azure para o Eclipse][].
1. Um IDE do Eclipse para desenvolvedores do Java EE, Indigo ou posterior. Isso pode ser baixado em <http://www.eclipse.org/downloads/>.

### Etapas básicas de instalação ###

1. No Eclipse, no menu **Ajuda**, selecione **Instalar novo software**.
1. Digite o local do site <http://dl.msopentech.com/eclipse> e pressione **Enter**.
1. Selecione os itens a serem instalados e clique em **Concluir**.

O Kit de Ferramentas do Azure para o Eclipse usa a versão mais recente do SDK do Azure. Isso pode ser baixado usando o Web Platform Installer (WebPI) em <http://go.microsoft.com/fwlink/?LinkID=252838>. No entanto, se não o tiver instalado, quando você criar o seu primeiro projeto de implantação do Azure, o Kit de Ferramentas do Azure para Eclipse vai instalar automaticamente a versão apropriada do SDK do Azure.

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Instalando o Kit de Ferramentas do Azure para Eclipse][]

[Criando um aplicativo do Hello World para o Azure no Eclipse][]

Para obter mais informações sobre como usar o Azure com o Java, confira a [Central de desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Repositórios das Bibliotecas do Azure no Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criando um aplicativo do Hello World para o Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalando o Kit de Ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Instalando o Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Caminho de compilação do Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333

<!---HONumber=AcomDC_1203_2015-->