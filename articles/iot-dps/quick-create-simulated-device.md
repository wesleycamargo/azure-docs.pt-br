---
title: Provisionar um dispositivo simulado TPM no Hub IoT do Azure usando C | Microsoft Docs
description: "Guia de Início Rápido do Azure – Criar e provisionar um dispositivo simulado TPM usando o SDK do dispositivo C para o Serviço de Provisionamento do Dispositivo Hub IoT do Azure"
services: iot-dps
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 12/20/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: dd101db9ea3c03a939efb6a64c231d885f33cedc
ms.sourcegitcommit: 6f33adc568931edf91bfa96abbccf3719aa32041
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2017
---
# <a name="create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Criar e provisionar um dispositivo TPM simulado usando o SDK do dispositivo C para o Serviço de Provisionamento do Dispositivo Hub IoT
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device.md)
> * [Java](quick-create-simulated-device-tpm-java.md)
> * [C#](quick-create-simulated-device-tpm-csharp.md)
> * [Python](quick-create-simulated-device-tpm-python.md)

Estas etapas mostram como criar um dispositivo simulado em seu computador de desenvolvimento executando o sistema operacional Windows, executar o simulador do TPM do Windows como [HSM (Módulo de Segurança de Hardware)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) do dispositivo e usar o código de exemplo para se conectar a esse dispositivo simulado com o Serviço de Provisionamento de Dispositivos e o Hub IoT. 

Não deixe de concluir as etapas em [Configurar o Serviço de Provisionamento de Dispositivos do Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar.

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Verifique se você tem o Visual Studio 2015 ou o [Visual Studio 2017](https://www.visualstudio.com/vs/) instalado em seu computador. Você deve ter a carga de trabalho ['Desenvolvimento de área de trabalho com C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada para sua instalação do Visual Studio.

2. Baixe e instale o [sistema de compilação CMake](https://cmake.org/download/). É importante que o Visual Studio com carga de trabalho de 'Desenvolvimento de área de trabalho com C++' está instalado em seu computador, **antes** da instalação de `cmake`.

3. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Confira [ferramentas de cliente Git do Software Freedom Conservancy](https://git-scm.com/download/) para obter a versão mais recente das ferramentas `git` a serem instaladas, que inclui o **Git Bash**, o aplicativo de linha de comando que você pode usar para interagir com seu repositório Git local. 

4. Abra um prompt de comando ou o Bash do Git. Clone o repositório do GitHub para obter exemplo de código de simulação do dispositivo:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

5. Crie uma pasta em sua cópia local do repositório GitHub para o processo de compilação do CMake. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

6. O exemplo de código usa um simulador do TPM do Windows. Execute o comando a seguir para habilitar a autenticação de token SAS. Ele também gera uma solução do Visual Studio para o dispositivo simulado.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Se `cmake` não encontrar o compilador do C++, você poderá obter erros de build ao executar o comando acima. Se isso acontecer, tente executar esse comando no [prompt de comando do Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

7. Em um prompt de comando separado, navegue até a pasta raiz do GitHub e execute o simulador do [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Ele escuta em um soquete nas portas 2321 e 2322. Não feche essa janela de comando; você precisará manter esse simulador em execução até o término deste Guia de Início Rápido. 

   Se você estiver na pasta *cmake*, execute os seguintes comandos:

    ```cmd/sh
    cd..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

<a id="simulatetpm"></a>

## <a name="simulate-tpm-device"></a>Simular dispositivo TPM

1. Abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln`e compile-a no Visual Studio.

2. No painel *Gerenciador de Soluções* no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**. 

3. Execute a solução. A janela de saída exibe a **_Chave de Endosso_** e a **_ID de Registro_** necessárias para registro de dispositivos. Anote esses valores. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Criar uma entrada de registro de dispositivo no portal

1. Faça logon no Portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o Serviço de Provisionamento de Dispositivos.

2. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** guia e clique no botão **Adicionar** na parte superior. 

3. Em **Adicionar a entrada da lista de registro**, insira as seguintes informações:
    - Selecione **TPM** como o atestado de identidade *Mecanismo*.
    - Insira a *ID de Registro* e *Chave de Endosso* para seu dispositivo do TPM.
    - Opcionalmente, você pode fornecer as seguintes informações:
        - Selecione um hub IoT vinculado com o serviço de provisionamento.
        - Insira uma ID de dispositivo exclusiva. Evite dados confidenciais ao nomear seu dispositivo.
        - Atualize o **Estado inicial do dispositivo gêmeo** com a configuração inicial desejada para o dispositivo.
    - Uma vez concluído, clique no botão **Salvar**. 

    ![Inserir informações de registro de dispositivo na folha do portal](./media/quick-create-simulated-device/enter-device-enrollment.png)  

   Em caso de registro bem-sucedido, a *ID de Registro* do seu dispositivo será exibida na lista na guia *Registros Individuais*. 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simular a primeira sequência de inicialização para o dispositivo

1. No portal do Azure, selecione a folha **Visão Geral** do seu serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_**.

    ![Extrair informações de ponto de extremidade do DPS na folha do portal](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. No *Gerenciador de Soluções* do Visual Studio em seu computador, navegue até a pasta **Provisionar\_Amostras**. Selecione o projeto de exemplo chamado **prov\_dev\_client\_sample** e abra o arquivo **prov\_dev\_client\_sample.c**.

3. Atribua o valor _ID do Escopo_ à variável `id_scope`. 

    ```c
    static const char* id_scope = "[ID Scope]";
    ```

4. Na função **main()** no mesmo arquivo, verifique se o **SECURE_DEVICE_TYPE** está definido como TPM.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    ```

   Comente ou exclua a instrução `hsm_type = SECURE_DEVICE_TYPE_X509;` que está presente por padrão. 

5. Clique com botão direito do mouse no projeto **prov\_dev\_client\_sample** e selecione **Definir como Projeto de Inicialização**. Execute a solução. 

6. Observe as mensagens que simulam a inicialização e a conexão do dispositivo com o Serviço de Provisionamento de Dispositivos para obter as informações do Hub IoT. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao serviço de provisionamento, a ID do dispositivo aparece na folha **Dispositivos IOT** do hub. 

    ![Dispositivo é registrado no Hub IoT](./media/quick-create-simulated-device/hub-registration.png) 

    Se você tiver alterado o *estado de dispositivo gêmeo inicial* do valor padrão na entrada de registro para o seu dispositivo, pode receber o estado desejado duas do hub e agir de acordo. Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este Guia de Início Rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
1. Feche a janela do simulador do TPM no seu computador.
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Abra a folha **Gerenciar Registros** de seu serviço e clique na guia **Registros Individuais**. Selecione *ID de REGISTRO* do dispositivo descrito no Guia de Início Rápido e clique no botão **Excluir** na parte superior. 
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Abra a folha **Dispositivos IoT** do hub, selecione *DEVICE ID* registrado nesse Guia de Início Rápido, e clique no botão **Excluir** na parte superior.

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você criou um dispositivo TPM simulado no seu computador e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos do Hub IoT. Para saber como registrar seu dispositivo TPM programaticamente, continue com o Guia de Início Rápido para registro programático de um dispositivo TPM. 

> [!div class="nextstepaction"]
> [Guia de Início Rápido do Azure – Registrar dispositivo TPM no Serviço de Provisionamento de Dispositivos do Hub IoT do Azure](quick-enroll-device-tpm-java.md)

