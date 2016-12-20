---
title: "Exibindo o Conteúdo do Javadoc no Eclipse para o Pacote de Bibliotecas do Azure para Java"
description: "Como exibir o conteúdo do Javadoc para as Bibliotecas do Azure no Eclipse."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 30f8b6a1-1d76-4d1c-861b-1db478c46e6b
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 11/01/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a0b0de62a891b579875edbb687e013316c491527


---
# <a name="displaying-javadoc-content-in-eclipse-for-the-azure-libraries-package-for-java"></a>Exibindo o Conteúdo do Javadoc no Eclipse para o Pacote de Bibliotecas do Azure para Java
O conteúdo do Javadoc para as bibliotecas do Azure para Java pode ser exibido em seu ambiente do Eclipse associando o conteúdo do Javadoc às bibliotecas do Azure para Java. As etapas a seguir mostram como usar essa funcionalidade no Eclipse.

Este procedimento pressupõe que você já adicionou a Biblioteca do Azure para Java ao seu caminho de compilação.

## <a name="to-display-javadoc-content-in-eclipse-for-the-azure-libraries-for-java"></a>Para exibir o conteúdo do Javadoc no Eclipse para as Bibliotecas do Azure para Java
* No Explorador de projeto do Eclipse, na seção **Bibliotecas Referenciadas** do seu projeto, abra o menu de contexto para a Biblioteca do Azure para Java JAR. Por exemplo, **microsoft-windowsazure-api-0.1.0.jar** (o número de versão pode ser diferente, dependendo de qual versão você instalou).
* Clique em **Propriedades**.
* Na caixa de diálogo **Propriedades**, no painel à esquerda, cliquem em **Javadoc Location**. A caixa de diálogo **Javadoc local** será exibida.
* Você pode especificar uma **URL Javadoc**, ou um **Javadoc no arquivo**.
  * Se você optar por especificar uma **URL Javadoc**, use URLs como **http://dl.windowsazure.com/javadoc** ou **http://dl.windowsazure.com/storage/javadoc**.
  * Se optar por usar **Javadoc no arquivo**, você pode especificar um arquivo externo ou um arquivo de espaço de trabalho.
    Faça sua escolha e procure/valide conforme necessário. O exemplo a seguir associa as Bibliotecas do Azure para Java com o Javadoc JAR correspondente que foi baixado localmente para uma pasta chamada **c:\MyAzureJARs**.
    ![][ic553487]
* *Opcional*: Clique em **Validar**. Problemas potenciais com o Javadoc JAR podem ser exibidos aqui.
* Clique em **OK**.

Quando associado à biblioteca, o conteúdo do Javadoc deve exibir seu IDE do Eclipse. Por exemplo, se `blob` definido do tipo `CloudBlockBlob` dentro de seu código, o seguinte é um exemplo do conteúdo do Javadoc que aparece quando você digita `blob.acquireLease` no código:

![][ic553488]

## <a name="see-also"></a>Consulte também
[Kit de ferramentas do Azure para Eclipse][Kit de ferramentas do Azure para Eclipse]

[Criação de um aplicativo Hello World do Azure no Eclipse][Criação de um aplicativo Hello World do Azure no Eclipse]

[Instalação do Kit de Ferramentas do Azure para o Eclipse][Instalação do Kit de Ferramentas do Azure para o Eclipse] 

Para obter mais informações sobre o uso do Azure com Java, consulte o [Central de desenvolvedores de Java no Azure][Central de desenvolvedores de Java no Azure].

<!-- URL List -->

[Central de desenvolvedores de Java no Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ferramentas do Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criação de um aplicativo Hello World do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->



<!--HONumber=Nov16_HO3-->


