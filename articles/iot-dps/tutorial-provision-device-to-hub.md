---
title: Provisionar um dispositivo usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure | Microsoft Docs
description: Provisionar o dispositivo para um único Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure
author: dsk-2015
ms.author: dkshir
ms.date: 04/12/2018
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 453159e51473b76d8a95b98237796ac490f8ed6a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34630129"
---
# <a name="provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Provisionar o dispositivo para um Hub IoT usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure

No tutorial anterior, você aprendeu como configurar um dispositivo para se conectar ao seu Serviço de Provisionamento de Dispositivos. Neste tutorial, você aprenderá a usar este serviço para provisionar seu dispositivo para um único Hub IoT usando **_listas de registro_** e provisionamento automático. Este tutorial mostra como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

## <a name="prerequisites"></a>pré-requisitos

Antes de prosseguir, não deixe de configurar seu dispositivo conforme discutido no tutorial [Configurar um dispositivo para provisionar usando o Serviço de Provisionamento de Dispositivos no Hub IoT do Azure](./tutorial-set-up-device.md).

Se você não estiver familiarizado com o processo de provisionamento automático, analise os [Conceitos de provisionamento automático](concepts-auto-provisioning.md) antes de continuar.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrar o dispositivo

Esta etapa envolve adicionar os artefatos de segurança exclusivos do dispositivo ao Serviço de Provisionamento de Dispositivos. Esses artefatos de segurança se baseiam no [mecanismo de atestado](concepts-device.md#attestation-mechanism) do dispositivo, da seguinte maneira:

- Para dispositivos baseados no TPM, você precisará do seguinte:
    - A *Chave de Endosso* que é exclusiva para cada chip ou simulação do TPM, obtida do fabricante do chip do TPM.  Leia [Compreender a chave de endosso do TPM](https://technet.microsoft.com/library/cc770443.aspx) para obter mais informações.
    - A *ID do registro* é usada para identificar exclusivamente um dispositivo no namespace/escopo. Essa ID pode ou não ser a mesmo ID do dispositivo. A ID é obrigatória para cada dispositivo. Para dispositivos baseados em TPM, a ID do registro pode ser derivada do TPM em si, por exemplo, um hash SHA-256 da chave de endosso do TPM.

    [![Informações de registro do TPM no portal](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- Para dispositivos baseados em X.509, você precisará do seguinte:
    - O [certificado emitido para o chip ou simulação X.509](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), na forma de um arquivo *.pem* ou um *.cer*. Para o registro individual, você precisa usar o *certificado assinante* por dispositivo para seu sistema X.509; já para grupos de registro, você precisa usar o *certificado raiz*. 

    [![Adicionar um registro individual para atestado de X.509 no portal](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Há duas maneiras de registrar o dispositivo no Serviço de Provisionamento de Dispositivos:

- **Grupos de registro** Isso representa um grupo de dispositivos que compartilham um mecanismo de atestado específico. É recomendável usar um grupo de registro para um grande número de dispositivos que compartilham uma configuração inicial desejada ou para dispositivos que vão todos para o mesmo locatário.

    [![Adicionar um registro de grupo para atestado de X.509 no portal](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Registros individuais** isso representa uma entrada para um único dispositivo que pode ser registrado no Serviço de Provisionamento de Dispositivos. Registros individuais podem usar tokens de certificados x509 ou tokens SAS (em um TPM real ou virtual) como mecanismos de atestado. É recomendável usar inscrições individuais para dispositivos que exigem configurações iniciais exclusivas e para dispositivos que só podem usar tokens SAS por meio do TPM ou TPM virtual, como o mecanismo de atestado. Registros individuais podem ter a ID de dispositivo de Hub IoT desejada especificada.

Agora registre o dispositivo com sua instância do Serviço de Provisionamento de Dispositivos usando os artefatos de segurança necessários com base no mecanismo de atestado do dispositivo: 

1. Entre no portal do Azure, clique no botão **Todos os recursos** no menu esquerdo e abra o serviço de Provisionamento de Dispositivos.

2. Na folha de resumo do Serviço de Provisionamento de Dispositivos, selecione **Gerenciar registros**. Selecione a guia **Registros Individuais** ou a guia **Grupos de Registro**, de acordo com a configuração do dispositivo. Clique no botão **Adicionar** na parte superior. Selecione **TPM** ou **X.509** como o *mecanismo* de atestado de identidade e insira os artefatos de segurança apropriados, conforme discutido anteriormente. Você pode inserir uma nova **ID de dispositivo do Hub IoT**. Uma vez concluído, clique no botão **Salvar**. 

3. Quando o dispositivo for registrado com êxito, você o verá exibido no portal conforme demonstrado abaixo:

    ![Registro de TPM bem-sucedido no portal](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Após o registro, o serviço de provisionamento fica aguardando que um desses dispositivos inicialize e se conecte com ele em algum momento. Quando o dispositivo é inicializado pela primeira vez, a biblioteca do SDK do cliente interage com o chip para extrair os artefatos de segurança do dispositivo e verifica o registro com o Serviço de Provisionamento de Dispositivos. 

## <a name="start-the-device"></a>Iniciar o dispositivo

Neste ponto, a configuração a seguir está pronta para registro do dispositivo:

1. Seu dispositivo ou grupo de dispositivos são registrados no Serviço de Provisionamento de Dispositivos e 
2. O dispositivo está pronto com o mecanismo de atestado configurado e podendo ser acessado pelo aplicativo usando o SDK do cliente do Serviço de Provisionamento de Dispositivos.

Inicie o dispositivo para permitir que seu aplicativo cliente inicie o registro com o Serviço de Provisionamento de Dispositivos.  

## <a name="verify-the-device-is-registered"></a>Verificar se o dispositivo está registrado

Uma vez que o dispositivo é inicializado, as ações a seguir devem ocorrer. Consulte o aplicativo de exemplo do simulador de TPM [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) para obter mais detalhes. 

1. O dispositivo envia uma solicitação de registro para o Serviço de Provisionamento de Dispositivos.
2. Para dispositivos do TPM, o Serviço de Provisionamento de Dispositivos envia de volta um desafio de registro que é respondido pelo dispositivo. 
3. Após um registro bem-sucedido, o Serviço de Provisionamento de Dispositivos envia o URI do Hub IoT, a ID do dispositivo e a chave criptografada de volta para o dispositivo. 
4. O aplicativo cliente do Hub IoT no dispositivo se conecta então ao seu hub. 
5. Após a conexão bem-sucedida ao hub, você verá o dispositivo aparecer no **Device Explorer** do Hub IoT. 

    ![Conexão bem-sucedida ao hub no portal](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Registrar o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registrado

Avance para o próximo tutorial para aprender a provisionar vários dispositivos entre hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Provisionar dispositivos entre Hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
