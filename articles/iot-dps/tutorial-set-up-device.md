---
title: Configurar dispositivo para o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
description: Configurar o dispositivo para provisionar por meio do Serviço de Provisionamento de Dispositivos no Hub IoT durante o processo de fabricação do dispositivo
author: dsk-2015
ms.author: dkshir
ms.date: 04/02/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 1e4e93c276fe62caae17c85bf9ac92282dfdfb88
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631261"
---
# <a name="set-up-a-device-to-provision-using-the-azure-iot-hub-device-provisioning-service"></a>Configurar um dispositivo para provisionar usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

No tutorial anterior, você aprendeu como configurar o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para provisionar automaticamente os dispositivos para o Hub IoT. Este tutorial mostra como configurar seu dispositivo durante o processo de fabricação, permitindo que ele seja provisionado automaticamente com o Hub IoT. Seu dispositivo é provisionado com base no [mecanismo de atestado](concepts-device.md#attestation-mechanism) após a primeira inicialização e conexão com o serviço de provisionamento. Este tutorial aborda os processos para:

> [!div class="checklist"]
> * Criar o SDK de cliente dos Serviços de Provisionamento de Dispositivos específico da plataforma
> * Extrair os artefatos de segurança
> * Criar o software de registro do dispositivo

## <a name="prerequisites"></a>pré-requisitos

Antes de prosseguir, crie sua instância do Serviço de Provisionamento de Dispositivos e um Hub IoT usando as instruções mencionadas no tutorial anterior [1 - Configurar recursos de nuvem](./tutorial-set-up-cloud.md).

Este tutorial usa [SDKs do IoT do Azure e bibliotecas para repositório de C](https://github.com/Azure/azure-iot-sdk-c), que contêm o SDK do cliente do Serviço de Provisionamento de Dispositivo para C. O SDK atualmente oferece suporte a TPM e X.509 em dispositivos com implementações Windows ou Ubuntu. Este tutorial baseia-se no uso de um cliente de desenvolvimento do Windows, o que também assume o domínio básico do Visual Studio 2017. 

Se você não estiver familiarizado com o processo de provisionamento automático, analise os [Conceitos de provisionamento automático](concepts-auto-provisioning.md) antes de continuar. 

## <a name="build-a-platform-specific-version-of-the-sdk"></a>Criar uma versão do SDK específica da plataforma

O SDK de cliente do Serviço de Provisionamento de Dispositivos ajuda a implementar o software de registro do dispositivo. Mas antes de poder usá-lo, você criará uma versão do SDK específica para o mecanismo de atestado e a plataforma de desenvolvimento do cliente. Neste tutorial, você criará um SDK que usa o Visual Studio 2017 em uma plataforma de desenvolvimento Windows para um tipo de atestado com suporte:

1. Instale as ferramentas necessárias e clone o repositório GitHub que contém o SDK de cliente do serviço de provisionamento para C:

   a. Verifique se você tem o Visual Studio 2015 ou o [Visual Studio 2017](https://www.visualstudio.com/vs/) instalado em seu computador. Você deve ter a carga de trabalho ['Desenvolvimento de área de trabalho com C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) habilitada para sua instalação do Visual Studio.

   b. Baixe e instale o [sistema de compilação CMake](https://cmake.org/download/). É importante que o Visual Studio com carga de trabalho de 'Desenvolvimento de área de trabalho com C++' esteja instalado em seu computador **antes** da instalação do CMake.

   c. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Confira as [Ferramentas de cliente Git da organização Software Freedom Conservancy ](https://git-scm.com/download/) para as ferramentas `git`mais recentes, incluindo **Git Bash**, um shell Bash de linha de comando para interagir com seu repositório Git local. 

   d. Abra o Git Bash e clone o repositório "SDKs do IoT do Azure e bibliotecas para C". O comando clone pode levar vários minutos para ser concluído, já que também baixa vários submódulos dependentes:
    
   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   e. Crie um novo subdiretório `cmake` dentro do subdiretório do repositório criado recentemente:

   ```cmd/sh
   mkdir azure-iot-sdk-c/cmake
   ``` 

2. No prompt de comando do Git Bash, mude para o subdiretório `cmake` do repositório azure-iot-sdk-c:

   ```cmd/sh
   cd azure-iot-sdk-c/cmake
   ```

3. Crie o SDK para sua plataforma de desenvolvimento e um dos mecanismos de atestado com suporte usando um dos comandos a seguir (observe também os dois caracteres de ponto final à direita). Após a conclusão, o CMake cria o subdiretório `/cmake` com conteúdo específico do dispositivo:
    - Para dispositivos que usam um TPM/HSM físico ou um certificado X.509 simulado como atestado:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON ..
        ```

    - Para dispositivos que usam o simulador do TPM como atestado:
        ```cmd/sh
        cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
        ```

Agora você está pronto para usar o SDK para criar seu código de registro do dispositivo. 
 
<a id="extractsecurity"></a> 

## <a name="extract-the-security-artifacts"></a>Extrair os artefatos de segurança 

A próxima etapa é extrair os artefatos de segurança para o mecanismo de atestado usado pelo dispositivo. 

### <a name="physical-device"></a>Dispositivo físico 

Se você compilou o SDK para usar o atestado de um TPM/HSM físico:

- Para um dispositivo TPM, você precisa descobrir a **Chave de Endosso** do fabricante do chip do TPM associada a ele. Você pode derivar uma única **ID de registro** para seu dispositivo TPM via hash da chave de endosso.  

- Para um dispositivo de X.509, você precisa obter os certificados emitidos para seus dispositivos: certificados de entidade final para registros de dispositivos individuais ou certificados raiz para registros de dispositivos em grupo. 

### <a name="simulated-device"></a>Dispositivo simulado

Se você compilou o SDK para usar o atestado de um certificado X.509 ou de um TPM simulado:

- Para um dispositivo do TPM simulado:
   1. Em um prompt de comando novo/separado, navegue até o subdiretório `azure-iot-sdk-c` e execute o simulador do TPM. Ele escuta em um soquete nas portas 2321 e 2322. Não feche essa janela de comando. Você precisará manter esse simulador em execução até o término do Início Rápido a seguir. 

      No subdiretório `azure-iot-sdk-c`, execute o comando a seguir para iniciar o simulador:

      ```cmd/sh
      .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
      ```

   2. Usando o Visual Studio, abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln` e compile-a usando o comando "Compilar solução" no menu "Compilar".

   3. No painel *Gerenciador de Soluções* no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com botão direito do mouse no projeto **tpm_device_provision** e selecione **Definir como Projeto de Inicialização**. 

   4. Execute a solução usando um dos comandos "Iniciar" no menu "Depurar". A janela de saída exibe a **_Chave de Endosso_** e a **_ID de Registro_** do TPM necessárias para a inscrição e o registro de dispositivos. Copie esses valores para uso posterior. Você pode fechar esta janela (com a ID de registro e a chave de endosso), mas deixe a janela do simulador do TPM que você iniciou na etapa 1 # em execução.

- Para um dispositivo de X.509 simulado:
  1. Usando o Visual Studio, abra a solução gerada na pasta *cmake* denominada `azure_iot_sdks.sln` e compile-a usando o comando "Compilar solução" no menu "Compilar".

  2. No painel *Gerenciador de Soluções* no Visual Studio, navegue até a pasta **Provisionar\_Ferramentas**. Clique com o botão direito do mouse no projeto **dice\_device\_enrollment** e selecione **Definir como Projeto de Inicialização**. 
  
  3. Execute a solução usando um dos comandos "Iniciar" no menu "Depurar". Na janela de saída, insira **i** para registro individual quando solicitado. A janela de saída exibe um certificado X.509 gerado localmente para seu dispositivo simulado. Copie para a área de transferência a saída que começa em *-----BEGIN CERTIFICATE-----* e termina no primeiro *-----END CERTIFICATE-----*, não deixando de incluir ambas essas linhas também. Observe que você precisa apenas do primeiro certificado da janela de saída.
 
  4. Crie um arquivo chamado **_X509testcert.pem_**, abra-o em um editor de texto de sua escolha e copie o conteúdo da área de transferência para o arquivo. Salve o arquivo, pois você o usará mais tarde para registrar o dispositivo. Quando o software de registro é executado, ele usa o mesmo certificado durante o provisionamento automático.    

Esses artefatos de segurança são necessários durante a inscrição para registrar seu dispositivo no Serviço de Provisionamento de Dispositivos. O serviço de provisionamento fica aguardando que um desses dispositivos inicialize e se conecte com ele mais tarde. Quando o dispositivo é inicializado pela primeira vez, a lógica do SDK de cliente interage com o chip (ou simulador) para extrair os artefatos de segurança do dispositivo e verifica o registro com o Serviço de Provisionamento de Dispositivos. 

## <a name="create-the-device-registration-software"></a>Criar o software de registro do dispositivo

A última etapa é codificar um aplicativo de registro que usa o SDK de cliente do Serviço de Provisionamento de Dispositivos para registrar o dispositivo no serviço Hub IoT. 

> [!NOTE]
> Nesta etapa, vamos pressupor o uso de um dispositivo simulado feito com a execução de um aplicativo de registro de exemplo do SDK na sua estação de trabalho. No entanto, os mesmos conceitos se aplicam se você estiver criando um aplicativo de registro para implantação em um dispositivo físico. 

1. No portal do Azure, selecione a folha **Visão Geral** do Serviço de Provisionamento de Dispositivos e anote o valor de **_Escopo da ID_**. O *escopo da ID* é gerado pelo serviço e assegura a exclusividade. É imutável e é usado para identificar exclusivamente as IDs de registro.

    ![Extrair informações de ponto de extremidade do DPS na folha do portal](./media/tutorial-set-up-device/extract-dps-endpoints.png) 

2. No *Gerenciador de Soluções* do Visual Studio em seu computador, navegue até a pasta **Provisionar\_Amostras**. Selecione o projeto de exemplo chamado **prov\_dev\_client\_sample** e abra o arquivo **prov\_dev\_client\_sample.c**.

3. Atribua o valor _Escopo da ID_ obtido na etapa 1 para a variável `id_scope` (removendo os colchetes à esquerda /`[` e à direita /`]`): 

    ```c
    static const char* global_prov_uri = "global.azure-devices-provisioning.net";
    static const char* id_scope = "[ID Scope]";
    ```

    Para referência, a variável `global_prov_uri`, que permite à API de registro de cliente do Hub IoT `IoTHubClient_LL_CreateFromDeviceAuth` se conectar à instância do Serviço de Provisionamento de Dispositivos designada.

4. Na função **main()** do mesmo arquivo, comente/remova o comentário da variável `hsm_type` que corresponde ao mecanismo de atestado que está sendo usado pelo software de registro do dispositivo (TPM ou X.509): 

    ```c
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

5. Salve suas alterações e recrie o exemplo **prov\_dev\_client\_sample** selecionando "Compilar solução" no menu "Compilar". 

6. Clique com o botão direito do mouse no projeto **prov\_dev\_client\_sample** na pasta **Provision\_Samples** e selecione **Definir como Projeto de Inicialização**. NÃO execute o aplicativo de exemplo agora.

> [!IMPORTANT]
> Não execute/inicie o dispositivo agora! Você precisa concluir o processo de registro do dispositivo no Serviço de Provisionamento de Dispositivos antes de iniciar o dispositivo. A seção Próximas etapas abaixo irá guiá-lo até o próximo artigo.

### <a name="sdk-apis-used-during-registration-for-reference-only"></a>APIs do SDK usadas durante o registro (apenas para referência)

Para referência, o SDK fornece as APIs a seguir para uso pelo aplicativo durante o registro. Essas APIs ajudam seu dispositivo a se conectar e se registrar no Serviço de Provisionamento de Dispositivos quando ele é inicializado. Em troca, seu dispositivo recebe as informações necessárias para estabelecer uma conexão com a instância do Hub IoT:

```C
// Creates a Provisioning Client for communications with the Device Provisioning Client Service.  
PROV_DEVICE_LL_HANDLE Prov_Device_LL_Create(const char* uri, const char* scope_id, PROV_DEVICE_TRANSPORT_PROVIDER_FUNCTION protocol)

// Disposes of resources allocated by the provisioning Client.
void Prov_Device_LL_Destroy(PROV_DEVICE_LL_HANDLE handle)

// Asynchronous call initiates the registration of a device.
PROV_DEVICE_RESULT Prov_Device_LL_Register_Device(PROV_DEVICE_LL_HANDLE handle, PROV_DEVICE_CLIENT_REGISTER_DEVICE_CALLBACK register_callback, void* user_context, PROV_DEVICE_CLIENT_REGISTER_STATUS_CALLBACK reg_status_cb, void* status_user_ctext)

// Api to be called by user when work (registering device) can be done
void Prov_Device_LL_DoWork(PROV_DEVICE_LL_HANDLE handle)

// API sets a runtime option identified by parameter optionName to a value pointed to by value
PROV_DEVICE_RESULT Prov_Device_LL_SetOption(PROV_DEVICE_LL_HANDLE handle, const char* optionName, const void* value)
```

Você também pode chegar à conclusão de que precisa refinar seu aplicativo de registro de cliente do Serviço de Provisionamento de Dispositivos usando primeiro um dispositivo simulado e uma configuração de serviço de teste. Depois que o aplicativo está funcionando no ambiente de teste, você pode compilá-lo para o dispositivo específico e copiar o executável para a imagem do dispositivo. 

## <a name="clean-up-resources"></a>Limpar recursos

Neste ponto, você pode ter os serviços de Provisionamento de Dispositivos e Hub IoT em execução no portal. Se você quiser abandonar a configuração de provisionamento de dispositivos e/ou atrasar a conclusão desta série de tutoriais, recomendamos desligá-los para evitar incorrer em custos desnecessários.

1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  
2. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar o SDK de cliente do Serviço de Provisionamento de Dispositivos específico da plataforma
> * Extrair os artefatos de segurança
> * Criar o software de registro do dispositivo

Avance para o próximo tutorial para aprender a provisionar o dispositivo para o Hub IoT registrando-o no Serviço de Provisionamento de Dispositivos no Hub IoT do Azure para provisionamento automático.

> [!div class="nextstepaction"]
> [Provisionar o dispositivo para o Hub IoT](tutorial-provision-device-to-hub.md)

