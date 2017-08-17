---
title: "Implantar um aplicativo Web Spring Boot no Linux no Serviço de Contêiner do Azure | Microsoft Docs"
description: "Este tutorial orienta você pelas etapas de implantar um aplicativo Spring Boot como um aplicativo Web do Linux no Microsoft Azure."
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
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: pt-br
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>Implantar um aplicativo Spring Boot no Linux no Serviço de Contêiner do Azure

O [Spring Framework] é uma solução de software livre que ajuda os desenvolvedores do Java a criar aplicativos de nível empresarial. Um dos projetos mais populares que é criado com base no Java é o [Spring Boot], que fornece uma abordagem simplificada para a criação de aplicativos Java autônomos.

O [Docker] é uma solução de software livre que ajuda os desenvolvedores a automatizar a implantação, o dimensionamento e o gerenciamento dos aplicativos deles que estão em execução em contêineres.

Este tutorial orienta você em como usar o Docker para desenvolver e implantar um aplicativo Spring Boot em um host do Linux no [Serviço de Contêiner do Azure].

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste tutorial, você precisará do seguinte:

* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN] ou inscrever-se para obter uma [conta gratuita do Azure].
* A [CLI (interface de linha de comando) do Azure].
* Um [JDK (Java Developer Kit)] atualizado.
* A ferramenta de compilação [Maven] do Apache (Versão 3).
* Um cliente [Git].
* Um cliente do [Docker].

> [!NOTE]
>
> Por causa dos requisitos de virtualização deste tutorial, você não pode seguir as etapas neste artigo em uma máquina virtual. Em vez disso, você deve usar um computador físico com recursos de virtualização habilitados.
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>Criar o aplicativo Web de Introdução ao Spring Boot no Docker

As etapas a seguir ajudam você a criar um aplicativo Web Spring Boot simples e testá-lo localmente.

1. Abra um prompt de comando. Em seguida, crie um diretório local para conter o aplicativo e altere para o diretório, por exemplo:
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- ou --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. Clone o exemplo de projeto [Introdução ao Spring Boot no Docker] para o diretório criado. Por exemplo:
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. Altere o diretório para o projeto concluído. Por exemplo:
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **Etapa opcional**: se você quiser executar o servidor Tomcat inserido na porta 80, em vez de na porta 8080 padrão (por exemplo, se você for testar localmente seu projeto do Spring Boot), configure a porta usando as seguintes etapas:

   a. Altere o diretório para o diretório de recursos. Por exemplo:
   ```
   cd src/main/resources
   ```

   b. Abra o arquivo **application.yml** em um editor de texto.

   c. Modifique a configuração **server:** para que o servidor seja executado na porta 80, por exemplo:
   ```
   server:
      port: 80
   ```

   d. Salve e feche o arquivo **application.yml**.

   e. Altere o diretório de volta para a pasta raiz do projeto concluído. Por exemplo:
   ```
   cd ../../..
   ```

5. Crie o arquivo JAR usando o Maven, por exemplo:
   ```
   mvn package
   ```

6. Quando o aplicativo Web tiver sido criado, vá para o diretório `target` em que o arquivo JAR está localizado e inicie o aplicativo Web, por exemplo:
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. Teste o aplicativo Web navegando até ele localmente usando um navegador da Web. Por exemplo, você pode usar o comando a seguir se a ondulação estiver disponível e você tiver configurado o servidor Tomcat para ser executado na porta 80:
   ```
   curl http://localhost
   ```

8. Você verá a seguinte mensagem exibida: **Hello Docker World!**

   ![Procurar aplicativo de exemplo localmente][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Criar um Registro de Contêiner do Azure para usar como um registro do Docker Privado

As etapas a seguir orientam você no uso do Portal do Azure para criar um Registro de Contêiner do Azure.

> [!NOTE]
> Se você quiser usar a CLI do Azure, em vez do Portal do Azure, siga as etapas em [Criar um registro de contêiner do Docker privado usando a CLI do Azure 2.0][1].

1. Entre no [Portal do Azure].

    Após você ter entrado em sua conta no Portal do Azure, siga as etapas em [Criar um Registro de Contêiner do Docker privado usando o Portal do Azure]. As etapas desse artigo são resumidas aqui:

2. Selecione o ícone do menu para **+ Novo**.

3. Selecione **Contêineres** e, em seguida, selecione **Registro de Contêiner do Azure**.

   ![Criar um novo Registro de Contêiner do Azure][AR01]

4. Quando a página de informações para o modelo de Registro de Contêiner do Azure for exibida, clique em **Criar**.

   ![Criar um novo Registro de Contêiner do Azure][AR02]

5. Quando a folha **Criar Registro de Contêiner** é exibida:

   a. Insira seu **Nome do registro** e **Grupo de recursos**.  
   
   b. Selecione **Habilitar** para o **Usuário administrador**.
   
   c. Selecione **Criar**.

   ![Definir configurações do Registro de Contêiner do Azure][AR03]

6. Depois que o registro de contêiner tiver sido criado, acesse-o no Portal do Azure. Em seguida, selecione **Chaves de Acesso**. Anote o nome de usuário e a senha para as próximas etapas.

   ![Chaves de acesso do Registro de Contêiner do Azure][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Configurar o Maven para usar as chaves de acesso do Registro de Contêiner do Azure

1. Vá para o diretório de configuração para sua instalação do Maven. Abra o arquivo **settings.xml** com um editor de texto.

2. Adicione as configurações de acesso do Registro de Contêiner do Azure da seção anterior deste tutorial para a coleção `<servers>` no arquivo **settings.xml**; por exemplo:

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. Vá para o diretório do projeto completo de seu aplicativo Spring Boot (por exemplo, **C:\SpringBoot\gs-spring-boot-docker\complete** ou **/users/robert/SpringBoot/gs-spring-boot-docker/complete**). Em seguida, abra o arquivo **pom.xml** com um editor de texto.

1. Atualize a coleção `<properties>` no arquivo **pom.xml**. Use o valor do servidor de logon para seu Registro de Contêiner do Azure da seção anterior deste tutorial, por exemplo:

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Atualize a coleção `<plugins>` no arquivo **pom.xml** para que o `<plugin>` contenha o endereço do servidor de logon e o nome de registro para seu Registro de Contêiner do Azure da seção anterior deste tutorial. Por exemplo:

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Navegue até o diretório de projeto concluído para o seu aplicativo Spring Boot. Em seguida, execute o seguinte comando para recompilar o aplicativo e enviar o contêiner por push para o Registro de Contêiner do Azure:

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> Quando você estiver enviando o contêiner do Docker por push ao Azure, você poderá receber uma mensagem de erro semelhante a uma das seguintes mesmo que seu contêiner do Docker tenha sido criado com êxito:
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> Se isso acontecer, talvez seja necessário entrar no Azure da linha de comando do Docker. Por exemplo:
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> Você pode fazer o push seu contêiner da linha de comando. Por exemplo:
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>Criar um aplicativo Web no Linux no Serviço de Aplicativo do Azure usando a imagem de contêiner

1. Entre no [Portal do Azure].

1. Clique no ícone do menu para **+ Novo** e, em seguida, clique em **Web + Móvel**.

2.  Clicar em **Aplicativo Web no Linux**.

   ![Criar um aplicativo Web no portal do Azure][LX01]

3. Quando a folha **Aplicativo Web no Linux** for exibida, siga as seguintes etapas:

   a. Insira um nome exclusivo para o **Nome do aplicativo**, por exemplo: *wingtiptoyslinux*.

   b. Escolha uma **Assinatura** na lista suspensa.

   c. Para criar um novo grupo de recursos, escolha um **grupo de recursos** existente ou especifique um nome.

   d. Clique em **Configurar contêiner** e insira as seguintes informações:

      * Escolha **Registro particular**.

      * **Marca de imagem e opcional**: especifique o nome do contêiner de antes. Por exemplo: *wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*.

      * **URL do servidor**: especifique a URL de registro de antes; por exemplo: *https://wingtiptoysregistry.azurecr.io*.

      * **Nome de usuário de logon** e **Senha**: especifique suas credenciais de entrada das suas **Chaves de Acesso**, que você usou em etapas anteriores.

   e. Depois de inserir todas as informações anteriores, selecione **OK**.

   ![Definir configurações do aplicativo Web][LX02]

1. Clique em **Criar**.

> [!NOTE]
> O Azure mapeará automaticamente solicitações de Internet para servidor Tomcat integrado que está em execução nas portas padrão 80 ou 8080. No entanto, se você tiver configurado seu servidor Tomcat inserido para ser executado em uma porta personalizada, precisará adicionar uma variável de ambiente ao seu aplicativo Web que defina a porta do servidor Tomcat inserido. Para fazer isso, execute as seguintes etapas:
>
>1. Entre no [Portal do Azure].

>2. Selecione o ícone para **Serviços de Aplicativos**. (Consulte o item n. 1 na imagem a seguir.)

>3. Selecione o aplicativo Web na lista. (Consulte o item n. 2 na imagem a seguir.)

>4. Clique em **Configurações do Aplicativo**. (Consulte o item n. 3 na imagem a seguir.)

>5. Na seção **Configurações do aplicativo**, adicione uma nova variável de ambiente denominada **PORT**. Em seguida, insira o número da porta personalizado para o valor. (Consulte o item n. 4 na imagem a seguir.)

>6. Selecione **Salvar**. (Consulte o item n. 5 na imagem a seguir.)

> ![Como salvar um número da porta personalizado no portal do Azure][LX03]
>

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar aplicativos Spring Boot no Azure, confira os seguintes artigos:

- [Implantar um aplicativo Spring Boot no Serviço de Aplicativo do Azure][2]


- [Executando um Aplicativo Spring Boot em um Cluster Kubernetes no Serviço de Contêiner do Azure](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>Recursos adicionais

Para saber mais sobre como usar o Azure com Java, confira o [Centro de Desenvolvedores Java do Azure] e as [Ferramentas Java para Visual Studio Team Services].

Para obter mais informações sobre o Spring Boot no projeto de exemplo do Docker, veja [Introdução ao Spring Boot no Docker].

Para obter ajuda na introdução a seus próprios aplicativos Spring Boot, confira [Spring Initializr](https://start.spring.io/).

Para obter mais informações de introdução sobre como criar um aplicativo Spring Boot simples, consulte o [Spring Initializr](https://start.spring.io/).

Para obter mais exemplos que mostram como usar imagens personalizadas do Docker com o Azure, veja [Usando uma imagem personalizada do Docker para o aplicativo Web do Azure no Linux].

<!-- URL List -->

[CLI (interface de linha de comando) do Azure]: /cli/azure/overview
[Serviço de Contêiner do Azure]: https://azure.microsoft.com/services/container-service/
[Centro de Desenvolvedores Java do Azure]: https://azure.microsoft.com/develop/java/
[Portal do Azure]: https://portal.azure.com/
[Criar um Registro de Contêiner do Docker privado usando o Portal do Azure]: /azure/container-registry/container-registry-get-started-portal
[Usando uma imagem personalizada do Docker para o aplicativo Web do Azure no Linux]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[conta gratuita do Azure]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[JDK (Java Developer Kit)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Ferramentas Java para Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[Benefícios do assinante do MSDN]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Introdução ao Spring Boot no Docker]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

