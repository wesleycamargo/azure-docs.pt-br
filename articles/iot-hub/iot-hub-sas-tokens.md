<properties
 pageTitle="Como gerar tokens de segurança do Hub IoT | Microsoft Azure"
 description="Descrição dos diferentes tipos de tokens de segurança usados pelo Hub IoT e como gerá-los."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="03/02/2016"
 ms.author="elioda"/>

# Como usar os tokens de segurança do Hub IoT

O Hub IoT usa tokens de segurança para autenticar dispositivos e serviços a fim de evitar o envio de chaves durante a transmissão. Além disso, os tokens de segurança têm limite de escopo e de prazo de validade. [SDKs do Hub IoT do Azure][lnk-apis-sdks] geram tokens automaticamente sem a necessidade de qualquer configuração especial. No entanto, alguns cenários exigem que o usuário gere e use tokens de segurança diretamente. Entre esses cenários estão o uso direto de superfícies HTTP, MQTT ou AMQP, ou a implementação do padrão de serviço de token, conforme explicado na [Orientação sobre Hub IoT][lnk-guidance-security].

Este artigo descreve:

* O formato dos tokens de segurança e como gerá-los.
* Os principais casos de uso para a utilização de tokens de segurança a fim de autenticar dispositivos e serviços back-end.

## Estrutura do token de segurança
Você usa tokens de segurança para conceder aos dispositivos e serviços acesso de tempo limitado à funcionalidade específica do Hub IoT. Para garantir que somente os dispositivos e serviços autorizados possam se conectar, os tokens de segurança devem ser assinados com uma chave de política de acesso compartilhada ou uma chave simétrica armazenada com uma identidade de dispositivo no registro de identidade.

Um token assinado com uma chave de política de acesso compartilhada concede acesso a todas funcionalidades associadas às permissões da política de acesso compartilhado. Consulte a [seção de segurança do guia do desenvolvedor de Hub IoT][lnk-devguide-security]. Por outro lado, um token assinado com uma chave simétrica de uma identidade de dispositivo apenas concede a permissão **DeviceConnect** para a identidade do dispositivo associado.

O token de segurança tem o seguinte formato:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estes são os valores esperados:

| Valor | Descrição |
| ----- | ----------- |
| {signature} | Uma cadeia de caracteres de assinatura HMAC-SHA256 no formato: `{URL-encoded-resourceURI} + "\n" + expiry` **Importante**: a chave é decodificada da base64 e usada como chave para executar o cálculo de HMAC-SHA256. |
| {resourceURI} | Prefixo URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome de host do Hub IoT (sem protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiry} | As cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970. |
| {URL-encoded-resourceURI} | Codificação de URL em letras minúsculas do URI de recurso em letras minúsculas |
| {policyName} | O nome da política de acesso compartilhado a qual se refere esse token. Estará ausente em tokens que fazem referência às credenciais de registro do dispositivo. |

**Observação sobre o prefixo**: o prefixo URI é computado por segmento e não por caractere. Por exemplo, `/a/b` é um prefixo para `/a/b/c`, mas não para `/a/bc`.

Essa é uma função de Nó que calcula o token a partir das entradas `resourceUri, signingKey, policyName, expiresInMins`. As seções a seguir detalharão como inicializar as entradas diferentes para os casos de uso com token diferente.

    var crypto = require('crypto');

    var generateSasToken = function(resourceUri, signingKey, policyName, expiresInMins) {
        resourceUri = encodeURIComponent(resourceUri.toLowerCase()).toLowerCase();

        // Set expiration in seconds
        var expires = (Date.now() / 1000) + expiresInMins * 60;
        expires = Math.ceil(expires);
        var toSign = resourceUri + '\n' + expires;

        // using crypto
        var decodedPassword = new Buffer(signingKey, 'base64').toString('binary');
        const hmac = crypto.createHmac('sha256', decodedPassword);
        hmac.update(toSign);
        var base64signature = hmac.digest('base64');
        var base64UriEncoded = encodeURIComponent(base64signature);

        // construct autorization string
        var token = "SharedAccessSignature sr=" + resourceUri + "&sig="
        * base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };

> [AZURE.NOTE] Como o prazo de validade do token é validado em máquinas do Hub IoT, é importante que o descompasso no relógio do computador que gera o token seja mínimo.

## Como usar tokens SAS como um dispositivo

Há duas maneiras de obter as permissões **DeviceConnect** com o Hub IoT com tokens de segurança: usando uma chave de identidade do dispositivo, ou uma chave de política de acesso compartilhado.

Além disso, é importante observar que qualquer funcionalidade acessível a partir de dispositivos fica exposta por padrão em pontos de extremidade com o prefixo `/devices/{deviceId}`.

> [AZURE.IMPORTANT] A única maneira de o Hub IoT autenticar um dispositivo específico é usando a chave simétrica de identidade do dispositivo. Em casos nos quais uma política de acesso compartilhado é usada para acessar a funcionalidade do dispositivo, a solução deve considerar o componente emissor do token de segurança como um subcomponente confiável.

Os pontos de extremidade voltados para o dispositivo são (independentemente do protocolo):

| Ponto de extremidade | Funcionalidade |
| ----- | ----------- |
| `{iot hub host name}/devices/{deviceId}/messages/events` | Enviar mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/devices/{deviceId}/devicebound` | Receber mensagens da nuvem para o dispositivo. |

### Como usar uma chave simétrica no registro de identidade

Ao usar a chave simétrica da identidade de um dispositivo para gerar um token, o elemento policyName (`skn`) do token é omitido.

Por exemplo, um token criado para acessar todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave simétrica para a identidade `{device id}`,
* sem nome de política,
* qualquer data de validade

Um exemplo de uso da função de Nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

O resultado, que concede acesso a todas as funcionalidades para o dispositivo1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [AZURE.NOTE] É possível gerar um token seguro usando a ferramenta .NET [Gerenciador de Dispositivo][lnk-device-explorer].

### Como usar a política de acesso compartilhado

Ao criar um token de uma política de acesso compartilhado, o campo de nome da política `skn` deve ser definido como o nome da política usada. Também é necessário que a política conceda a permissão **DeviceConnect**.

Os dois cenários principais para usar as políticas de acesso compartilhado para acessar a funcionalidade do dispositivo são:

* [gateways de protocolo em nuvem][lnk-azure-protocol-gateway],
* [serviços de token][lnk-devguide-security] usados para implementar esquemas de autenticação personalizada.

Como a política de acesso compartilhado pode conceder acesso de conexão como qualquer dispositivo, é importante usar o URI do recurso correto durante a criação dos tokens de segurança. Isso é especialmente importante para os serviços de token, que precisam estabelecer o escopo do token para um dispositivo específico usando o URI do recurso. Esse ponto é menos relevante para os gateways de protocolo, pois eles já estão mediando o tráfego para todos os dispositivos.

Por exemplo, um serviço de token usando a política de acesso compartilhado criada anteriormente chamada **dispositivo** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma das chaves da política `device`,
* nome da política: `device`,
* qualquer data de validade

Um exemplo de uso da função de Nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

O resultado, que concede acesso a todas as funcionalidades para o dispositivo1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Um gateway de protocolo poderia usar o mesmo token para todos os dispositivos, simplesmente definindo o URI do recurso como `myhub.azure-devices.net/devices`.

## Como usar tokens de segurança a partir de componentes de serviço

Os componentes de serviço só podem gerar tokens de segurança usando políticas de acesso compartilhado que concedem as permissões apropriadas, conforme explicado na [seção de segurança do guia do desenvolvedor do Hub IoT][lnk-devguide-security].

Essas são as funções de serviço expostas nos pontos de extremidade:

| Ponto de extremidade | Funcionalidade |
| ----- | ----------- |
| `{iot hub host name}/devices` | Criar, atualizar, recuperar e excluir as identidades do dispositivo. |
| `{iot hub host name}/messages/events` | Receber mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/servicebound/feedback` | Receber comentários das mensagens da nuvem para o dispositivo. |
| `{iot hub host name}/devicebound` | Enviar mensagens da nuvem para o dispositivo. |

Por exemplo, um serviço que usa a política de acesso compartilhado criada anteriormente chamada **registryRead** criaria um token com os seguintes parâmetros:

* URI do recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma das chaves da política `registryRead`,
* nome da política: `registryRead`,
* qualquer data de validade

    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

O resultado, que concederia acesso de leitura em todas as identidades de dispositivo, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead


[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth
[lnk-devguide-security]: iot-hub-devguide.md#security
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

<!---HONumber=AcomDC_0316_2016-->