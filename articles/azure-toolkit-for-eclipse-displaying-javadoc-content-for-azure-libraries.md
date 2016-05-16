<properties
    pageTitle="Exibindo o Conteúdo do Javadoc no Eclipse para o Pacote de Bibliotecas do Azure para Java"
    description="Como exibir o conteúdo do Javadoc para as Bibliotecas do Azure no Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="05/04/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# Exibindo o Conteúdo do Javadoc no Eclipse para o Pacote de Bibliotecas do Azure para Java #

O conteúdo do Javadoc para as bibliotecas do Azure para Java pode ser exibido em seu ambiente do Eclipse associando o conteúdo do Javadoc às bibliotecas do Azure para Java. As etapas a seguir mostram como usar essa funcionalidade no Eclipse.

Este procedimento pressupõe que você já adicionou a Biblioteca do Azure para Java ao seu caminho de compilação.

## Para exibir o conteúdo do Javadoc no Eclipse para as Bibliotecas do Azure para Java ##

* No Explorador de projeto do Eclipse, na seção **Bibliotecas Referenciadas** do seu projeto, abra o menu de contexto para a Biblioteca do Azure para Java JAR. Por exemplo, **microsoft-windowsazure-api-0.1.0.jar** (o número de versão pode ser diferente, dependendo de qual versão você instalou).
* Clique em **Propriedades**.
* Na caixa de diálogo **Propriedades**, no painel à esquerda, cliquem em **Javadoc Location**. A caixa de diálogo **Javadoc local** será exibida.
* Você pode especificar uma **URL Javadoc**, ou um **Javadoc no arquivo**.
    * Se optar por especificar uma **URL Javadoc**, use as URLs como **http://dl.windowsazure.com/javadoc** ou **http://dl.windowsazure.com/storage/javadoc**.
* Se optar por usar **Javadoc no arquivo**, você pode especificar um arquivo externo ou um arquivo de espaço de trabalho. Faça sua escolha e procure/valide conforme necessário. O exemplo a seguir associa as Bibliotecas do Azure para Java com o Javadoc JAR correspondente que foi baixado localmente para uma pasta chamada **c:\\MyAzureJARs**. ![][ic553487]
* *Opcional*: Clique em **Validar**. Problemas potenciais com o Javadoc JAR podem ser exibidos aqui.
* Clique em **OK**.

Quando associado à biblioteca, o conteúdo do Javadoc deve exibir seu IDE do Eclipse. Por exemplo, se `blob` definido do tipo `CloudBlockBlob` dentro de seu código, o seguinte é um exemplo do conteúdo do Javadoc que aparece quando você digita `blob.acquireLease` no código:

![][ic553488]

## Consulte também ##

[Kit de ferramentas do Azure para Eclipse][]

[Criar um aplicativo Hello World do Azure no Eclipse][]

[Instalação do Kit de Ferramentas do Azure para Eclipse][]

Para saber mais sobre como usar o Azure com Java, confira a [Central de Desenvolvimento Java do Azure][].

<!-- URL List -->

[Central de Desenvolvimento Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalação do Kit de Ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!---HONumber=AcomDC_0504_2016-->