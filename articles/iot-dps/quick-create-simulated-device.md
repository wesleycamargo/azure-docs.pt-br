---
title: Este início rápido mostra como provisionar um dispositivo simulado TPM no Hub IoT do Azure usando C | Microsoft Docs
description: Neste início rápido, você criará e provisionará um dispositivo TPM simulado usando o SDK do dispositivo C para o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 07/13/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 15c0ce5a545b0bd6b2d1f320b50e9990f8278296
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39091458"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Início Rápido: Provisionar um dispositivo TPM simulado usando o SDK de C do IoT do Azure

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

Neste início rápido, você aprenderá a criar e a executar um simulador de dispositivo TPM (Trusted Platform Module) em um computador de desenvolvimento com Windows. Você conectará esse dispositivo simulado a um hub IoT usando uma instância do Serviço de Provisionamento de Dispositivos. Usaremos um código de exemplo do [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) para ajudar a inscrever o dispositivo com uma instância do Serviço de Provisionamento de Dispositivos e simular uma sequência de inicialização para o dispositivo.

Se você não estiver familiarizado com o processo de provisionamento automático, analise os [Conceitos de provisionamento automático](concepts-auto-provisioning.md). Não se esqueça de concluir as etapas em [Configurar o Serviço de Provisionamento de Dispositivos no Hub IoT com o Portal do Azure](./quick-setup-auto-provision.md) antes de continuar com este início rápido. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio 2015 ou [Visual Studio 2017](https://www.visualstudio.com/vs/) com a carga de trabalho ["Desenvolvimento para Desktop com C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada.
* Versão mais recente do [Git](https://git-scm.com/download/) instalada.


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Preparar um ambiente de desenvolvimento para o SDK de C do IoT do Azure

Nesta seção, você preparará um ambiente de desenvolvimento usado para compilar o [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c) e o exemplo de simulador de dispositivo de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview).

1. Baixe a versão mais recente do [sistema de compilação CMake](https://cmake.org/download/). No mesmo site, pesquise o hash criptográfico da versão de distribuição binária que você escolher. Verifique o binário baixado usando o valor de hash criptográfico correspondente. O exemplo a seguir usou o Windows PowerShell para verificar o hash criptográfico da versão 3.11.4 da distribuição MSI x64:

    ```PowerShell
    PS C:\Users\wesmc\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Users\wesmc\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```

    É importante que os pré-requisitos do Visual Studio (Visual Studio e a carga de trabalho de "Desenvolvimento para Desktop com C++") estejam instalados em seu computador, **antes** da instalação de `CMake`. Após a instalação dos pré-requisitos, e verificação do download, instale o sistema de compilação CMake.

2. Abra um prompt de comando ou o shell Bash do Git. Execute o seguinte comando para clonar o repositório do GitHub [SDK de C do IoT do Azure](https://github.com/Azure/azure-iot-sdk-c):
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Atualmente, o tamanho desse repositório está em torno de 220 MB. Essa operação deve demorar alguns minutos.


3. Crie um subdiretório `cmake` no diretório raiz do repositório git e navegue até essa pasta. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Compilar o SDK e executar o simulador de dispositivo TPM

Nesta seção, você compilará o SDK de C do IoT do Azure, que inclui o código de exemplo de simulador de dispositivo TPM. Este exemplo fornece um [mecanismo de atestado](concepts-security.md#attestation-mechanism) de TPM por meio da autenticação de Token SAS (Assinatura de Acesso Compartilhado).

1. Do subdiretório `cmake` criado no repositório git azure-iot-sdk-c, execute o seguinte comando para criar o exemplo. Uma solução do Visual Studio para o dispositivo simulado também será gerada por esse comando de compilação.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Se `cmake` não encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Após o sucesso da compilação, as últimas linhas de saída serão semelhantes à seguinte saída:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Navegue até a pasta raiz do repositório git clonado e execute o simulador [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview) usando o caminho mostrado abaixo. Esse simulador escuta em um soquete nas portas 2321 e 2322. Não feche essa janela de comando; você precisará manter esse simulador em execução até o término deste início rápido. 

   Se você estiver na pasta *cmake*, execute os seguintes comandos:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Você não verá qualquer saída do simulador. Deixe-o continuar simulando um dispositivo TPM.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Ler as chaves de criptografia do dispositivo TPM

Nesta seção, você compilará e executará um exemplo que lerá a ID do registro e a chave de endosso do simulador de TPM que você deixou em execução e escutando nas portas 2321 e 2322. Esses valores serão usados para a inscrição do dispositivo com sua instância do Serviço de Provisionamento de Dispositivos.

1. Inicie o Visual Studio e abra o arquivo da nova solução chamado `azure_iot_sdks.sln`. Esse arquivo de solução está localizado na pasta `cmake` que você criou anteriormente na raiz do repositório git azure-iot-sdk-c.

2. No menu do Visual Studio, selecione **Compilar** > **Compilar Solução** para compilar todos os projetos na solução.

3. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**. 

4. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. O aplicativo lê e exibe uma **_ID de Registro_** e uma **_Chave de Endosso_**. Copie esses valores. Eles serão usados na próxima seção para inscrição do dispositivo. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Criar uma entrada de registro de dispositivo no portal

1. Entre no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

2. Selecione a guia **Gerenciar registros** e clique no botão **Adicionar registro individual** na parte superior. 

3. Em **Adicionar registro**, insira as informações a seguir e clique no botão **Salvar**.

    - **Mecanismo:** selecione **TPM** como o *Mecanismo* do atestado de identidade.
    - **Chave de endosso:** insira a *Chave de endosso* gerada para seu dispositivo TPM executando o projeto *tpm_device_provision*.
    - **ID do Registro:** insira a *ID de Registro* gerada para seu dispositivo TPM executando o projeto *tpm_device_provision*.
    - **Dispositivo IoT Edge:** selecione **Desabilitar**.
    - **ID do dispositivo Hub IoT:** insira **test-docs-device** para dar uma ID ao dispositivo.

    ![Inserir informações de registro de dispositivo no portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

    Em caso de registro bem-sucedido, a *ID de Registro* do seu dispositivo será exibida na lista na guia *Registros Individuais*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simular a primeira sequência de inicialização para o dispositivo

Nesta seção, você configurará o exemplo de código para usar o [AMQP (Advanced Message Queuing Protocol)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) e enviar a sequência de inicialização do dispositivo para a instância do Serviço de Provisionamento de Dispositivo. Essa sequência de inicialização fará com que o dispositivo seja reconhecido e atribuído a um hub IoT vinculado à instância do Serviço de Provisionamento de Dispositivo.

1. No Portal do Azure, selecione a guia **Visão Geral** do Serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_**.

    ![Extrair informações de ponto de extremidade do DPS no portal](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. Na janela *Gerenciador de Soluções* do Visual Studio, navegue até a pasta **Provisionar\_Exemplos**. Expanda o projeto de exemplo chamado **prov\_dev\_client\_sample**. Expanda **Arquivos de Origem** e abra **prov\_dev\_client\_sample.c**.

3. Próximo à parte superior do arquivo, localize as instruções `#define` para cada protocolo de dispositivo, conforme mostrado abaixo. Remova a marca de comentário apenas de `SAMPLE_AMQP`.

    Atualmente, o [protocol MQTT não tem suporte para o Registro Individual de TPM](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Localize a constante `id_scope` e substitua o valor pelo seu valor de **Escopo de ID** copiado anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Encontre a definição da função `main()` no mesmo arquivo. Verifique se a variável `hsm_type` está definida como `SECURE_DEVICE_TYPE_TPM` em vez de `SECURE_DEVICE_TYPE_X509`, conforme mostrado abaixo.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**. 

7. No menu do Visual Studio, selecione **Depurar** > **Iniciar sem depuração** para executar a solução. No prompt para recompilar o projeto, clique em **Sim** para recompilar o projeto antes da execução.

    A saída a seguir é um exemplo da inicialização bem-sucedida do exemplo de cliente de dispositivo provisionamento, e da conexão com uma instância de Serviço de Provisionamento de Dispositivos para obter informações e registrar o hub IoT:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. Após o provisionamento do dispositivo simulado no Hub IoT por seu serviço de provisionamento, a ID do dispositivo aparece em **Dispositivos IOT** do hub. 

    ![Dispositivo é registrado no Hub IoT](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este Guia de Início Rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
2. Feche a janela do simulador do TPM no seu computador.
3. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra **Gerenciar Registros** de seu serviço e clique na guia **Registros Individuais**. Selecione *ID de REGISTRO* do dispositivo descrito no Guia de Início Rápido e clique no botão **Excluir** na parte superior. 
4. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Abra **Dispositivos IoT** do hub, selecione *DEVICE ID* registrado nesse Guia de Início Rápido, e clique no botão **Excluir** na parte superior.

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você criou um dispositivo TPM simulado no seu computador e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos do Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue com o Guia de Início Rápido para registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Guia de Início Rápido do Azure – Registrar dispositivo TPM no Serviço de Provisionamento de Dispositivos do Hub IoT do Azure](quick-enroll-device-tpm-java.md)

