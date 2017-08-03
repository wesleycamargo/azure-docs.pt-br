---
title: "Publicar um aplicativo Spring Boot como um contêiner do Docker usando o Kit de Ferramentas do Azure para IntelliJ | Microsoft Docs"
description: "Saiba como publicar um aplicativo Web para o Microsoft Azure como um contêiner do Docker usando o Kit de ferramentas do Azure para IntelliJ."
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 8d1f0f0f95647c85b2b5f3550e306e3ce35e81c3
ms.contentlocale: pt-br
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-intellij"></a>Publicar um aplicativo Spring Boot como um contêiner do Docker usando o Kit de Ferramentas do Azure para IntelliJ

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores Java criar aplicativos de nível empresarial. Um dos projetos mais populares que é criado com base nessa plataforma é o [Spring Boot], que oferece um método simplificado para criar aplicativos Java autônomos.

**[Docker]**  é uma solução de software livre que ajuda os desenvolvedores a automatizar a implantação, a colocação em escala e o gerenciamento de seus aplicativos que estão sendo executados em contêineres.

Este tutorial orienta você pelas etapas para implantar um aplicativo Spring Boot como um contêiner de Docker para o Microsoft Azure usando o Kit de Ferramentas do Azure para IntelliJ.

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repo"></a>Clonagem do repositório padrão do aplicativo Spring Boot Docker

As etapas a seguir orientam você durante a clonagem de repositório do Docker do Spring Boot usando IntelliJ. Se você quiser usar uma linha de comando, consulte [Implantar um aplicativo Spring Boot em Linux no Serviço de Contêiner do Azure][Deploy Spring Boot on Linux in ACS].

1. Abra o IntelliJ.

1. Na tela de boas-vinda, escolha a opção **GitHub** no menu suspenso **Fazer check-out do controle de versão**.

   ![Fazer check-out do controle de versão][CL01]

1. Se você for solicitado a fazer logon, insira suas credenciais.

   * Se você estiver usando um nome de usuário/senha para fazer logon no GitHub:

      ![Insira a caixa de diálogo de credenciais do GitHub][CL02a]

   * Se você estiver usando um token para fazer logon no GitHub:

      ![Insira a caixa de diálogo de credenciais do GitHub][CL02b]

1. Digite `https://github.com/spring-guides/gs-spring-boot-docker.git` para a URL do repositório, especifique o caminho local e as informações de pasta e, em seguida, clique em **Clonar**.

   ![Caixa de diálogo Clonar repositório][CL03]

1. Escolha **Não** quando for solicitado a criar um projeto do IntelliJ.

   ![Criar um projeto do IntelliJ][CL04]

1. Na tela de boas-vinda, clique em **Importar Projeto**.

   ![Importar projeto][CL05]

1. Localize o caminho em que você clonou o repositório do Spring Boot, realce a pasta **completo** na raiz e depois clique em **OK**.

   ![Importar projeto][CL06]

1. Quando solicitado, escolha **Criar projeto de fontes existentes**.

   ![Criar projeto de contes existentes][CL07]

1. Especifique o nome do projeto ou aceite o padrão, verifique se o caminho correto para o **completo** e, em seguida, clique em **Próximo**.

   ![Especifique o nome do projeto][CL08]

1. Personalize quaisquer diretórios para importação e, em seguida, clique em **Próximo**.

   ![Escolher diretórios][CL09]

1. Examine as bibliotecas para importar e, em seguida, clique em **Próximo**.

   ![Examine as bibliotecas do projeto][CL10]

1. Examine a estrutura do módulo e, em seguida, clique em **Próximo**.

   ![Examinar a estrutura do módulo][CL11]

1. Especifique o seu JDK e, em seguida, clique em **Próximo**.

   ![Especificar o JDK][CL12]

1. Clique em **Concluir**.

   ![Concluir][CL13]

1. O IntelliJ importará o aplicativo Spring Boot como um projeto e exibirá a estrutura quando a importação estiver concluída.

   ![Aplicativo Spring Boot no IntelliJ][CL14]

## <a name="building-your-spring-boot-app"></a>Como criar seu aplicativo Spring Boot

### <a name="build-the-app-using-the-maven-pom"></a>Crie o aplicativo usando o POM do Maven

1. Abra a Janela de Ferramentas do Maven, se ainda não estiver aberta. Para fazer isso, clique em **Exibir** e, em seguida, **Janelas de Ferramentas** e então em **Projetos do Maven**.

   ![Exibir Janela de Ferramentas do Maven][BU01]

1. Na Janela de Ferramentas do Maven, clique com o botão direito do mouse em **Pacote** e escolha **Executar Build do Maven**. (Se seu projeto do Maven não aparecer automaticamente, talvez você precise clicar no ícone **Reimportar** na barra de ferramentas do Maven.)

   ![Executar build do Maven][BU02]

1. O IntelliJ deve exibir uma mensagem de SUCESSO DO BUILD quando seu aplicativo Spring Boot tiver sido criado com êxito.

   ![Sucesso do build][BU03]

### <a name="create-a-deployment-ready-artifact"></a>Criar um artefato pronto para implantação

Para publicar seu aplicativo Web, você precisará criar um artefato pronto para implantação. Para fazer isso, execute as seguintes etapas:

1. Abra seu projeto de aplicativo Web no IntelliJ.

1. Clique em **Arquivo** e depois em **Estrutura do Projeto**.

   ![Menu Estrutura do Projeto][ART01]

1. Clique no símbolo de sinal de adição verde ("**+**") para adicionar um artefato, clique em **JAR** e, em seguida, clique em **Vazio**.

   ![Adicionar Artefato][ART02]

1. Nomeie seu artefato sem adicionar a extensão ".jar" e, em seguida, especifique a pasta de destino para a saída do Maven.

   ![Especificar propriedades de artefato][ART03]

1. OPCIONAL: crie um manifesto para o artefato:

   a. Clique em **Criar Manifesto**.

      ![Especificar o caminho do artefato][ART04a]

   b. Escolha o caminho padrão para o artefato e clique em **OK**.

      ![Especificar o caminho do artefato][ART04b]

   c. Clique no botão de reticências **…** para especificar a classe principal.

      ![Localizar a classe principal][ART04c]

   d. Escolha sua classe principal e, em seguida, clique em **OK**.

      ![Especificar a classe principal][ART04d]

1. Clique em **OK**.

   ![Fechar a caixa de diálogo de Propriedades do Artefato][ART05]

> [!NOTE]
>
> Para obter mais informações sobre como criar artefatos no IntelliJ, consulte [Configurar Artefatos] no site da JetBrains.
>

### <a name="build-the-artifact-for-deployment"></a>Faça o build do artefato para implantação

1. Clique em **Criar** e, em seguida, clique em **Artefatos**.

   ![Menu de Criar Artefatos][BU04]

1. Quando o menu de contexto **Criar Artefato** for exibido, clique em **Criar**.

   ![Menu de contexto Criar Artefato][BU05]

1. O IntelliJ deve exibir o artefato concluído para o aplicativo Spring Boot na Janela de Ferramentas do Projeto.

   ![Artefato criado][BU06]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publicando seu aplicativo Web no Azure usando um contêiner do Docker

1. Se você não estiver conectado à sua conta do Azure, siga as etapas no artigo [Instruções de conexão para o Kit de Ferramentas do Azure para IntelliJ][Azure Sign In for IntelliJ].

1. Na janela de ferramentas do Explorador de Projeto, clique com o botão direito do mouse no projeto e, em seguida, selecione **Azure** > **Publicar como Contêiner Docker**.

   ![Publicar como Contêiner do Docker][PU01]

1. Quando a caixa de diálogo **Implantar Contêiner do Docker no Azure** for exibida, quaisquer hosts do Docker existentes serão exibidos. Se você optar por implantar em um host existente, poderá pular para a etapa 4. Caso contrário, você precisará executar as etapas a seguir para criar um novo host:

   a. Clique no símbolo de sinal de adição verde ("**+**").

      ![Adicionar novo host do Docker][PU02]

   b. Quando a caixa de diálogo **Criar Host do Docker** for exibida, você poderá optar por aceitar os padrões ou especificar quaisquer configurações personalizadas para o novo host do Docker. (Descrições detalhadas das diversas configurações estão disponíveis no artigo [Publicar um aplicativo Web como um contêiner do Docker usando o Kit de Ferramentas do Azure para IntelliJ][Publish Container with Azure Toolkit].) Clique em **Próximo** quando tiver especificado as configurações a serem usadas.

      ![Especificar opções de host do Docker][PU03a]

   c. Você pode optar por usar as credenciais de logon existentes de um Azure Key Vault ou inserir novas credenciais de logon do Docker. Clique em **Concluir** quando tiver especificado suas opções.

      ![Especificar as credenciais de host do Docker][PU03b]

1. Realce o host do Docker e, em seguida, clique em **Próximo**.

   ![Selecione o host do Docker a usar][PU04]

1. Na última página da caixa de diálogo **Implantar Contêiner do Docker no Azure**, você precisará especificar as seguintes opções:

   a. Você pode optar por especificar um nome personalizado para o contêiner que hospedará o contêiner do Docker ou aceitar o padrão.

   b. Você precisa inserir as portas TCP para o host do docker usando a seguinte sintaxe: "*[porta externa]*:*[porta interna]*. Por exemplo, "80:8080" especifica uma porta externa de "80" e a porta do Spring Boot interna padrão de "8080".
   
      Se você tiver personalizado sua porta interna (por exemplo, editando o arquivo *application.yml*), precisará especificar o número da porta para o roteamento correto ocorrer no Azure.

   c. Depois de configurar essas opções, clique em **Concluir**.

   ![Implantar o Contêiner do Docker no Azure][PU05]

1. Quando o Kit de Ferramentas do Azure terminar a publicação, o Log de Atividades do Azure exibirá **Publicado** como o status.

   ![Host do Docker implantado com êxito][PU06]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

Consulte [Como criar projetos do Spring Boot](https://www.jetbrains.com/help/idea/creating-spring-boot-projects.html) no site da JetBrains para conhecer outros métodos para criar aplicativos Spring Boot usando o IntelliJ.

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Sign In for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Configurar Artefatos]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL01.png
[CL02a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02a.png
[CL02b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02b.png
[CL03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL09.png
[CL10]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL10.png
[CL11]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL11.png
[CL12]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL12.png
[CL13]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL13.png
[CL14]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL14.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART03.png
[ART04a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04a.png
[ART04b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04b.png
[ART04c]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04c.png
[ART04d]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04d.png
[ART05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART05.png

[BU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU02.png
[BU03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU03.png
[BU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU04.png
[BU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU05.png
[BU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU06.png

[PU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU02.png
[PU03a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03a.png
[PU03b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03b.png
[PU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU06.png

