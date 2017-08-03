---
title: "Publicar um aplicativo Spring Boot como um contêiner do Docker usando o Kit de Ferramentas do Azure para Eclipse | Microsoft Docs"
description: "Saiba como publicar um aplicativo Web para o Microsoft Azure como um contêiner do Docker usando o Kit de ferramentas do Azure para Eclipse."
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
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: pt-br
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Publicar um aplicativo Spring Boot como um contêiner do Docker usando o Kit de Ferramentas do Azure para Eclipse

O **[Spring Framework]** é uma solução de software livre que ajuda os desenvolvedores Java criar aplicativos de nível empresarial. Um dos projetos mais populares que é criado com base nessa plataforma é o [Spring Boot], que oferece um método simplificado para criar aplicativos Java autônomos.

**[Docker]**  é uma solução de software livre que ajuda os desenvolvedores a automatizar a implantação, a colocação em escala e o gerenciamento de seus aplicativos que estão sendo executados em contêineres.

Este tutorial orienta você pelas etapas para implantar um aplicativo Spring Boot como um contêiner de Docker para o Microsoft Azure usando o Kit de Ferramentas do Azure para Eclipse.

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>Clonagem do repositório padrão do aplicativo Spring Boot Docker

### <a name="importing-the-public-repository"></a>Como importar o repositório público

As etapas a seguir orientam você durante a clonagem do repositório do Spring Boot Docker para seu computador local usando IntelliJ. Se você quiser usar uma linha de comando, consulte [Implantar um aplicativo Spring Boot em Linux no Serviço de Contêiner do Azure][Deploy Spring Boot on Linux in ACS].

1. Abra o Eclipse.

1. Clique em **Arquivo** e, em seguida, em **Importação**.

   ![Menu de Importação de Arquivo][CL01]

1. Quando a caixa de diálogo **Importação** se abrir:

   a. Expanda **Git**.

   b. Destaque **Projetos do Git**.
   
   c. Clique em **Avançar**.

   ![Caixa de diálogo de Importação][CL02]

1. Na página **Selecionar a Origem do Repositório**:

   a. Destaque **Clonar URI**.
   
   b. Clique em **Avançar**.

   ![Selecionar a Origem do Repositório][CL03]

1. Na página **Repositório Git de Origem**:

   a. Digite `https://github.com/spring-guides/gs-spring-boot-docker.git` para o **URI** do repositório; isso deve preencher automaticamente os campos **Host** e **Caminho do repositório** com os valores corretos.
   
   b. O repositório Spring Boot é público, assim, você não precisa inserir seu nome de usuário e senha do Git.
   
   c. Clique em **Avançar**.

   ![Repositório Git de Origem][CL04]

1. Na página **Branch**, clique em **Próximo**.

   ![Branch][CL05]

1. Na página **Destino Local**:

   a. Especifique a pasta local em que você deseja colocar seu repositório local.
   
   b. Clique em **Avançar**.

   ![Destino local][CL06]

1. Na página **Selecionar um assistente a usar para importar projetos**:

   a. Selecione **Importar como um projeto geral**.
   
   b. Clique em **Avançar**.

   ![Destino local][CL07]

1. Na página **Importar Projetos**:

   a. Especifique o **Nome do projeto**.
   
   b. Clique em **Concluir**.

   ![Importar projetos][CL08]

1. Quando o repositório tiver sido clonado com êxito, você verá todos os arquivos listados no Eclipse.

   ![Repositório local][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>Criando um projeto Maven do seu repositório local

O repositório do Spring Boot Docker contém um projeto Maven completo que você usará para este tutorial. 

1. Clique em **Arquivo** e, em seguida, em **Importação**.

   ![Menu de Importação de Arquivo][CL01]

1. Quando a caixa de diálogo **Importação** se abrir:

   a. Expanda **Maven**.
   
   b. Destaque **Projetos Maven Existentes**.
   
   c. Clique em **Avançar**.

   ![Caixa de diálogo de Importação][MV01]

1. Na página **Projetos Maven**:

   a. Especifique a pasta `complete` dentro de seu repositório local para o **Diretório Raiz**.
   
   b. Expanda a seção **Avançado** e insira um nome personalizado para o **Modelo de nome**.
   
   c. Marque a caixa para o arquivo `pom.xml` no projeto.
   
   d. Clique em **Concluir**.

   ![Projetos Maven][MV02]

1. Quando o projeto Maven tiver sido aberto com êxito, você verá um segundo projeto listado no Eclipse.

   ![Projeto do Maven local][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>Como criar seu aplicativo Spring Boot com Maven

1. No Eclipse Project Explorer, destaque o projeto Maven.

1. Clique em **Executar**, em seguida, em **Executar como** e então em **Build do Maven**.

   ![Executar como build do Maven][BU01]

1. Quando seu aplicativo tiver sido criado com êxito, a janela do console listará o status.

   ![Build bem-sucedido do Maven][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publicando seu aplicativo Web no Azure usando um contêiner do Docker

1. No Eclipse Project Explorer, destaque o projeto Maven.

1. Clique no menu **Publicar** do Azure e, em seguida, clique em **Publicar como contêiner do Docker**.

   ![Publicar como contêiner do Docker][PU01]

1. Quando a caixa de diálogo **Implantar Contêiner do Docker no Azure** for exibida:

   a. Insira um **nome de imagem do Docker** personalizado.
   
   b. Para o **Artefato a implantar**, especifique o caminho para o arquivo `gs-spring-boot-docker-0.1.0.jar` que você acabou de criar.

   ![Especificar opções do Docker][PU02]

   Quaisquer hosts do Docker existentes serão exibidos. Se você optar por implantar em um host existente, poderá pular para a etapa 4. Caso contrário, você precisará executar as etapas a seguir para criar um novo host:

   a. Clique em **Adicionar**.

      ![Adicionar novo host do Docker][PU03]

   b. Quando a caixa de diálogo **Criar Host do Docker** for exibida, você poderá optar por aceitar os padrões ou especificar quaisquer configurações personalizadas para o novo host do Docker. (Descrições detalhadas das diversas configurações estão disponíveis no artigo [Publicar um aplicativo Web como um contêiner do Docker usando o Kit de Ferramentas do Azure para IntelliJ][Publish Container with Azure Toolkit].) Clique em **Próximo** quando tiver especificado as configurações a serem usadas.

      ![Especificar opções de host do Docker][PU04]

   c. Você pode optar por usar as credenciais de logon existentes de um Azure Key Vault ou inserir novas credenciais de logon do Docker. Clique em **Concluir** quando tiver especificado suas opções.

      ![Especificar as credenciais de host do Docker][PU05]

1. Realce o host do Docker e, em seguida, clique em **Próximo**.

   ![Selecione o host do Docker a usar][PU06]

1. Na última página da caixa de diálogo **Implantar Contêiner do Docker no Azure**, você precisará especificar as seguintes opções:

   a. Você pode optar por especificar um nome personalizado para o contêiner que hospedará o contêiner do Docker ou aceitar o padrão.

   b. Você precisa inserir as portas TCP para o host do docker usando a seguinte sintaxe: "*[porta externa]*:*[porta interna]*. Por exemplo, "80:8080" especifica uma porta externa de "80" e a porta do Spring Boot interna padrão de "8080".
   
      Se você tiver personalizado sua porta interna (por exemplo, editando o arquivo *application.yml*), precisará especificar o número da porta para o roteamento correto ocorrer no Azure.

   c. Depois de configurar essas opções, clique em **Concluir**.

   ![Implantar o Contêiner do Docker no Azure][PU07]

1. Quando o Kit de Ferramentas do Azure terminar a publicação, o Log de Atividades do Azure exibirá **Publicado** como o status.

   ![Host do Docker implantado com êxito][PU08]

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

