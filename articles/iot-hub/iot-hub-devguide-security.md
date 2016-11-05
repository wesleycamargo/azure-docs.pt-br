---
title: Guia do desenvolvedor – controle de acesso ao Hub IoT | Microsoft Docs
description: Guia do desenvolvedor de Hub IoT do Azure – como controlar o acesso ao Hub IoT e gerenciar a segurança
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett

---
# <a name="control-access-to-iot-hub"></a>Controlar o acesso ao Hub IoT
## <a name="overview"></a>Visão geral
Esta seção descreve as opções para proteger o Hub IoT. O Hub IoT usa *permissões* para conceder acesso aos pontos de extremidade de cada Hub IoT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

Este artigo descreve:

* As permissões diferentes que você pode conceder a um aplicativo de dispositivo ou back-end para acessar o hub IoT.
* O processo de autenticação e os tokens que ele usa para verificar permissões.
* Como analisar credenciais para limitar o acesso a recursos específicos.
* Suporte de Hub IoT para certificados X.509.
* Mecanismos de autenticação de dispositivo personalizados que usam registros de identidade de dispositivo existente ou esquemas de autenticação.

### <a name="when-to-use"></a>Quando usar
Você deve ter permissões adequadas para acessar qualquer um dos pontos de extremidade de Hub IoT. Por exemplo, um dispositivo deve incluir um token contendo as credenciais de segurança juntamente com todas as mensagens que ele envia para o Hub IoT.

## <a name="access-control-and-permissions"></a>Controle e permissões de acesso
Você pode conceder [permissões](#iot-hub-permissions) das seguintes maneiras:

* **Políticas de acesso compartilhado no nível do Hub**. As políticas de acesso compartilhado podem conceder qualquer combinação de [permissões](#iot-hub-permissions). Você pode definir políticas no [portal do Azure][lnk-management-portal], ou programaticamente usando [API do provedor de recursos de Hub Iot do Azure][lnk-resource-provider-apis]. Um hub IoT recém-criado tem as seguintes políticas padrão:
  
  * **iothubowner**: política com todas as permissões.
  * **service**: política com a permissão ServiceConnect.
  * **device**: política com a permissão DeviceConnect.
  * **registryRead**: política com a permissão RegistryRead.
  * **registryReadWrite**: política com as permissões RegistryRead e RegistryWrite.
* **Credenciais de segurança de acordo com o dispositivo**. Cada Hub IoT contém um [registro de identidade do dispositivo][lnk-identity-registry]. É possível configurar as credenciais de segurança para cada dispositivo nesse registro concedendo permissões de **DeviceConnect** com escopo nos pontos de extremidade correspondentes do dispositivo.

Por exemplo, em uma solução de IoT típica:

* O componente de gerenciamento de dispositivo usa a política *registryReadWrite* .
* O componente de processador de eventos usa a política *service* .
* O componente de lógica de negócios do dispositivo em tempo de execução usa a política *service* .
* Os dispositivos individuais se conectam usando as credenciais armazenadas no registro de identidade do Hub IoT.

## <a name="authentication"></a>Autenticação
O Hub IoT do Azure concede acesso aos pontos de extremidade, verificando um token com base nas políticas de acesso compartilhado e nas credenciais de segurança de registro de identidade do dispositivo.

As credenciais de segurança, como as chaves simétricas, nunca são enviadas pela conexão.

> [!NOTE]
> O provedor de recursos de Hub IoT do Azure é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Azure Resource Manager][lnk-azure-resource-manager].
> 
> 

Para saber mais sobre como construir e usar os tokens de segurança, veja o artigo [Tokens de segurança do Hub IoT][lnk-sas-tokens].

### <a name="protocol-specifics"></a>Especificações de protocolo
Cada protocolo com suporte, como MQTT, AMQP e HTTP, transporta os tokens de maneiras diferentes.

Ao usar MQTT, o pacote CONNECT tem a deviceId como a ClientId, {iothubhostname}/{deviceId} no campo Nome de Usuário e um token SAS no campo Senha. {iothubhostname} deve ser o CName completo do Hub IoT (por exemplo, contoso.azure-devices.net).

Ao usar [AMQP][lnk-amqp], o Hub IoT dá suporte a [SASL PLAIN][lnk-sasl-plain] e [Segurança baseada em declarações AMQP][lnk-cbs].

Se você usar a segurança baseada em declarações AMQP, o padrão especificará como transmitir esses tokens.

Para SASL PLAIN, o **nome de usuário** pode ser:

* `{policyName}@sas.root.{iothubName}` se forem usados tokens de nível do hub.
* `{deviceId}@sas.{iothubname}` se forem usados tokens no escopo do dispositivo.

Em ambos os casos, o campo de senha contém o token, conforme descrito no artigo [Tokens de segurança do Hub IoT][lnk-sas-tokens].

O HTTP implementa a autenticação incluindo um token válido no cabeçalho da solicitação **Authorization** .

#### <a name="example"></a>Exemplo
Nome de usuário (a DeviceId diferencia maiúsculas de minúsculas): `iothubname.azure-devices.net/DeviceId`

Senha (gerar SAS com o Gerenciador de Dispositivo): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [!NOTE]
> Os [SDKs de Hub IoT do Azure][lnk-sdks] geram tokens automaticamente durante a conexão com o serviço. Em alguns casos, os SDKs não dão suporte a todos os protocolos ou a todos os métodos de autenticação.
> 
> 

### <a name="special-considerations-for-sasl-plain"></a>Considerações especiais para SASL PLAIN
Ao usar o SASL PLAIN com AMQP, um cliente que está se conectando a um hub IoT pode usar um único token para cada conexão TCP. Quando o token expirar, a conexão TCP será desconectada do serviço, disparando uma reconexão. Esse comportamento, embora não seja problemático para um componente de back-end de aplicativo, é muito prejudicial para um aplicativo do lado do dispositivo, por estes motivos:

* Gateways normalmente se conectam em nome de vários dispositivos. Ao usar SASL PLAIN, eles precisam criar uma conexão TCP distinta para cada dispositivo que se conecta a um hub IoT. Esse cenário aumenta de forma considerável o consumo de energia e de recursos de rede, além de aumentar a latência de cada conexão de dispositivo.
* Os dispositivos com limitações de recursos são afetados de forma adversa pelo aumento do uso de recursos para se reconectar após cada expiração do token.

## <a name="scope-hub-level-credentials"></a>Escopo das credenciais no nível do hub
Você pode definir o escopo das políticas de segurança no nível do hub por meio da criação de tokens com um URI de recursos restrito. Por exemplo, o ponto de extremidade para enviar mensagens do dispositivo para a nuvem é **/devices/{deviceId}/messages/events**. Você também pode usar uma política de acesso compartilhado no nível do hub com permissões **DeviceConnect** para assinar um token cujo resourceURI é **/devices/{deviceId}**. Essa abordagem cria um token que pode ser usado somente para enviar mensagens em nome do dispositivo **deviceId**.

Esse mecanismo é semelhante à [Política de editor dos hubs de eventos][lnk-event-hubs-publisher-policy] e permite implementar métodos de autenticação personalizados.

## <a name="security-tokens"></a>Tokens de segurança
O Hub IoT usa tokens de segurança para autenticar dispositivos e serviços a fim de evitar o envio de chaves durante a transmissão. Além disso, os tokens de segurança têm limite de escopo e de prazo de validade. [Os SDKs de Hub IoT do Azure][lnk-sdks] geram tokens automaticamente sem precisar de configuração especial. No entanto, alguns cenários exigem que o usuário gere e use tokens de segurança diretamente. Nesse cenário estão o uso direto de superfícies MQTT, AMQP ou HTTP, ou a implementação do padrão de serviço de token, conforme explicado em [Autenticação de dispositivo personalizado][lnk-custom-auth].

O Hub IoT também permite que os dispositivos se autentiquem no Hub IoT usando [certificados X.509][lnk-x509]. 

### <a name="security-token-structure"></a>Estrutura do token de segurança
Você usa tokens de segurança para conceder aos dispositivos e serviços acesso de tempo limitado à funcionalidade específica do Hub IoT. Para garantir que somente os dispositivos e serviços autorizados possam se conectar, os tokens de segurança devem ser assinados com uma chave de política de acesso compartilhada ou uma chave simétrica armazenada com uma identidade de dispositivo no registro de identidade.

Um token assinado com uma chave de política de acesso compartilhada concede acesso a todas funcionalidades associadas às permissões da política de acesso compartilhado. Por outro lado, um token assinado com uma chave simétrica de uma identidade de dispositivo apenas concede a permissão **DeviceConnect** para a identidade do dispositivo associado.

O token de segurança tem o seguinte formato:

    SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estes são os valores esperados:

| Valor | Descrição |
| --- | --- |
| {signature} |Uma cadeia de caracteres de assinatura HMAC-SHA256 no formato: `{URL-encoded-resourceURI} + "\n" + expiry`. **Importante**: a chave é decodificada da base64 e usada como chave para executar o cálculo de HMAC-SHA256. |
| {resourceURI} |Prefixo URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token, começando com o nome de host do Hub IoT (sem protocolo). Por exemplo, `myHub.azure-devices.net/devices/device1` |
| {expiry} |As cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970. |
| {URL-encoded-resourceURI} |Codificação de URL em letras minúsculas do URI de recurso em letras minúsculas |
| {policyName} |O nome da política de acesso compartilhado a qual se refere esse token. Estará ausente em tokens que fazem referência às credenciais de registro do dispositivo. |

**Observação sobre o prefixo**: o prefixo URI é computado por segmento e não por caractere. Por exemplo, `/a/b` é um prefixo para `/a/b/c`, mas não para `/a/bc`.

Essa é uma função Node.js que calcula o token por meio das entradas `resourceUri, signingKey, policyName, expiresInMins`. As seções a seguir detalharão como inicializar as entradas diferentes para casos de uso com token diferentes.

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
        + base64UriEncoded + "&se=" + expires;
        if (policyName) token += "&skn="+policyName;
        // console.log("signature:" + token);
        return token;
    };

 Para fins de comparação, o código Python equivalente é:

    from base64 import b64encode, b64decode
    from hashlib import sha256
    from hmac import HMAC
    from urllib import urlencode

    def generate_sas_token(uri, key, policy_name='device', expiry=3600):
        ttl = time() + expiry
        sign_key = "%s\n%d" % (uri, int(ttl))
        signature = b64encode(HMAC(b64decode(key), sign_key, sha256).digest())

        return 'SharedAccessSignature ' + urlencode({
            'sr' :  uri,
            'sig': signature,
            'se' : str(int(ttl)),
            'skn': policy_name
        })

> [!NOTE]
> Como o prazo de validade do token é validado em máquinas do Hub IoT, é importante que o descompasso no relógio do computador que gera o token seja mínimo.
> 
> 

### <a name="use-sas-tokens-in-a-device-client"></a>Usar tokens SAS em um cliente de dispositivo
Há duas maneiras de obter permissões **DeviceConnect** com o Hub IoT com tokens de segurança: use a [chave de dispositivo simétrica do registro de identidade de dispositivos](#use-a-symmetric-key-in-the-identity-registry), ou use uma [chave de política de acesso compartilhado](#use-a-shared-access-policy).

Lembre-se que toda funcionalidade acessível por meio de dispositivos fica exposta por padrão em pontos de extremidade com o prefixo `/devices/{deviceId}`.

> [!IMPORTANT]
> A única maneira de o Hub IoT autenticar um dispositivo específico é usando a chave simétrica de identidade do dispositivo. Em casos nos quais uma política de acesso compartilhado é usada para acessar a funcionalidade do dispositivo, a solução deve considerar o componente emissor do token de segurança como um subcomponente confiável.
> 
> 

Os pontos de extremidade voltados para o dispositivo são (independentemente do protocolo):

| Ponto de extremidade | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices/{deviceId}/messages/events` |Enviar mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/devices/{deviceId}/devicebound` |Receber mensagens da nuvem para o dispositivo. |

### <a name="use-a-symmetric-key-in-the-identity-registry"></a>Usar uma chave simétrica no registro de identidade
Ao usar a chave simétrica da identidade de um dispositivo para gerar um token, o elemento policyName (`skn`) do token é omitido.

Por exemplo, um token criado para acessar todas as funcionalidades do dispositivo deve ter os seguintes parâmetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: qualquer chave simétrica para a identidade `{device id}` ,
* sem nome de política,
* qualquer data de validade

Um exemplo de uso da função de Nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var deviceKey ="...";

    var token = generateSasToken(endpoint, deviceKey, null, 60);

O resultado, que concede acesso a todas as funcionalidades para o dispositivo1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697

> [!NOTE]
> É possível gerar um token seguro usando a ferramenta [Gerenciador de Dispositivos][lnk-device-explorer] do .NET.
> 
> 

### <a name="use-a-shared-access-policy"></a>Usar uma política de acesso compartilhado
Ao criar um token de uma política de acesso compartilhado, o campo de nome da política `skn` deve ser definido como o nome da política usada. Também é necessário que a política conceda a permissão **DeviceConnect** .

Os dois cenários principais para usar as políticas de acesso compartilhado para acessar a funcionalidade do dispositivo são:

* [gateways de protocolo de nuvem][lnk-endpoints],
* [serviços de token][lnk-custom-auth] usados para implementar esquemas de autenticação personalizada.

Como a política de acesso compartilhado pode conceder acesso de conexão como qualquer dispositivo, é importante usar o URI do recurso correto durante a criação dos tokens de segurança. Isso é especialmente importante para os serviços de token, que precisam estabelecer o escopo do token para um dispositivo específico usando o URI do recurso. Esse ponto é menos relevante para os gateways de protocolo, pois eles já estão mediando o tráfego para todos os dispositivos.

Por exemplo, um serviço de token usando a política de acesso compartilhado criada anteriormente chamada **dispositivo** criaria um token com os seguintes parâmetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices/{device id}`,
* chave de assinatura: uma das chaves da política `device` ,
* nome da política: `device`,
* qualquer data de validade

Um exemplo de uso da função de Nó acima seria:

    var endpoint ="myhub.azure-devices.net/devices/device1";
    var policyName = 'device';
    var policyKey = '...';

    var token = generateSasToken(endpoint, policyKey, policyName, 60);

O resultado, que concede acesso a todas as funcionalidades para o dispositivo1, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices%2fdevice1&sig=13y8ejUk2z7PLmvtwR5RqlGBOVwiq7rQR3WZ5xZX3N4%3D&se=1456971697&skn=device

Um gateway de protocolo poderia usar o mesmo token para todos os dispositivos, simplesmente definindo o URI do recurso para `myhub.azure-devices.net/devices`.

### <a name="use-security-tokens-from-service-components"></a>Usar tokens de segurança de componentes de serviço
Os componentes de serviço só podem gerar tokens de segurança usando políticas de acesso compartilhado que concedem as permissões apropriadas, conforme explicado anteriormente.

Essas são as funções de serviço expostas nos pontos de extremidade:

| Ponto de extremidade | Funcionalidade |
| --- | --- |
| `{iot hub host name}/devices` |Criar, atualizar, recuperar e excluir as identidades do dispositivo. |
| `{iot hub host name}/messages/events` |Receber mensagens do dispositivo para a nuvem. |
| `{iot hub host name}/servicebound/feedback` |Receber comentários das mensagens da nuvem para o dispositivo. |
| `{iot hub host name}/devicebound` |Enviar mensagens da nuvem para o dispositivo. |

Por exemplo, um serviço que usa a política de acesso compartilhado criada anteriormente chamada **registryRead** criaria um token com os seguintes parâmetros:

* URI de recurso: `{IoT hub name}.azure-devices.net/devices`,
* chave de assinatura: uma das chaves da política `registryRead` ,
* nome da política: `registryRead`,
* qualquer data de validade
  
    var endpoint ="myhub.azure-devices.net/devices"; var policyName = 'device'; var policyKey = '...';
  
    var token = generateSasToken(endpoint, policyKey, policyName, 60);

O resultado, que concederia acesso de leitura em todas as identidades de dispositivo, seria:

    SharedAccessSignature sr=myhub.azure-devices.net%2fdevices&sig=JdyscqTpXdEJs49elIUCcohw2DlFDR3zfH5KqGJo4r4%3D&se=1456973447&skn=registryRead

## <a name="supported-x.509-certificates"></a>Certificados X.509 com suporte
Você pode usar qualquer certificado X.509 para autenticar um dispositivo com o Hub IoT. Isso inclui:

* **Um certificado X.509 existente**. Talvez um dispositivo já tenha um certificado X.509 associado a ele. O dispositivo pode usar este certificado para se autenticar no Hub IoT.
* **Um certificado X-509 gerado automaticamente e autoassinado**. Um fabricante de dispositivos ou um implantador interno pode gerar esses certificados e armazenar a chave privada correspondente (e o certificado) no dispositivo. Você pode usar ferramentas como o [OpenSSL][lnk-openssl] e o utilitário [Windows SelfSignedCertificate][lnk-selfsigned] para essa finalidade.
* **Certificado X.509 assinado por autoridade de certificação**. Você também pode usar um certificado X.509 gerado e assinado por uma Autoridade de Certificação (CA) para identificar um dispositivo e autenticar um dispositivo no Hub IoT.

Um dispositivo pode usar um certificado X.509 ou um token de segurança para autenticação, mas não ambos.

### <a name="register-an-x.509-client-certificate-for-a-device"></a>Registrar um certificado de cliente X.509 para um dispositivo
O [SDK de serviço IoT do Azure para C#][lnk-service-sdk] (versão 1.0.8+) dá suporte ao registro de um dispositivo que usa um certificado de cliente X.509 para autenticação. Outras APIs, como a importação/exportação de dispositivos também oferece suporte a certificados de cliente X.509.

### <a name="c\#-support"></a>Suporte a C\
A classe **RegistryManager** fornece uma maneira programática de registrar um dispositivo. Em particular, os métodos **AddDeviceAsync** e **UpdateDeviceAsync** permitem que um usuário registre e atualize um dispositivo no registro de identidade de dispositivo do Hub Iot. Esses dois métodos têm uma instância **Dispositivo** como entrada. A classe **Dispositivo** inclui uma propriedade **Autenticação** que permite ao usuário especificar as impressões digitais primárias e secundárias do certificado X.509. A impressão digital representa um hash SHA-1 do certificado X.509 (armazenado usando a codificação binária DER). Os usuários têm a opção de especificar uma impressão digital primária ou uma impressão digital secundária ou ambas. As impressões digitais primárias e secundárias têm suporte para lidar com cenários de substituição do certificado.

> [!NOTE]
> O Hub IoT Hub não exige ou armazena o certificado de cliente X.509 inteiro, somente a impressão digital.
> 
> 

Veja um exemplo de trecho de código em C\# para registrar um dispositivo usando um certificado de cliente X.509:

```
var device = new Device(deviceId)
{
  Authentication = new AuthenticationMechanism()
  {
    X509Thumbprint = new X509Thumbprint()
    {
      PrimaryThumbprint = "921BC9694ADEB8929D4F7FE4B9A3A6DE58B0790B"
    }
  }
};
RegistryManager registryManager = RegistryManager.CreateFromConnectionString(deviceGatewayConnectionString);
await registryManager.AddDeviceAsync(device);
```

### <a name="use-an-x.509-client-certificate-during-runtime-operations"></a>Usar um certificado de cliente X.509 durante operações em tempo de execução
O [SDK do dispositivo IoT do Azure para .NET][lnk-client-sdk] (versão 1.0.11 ou superior) dá suporte ao uso de certificados de cliente X.509.

### <a name="c\#-support"></a>Suporte a C\
A classe **DeviceAuthenticationWithX509Certificate** dá suporte à criação de instâncias  **DeviceClient** usando um certificado de cliente X.509.

Veja um trecho de código de exemplo:

```
var authMethod = new DeviceAuthenticationWithX509Certificate("<device id>", x509Certificate);

var deviceClient = DeviceClient.Create("<IotHub DNS HostName>", authMethod);
```

## <a name="custom-device-authentication"></a>Autenticação personalizada de dispositivo
Você pode usar o [registro de identidade de dispositivo][lnk-identity-registry] do Hub IoT para configurar credenciais de segurança e controle de acesso por dispositivo usando [tokens][lnk-sas-tokens]. No entanto, se uma solução IoT já tiver um investimento considerável em um registro de identidade de dispositivo personalizado e/ou em um esquema de autenticação, você poderá integrar essa infraestrutura existente ao Hub IoT por meio da criação de um *serviço de token*. Dessa forma, você pode usar outros recursos de IoT em sua solução.

Um serviço de token é um serviço de nuvem personalizado. Ele usa uma *política de acesso compartilhado* do Hub IoT com permissões **DeviceConnect** para criar tokens *device-scoped*. Esses tokens permitem que um dispositivo se conecte ao seu Hub IoT.

  ![Etapas do padrão do serviço de token][img-tokenservice]

Estas são as principais etapas do padrão de serviço do token:

1. Crie uma política de acesso compartilhado do Hub IoT com permissões **DeviceConnect** para seu Hub IoT. Você pode criar essa política no [portal do Azure][lnk-management-portal] ou de forma programática. O serviço de token usa essa política para assinar os tokens criados.
2. Quando um dispositivo precisar acessar o Hub IoT, ele solicitará um token assinado ao serviço de token. O dispositivo pode autenticar com o seu esquema personalizado de registro/autenticação de identidade de dispositivos para determinar a identidade do dispositivo usada pelo serviço de token para criar o token.
3. O serviço de token retorna um token. O token é criado usando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está sendo autenticado. O serviço de token usa a política de acesso compartilhado para construir o token.
4. O dispositivo usa o token diretamente com o Hub IoT.

> [!NOTE]
> Você pode usar a classe do .NET [SharedAccessSignatureBuilder][lnk-dotnet-sas] ou a classe Java [IotHubServiceSasToken][lnk-java-sas] para criar um token no serviço de token.
> 
> 

O serviço de token pode definir a expiração do token como desejado. Quando o token expira, o Hub IoT rompe a conexão do dispositivo. Em seguida, o dispositivo deve solicitar um novo token ao serviço de token. Se você usar um tempo de expiração curto, aumentará a carga no dispositivo e no serviço de token.

Para que um dispositivo se conecte ao seu hub, você ainda deve adicioná-lo ao registro de identidades do dispositivo do Hub IoT, mesmo se o dispositivo estiver usando um token e não uma chave de dispositivo para se conectar. Portanto, você pode continuar a usar o controle de acesso por dispositivo habilitando ou desabilitando as identidades de dispositivo no [registro de identidade do Hub IoT][lnk-identity-registry] quando o dispositivo é autenticado com um token. Isso reduz os riscos de usar tokens com tempos de expiração longos.

### <a name="comparison-with-a-custom-gateway"></a>Comparação com um gateway personalizado
O padrão de serviço do token é a maneira recomendada de implementar um esquema personalizado de registro/autenticação de identidade com o Hub IoT. É recomendável porque o Hub IoT continua tratando a maior parte do tráfego da solução. No entanto, há casos nos quais o esquema de autenticação personalizado está tão entremeado com o protocolo que é necessário ter um serviço que processe todo o tráfego (*gateway personalizado*). Um exemplo disso é o [TLS (Transport Layer Security) e as PSKs (chaves pré-compartilhadas)][lnk-tls-psk]. Para saber mais, confira o tópico [gateway de protocolo][lnk-protocols].

## <a name="reference"></a>Referência
### <a name="iot-hub-permissions"></a>Permissões de Hub IoT
A tabela a seguir lista as permissões que você pode usar para controlar o acesso ao seu hub IoT.

| Permissão | Observações |
| --- | --- |
| **RegistryRead** |Concede acesso de leitura ao registro de identidade do dispositivo. Para saber mais, confira [Registro de identidade do dispositivo][lnk-identity-registry]. |
| **RegistryReadWrite** |Concede acesso de leitura e gravação ao registro de identidade do dispositivo. Para saber mais, confira [Registro de identidade do dispositivo][lnk-identity-registry]. |
| **ServiceConnect** |Concede acesso aos pontos de extremidade de comunicação e de monitoramento voltados para o serviço de nuvem. Por exemplo, ela concede permissão para que os serviços de nuvem de back-end recebam mensagens do dispositivo para a nuvem, enviem mensagens da nuvem para o dispositivo e obtenham as confirmações de entrega correspondentes. |
| **DeviceConnect** |Concede acesso aos pontos de extremidade de comunicação voltados para o dispositivo. Por exemplo, ela concede permissão de envio de mensagens do dispositivo para a nuvem e de recebimento de mensagens da nuvem para o dispositivo. Essa permissão é usada por dispositivos. |

### <a name="additional-reference-material"></a>Material de referência adicional
Outros tópicos de referência no Guia do desenvolvedor incluem:

* [Pontos de extremidade do Hub IoT][lnk-endpoints] descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
* [Limitação e cotas][lnk-quotas] descreve as cotas que se aplicam ao serviço Hub IoT e o comportamento de limitação esperado ao usar o serviço.
* [SDKs de dispositivo e serviço do Hub IoT][lnk-sdks] lista os vários SDKs de linguagem que você pode usar no desenvolvimento de aplicativos de dispositivo e serviço que interagem com o Hub IoT.
* [Linguagem de consulta para gêmeos, métodos e trabalhos][lnk-query] descreve a linguagem de consulta que você pode usar para recuperar informações do Hub IoT sobre gêmeos de dispositivo, métodos e trabalhos.
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt] fornece mais informações sobre o suporte do Hub IoT para o protocolo MQTT.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu como controlar o acesso ao Hub IoT, pode estar interessado nos seguintes tópicos do Guia do desenvolvedor:

* [Usar dispositivos gêmeos para sincronizar o estado e as configurações][lnk-devguide-device-twins]
* [Invocar um método direto em um dispositivo][lnk-devguide-directmethods]
* [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs]

Se você quiser experimentar alguns dos conceitos descritos neste artigo, talvez se interesse pelos seguintes tutoriais de Hub IoT:

* [Introdução ao Hub IoT do Azure][lnk-getstarted-tutorial]
* [Como enviar mensagens de nuvem para o dispositivo com o Hub IoT][lnk-c2d-tutorial]
* [Como processar mensagens do dispositivo para a nuvem do Hub IoT][lnk-d2c-tutorial]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-openssl]: https://www.openssl.org/
[lnk-selfsigned]: https://technet.microsoft.com/library/hh848633

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-devguide-security.md#security-tokens
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-custom-auth]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-x509]: iot-hub-devguide-security.md#supported-x509-certificates
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-service-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/service
[lnk-client-sdk]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md

[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md



<!--HONumber=Oct16_HO2-->


