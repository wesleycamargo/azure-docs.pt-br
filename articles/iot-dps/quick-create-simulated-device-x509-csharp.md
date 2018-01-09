---
title: Provisionar um dispositivo simulado X.509 no Hub IoT do Azure usando C# | Microsoft Docs
description: "Guia de Início Rápido do Azure – Criar e provisionar um dispositivo X.509 simulado usando o SDK do dispositivo C# para o Serviço de Provisionamento do Dispositivo Hub IoT do Azure"
services: iot-dps
keywords: 
author: JimacoMS2
ms.author: v-jamebr
ms.date: 12/21/2017
ms.topic: hero-article
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 631b3dbd644b9fb9c14cb6e39a67560b070cca89
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/02/2018
---
# <a name="create-and-provision-a-simulated-x509-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Criar e provisionar um dispositivo X.509 simulado usando o SDK do dispositivo C# para o Serviço de Provisionamento do Dispositivo Hub IoT
> [!div class="op_single_selector"]
> * [C](quick-create-simulated-device-x509.md)
> * [Java](quick-create-simulated-device-x509-java.md)
> * [C#](quick-create-simulated-device-x509-csharp.md)
> * [Python](quick-create-simulated-device-x509-python.md)

Estas etapas mostram como o [SDK C# do Hub IoT do Azure](https://github.com/Azure/azure-iot-sdk-csharp) simulou o exemplo do dispositivo X.509 em uma computador de desenvolvimento com o SO Windows e conectou o dispositivo simulado com o Serviço de Provisionamento de Dispositivos e o seu hub IoT.

Não deixe de concluir as etapas em [Configurar o Serviço de Provisionamento de Dispositivos do Hub IoT com o portal do Azure](./quick-setup-auto-provision.md) antes de continuar.

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Preparar o ambiente de desenvolvimento 

1. Verifique se você tem o [SDK do .NET Core](https://www.microsoft.com/net/download/windows) instalado no computador. 

1. Verifique se o `git` está instalado em seu computador e é adicionado às variáveis de ambiente que podem ser acessadas pela janela de comando. Confira [ferramentas de cliente Git do Software Freedom Conservancy](https://git-scm.com/download/) para obter a versão mais recente das ferramentas `git` a serem instaladas, que inclui o **Git Bash**, o aplicativo de linha de comando que você pode usar para interagir com seu repositório Git local. 

4. Abra um prompt de comando ou o Bash do Git. Clone o repositório GitHub do [SDK para C# de IoT do Azure](https://github.com/Azure/azure-iot-sdk-csharp):
    
    ```cmd
    git clone --recursive https://github.com/Azure/azure-iot-sdk-csharp.git
    ```

## <a name="create-a-self-signed-x509-device-certificate-and-individual-enrollment-entry"></a>Criar um certificado de dispositivo X.509 autoassinado e entrada de registro individual

1. No prompt de comando, altere os diretórios para o diretório do projeto de exemplo de provisionamento do dispositivo X.509.

    ```cmd
    cd .\azure-iot-sdk-csharp\provisioning\device\samples\ProvisioningDeviceClientX509
    ```

1. O código de exemplo está configurado para usar os certificados X.509 armazenados em um arquivo formatado PKCS12 protegido por senha (certificate.pfx). Além disso, você precisa de um arquivo de certificado de chave pública (certificate.cer) para criar um registro individual mais tarde neste Guia de Início Rápido. Para gerar um certificado autoassinado e seus arquivos .cer e .pfx associados, execute o seguinte comando:

    ```cmd
    powershell .\GenerateTestCertificate.ps1
    ```

2. O script solicita uma senha PFX. Lembre-se desta senha, você deve usá-la quando executar a amostra.

    ![ Inserir a senha PFX](./media/quick-create-simulated-device-x509-csharp/generate-certificate.png)  


4. Faça logon no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de provisionamento.

4. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** guia e clique no botão **Adicionar** na parte superior. 

5. Em **Adicionar a entrada da lista de registro**, insira as seguintes informações:
    - Selecione **X.509** como o *Mecanismo* de atestado de identidade.
    - No *arquivo Certificate .pem ou .cer*, selecione o arquivo de certificado **certificate.cer** criado nas etapas anteriores usando o widget *Gerenciador de Arquivos*.
    - Deixe **ID do Dispositivo** em branco. Seu dispositivo será provisionado com a ID do dispositivo configurado para o CN (Nome Comum) no certificado X.509, **iothubx509device1**. Esse também será o nome usado para a ID de registro da entrada de registro individual. 
    - Opcionalmente, você pode fornecer as seguintes informações:
        - Selecione um hub IoT vinculado com o serviço de provisionamento.
        - Atualize o **Estado inicial do dispositivo gêmeo** com a configuração inicial desejada para o dispositivo.
    - Uma vez concluído, clique no botão **Salvar**. 

    ![Inserir informações de registro de dispositivo X.509 na folha do portal](./media/quick-create-simulated-device-x509-csharp/enter-device-enrollment.png)  

   No registro bem-sucedido, a entrada do registro X.509 aparece como **iothubx509device1** na coluna *ID do Registro*, na guia *Registros Individuais*. 

## <a name="provision-the-simulated-device"></a>Provisionar o dispositivo simulado

1. Na folha **Visão Geral** do serviço de provisionamento, anote o valor de **_Escopo da ID_**.

    ![Extrair informações de ponto de extremidade do DPS na folha do portal](./media/quick-create-simulated-device-x509-csharp/copy-scope.png) 


2. Digite o seguinte comando para compilar e executar o exemplo de provisionamento de dispositivo X.509. Substitua o valor `<IDScope>` pelo Escopo da ID do serviço de provisionamento. 

    ```cmd
    dotnet run <IDScope>
    ```

6. Quando solicitado, insira a senha do arquivo PFX criado anteriormente. Observe as mensagens que simulam a inicialização e a conexão do dispositivo com o Serviço de Provisionamento de Dispositivos para obter as informações do Hub IoT. 

    ![Saída do dispositivo de exemplo](./media/quick-create-simulated-device-x509-csharp/sample-output.png) 

1. Verifique se o dispositivo foi provisionado. No provisionamento bem-sucedido do dispositivo simulado para o Hub IoT vinculado ao serviço de provisionamento, a ID do dispositivo aparece na folha **Dispositivos IOT** do hub. 

    ![Dispositivo é registrado no Hub IoT](./media/quick-create-simulated-device-x509-csharp/hub-registration.png) 

    Se você tiver alterado o *estado de dispositivo gêmeo inicial* do valor padrão na entrada de registro para o seu dispositivo, pode receber o estado desejado duas do hub e agir de acordo. Para saber mais, veja [Noções básicas e uso de dispositivos gêmeos no Hub IoT](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja continuar a trabalhar e explorar o dispositivo cliente de exemplo, não limpe os recursos criados neste Guia de Início Rápido. Caso contrário, use as etapas a seguir para excluir todos os recursos criados por este Guia de Início Rápido.

1. Feche a janela de saída de exemplo de dispositivo cliente em seu computador.
1. Feche a janela do simulador do TPM no seu computador.
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione o serviço de Provisionamento de Dispositivos. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  
1. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e selecione seu Hub IoT. Na parte superior da folha **Todos os recursos**, clique em **Excluir**.  

## <a name="next-steps"></a>Próximas etapas

Neste Guia de Início Rápido, você criou um dispositivo simulado X.509 no seu computador Windows e o provisionou no Hub IoT usando o Serviço de Provisionamento de Dispositivos do Hub IoT do Azure no portal. Para saber como registrar seu dispositivo X.509 programaticamente, continue com o Guia de Início Rápido para registro programático de dispositivos X.509. 

> [!div class="nextstepaction"]
> [Guia de Início Rápido do Azure – Registre dispositivos X.509 no Serviço de Provisionamento de Dispositivos do Hub IoT do Azure](quick-enroll-device-x509-node.md)
