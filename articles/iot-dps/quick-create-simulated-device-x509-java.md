---
title: Provisionar um dispositivo simulado X.509 no Hub IoT do Azure usando Java | Microsoft Docs
description: "Guia de Início Rápido do Azure – Criar e provisionar um dispositivo X.509 simulado usando o SDK do dispositivo Java para o Serviço de Provisionamento do Dispositivo Hub IoT"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 12/21/2017
ms.topic: quickstart
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: java
ms.custom: mvc
ms.openlocfilehash: 508df6e7c677d87c6b893519c59b02880e16d7ec
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2017
---
# <a name="create-and-provision-a-simulated-x509-device-using-java-device-sdk-for-iot-hub-device-provisioning-service"></a>Criar e provisionar um dispositivo X.509 simulado usando o SDK do dispositivo Java para o Serviço de Provisionamento do Dispositivo Hub IoT
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device-x509.md)
> * [Java](quick-create-simulated-device-x509-java.md)
> * [C#](quick-create-simulated-device-x509-csharp.md)
> * [Python](quick-create-simulated-device-x509-python.md)

Estas etapas mostram como simular um dispositivo X.509 no computador de desenvolvimento que executa o sistema operacional Windows e usam um exemplo de código para conectar este dispositivo simulado com o Serviço de Provisionamento do Dispositivo e o hub IoT. 

Conclua as etapas em [Configurar o Serviço de Provisionamento do Dispositivo Hub IoT com o Portal do Azure](./quick-setup-auto-provision.md) antes de continuar.


## <a name="prepare-the-environment"></a>Preparar o ambiente 

1. Certifique-se de ter o [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) instalado no computador.

1. Baixe e instale o [Maven](https://maven.apache.org/install.html).

1. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Confira [ferramentas de cliente Git do Software Freedom Conservancy](https://git-scm.com/download/) para obter a versão mais recente das ferramentas `git` a serem instaladas, que inclui o **Git Bash**, o aplicativo de linha de comando que você pode usar para interagir com seu repositório Git local. 

1. Abra um prompt de comando. Clone o repositório do GitHub para obter exemplo de código de simulação do dispositivo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

1. Navegue até o projeto do gerador de certificados e compile o projeto. 

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator
    mvn clean install
    ```

1. Navegue até a pasta de destino e execute o arquivo jar criado.

    ```cmd/sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

1. Crie informações de registro em uma das seguintes maneiras, conforme sua configuração:

    - **Registro individual**:

        1. Insira **N** em _Do you want to input common name_. Copie para a área de transferência a saída `Client Cert` de *-----BEGIN CERTIFICATE-----* que termina em *-----END CERTIFICATE-----*.

            ![Gerador de certificado individual](./media/java-quick-create-simulated-device-x509/individual.png)

        1. Crie um arquivo chamado **_X509individual.pem_** no computador Windows, abra-o em um editor de sua escolha e copie o conteúdo da área de transferência para o arquivo. Salve o arquivo.

        1. Insira **N** em _Do you want to input Verification Code_ e mantenha a saída do programa aberta para referência mais tarde no Guia de Início Rápido. Anote os valores _Client Cert_ e _Client Cert Private Key_.
    
    - **Grupos de registros**:

        1. Insira **N** em _Do you want to input common name_. Copie para a área de transferência a saída `Root Cert` de *-----BEGIN CERTIFICATE-----* que termina em *-----END CERTIFICATE-----*.

            ![Gerador de certificado de grupo](./media/java-quick-create-simulated-device-x509/group.png)

        1. Crie um arquivo chamado **_X509group.pem_** no computador Windows, abra-o em um editor de sua escolha e copie o conteúdo da área de transferência para o arquivo. Salve o arquivo.

        1. Insira **Y** em _Do you want to input Verification Code_ e mantenha a saída do programa aberta para referência mais tarde no Guia de Início Rápido. Anote os valores _Client Cert_, _Client Cert Private Key_, _Signer Cert_ e _Root Cert_.


## <a name="create-a-device-enrollment-entry"></a>Criar uma entrada de registro de dispositivo

1. Faça logon no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de provisionamento.

1. Crie as informações de registro em uma das seguintes maneiras, conforme sua configuração:

    - **Registro individual**: 

        1. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** guia e clique no botão **Adicionar** na parte superior. 

        1. Em **Adicionar a entrada da lista de registro**, insira as seguintes informações:
            - Selecione **X.509** como o *Mecanismo* de atestado de identidade.
            - No *arquivo Certificate .pem ou .cer*, selecione o arquivo de certificado **_X509individual.pem_** criado nas etapas anteriores usando o widget *Explorador de Arquivos*.
            - Opcionalmente, você pode fornecer as seguintes informações:
                - Selecione um hub IoT vinculado com o serviço de provisionamento.
                - Insira uma ID de dispositivo exclusiva. Evite dados confidenciais ao nomear seu dispositivo. 
                - Atualize o **Estado inicial do dispositivo gêmeo** com a configuração inicial desejada para o dispositivo.
            - Uma vez concluído, clique no botão **Salvar**. 

        ![Inserir informações de registro de dispositivo X.509 na folha do portal](./media/java-quick-create-simulated-device-x509/enter-device-enrollment.png)  

       No registro bem-sucedido, o dispositivo X.509 é exibido como **microsoftriotcore** na coluna *ID de Registro* na guia *Registros Individuais*. 

    - **Grupos de registros**: 

        1. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Certificados** e clique no botão **Adicionar** na parte superior.

        1. Em **Adicionar Certificado**, insira as seguintes informações:
            - Digite um nome de certificado exclusivo.
            - Selecione o arquivo **_X509group.pem_** criado anteriormente.
            - Uma vez concluído, clique no botão **Salvar**.

        ![Adicionar certificado](./media/java-quick-create-simulated-device-x509/add-certificate.png)

        1. Selecione o certificado recém-criado:
            - Clique em **Gerar Código de Verificação**. Copie o código gerado.
            - Insira o _código de verificação_ ou clique com o botão direito do mouse para colar na janela _provisioning-x509-cert-generator_ em execução.  Pressione **Enter**.
            - Copie para a área de transferência a saída `Verification Cert` de *-----BEGIN CERTIFICATE-----* que termina em *-----END CERTIFICATE-----*.
            
                ![Gerador de validação](./media/java-quick-create-simulated-device-x509/validation-generator.png)

            - Crie um arquivo chamado **_X509validation.pem_** no computador Windows, abra-o em um editor de sua escolha e copie o conteúdo da área de transferência para o arquivo. Salve o arquivo.
            - Selecione o arquivo **_X509validation.pem_** no portal do Azure. Clique em **Verificar**.

            ![Validar certificado](./media/java-quick-create-simulated-device-x509/validate-certificate.png)

        1. Selecione **Gerenciar registros**. Selecione a guia **Grupos de Registro** e clique no botão **Adicionar** na parte superior.
            - Insira um nome de grupo exclusivo.
            - Selecionar o nome do certificado exclusivo criado anteriormente
            - Opcionalmente, você pode fornecer as seguintes informações:
                - Selecione um hub IoT vinculado com o serviço de provisionamento.
                - Atualize o **Estado inicial do dispositivo gêmeo** com a configuração inicial desejada para o dispositivo.

        ![Inserir informações de registro do grupo X.509 na folha do portal](./media/java-quick-create-simulated-device-x509/enter-group-enrollment.png)

        No registro bem-sucedido, o grupo de dispositivos X.509 aparece na coluna *Nome do Grupo* na guia *Grupos de Registro*.


## <a name="simulate-the-device"></a>Simular o dispositivo

1. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Visão Geral** e anote o _Escopo da ID_ e o _Ponto de Extremidade Global do Serviço de Provisionamento_.

    ![Informações de serviço](./media/java-quick-create-simulated-device-x509/extract-dps-endpoints.png)

1. Abra um prompt de comando. Navegue até a pasta de projeto de exemplo.

    ```cmd/sh
    cd azure-iot-sdk-java/provisioning/provisioning-samples/provisioning-X509-sample
    ```

1. Crie as informações de registro em uma das seguintes maneiras, conforme sua configuração:

    - **Registro individual**: 

        1. Edite `/src/main/java/samples/com/microsoft/azure/sdk/iot/ProvisioningX509Sample.java` para incluir o _Escopo da ID_ e o _Ponto de Extremidade Global do Serviço de Provisionamento_ conforme anotado antes. Inclua também _Client Cert_ e _Client Cert Private Key_ conforme anotado antes.

            ```java
            private static final String idScope = "[Your ID scope here]";
            private static final String globalEndpoint = "[Your Provisioning Service Global Endpoint here]";
            private static final ProvisioningDeviceClientTransportProtocol PROVISIONING_DEVICE_CLIENT_TRANSPORT_PROTOCOL = ProvisioningDeviceClientTransportProtocol.HTTPS;
            private static final String leafPublicPem = "<Your Public PEM Certificate here>";
            private static final String leafPrivateKey = "<Your Private PEM Key here>";
            ```

            - Use o seguinte formato para incluir certificado e chave:
            
                ```java
                private static final String leafPublicPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                private static final String leafPrivateKey = "-----BEGIN PRIVATE KEY-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXX\n" +
                    "-----END PRIVATE KEY-----\n";
                ```

    - **Grupos de registros**: 

        1. Siga as instruções para **Registro individual** acima.

        1. Adicione as seguintes linhas de código ao início da função `main`:
        
            ```java
            String intermediatePem = "<Your Signer Certificate here>";          
            String rootPem = "<Your Root Certificate here>";
                
            signerCertificates.add(intermediatePem);
            signerCertificates.add(root);
            ```
    
            - Use o seguinte formato para incluir seus certificados:
        
                ```java
                String intermediatePem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                String rootPem = "-----BEGIN CERTIFICATE-----\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "+XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
                    "-----END CERTIFICATE-----\n";
                ```

1. Compile o exemplo. Navegue até a pasta de destino e execute o arquivo jar criado.

    ```cmd/sh
    mvn clean install
    cd target
    java -jar ./provisioning-x509-sample-{version}-with-deps.jar
    ```

1. No portal, navegue até o hub IoT vinculado ao seu serviço de provisionamento e abra a folha **Device Explorer**. No provisionamento bem-sucedido do dispositivo X.509 simulado para o hub, sua ID de dispositivo aparecerá na folha **Device Explorer** com o *STATUS* **habilitado**. Observe que talvez você precise clicar no botão **Atualizar** na parte superior, se você já tiver aberto a folha antes de executar o aplicativo de dispositivo de exemplo. 

    ![Dispositivo é registrado no Hub IoT](./media/java-quick-create-simulated-device-x509/hub-registration.png) 

> [!NOTE]
> Se você tiver alterado o *estado de dispositivo gêmeo inicial* do valor padrão na entrada de registro para o seu dispositivo, pode receber o estado desejado duas do hub e agir de acordo. Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md).
>


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este Guia de Início Rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra a folha **Gerenciar Registros** de seu serviço e clique na guia **Registros Individuais**. Selecione *ID de REGISTRO* do dispositivo descrito no Guia de Início Rápido e clique no botão **Excluir** na parte superior. 
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Abra a folha **Dispositivos IoT** do hub, selecione *DEVICE ID* registrado nesse Guia de Início Rápido, e clique no botão **Excluir** na parte superior.


## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você criou um dispositivo simulado X.509 no seu computador Windows e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos do Hub IoT do Azure no portal. Para saber como registrar seu dispositivo X.509 programaticamente, continue com o Guia de Início Rápido para registro programático de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Guia de Início Rápido do Azure – Registre dispositivos X.509 no Serviço de Provisionamento de Dispositivos do Hub IoT do Azure](quick-enroll-device-x509-java.md)
