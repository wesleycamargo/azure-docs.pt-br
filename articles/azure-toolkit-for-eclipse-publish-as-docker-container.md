---
title: "Publicar um contêiner do Docker usando o Kit de ferramentas do Azure para Eclipse | Microsoft Docs"
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
ms.date: 04/14/2017
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f738647fa1dad757b53611c7d4abe2dd99fe8838
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Como publicar um aplicativo Web como um contêiner do Docker usando o Kit de ferramentas do Azure para Eclipse

Contêineres do Docker são um método amplamente usado para implantar aplicativos Web em que os desenvolvedores podem consolidar todos os arquivos de projeto e dependências em um único pacote para implantação em um servidor. O Kit de ferramentas do Azure para Eclipse simplifica o processo para desenvolvedores de Java adicionando funcionalidades para *Publicar como contêiner do Docker* para implantação no Microsoft Azure e este artigo consiste em um passo a passo pelas etapas necessárias para publicar os aplicativos do Azure como contêineres do Docker.

> [!NOTE]
>
> Para obter mais informações sobre o Docker, consulte o [site do Docker].
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Publicando seu aplicativo Web no Azure usando um contêiner do Docker

1. Abra seu projeto de aplicativo Web no Eclipse.

1. Use um dos métodos a seguir para iniciar o assistente Publicar como Contêiner do Docker:

   * Clique com o botão direito do mouse no projeto na exibição **Navegador**, depois clique em **Azure** e, em seguida, clique em **Publicar como Contêiner do Docker**:  ![Publicar como Contêiner do Docker][PUB01]

   * Clique no menu **Publicar** na barra de ferramentas do Eclipse e clique em **Publicar como Contêiner do Docker**:  ![Publicar como Contêiner do Docker][PUB02]

1. Quando o assistente **Implantar o contêiner do Docker no Azure** é exibido, você vê uma caixa de diálogo semelhante à ilustração a seguir: ![Assistente Implantar o contêiner do Docker no Azure][PUB03]

   a. Insira um nome exclusivo na caixa de texto para o host do Docker na caixa de texto **Nome da imagem do Docker**. (O assistente criará automaticamente um nome para você, mas você poderá modificá-lo se quiser.)

   b. A janela **Hosts** exibirá quaisquer hosts do Docker que você tiver criado.
      * Se você não tiver criado nenhum host do Docker, esta seção da caixa de diálogo estará vazia.
      * Se você já tiver criado um host do Docker, você poderá optar por implantar seu aplicativo Web em um host existente; caso contrário, siga as etapas abaixo para criar um novo host do Docker.

1. Para criar um novo host do Docker, clique no botão **Adicionar**; isso iniciará a caixa de diálogo **Criar Host do Docker**.
      ![Assistente Implantar o Contêiner do Docker no Azure][PUB04]

   a. Especifique as opções a seguir para o host do Docker. (O assistente gerará a maioria das opções automaticamente para você, mas você poderá modificar as opções que quiser personalizar.)

      * **Nome**: este é um nome exclusivo para o host do Docker. (Isso não é o mesmo que o nome da imagem do Docker que você especificou anteriormente.)

      * **Assinatura**: especifique qual assinatura do Azure você usará para o host.
      
      * **Região**: especifique a região geográfica em que o host estará localizado.
      
      * Sobre a guia **Sistema Operacional e Tamanho**:
         * **Sistema Operacional de Host**: especifique o sistema operacional da máquina virtual que conterá o seu host.
         * **Tamanho**: especifique o tamanho da máquina virtual de seu host.

      * Na guia **Grupo de Recursos**:
         * **Novo grupo de recursos**: permite que você crie um novo grupo de recursos para o host.
         * **Grupo de recursos existente**: permite que você especifique um grupo de recursos existente de sua conta do Azure.

      * Na guia **Rede**:
         * **Nova rede virtual**: permite que você crie uma nova rede virtual para o host.
         * **Rede virtual existente**: permite que você especifique uma rede virtual existente de sua conta do Azure.

      * Sobre na guia **Armazenamento**:
         * **Nova conta de armazenamento**: permite que você crie uma nova conta de armazenamento para o host.
         * **Conta de armazenamento existente**: permite que você especifique uma conta de armazenamento existente da sua conta do Azure.

   b. Quando você tiver especificado todas as opções acima, clique em **Avançar**.

   c. Escolha uma das opções a seguir para o logon de máquina virtual do host do Docker:    ![Criar Host do Docker][PUB05]

      * **Importar credenciais do Azure Key Vault**: permite que você especifique um conjunto de credenciais que são armazenadas em sua assinatura do Azure salva anteriormente.

      > [!NOTE]
      > Um Azure Key Vault que é criado com uma conta ou entidade de serviço específica não poderá ser acessado automaticamente por outra conta ou entidade de serviço que compartilhe a mesma assinatura. Para permitir que outra conta ou entidade de serviço use o Key Vault, você precisará usar o Portal do Azure para adicionar a conta ou a entidade de serviço.

      * **Novas credenciais de logon**: permite que você crie um novo conjunto de credenciais de logon, o que exigirá que você especifique as seguintes opções na guia **Credenciais de VM**:
         * **Nome de usuário**: especifica o nome de usuário para o logon de máquina virtual.
         * **Senha** e **Confirmar**: especifica a senha para o logon de máquina virtual.
         * **SSH**: especifica as definições do SSH (Secure Shell) para o host do Docker; você pode escolher entre as seguintes opções:
            * **Nenhum**: especifica que a sua máquina virtual não permitirá conexões SSH.
            * **Gerar automaticamente**: essa opção cria automaticamente as configurações necessárias para conectar-se via SSH.
            * **Importar do diretório**: permite que você especifique um diretório que contém um conjunto de configurações de SSH salvas anteriormente. Mais especificamente, o diretório deve conter os dois arquivos a seguir:
               * *id_rsa*: esse arquivo contém a identificação da RSA para um usuário.
               * *id_rsa. pub*: esse arquivo contém a chave pública RSA, que será usada para autenticação.
        
      * No **Acesso de Daemon do Docker**, especifique as seguintes opções: ![Criar Host do Docker][PUB06]

         * **Porta de Daemon do Docker**: especifica a porta TCP exclusiva para o host do Docker.
         * **Segurança TLS**: especifica as configurações do protocolo TLS para o host do Docker; você pode escolher entre as seguintes opções:
            * **Nenhum**: especifica que a sua máquina virtual não permitirá conexões TLS.
            * **Gerar automaticamente**: essa opção cria automaticamente as configurações necessárias para conectar-se via TLS.
            * **Importar do diretório**: permite que você especifique um diretório que contém um conjunto de configurações de TLS salvas anteriormente. Mais especificamente, o diretório deve conter os seis arquivos a seguir:
               * *ca.pem* e *ca-key.pem*: esses arquivos contêm o certificado e a chave pública da autoridade de certificado TLS.
               * *cert.pem* e *key.pem*: esses arquivos contêm o certificado do cliente e a chave pública que serão usados para autenticação TLS.
               * *server.pem* e *server-key.pem*: esses arquivos contêm o certificado do servidor e a chave pública para o host.

   d. Quando você tiver especificado todas as opções acima, clique em **Concluir**.

1. Quando o **assistente Implantar o Contêiner do Docker no Azure** for exibido novamente, clique em **Avançar**.
   ![Assistente Implantar o Contêiner do Docker no Azure][PUB07]

1. Na última página do assistente, especifique as opções a seguir:

   * **Nome do contêiner do Docker**: esse é o nome exclusivo do contêiner do Docker.

   * Escolha uma das seguintes imagens do Docker:

      * **Imagem do Docker predefinida**: permite que você especifique uma imagem já existente do Docker do Azure.

      > [!NOTE]
      > A lista de imagens do Docker no menu suspenso consiste em várias imagens que o Kit de ferramentas do Azure foi configurado para aplicar como patches, de modo que o artefato será implantado automaticamente.

      * **Dockerfile personalizado**: permite que você especifique um Dockerfile salvo anteriormente do computador local.

      > [!NOTE]
      > Essa é uma funcionalidade mais avançada para desenvolvedores que desejam implantar um Dockerfile próprio. No entanto, cabe aos desenvolvedores que usam essa opção garantir que o Dockerfile seja compilado corretamente. O Kit de ferramentas do Azure não valida o conteúdo em um Dockerfile personalizado, portanto, a implantação poderá falhar se o Dockerfile tiver problemas. Além disso, o Kit de ferramentas do Azure espera que o Dockerfile personalizado contenha um artefato de aplicativo Web e tentará abrir uma conexão HTTP; se os desenvolvedores publicarem um tipo diferente de artefato, eles poderão receber erros inócuos após a implantação.

   * **Configurações de porta**: Especifica a associação de porta TCP exclusiva para seu contêiner do Docker.
   ![Assistente Implantar o Contêiner do Docker no Azure][PUB08]

1. Quando você tiver concluído todas as etapas acima, clique em **Concluir**.

O Kit de ferramentas do Azure começará a implantar seu aplicativo Web no Azure em um contêiner do Docker. 

## <a name="see-also"></a>Consulte também
Para obter mais informações sobre os kits de ferramentas do Azure para Java IDEs, confira os links a seguir:

* [Kit de ferramentas do Azure para Eclipse]
  * [Novidades no Kit de Ferramentas do Azure para o Eclipse]
  * [Instalação do Kit de Ferramentas do Azure para o Eclipse]
  * [Instruções de entrada para o Kit de ferramentas do Azure para Eclipse]
  * [Criar um aplicativo Web Hello World para o Azure no Eclipse]
* [Kit de Ferramentas do Azure para IntelliJ]
  * [Novidades no Kit de Ferramentas do Azure para IntelliJ]
  * [Instalação do Kit de Ferramentas do Azure para IntelliJ]
  * [Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ]
  * [Criar um aplicativo Web Hello World para o Azure no IntelliJ]

Para saber mais sobre como usar o Azure com Java, confira o [Centro de Desenvolvedores Java do Azure] e as [Ferramentas Java para Visual Studio Team Services].

Para obter recursos adicionais para o Docker, consulte o [site do Docker] oficial.

<!-- URL List -->

[Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar um aplicativo Web Hello World para o Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar um aplicativo Web Hello World para o Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalação do Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalação do Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Instruções de entrada para o Kit de ferramentas do Azure para Eclipse]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Instruções de entrada para o Kit de ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Novidades no Kit de Ferramentas do Azure para o Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Novidades no Kit de Ferramentas do Azure para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de Desenvolvedores Java do Azure]: https://azure.microsoft.com/develop/java/
[Ferramentas Java para Visual Studio Team Services]: https://java.visualstudio.com/

[site do Docker]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png

