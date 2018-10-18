---
title: Conectividade de dispositivo no Azure IoT Central | Microsoft Docs
description: Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: d2ed07be829e48cc4fc0538c08fd498dea99e71e
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985168"
---
# <a name="device-connectivity-in-azure-iot-central"></a>Conectividade de dispositivo no Azure IoT Central | Microsoft Docs

Este artigo apresenta os principais conceitos relacionados à conectividade de dispositivo no Microsoft Azure IoT Central.

O Azure IoT Central usa [ DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure](https://docs.microsoft.com/azure/iot-dps/about-iot-dps), permitindo que o IoT Central dê suporte à integração e conexão de dispositivos em escala.

-   Os clientes agora podem gerar credenciais de dispositivos e configurar os dispositivos offline sem precisar primeiro registrar os dispositivos no IoT Central
-   O IoT Central dá suporte à conexão de dispositivo com conectividade baseada em certificado X509 recomendada pelo setor, enquanto continua a dar suporte e melhorar a conectividade de SAS (Assinaturas de Acesso Compartilhado)
-   Os clientes do IoT Central agora podem trazer suas próprias IDs de Dispositivos para registrar dispositivos na IoT Central, permitindo integração simples com sistemas de back office existentes
-   Há uma maneira consistente de conectar dispositivos ao IoT Central 

>[!NOTE]
>O IoT Central usa o DPS (Serviço de Provisionamento de Dispositivos) no Hub IoT do Azure para todos os registros e conexões de dispositivos, [saiba mais sobre DPS](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

Com base no seu caso de uso, siga as instruções para conectar dispositivos ao IoT Central
1. [Conectar um único dispositivo rapidamente (usando Assinaturas de Acesso Compartilhado)](#connect-a-single-device)
1. [Conectar dispositivos em escala usando SAS (Assinaturas de Acesso Compartilhado)](#connect-devices-at-scale-using-shared-access-signatures)
1. [Conectar dispositivos em escala usando certificados X509](#connect-devices-using-x509-certificates) **recomendado para cargas de trabalho de produção**
1. [Conectar sem primeiro registrar dispositivos](#connect-without-first-registering-devices) 


>[!NOTE]
>Aqui está o ponto de extremidade global de dispositivos para conexão e provisionamento **global.azure-devices-provisioning.net**.

## <a name="connect-a-single-device"></a>Conectar um único dispositivo
Conectar um único dispositivo ao IoT Central usando SAS é fácil e são necessárias apenas algumas etapas 
1. Adicione um **dispositivo real** do Device Explorer, clique em **+Novo > Real** para adicionar um dispositivo real.
    * Insira a ID do Dispositivo **<span style="color:Red">(deve estar em minúsculas)</span>** ou use a ID do Dispositivo sugerida.
    * Insira o nome do dispositivo ou use o nome sugerido   
    ![Adicionar dispositivo](media\concepts-connectivity\add-device.png)
1. Obtenha detalhes da conexão como a **ID do escopo, ID do dispositivo e Chave Primária** do dispositivo adicionado, clicando em **Conectar** na página do dispositivo.
    * A **[ID do escopo](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope)** é por Aplicativo do IoT Central e é gerada pelo DPS, usado para garantir ID de dispositivo exclusiva em um aplicativo.
    * A **ID do dispositivo** é a ID de dispositivo exclusiva por Aplicativo, o dispositivo precisa enviar a ID do dispositivo como parte da chamada de registro.   
    * **Chave primária** é um token SAS, gerado pelo IoT Central para esse dispositivo específico. 
    ![Detalhes da conexão](media\concepts-connectivity\device-connect.PNG)
1. Use esses detalhes de conexão **Identidade do Dispositivo, Nome do Dispositivo e Chave Primária do Dispositivo** no código do dispositivo para provisionar e conectar o dispositivo e começar a ver o fluxo de dados instantaneamente. Se você estiver usando o dispositivo MxChip, siga as [instruções passo-a-passo aqui ](howto-connect-devkit.md#add-a-real-device), inicie pela seção **Preparar o dispositivo DevKit**.   

    A seguir, são apresentadas as referências para outras linguagens que talvez você queira usar.

    *   **Linguagem C:** se você estiver usando C, siga [este cliente de dispositivo de exemplo C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) para conectar um dispositivo de exemplo. Use as seguintes configurações no exemplo.   

         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
         
         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";

         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```

    *   **Node.js:** se você quiser usar o Node.js [use as instruções passo-a-passo aqui](tutorial-add-device.md#prepare-the-client-code), inicie pela seção **Preparar o código do cliente**.



## <a name="connect-devices-at-scale-using-shared-access-signatures"></a>Conectar dispositivos em escala usando Assinaturas de Acesso Compartilhado

Para conectar dispositivos em escala com o IoT Central usando SAS, há duas etapas envolvidas 
1. **Registrar dispositivos**, importando-os para o IoT Central por meio de um arquivo CSV e exportar dispositivos com detalhes de conexão de dispositivo para usar para conectar os dispositivos
1. **Configurar dispositivo** O dispositivo é programado com os detalhes da conexão ( **ID do Escopo, ID do Dispositivo e Chave Primária**), permitindo chamar o serviço de provisionamento para obter a atribuição de aplicativo do IoT Central/informações de conexão quando for ativado.

>[!NOTE]
>Uma opção avançada também está disponível, em que é possível conectar dispositivos sem precisar primeiro registrar dispositivos no IoT Central, [saiba mais aqui](https://docs.microsoft.com/azure/iot-dps/about-iot-dps).

**Registrar dispositivos**

Para conectar um grande número de dispositivos ao aplicativo, o Azure IoT Central oferece dispositivos de importação em massa por meio de um arquivo CSV. 

Requisitos do arquivo CSV: o arquivo CSV deve ter as seguintes colunas (e cabeçalhos)
1.  IOTC_DeviceID **<span style="color:Red">(deve estar em minúsculas)</span>**
1.  IOTC_DeviceName (opcional)



Importar dispositivos para registrá-los no aplicativo
1.  Escolha **Explorer** no menu de navegação esquerdo.
1.  No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja criar os dispositivos em massa. 
1.  Clique em **Importar**, selecione o arquivo CSV que contém a lista de IDs do Dispositivo a serem importados.
O arquivo CSV deve ter as seguintes colunas (e cabeçalhos)
    *   IOTC_DeviceID **<span style="color:Red">(deve estar em minúsculas)</span>**
    *   IOTC_DeviceName (opcional)
1.  Depois que a importação estiver concluída, uma mensagem de êxito será exibida na grade do dispositivo.

Exporte dispositivos para obter os detalhes da conexão, a opção Exportar cria um arquivo CSV com a ID do Dispositivo, o Nome do Dispositivo e a Chave do Dispositivo. Use esses detalhes para conectar o dispositivo ao IoT Central.
Para exportar em massa os dispositivos do seu aplicativo:
1.  Escolha **Explorer** no menu de navegação esquerdo.
1.  Selecione os dispositivos que você deseja exportar e, em seguida, clique em **Exportar**.
1.  Quando a exportação for concluída, será mostrada uma mensagem com um link para baixar o arquivo gerado.
1.  Clique na mensagem de êxito para baixar o arquivo para uma pasta local no disco.
1.  O arquivo CSV exportado terá as seguintes informações de colunas: **ID do Dispositivo, Nome do Dispositivo, Chaves Primárias/Secundárias do Dispositivo e impressões digitais do certificado principal/secundário**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY


**Configuração do dispositivo**

Use esses detalhes de conexão, **Identidade do Dispositivo (IOTC_DEVICEID), Chave Primária do Dispositivo ( IOTC_SASKEY_PRIMARY) e ID do Escopo** no código do dispositivo para provisionar e conectar o dispositivo. Se ainda não o fez, obtenha a **ID do Escopo** do aplicativo do IoT Central **Administração >   Conexão do Dispositivo > ID do Escopo**.
Se você estiver usando o dispositivo **MxChip** para conectar-se siga as [instruções passo a passo aqui](howto-connect-devkit.md#add-a-real-device), inicie pela seção **Preparar dispositivo DevKit** .   

A seguir, são apresentadas as referências para outras linguagens que talvez você queira usar.

   *   **Linguagem C:** se você estiver usando C, siga [este cliente de dispositivo de exemplo C](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md) para conectar um dispositivo de exemplo. Use as seguintes configurações no exemplo.   
         ```
         hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;

         static const char* const SYMMETRIC_KEY_VALUE = "Enter Primary Symmetric key here";
         static const char* const REGISTRATION_NAME = "Enter Device Id here";
        ```
    * **Node.js:** se você quiser usar o Node.js [use as instruções passo-a-passo aqui](tutorial-add-device.md#prepare-the-client-code), inicie pela seção **Preparar o código do cliente**.


## <a name="connect-devices-using-x509-certificates"></a>Conectar dispositivos usando X509 certificados

Usar certificados X.509 como um mecanismo de atestado é uma maneira excelente de escalar **produção** e simplificar o provisionamento de dispositivos. Os certificados X.509 normalmente são organizados em uma cadeia de certificados de confiança na qual cada certificado na cadeia é assinado pela chave privada do próximo certificado mais alto e assim por diante, terminando em um certificado raiz autoassinado. Isso estabelece uma cadeia de confiança delegada a partir do certificado raiz gerado por uma AC (autoridade de certificação) raiz confiável através de cada AC intermediária para o certificado "secundário" de entidade final instalado em um dispositivo. Para saber mais, consulte [Autenticação de dispositivo usando certificados de AC X.509](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Para conectar dispositivos ao IoT Central usando certificados X509, há três etapas principais envolvidas 
1. **Configurar as configurações de conexão** no aplicativo do IoT Central adicionando/verificando o certificado raiz/intermediário X509 usado para gerar os certificados de dispositivo.  Há duas etapas para definir as configurações de conexão para os certificados X509.  

    *   **Adicione o certificado raiz ou intermediário X509** que você está usando para gerar os certificados de dispositivo secundários. Vá para Administração> Conexão de Dispositivo> Certificados. 
    
        ![Configurações de conexão](media\concepts-connectivity\connection-settings.PNG)
    *   **Verificação de certificado:** verificar a propriedade do certificado garante que o remetente do certificado esteja em posse da chave privada do certificado. Para verificar o certificado
        *  Gere o código de verificação, clique no botão próximo ao campo Código de verificação para gerar o código de verificação. 
        *  Crie um certificado de verificação X.509 com o código de verificação, salve o certificado como um arquivo .cer. 
        *  Carregue o certificado de verificação assinado e clique em verificar.

        ![Configurações de conexão](media\concepts-connectivity\verify-cert.png)
    *   **Certificado secundário:** durante o ciclo de vida da solução de IoT, você precisará rolar certificados. Duas das principais razões para implantar certificados seria uma violação de segurança e expirações de certificado. Os certificados secundários são usados para reduzir o tempo de inatividade dos dispositivos que estão tentando provisionar enquanto você atualiza o certificado Primário.

    **SOMENTE PARA FINS DE TESTES** 
    
    A seguir, são apresentadas algumas ferramentas de linha de comando do utilitário que você poderá usar para gerar certificados de CA e certificados de dispositivo.

    * Se você estiver usando MxChip, aqui está uma [ferramenta de linha de comando](http://aka.ms/iotcentral-docs-dicetool) para gerar certificados de CA, adicioná-la ao aplicativo do IoT Central e verificar os certificados. 

    *   Use esta [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para
        * Criar a cadeia de certificados (siga a Etapa 2 nos documentos do GitHub). 
         Salve os certificados como arquivos .cer e carregue para o IoT Central (Primário).   
        * Obtenha o Código de Verificação do aplicativo do IoT Central, gere o certificado (siga a etapa 3 dos documentos do GitHub) e carregue para verificar. 
        * Crie certificados secundários com a ID do dispositivo como um parâmetro para a ferramenta (siga a Etapa 4). Salve o certificado e use-o no dispositivo.     

1. **Registre dispositivos**, importando-os para o IoT Central por meio de um arquivo CSV.

1. **Configuração do dispositivo**: gere os certificados secundários usando o certificado raiz carregado. Certifique-se de usar a **ID do Dispositivo** como o CNAME nos certificados secundários e que está em **minúsculas**. Aqui está uma [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para gerar certificados de dispositivo/secundários de **SOMENTE PARA FINS DE TESTES**.

    Programe o dispositivo com informações de serviço de provisionamento, permitindo que ele obtenha os detalhes de conexão e a atribuição de aplicativo do IoT Central quando ativado.    

    **Referência adicional** 
    *   Exemplo de implementação para [RaspberryPi.](http://aka.ms/iotcentral-docs-Raspi-releases)  

    *   [Exemplo de cliente de dispositivo em C.](https://github.com/Azure/azure-iot-sdk-c/blob/dps_symm_key/provisioning_client/devdoc/using_provisioning_client.md)

>[!NOTE]
>Use a **ID do Dispositivo** como um cname ao gerar os certificados secundários para dispositivos.

>[!NOTE]
>A **ID do Dispositivo** deve estar em minúsculas 
 
## <a name="connect-without-first-registering-devices"></a>Conectar sem primeiro registrar dispositivos
Um dos principais cenários que o IoT Central permite é que os OEMs criem dispositivos em massa, gerem credenciais e os configurem na fábrica sem precisar primeiro registrá-los no IoT Central. Quando os dispositivos estiverem ativados e conectados ao IoT Central, o operador aprovará o dispositivo para conectar-se ao aplicativo do IoT Central.

A seguir, é apresentado o fluxo para conectar dispositivos com esse recurso

![Configurações de conexão](media\concepts-connectivity\device-connection-flow.PNG)


Siga as etapas com base em sua escolha de esquema de autenticação de dispositivo (X509/SAS)

1. **Configurações de conexão** 
    * **Certificados X509:** [adicione e verifique o certificado raiz/intermediário](#connect-devices-using-x509-certificates) e use-o para gerar os certificados de dispositivo na próxima etapa.
    * **SAS:** copie a Chave primária (essa chave é a chave de SAS do grupo para esse aplicativo do IoT Central) e use-a para gerar as chaves de SAS do dispositivo na próxima etapa. 
![SAS de configurações de conexão](media\concepts-connectivity\connection-settings-sas.png)

1. **Gerar credenciais de dispositivo** 
    *   **Certificados X509:** gere os certificados secundários para os dispositivos usando o certificado raiz/intermediário que você adicionou a esse aplicativo. Certifique-se de usar a **ID do Dispositivo** como um cname nos certificados secundários e **<span style="color:Red">(deve estar em minúsculas)</span>**. Aqui está uma [ferramenta de linha de comando](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md ) para gerar certificados de dispositivo/secundários para testes.
    *   **SAS** As chaves de SAS do dispositivo podem ser geradas usando esta [ferramenta de linha de comando](https://www.npmjs.com/package/dps-keygen). Use a chave de SAS Primária (chave de SAS de grupo) da etapa anterior. Certifique-se de que a ID do Dispositivo **<span style="color:Red">está em minúsculas</span>**.

        Use as instruções abaixo para gerar a chave de SAS do dispositivo           

        ```
        npm i -g dps-keygen
        ```
    
        **Uso**
                        
        ```
        dps-keygen <Primary_Key(GroupSAS)> <device_id>
        ```

1. **Configuração do dispositivo** 
    
     Faça flash do dispositivo com **ID do Escopo, ID do Dispositivo, Certificado de dispositivo/Chave de SAS** e, em seguida, ative o dispositivo para conectar-se ao aplicativo do IoT Central.

1. **Conecte o dispositivo ao IoT Central:** após ativar os dispositivos, conecte-se ao IoT Central/DPS para registrar-se.

1. **Associe o dispositivo a um modelo:** o dispositivo conectado será exibido em **Dispositivos não associados** no **Device Explorer**. O status de provisionamento do dispositivo é **Registrado**. **Associe** o dispositivo ao modelo de dispositivo apropriado e aprove o dispositivo para conectar-se ao aplicativo do IoT Central. O dispositivo obtém os detalhes da conexão para o aplicativo do IoT Central, conecta-se e começa a enviar dados. O provisionamento de dispositivos agora está completo e o *Status de provisionamento* muda para **Provisionado**.

## <a name="device-provisioning-status"></a>Status de Provisionamento de Dispositivos
Há uma série de etapas envolvidas quando um dispositivo real é conectado ao Azure IoT Central 
1. **Registrado**: o dispositivo é primeiro **Registrado**, o que significa que o dispositivo é criado no IoT Central e tem a ID do Dispositivo para o dispositivo.
O dispositivo é Registrado quando  
    *   Um novo dispositivo real é adicionado no **Explorer**
    *   Um conjunto de dispositivos é adicionado usando **Importar** no **Explorer**
    *   Um dispositivo que não foi registrado, mas conecta-se com credenciais válidas e é visível em dispositivos **Não associados**. 

    Em todos os casos acima, o *Status de provisionamento* é **Registrado**

1. **Provisionado**: a próxima etapa é quando o dispositivo conecta-se com credenciais válidas e o IoT Central conclui a etapa de provisionamento (criando o dispositivo no Hub IoT). Em seguida, ele retorna a cadeia de conexão ao dispositivo para conectar-se e começar a enviar dados. O *Status de provisionamento* de dispositivos agora muda de **Registrado** para **Provisionado**.

1.  **Bloqueado**: o operador pode bloquear um dispositivo porque, quando um dispositivo é bloqueado ele não pode enviar dados ao IoT Central e precisa ser redefinido. Os dispositivos bloqueados têm o *Status de provisionamento* como **Bloqueado**. O operador também pode desbloquear o dispositivo. Após desbloquear o *Status de provisionamento* de dispositivos, retorne ao *Status de provisionamento* (Registrado ou Provisionado). 

## <a name="getting-device-connection-string"></a>Obter a cadeia de conexão do dispositivo
É possível obter uma cadeia de conexão do dispositivo do Hub IoT para o Hub IoT usando as seguintes etapas 
1. Obtenha detalhes da conexão como **ID do Escopo, ID do Dispositivo, Chave Primária do Dispositivo** da página do dispositivo (vá para a página do dispositivo > clique em Conectar) 

    ![Detalhes da conexão](media\concepts-connectivity\device-connect.PNG)

1. Obtenha a cadeia de conexão do dispositivo usando a ferramenta de linha de comando a seguir.
    Use as instruções a seguir para obter a cadeia de conexão do dispositivo  

    ```cmd/sh
    npm i -g dps-keygen
    ```
    **Uso**

    Para criar uma cadeia de conexão, localize o binário em pasta/bin
    ```cmd/sh
    dps_cstr <scope_id> <device_id> <Primary Key(for device)>
    ```
    Saiba mais sobre a [ferramenta dps-keygen aqui.](https://www.npmjs.com/package/dps-keygen)

## <a name="sdk-support"></a>Suporte a SDK

Os SDKs do Dispositivo do Azure oferecem a maneira mais fácil para implementar o código nos dispositivos que conectam ao aplicativo Azure IoT Central. Estão disponíveis os SDKs do dispositivo a seguir:

- [SDK do IoT do Azure para C](https://github.com/azure/azure-iot-sdk-c)
- [SDK do IoT do Azure para Python](https://github.com/azure/azure-iot-sdk-python)
- [SDK do IoT do Azure para Node.js](https://github.com/azure/azure-iot-sdk-node)
- [SDK do IoT do Azure para Java](https://github.com/azure/azure-iot-sdk-java)
- [SDK do IoT do Azure para .NET](https://github.com/azure/azure-iot-sdk-csharp)

Cada dispositivo conecta usando uma cadeia de conexão única que identifica o dispositivo. Um dispositivo somente poderá conectar ao Hub IoT no qual estiver registrado. Ao criar um dispositivo real no aplicativo Azure IoT Central, o aplicativo gera uma cadeia de conexão a ser usada.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Recursos de SDK e conectividade do Hub IoT

Toda a comunicação do dispositivo com o Hub IoT usa as opções de conectividade do Hub IoT a seguir:

- [Mensagens de dispositivo para nuvem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dispositivos gêmeos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

A tabela a seguir resume como os recursos do dispositivo Azure IoT Central são mapeados para os recursos do Hub IoT:

| Azure IoT Central | Hub IoT do Azure |
| ----------- | ------- |
| Medida: Telemetria | Mensagens do dispositivo para a nuvem |
| Propriedades do dispositivo | Propriedades relatadas do dispositivo gêmeo |
| Configurações | Propriedades desejadas e relatadas do dispositivo gêmeo |

Para saber mais sobre como usar os SDKs do Dispositivo, consulte um dos artigos a seguir para exemplo de código:

- [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
- [Conectar um dispositivo Raspberry Pi ao aplicativo Azure IoT Central](howto-connect-raspberry-pi-python.md)
- [Conectar um dispositivo do kit de DevDiv ao aplicativo Azure IoT Central](howto-connect-devkit.md).


## <a name="protocols"></a>Protocolos

Os SDKs do Dispositivo dão suporte aos protocolos de rede a seguir para conectar um Hub IoT:

- MQTT
- AMQP
- HTTPS

Para obter informações sobre esses diferentes protocolos e as diretrizes sobre como escolher um deles, consulte [Escolher um protocolo de comunicação](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Se o dispositivo não puder usar nenhum dos protocolos com suporte, será possível usar o Azure IoT Edge para fazer a conversão de protocolo. O IoT Edge dá suporte a outros cenários de inteligência na borda para transferir o processamento para a borda do aplicativo Azure IoT Central.

## <a name="security"></a>Segurança

Todos os dados trocados entre dispositivos e o Azure IoT Central são criptografados. O Hub IoT autentica todas as solicitações de um dispositivo que conecta a qualquer ponto de extremidade do Hub IoT voltado para o dispositivo. Para evitar a troca de credenciais pela rede, um dispositivo usa tokens assinados para autenticar. Para obter mais informações, consulte [Controlar o acesso ao Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Atualmente, os dispositivos que conectam ao Azure IoT Central devem usar tokens de SAS. Certificados X.509 não têm suporte para dispositivos que conectam ao Azure IoT Central.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu sobre a conectividade de dispositivo no Azure IoT Central, consulte as etapas a seguir sugeridas:

- [Preparar e conectar um dispositivo DevKit](howto-connect-devkit.md)
- [Preparar e conectar um Raspberry Pi](howto-connect-raspberry-pi-python.md)
- [Conectar um cliente Node.js genérico ao aplicativo Azure IoT Central](howto-connect-nodejs.md)
