---
title: Baixar o SDK do Azure para Java | Microsoft Docs
description: "Saiba como baixar o SDK do Azure para Java, com código de exemplo fornecido para projetos Maven."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 4b8f8fe6-1b26-4bb4-9be9-6ae757a59e66
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm;asirveda
translationtype: Human Translation
ms.sourcegitcommit: 0933eabf99ef94f2c4b425f1435530edfde65e3f
ms.openlocfilehash: 9e55eb52797f1363cbb65050574e549c575ca5d8
ms.lasthandoff: 01/14/2017


---
# <a name="download-the-azure-sdk-for-java"></a>Baixar o SDK do Azure para Java
Este artigo contém instruções sobre como baixar e instalar as Bibliotecas de Gerenciamento do Azure para Java.

> [!NOTE]
> As Bibliotecas de Gerenciamento do Azure para Java são distribuídas sob a [Licença do Apache, versão 2.0][license].
>

## <a name="azure-libraries-for-java---manual-download"></a>Bibliotecas do Azure para Java - Download manual
Para instalar manualmente as Bibliotecas de Gerenciamento do Azure para Java, clique em <http://go.microsoft.com/fwlink/?LinkId=690320> para baixar um arquivo ZIP que contém todas as bibliotecas e todas as dependências.

Depois de baixar o arquivo zip em seu computador, extraia seu conteúdo e use uma das seguintes opções para adicionar os arquivos JAR ao seu projeto:

* Importe os arquivos JAR para o seu projeto do Java no Eclipse ou IntelliJ.
* Configure os caminhos de compilação em seu projetos do Java no Eclipse ou IntelliJ para incluir o caminho para os arquivos JAR.

> [!NOTE]
> Confira os arquivos license.txt e ThirdPartyNotices.txt dentro do ZIP para ver a licença e outras informações.
>

## <a name="azure-management-libraries-for-java---building-with-maven"></a>Bibliotecas de Gerenciamento do Azure para Java - Compilação com o Maven
### <a name="step-1---set-up-your-project-to-use-maven-for-build"></a>Etapa 1 - Configurar seu projeto para usar o Maven para compilação
Para criar projetos do Maven no Eclipse que usem as Bibliotecas de Gerenciamento do Azure para Java, siga as instruções no artigo [Getting Started with Azure Management Libraries for Java][maven-getting-started] (Introdução às Bibliotecas de Gerenciamento do Azure para Java).

### <a name="step-2---configure-your-maven-settings-with-the-requisite-dependencies"></a>Etapa 2 - Definir as configurações do Maven com as dependências necessárias
Depois que o projeto foi configurado para usar o Maven para build, é possível adicionar as dependências necessárias ao arquivo pom.xml usando uma sintaxe como mostrada no exemplo a seguir. Observe que você não precisa adicionar cada dependência que é listada no exemplo a seguir; você precisa adicionar apenas as dependências específicas necessários para o seu projeto.

> [!NOTE]
> Dentro de cada elemento `<version>` no exemplo a seguir, substitua os espaços reservados "n.n.n" neste exemplo por números de versão válidos, que podem ser obtidos no [Repositório de bibliotecas do Azure no Maven].
>
>

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-compute</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-network</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-sql</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-storage</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-websites</artifactId>
        <version>n.n.n</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-svc-mgmt-media</artifactId>
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

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre os kits de ferramentas do Azure para Java IDEs, confira os links a seguir:

* [Kit de ferramentas do Azure para Eclipse]
  * [Instalação do Kit de Ferramentas do Azure para o Eclipse]
  * [Criar um aplicativo Web Hello World para o Azure no Eclipse]
  * [Novidades no Kit de Ferramentas do Azure para o Eclipse]
* [Kit de Ferramentas do Azure para IntelliJ]
  * [Instalação do Kit de Ferramentas do Azure para IntelliJ]
  * [Criar um aplicativo Web Hello World para o Azure no IntelliJ]
  * [Novidades no Kit de Ferramentas do Azure para IntelliJ]

Para saber mais sobre como usar o Azure com o Java, confira o [Centro de Desenvolvedores Java do Azure].

> [!NOTE]
> Para obter informações detalhadas sobre como configurar os caminhos de compilação no Eclipse, confira o artigo [Caminho de compilação do Java] no site do Eclipse.
>

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo Web Hello World para o Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo Web Hello World para o Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalação do Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de Desenvolvedores Java do Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Repositório de bibliotecas do Azure no Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Caminho de compilação do Java]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998

