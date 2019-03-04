---
title: Início Rápido para aprender a usar a Configuração de Aplicativo do Azure | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos Java Spring.
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960592"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>Início Rápido: Criar um aplicativo Java Spring com a Configuração de Aplicativo

A Configuração de Aplicativo do Azure é um serviço de configuração gerenciada no Azure. Ela permite armazenar e gerenciar com facilidade todas as suas configurações de aplicativo em um só lugar, separado do código. Este Início Rápido mostra como incorporar o serviço em um aplicativo Java Spring.

Você pode usar qualquer editor de código para concluir as etapas deste início rápido. No entanto, o [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente nas plataformas Windows, macOS e Linux.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este Início Rápido, instale um [JDK (Java Development Kit)](https://aka.ms/azure-jdks) compatível com a versão 8 e o [Apache Maven](http://maven.apache.org/) com a versão 3.0 ou posterior.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

1. Para criar um repositório de configurações de aplicativo, primeiro entre no [portal do Azure](https://aka.ms/azconfig/portal). No canto superior esquerdo da página, clique em **+ Criar um recurso**. Na caixa de texto **Pesquisar no Marketplace**, digite **Configuração de Aplicativo** e pressione **Enter**.

    ![Pesquisar a Configuração de Aplicativo](./media/quickstarts/azure-app-configuration-new.png)

2. Clique em **Configuração de Aplicativo** nos resultados da pesquisa e, em seguida, em **Criar**.

3. Na página **Configuração de Aplicativo** > **Criar**, insira as seguintes configurações:

    | Configuração | Valor sugerido | DESCRIÇÃO |
    |---|---|---|
    | **Nome do recurso** | Nome globalmente exclusivo | Insira um nome exclusivo do recurso a ser usado para o recurso do repositório de configurações de aplicativo. O nome deve ser uma cadeia de caracteres com 1 a 63 caracteres e deve conter somente números, letras e o caractere `-`. O nome não pode começar nem terminar com o caractere `-` e os caracteres `-` consecutivos não são válidos.  |
    | **Assinatura** | Sua assinatura | Selecione a assinatura do Azure que deseja usar para testar a Configuração de Aplicativo. Se sua conta tiver apenas uma assinatura, ela será automaticamente selecionada e o menu suspenso **Assinatura** não será exibido. |
    | **Grupo de recursos** | *AppConfigTestResources* | Selecione ou crie um grupo de recursos para o recurso do repositório de configurações de aplicativo. Esse grupo é útil para organizar vários recursos que você talvez queira excluir ao mesmo tempo, excluindo o grupo de recursos. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
    | **Localidade** | *Centro dos EUA* | Use **Local** para especificar a localização geográfica em que o recurso do SignalR está hospedado. Para obter o melhor desempenho, recomendamos que você crie o recurso na mesma região que outros componentes do seu aplicativo. |

    ![Criar um repositório de configurações de aplicativo](./media/quickstarts/azure-app-configuration-create.png)

4. Clique em **Criar**. A implantação pode levar alguns minutos para ser concluída.

5. Após a conclusão da implantação, clique em **Configurações** > **Chaves de Acesso**. Anote a cadeia de conexão primária somente leitura ou a primária de leitura/gravação. Você usará isso posteriormente para configurar seu aplicativo para que ele se comunique com o repositório de configurações de aplicativo recém-criado. A cadeia de conexão tem o seguinte formato:

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    Você precisará usar a cadeia inteira no aplicativo.

6. Clique em **Gerenciador de Pares Chave-Valor** e em **+ Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | /application/config.message | Olá |

    Você deixará **Rótulo** e **Tipo de Conteúdo** vazio por enquanto.

## <a name="create-a-spring-boot-app"></a>Criar um aplicativo Spring Boot

Você usará o [Spring Initializr](https://start.spring.io/) para criar um projeto do Spring Boot.

1. Navegue até <https://start.spring.io/>.

2. Especifique as seguintes opções:

   * Gere um projeto **Maven** com **Java**.
   * Especifique uma versão **Spring Boot** igual ou maior que 2.0.
   * Especifique os nomes de **Grupo** e **Artefato** do aplicativo.
   * Adicione a dependência da **Web**.

3. Quando você tiver especificado as opções listadas acima, clique em **Gerar Projeto**. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="connect-to-app-configuration-store"></a>Conectar-se ao repositório de configurações de aplicativo

1. Depois de ter extraído os arquivos no sistema local, seu aplicativo Spring Boot simple estará pronto para edição. Localize o arquivo *pom.xml* no diretório raiz do aplicativo.

2. Abra o arquivo *pom.xml* em um editor de texto e adicione o iniciador da Configuração do Azure do Spring Cloud à lista de `<dependencies>`:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. Crie um arquivo Java chamado *MessageProperties.java* no diretório do pacote do aplicativo. Adicione as linhas a seguir.

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. Crie um arquivo Java chamado *HelloController.java* no diretório do pacote do aplicativo. Adicione as linhas a seguir.

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. Abra o arquivo Java do aplicativo principal e adicione `@EnableConfigurationProperties` para habilitar esse recurso.

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. Crie um arquivo chamado `bootstrap.yaml` no diretório de recursos do aplicativo, adicione as linhas a seguir ao arquivo e substitua os valores de exemplo pelas propriedades adequadas ao repositório de configurações de aplicativo.

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Crie seu aplicativo Spring Boot com Maven e execute-o; por exemplo:

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. Quando seu aplicativo estiver em execução, você poderá usar *curl* para testá-lo, por exemplo:

      ```shell
      curl -X GET http://localhost:8080/
      ```
    Você deverá ver a mensagem que inseriu no repositório de configurações de aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste Início Rápido, você criou um repositório de configurações de aplicativo e usou-o com um aplicativo Java Spring. Visite a [home page do Spring no Azure](https://docs.microsoft.com/java/azure/spring-framework/) para obter mais informações.

Para saber mais sobre como usar a Configuração de Aplicativo, continue no próximo tutorial, que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Identidades gerenciadas para a integração de recursos do Azure](./integrate-azure-managed-service-identity.md)