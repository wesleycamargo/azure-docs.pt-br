<properties
 pageTitle="Tópicos do guia do desenvolvedor para Hub IoT | Microsoft Azure"
 description="Guia do desenvolvedor do Hub IoT do Azure que inclui pontos de extremidade do Hub IoT, segurança, Registro de identidade de dispositivo e mensagens"
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Guia do desenvolvedor do Hub IoT do Azure

O Hub IoT do Azure é um serviço totalmente gerenciado que permite comunicações bidirecionais confiáveis e seguras entre milhões de dispositivos IoT e um back-end de aplicativo.

Com o Hub IoT do Azure, é possível:

* Proteger as comunicações usando credenciais de segurança e controle de acesso por dispositivo
* Enviar, de forma confiável, mensagens em larga escala do dispositivo para a nuvem e da nuvem para o dispositivo e
* Facilitar a conectividade do dispositivo com as bibliotecas de dispositivo a fim de obter as plataformas e os idiomas mais populares.

Estas são as áreas abordadas neste documento:

- [Pontos de extremidade](#endpoints). Esta seção descreve os vários pontos de extremidade que cada Hub IoT expõe para operações de tempo de execução e de gerenciamento.
- [Registro de identidade do dispositivo](#device-identity-registry) Esta seção descreve quais informações são armazenadas no registro de identidade de dispositivo de cada Hub IoT, e como elas podem ser acessadas e modificadas.
- [Segurança](#security). Esta seção descreve o modelo de segurança para dispositivos e componentes da nuvem usados para conceder acesso à funcionalidade do Hub IoT.
- [Mensagens](#messaging). Esta seção descreve os recursos de sistema de mensagens (do dispositivo para a nuvem e da nuvem para o dispositivo) expostos pelo Hub IoT.
- [Cotas e limitação](#throttling). Esta seção resume as cotas que se aplicam ao uso do Hub IoT.

## Pontos de extremidade <a id="endpoints"></a>

O Hub IoT do Azure é um serviço multilocatário que expõe suas funcionalidades a diversos atores. O diagrama a seguir mostra os vários pontos de extremidade expostos pelo Hub IoT.

![Pontos de extremidade do Hub IoT][img-endpoints]

A seguir, uma descrição dos pontos de extremidade:

* **Provedor de recursos** - o provedor de recursos do Hub IoT expõe uma interface do [Gerenciador de Recursos do Azure][lnk-arm], que permite aos proprietários de assinatura do Azure criar Hubs IoT, atualizar propriedades do Hub IoT e excluir Hubs IoT. As propriedades do Hub IoT determinam as políticas de segurança no nível do hub (em vez do controle de acesso no nível do dispositivo. Consulte [Controle de acesso ](#accesscontrol)) e as opções funcionais para o sistema de mensagens da nuvem para o dispositivo e do dispositivo para a nuvem. O provedor de recursos também permite [exportar identidades de dispositivo](#importexport).
* **Gerenciamento de identidade de dispositivos**: cada Hub IoT expõe um conjunto de pontos de extremidade HTTP REST para gerenciamento de identidades de dispositivo (criar, recuperar, atualizar e excluir). As identidades de dispositivo são usadas para controle de acesso e autenticação do dispositivo. Consulte [Registro de identidade do dispositivo](#device-identity-registry) para saber mais.
* **Pontos de extremidade do dispositivo** - para cada dispositivo provisionado no registro de identidade de dispositivo, o Hub IoT expõe um conjunto de pontos de extremidade que serão usados para comunicação nesse dispositivo. Atualmente, esses pontos de extremidade são expostos em HTTP e [AMQP][lnk-amqp]\:
    - *Enviar mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é usado para enviar mensagens do dispositivo para a nuvem. Para saber mais, consulte [Sistema de mensagens do dispositivo para a nuvem](#d2c).
    - *Receber mensagens da nuvem para o dispositivo*. Esse ponto de extremidade é usado pelo dispositivo para receber mensagens da nuvem para o dispositivo direcionadas. Para saber mais, consulte [Sistema de mensagens da nuvem para o dispositivo](#c2d).
* **Pontos de extremidade do serviço** - cada Hub IoT também expõe um conjunto de pontos de extremidade usados pelo seu back-end de aplicativo (*serviço*) para se comunicar com seus dispositivos. Atualmente, esses pontos de extremidade são expostos apenas com o protocolo [AMQP][lnk-amqp].
    - *Receber mensagens do dispositivo para a nuvem*. Esse ponto de extremidade é compatível com os [Hubs de Eventos do Azure][lnk-event-hubs] e pode ser usado para ler todas as mensagens do dispositivo para a nuvem enviadas por seus dispositivos. Para saber mais, consulte [Sistema de mensagens do dispositivo para a nuvem](#d2c).
    - *Enviar mensagens da nuvem para o dispositivo e receber confirmações de entrega*. Esses pontos de extremidade permitem que o seu back-end de aplicativo envie mensagens confiáveis da nuvem para o dispositivo e receba confirmações de entrega ou de vencimento correspondentes. Para saber mais, consulte [Sistema de mensagens da nuvem para o dispositivo](#c2d).

O artigo [APIs e SDKs do Hub IoT][lnk-apis-sdks] descreve as várias maneiras pelas quais você pode acessar esses pontos de extremidade.

Por fim, é importante observar que todos os pontos de extremidade do Hub IoT são expostos via [TLS][lnk-tls] e que nenhum ponto de extremidade é exposto em canais sem criptografia/desprotegidos.

### Como ler de pontos de extremidade compatíveis com os Hubs de Eventos. <a id="eventhubcompatible"></a>

Ao usar o [SDK do Barramento de Serviço do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) ou os [Hubs de Eventos - Host Processador de Eventos][], é possível usar quaisquer cadeias de conexão do Hub IoT com as permissões corretas e usar `messages/events` como nome do Hub de Eventos.

Ao usar os SDKs (ou integrações de produtos) que não reconhecem o Hub IoT, será necessário recuperar um ponto de extremidade compatível com os Hubs de Eventos e o nome do Hub de Evento das configurações do Hub IoT no [portal do Azure][]\:

1. Na folha do Hub IoT, clique em **Configurações** e em **Sistema de Mensagens**,
2. Na seção **Configurações de dispositivo para nuvem**, você encontrará as caixas **Ponto de extremidade compatível com o Hub de Eventos**, **Nome compatível com o Hub de Eventos** e **Partições**.

    ![][img-eventhubcompatible]

> [AZURE.NOTE]Às vezes, o SDK requer um valor de **Nome do Host** ou de **Namespace**. Nesse caso, remova o esquema do **ponto de extremidade compatível com o Hub de Eventos**. Por exemplo, se o ponto de extremidade compatível com o Hub de Eventos for `sb://iothub-ns-myiothub-1234.servicebus.windows.net/`, o **Nome do Host** será `iothub-ns-myiothub-1234.servicebus.windows.net` e o **Namespace** será `iothub-ns-myiothub-1234`.

Dessa forma, você pode usar qualquer política de segurança de acesso compartilhado com permissões **ServiceConnect** para conectar-se ao Hub de Eventos especificado.

Caso você tenha de criar uma cadeia de conexão do Hub de Eventos com as informações acima, use o seguinte padrão:

        Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}


A seguir, uma lista de SDKs e uma integração que podem ser usados com o Hub IoT:

* [Cliente Java dos Hubs de Eventos](https://github.com/hdinsight/eventhubs-client)
* [Spout do Apache Storm](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md)

## Registro de identidade do dispositivo

Cada Hub IoT tem um registro de identidade de dispositivo usado para criar recursos de acordo com o dispositivo no serviço, por exemplo, uma fila contendo mensagens em andamento da nuvem para o dispositivo, e permite o acesso aos pontos de extremidade voltados para o dispositivo, como explicado na seção [Controle de Acesso](#accesscontrol).

Em um alto nível, o registro de identidade do dispositivo é uma coleção compatível com REST de recursos de identidade do dispositivo. As seções a seguir detalham as propriedades do recurso de identidade do dispositivo e as operações que o registro permite em identidades.

> [AZURE.NOTE]Consulte [SDKs e APIs do Hub IoT][lnk-apis-sdks] para saber mais detalhes sobre o protocolo HTTP e os SDKs disponíveis para interagir com o registro de identidade do dispositivo.

### Propriedades de identidade de dispositivo <a id="deviceproperties"></a>

As identidades de dispositivo são representadas como documentos JSON com as seguintes propriedades.

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| deviceId | obrigatória, somente leitura em atualizações | Uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII 7 bits + `{'-', ':', '.', '+', '&percnt;', '_', '&num;', '&ast;', '?', '!', '(', ')', ',', '=', '&commat;', ';', '&dollar;', '''}`. |
| generationId | obrigatória, somente leitura | Uma cadeia de caracteres que diferencia maiúsculas de minúsculas gerada pelo hub com até 128 caracteres. Isso é usado para distinguir os dispositivos com o mesmo **deviceId** quando são excluídos e recriados. |
| etag | obrigatória, somente leitura | Uma cadeia de caracteres que representa uma etag fraca para a identidade do dispositivo, de acordo com [RFC7232][lnk-rfc7232].|
| auth | opcional | Um objeto composto que contém as informações de autenticação e os materiais de segurança. |
| auth.symkey | opcional | Um objeto composto que contém as chaves primária e secundária, armazenadas no formato base64. |
| status | obrigatório | Pode estar **Habilitado** ou **Desabilitado**. Se estiver **Habilitado**, o dispositivo terá permissão para se conectar. Se estiver **Desabilitado**, este dispositivo não poderá acessar qualquer ponto de extremidade voltado para o dispositivo. |
| statusReason | opcional | Uma cadeia de caracteres com 128 caracteres que armazena o motivo do status de identidade do dispositivo. Todos os caracteres UTF-8 são permitidos. |
| statusUpdateTime | somente leitura | Data e hora da última atualização de status. |
| connectionState | somente leitura | **Conectado** ou **Desconectado**, representa a exibição Hub IoT do status de conexão do dispositivo. |
| connectionStateUpdatedTime | somente leitura | Data e hora da última atualização do estado da conexão. |
| lastActivityTime | somente leitura | Data e hora da última vez em que o dispositivo se conectou, recebeu ou enviou uma mensagem. |

> [AZURE.NOTE]O estado da conexão pode representar apenas a visão do Hub IoT do status da conexão. Esse estado pode ser atrasado dependendo das configurações e das condições da rede.

### Operações de identidade do dispositivo

O registro de identidade de dispositivo do Hub IoT expõe as seguintes operações:

* Criar identidade do dispositivo
* Atualizar identidade do dispositivo
* Recuperar a identidade do dispositivo por ID
* Excluir identidade do dispositivo
* Listar até 1000 identidades

Todas essas operações permitem o uso da simultaneidade otimista como especificado na [RFC7232][lnk-rfc7232].

> [AZURE.IMPORTANT]A única maneira de recuperar todas as identidades em um registro de identidade do hub é usar a funcionalidade [Exportar](#importexport).

O registro de identidade de dispositivo do hub não contém metadados de aplicativo, pode ser acessado como um dicionário usando o **deviceId** como a chave e não tem suporte para consultas expressivas. Qualquer solução de IoT terá um repositório específico para a solução contendo metadados específicos do aplicativo, como o espaço em que um sensor de temperatura é implantado em uma solução inteligente de construção.

### Desabilitando dispositivos

Você pode desabilitar dispositivos atualizando a propriedade **status** de uma identidade no registro. Normalmente, isso é usado em dois cenários:

1. Durante um processo de orquestração de provisionamento. Para saber mais, consulte [Diretrizes para o Hub IoT do Azure - provisionamento][lnk-guidance-provisioning].
2. Por algum motivo, você considera um dispositivo comprometido ou temporariamente não autorizado.

### Exportar identidades de dispositivo <a id="importexport"></a>

As exportações são trabalhos de execução longa que usam um contêiner de blob fornecido pelo cliente para ler e gravar dados de identidade do dispositivo.

Você pode exportar identidades de dispositivo em massa de registro de identidade de um Hub IoT, usando operações assíncronas no ponto de extremidade do Provedor de Recursos do Hub IoT ([Pontos de Extremidade](#endpoints)).

As operações a seguir podem ser executadas em trabalhos de exportação:

* Criar um trabalho de exportação
* Recuperar o status de um trabalho em execução
* Cancelar um trabalho em execução

> [AZURE.NOTE]Não importa o momento, cada hub pode ter apenas um único trabalho em execução.

Para obter informações detalhadas sobre as APIs de importação e exportação, consulte [Hub IoT do Azure - APIs do provedor de recursos][lnk-resource-provider-apis].

#### Trabalhos

Todos os trabalhos de exportação têm as propriedades a seguir:

| Propriedade | Opções | Descrição |
| -------- | ------- | ----------- |
| jobId | gerada pelo sistema, ignorada na criação | |
| creationTime | gerada pelo sistema, ignorada na criação | |
| endOfProcessingTime | gerada pelo sistema, ignorada na criação | |
| type | somente leitura | **Exportação** |
| status | gerada pelo sistema, ignorada na criação | **Em fila**, **Iniciado**, **Concluído**, **Falha** |
| progresso | gerada pelo sistema, ignorada na criação | Valor inteiro da porcentagem de conclusão. |
| outputBlobContainerURI | obrigatório para todos os trabalhos | URI da Assinatura de Acesso Compartilhado do blob com acesso de gravação a um contêiner de blob (consulte [Criar e usar uma SAS com o serviço Blob][lnk-createuse-sas] e [Criar uma Assinatura de Acesso Compartilhado em Java][lnk-sas-java]). Isso é usado para mostrar o status do trabalho e os resultados. |
| includeKeysInExport | opcional | Se for **true**, as chaves serão incluídas na saída da exportação; caso contrário, as chaves serão exportadas como **null**. O padrão é **false**. |
| failureReason | gerada pelo sistema, ignorada na criação | Se o status for **Falha**, uma cadeia de caracteres mostrará o motivo. |

#### Trabalhos de exportação

Os trabalhos de exportação obtêm um URI de Assinatura de Acesso Compartilhado de blob como um parâmetro. Isso concede acesso de gravação a um contêiner de blob e é usado para gerar o resultado da operação.

Os resultados são gravados no contêiner de blob especificado em um arquivo chamado `job_{job_id}_devices.txt`. Esse arquivo contém identidades de dispositivo serializadas como JSON, conforme especificado nas [Propriedades de identidade de dispositivo](#deviceproperties). Os materiais de segurança serão definidos como **null** caso **includeKeysInExport** seja definido como **false**.

**Exemplo**:

    {"deviceId":"devA","auth":{"symKey":{"primaryKey":"123"}},"status":"enabled"}
    {"deviceId":"devB","auth":{"symKey":{"primaryKey":"234"}},"status":"enabled"}
    {"deviceId":"devC","auth":{"symKey":{"primaryKey":"345"}},"status":"enabled"}
    {"deviceId":"devD","auth":{"symKey":{"primaryKey":"456"}},"status":"enabled"}


## Segurança <a id="security"></a>

Esta seção descreve as opções para proteger o Hub IoT do Azure.

### Controle de acesso <a id="accesscontrol"></a>

O Hub IoT usa o seguinte conjunto de *permissões* para conceder acesso ao ponto de extremidade de cada Hub IoT. As permissões limitam o acesso a um hub IoT com base na funcionalidade.

* **RegistryRead**. Essa permissão concede acesso de leitura ao registro de identidade do dispositivo. Para saber mais, consulte [Registro de identidade do dispositivo](#device-identity-registry).
* **RegistryReadWrite**. Concede acesso de leitura e gravação ao registro de identidade do dispositivo. Para saber mais, consulte [Registro de identidade do dispositivo](#device-identity-registry).
* **ServiceConnect**. Concede acesso aos pontos de extremidade de comunicação e de monitoramento voltados para o serviço de nuvem. Por exemplo, ela concede permissão para que os serviços de nuvem recebam mensagens do dispositivo para a nuvem, enviem mensagens da nuvem para o dispositivo e obtenham as confirmações de entrega correspondentes.
* **DeviceConnect**. Concede acesso aos pontos de extremidade de comunicação voltados para o dispositivo. Por exemplo, ela concede permissão de envio de mensagens do dispositivo para a nuvem e de recebimento de mensagens da nuvem para o dispositivo. Essa permissão é usada principalmente por dispositivos.

As permissões são concedidas das seguintes maneiras:

* **Políticas de acesso compartilhado no nível do Hub**. As *políticas de acesso compartilhado* podem conceder qualquer combinação das permissões listadas na seção anterior. Você pode definir políticas no [portal do Azure][lnk-management-portal] ou de forma programática usando as [APIs do provedor de recursos do Hub IoT do Azure][lnk-resource-provider-apis]. Um hub IoT recém-criado tem as seguintes políticas padrão:

    - *iothubowner*: política com todas as permissões,
    - *service*: política com a permissão **ServiceConnect**,
    - *device*: política com a permissão **DeviceConnect**,
    - *registryRead*: política com a permissão **RegistryRead**,
    - *registryReadWrite*: política com as permissões **RegistryRead** e **RegistryWrite**.

* **Credenciais de segurança de acordo com o dispositivo**. Cada Hub IoT contém um [registro de identidade do dispositivo](#device-identity-registry). É possível configurar as credenciais de segurança para cada dispositivo nesse registro concedendo permissões de **DeviceConnect** com escopo nos pontos de extremidade correspondentes do dispositivo.

**Exemplo**. Em uma solução IoT, normalmente há um componente de gerenciamento de dispositivo que usa a política *registryReadWrite*, um componente processador de eventos e um componente de lógica de negócios de dispositivo em tempo de execução que usam a política de *serviço*. Os dispositivos individuais se conectam usando as credenciais armazenadas no registro de identidade do Hub IoT.

Para obter orientação sobre os tópicos de segurança do Hub IoT, consulte a seção [Diretrizes para o Hub IoT do Azure][lnk-guidance-security].

### Autenticação

O Hub IoT do Azure concede acesso aos pontos de extremidade, verificando um token com base nas políticas de acesso compartilhado e nas credenciais de segurança de registro de identidade do dispositivo.

As credenciais de segurança, como as chaves simétricas, nunca são enviadas pela conexão.

> [AZURE.NOTE]O provedor de recursos do Hub IoT do Azure é protegido por meio de sua assinatura do Azure, assim como todos os provedores no [Gerenciador de Recursos do Azure][lnk-azure-resource-manager].

#### Formato do token de segurança <a id="tokenformat"></a>

O token de segurança tem o seguinte formato:

	SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}

Estes são os valores esperados:

| Valor | Descrição |
| ----- | ----------- |
| {signature} | Uma cadeia de caracteres de assinatura HMAC-SHA256 no formato: `{URL-encoded-resourceURI} + "\n" + expiry` |
| {resourceURI} | O prefixo URI (por segmento) dos pontos de extremidade que podem ser acessados com esse token. Por exemplo, `/events` |
| {expiry} | As cadeias de caracteres UTF8 para o número de segundos desde a época 00:00:00 UTC em 1º de janeiro de 1970. |
| {URL-encoded-resourceURI} | URI do recurso codificada em URL (minúscula) |
| {policyName} | O nome da política de acesso compartilhado a qual se refere esse token. Estará ausente em tokens que fazem referência às credenciais de registro do dispositivo. |

**Observação sobre o prefixo**: o prefixo URI é computado por segmento e não por caractere. Por exemplo, `/a/b` é um prefixo para `/a/b/c`, mas não para `/a/bc`.

#### Especificações de protocolo

Cada protocolo com suporte (por exemplo, HTTP e AMQP) transporta os tokens de maneiras diferentes.

A autenticação HTTP é implementada com a inclusão de um token válido no cabeçalho da solicitação de **Autorização**. Um parâmetro de consulta chamado **Autorização** também pode transportar o token.

Quando usa [AMQP][lnk-amqp], o Hub IoT dá suporte ao [SASL PLAIN][lnk-sasl-plain] e à [Segurança baseada em declarações AMQP][lnk-cbs].

No caso da segurança baseada em declarações AMQP, o padrão especifica como transmitir esses tokens.

Para SASL PLAIN, o **nome de usuário** pode ser:

* `{policyName}&commat;sas.root.{iothubName}` no caso de tokens no nível do hub.
* `{deviceId}` no caso de tokens com escopo do dispositivo.

Em ambos os casos, o campo de senha contém o token conforme descrito na seção [Formato do token](#tokenformat).

> [AZURE.NOTE]Os [SDKs do Hub IoT do Azure ][lnk-apis-sdks] geram tokens automaticamente durante a conexão com o serviço. Em alguns casos, os SDKs apresentam limitações com relação ao protocolo com suporte ou ao método de autenticação disponível. Para saber mais, consulte a documentação [SDKs do Hub IoT do Azure][lnk-apis-sdks].

#### SASL PLAIN comparado ao CBS

Ao usar o SASL PLAIN, um cliente que está se conectando a um hub IoT pode usar um único token para cada conexão TCP. Quando o token expirar, a conexão TCP será desconectada do serviço, disparando uma reconexão. Esse comportamento, embora não seja problemático para um componente de back-end de aplicativo, é muito prejudicial para um aplicativo do lado do dispositivo, por estes motivos:

*  Gateways normalmente se conectam em nome de vários dispositivos. Ao usar SASL PLAIN, eles precisam criar uma conexão TCP distinta para cada dispositivo que se conecta a um hub IoT. Isso aumenta de forma considerável o consumo de energia e de recursos de rede, além de aumentar a latência de cada conexão de dispositivo.
* Os dispositivos com limitações de recursos serão afetados principalmente pelo aumento do uso de recursos para se reconectar após cada expiração do token.

### Escopo das credenciais no nível do hub

Você pode definir o escopo das políticas de segurança no nível do hub por meio da criação de tokens com um URI de recursos restrito. Por exemplo, o ponto de extremidade para envio de mensagens do dispositivo para a nuvem é `/devices/{deviceId}/events`. Você também pode usar uma política de acesso compartilhado no nível do hub com permissões **DeviceConnect** para assinar um token cujo resourceURI é `/devices/{deviceId}`, criando um token que só pode ser usado para enviar os dispositivos em nome da **deviceId** do dispositivo.

Esse mecanismo é parecido com a [política de editor de Hubs de Eventos][lnk-event-hubs-publisher-policy] e permite a implementação de métodos de autenticação personalizados, conforme é explicado na seção de segurança [Diretrizes para o Hub IoT do Azure][lnk-guidance-security].

## Mensagens

O Hub IoT fornece primitivos do sistema de mensagens para a comunicação entre um back-end de aplicativo (*serviço* ou *nuvem*) e dispositivos, e vice-versa. Chamamos essas funcionalidades de [Dispositivo para a Nuvem](#d2c) e [Nuvem para o Dispositivo](#c2d).

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Isso permite a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens D2C e C2D.

O Hub IoT dá suporte a vários protocolos voltados para o dispositivo (como AMQP e HTTP/1). Para dar suporte à interoperabilidade contínua entre protocolos, o Hub IoT define um formato de mensagem comum compatível com todos os protocolos voltados para o dispositivo.

### Formato da mensagem <a id="messageformat"></a>

As mensagens do Hub IoT são formadas por:

* Um conjunto de *propriedades do sistema*. Essas propriedades são interpretadas ou definidas pelo Hub IoT. Esse conjunto é predeterminado.
* Um conjunto de *propriedades do aplicativo*. Esse é um dicionário de propriedades de cadeia de caracteres que o aplicativo pode definir e acessar sem precisar desserializar o corpo. Essas propriedades nunca são modificadas pelo Hub IoT.
* Um corpo de binário opaco.

Consulte [SDKs e APIs do Hub IoT][lnk-apis-sdks] para saber mais sobre como a mensagem é codificada em protocolos diferentes.

Esse é o conjunto de propriedades do sistema em mensagens do Hub IoT.

| Propriedade | Descrição |
| -------- | ----------- |
| MessageId | Um identificador configurável pelo usuário para a mensagem, normalmente usado para padrões de solicitação-resposta. Formato: uma cadeia de caracteres que diferencia maiúsculas de minúsculas (com até 128 caracteres) de caracteres alfanuméricos ASCII 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência | Um número (exclusivo por fila de dispositivo) atribuído pelo Hub IoT a cada mensagem C2D. |
| Para | Usado em [Nuvem para o Dispositivo](#c2d) para o destino da mensagem.|
| ExpiryTimeUtc | Data e hora de expiração da mensagem. |
| EnqueuedTime | Hora do recebimento da mensagem pelo Hub IoT. |
| CorrelationId | A propriedade de cadeia de caracteres que geralmente contém a ID da mensagem da solicitação em padrões de solicitação-resposta. |
| UserId | Usada para especificar a origem das mensagens. Quando as mensagens são geradas pelo Hub IoT, são definidas como `{iot hub name}`. |
| Ack | Usado em mensagens C2D para solicitar ao Hub IoT a geração de mensagens de comentários como resultado do consumo da mensagem pelo dispositivo. Valores possíveis: **nenhum** (padrão): nenhuma mensagem de comentários é gerada, **positivo**: ocorre o recebimento de uma mensagem de comentários, se a mensagem estiver completa, **negativo**: ocorre o recebimento de uma mensagem de comentários, se a mensagem expirar (ou se a contagem máxima de entrega tiver sido atingida) sem ser concluída pelo dispositivo, **total**: positivos e negativos. Para saber mais, consulte [Comentários sobre a mensagem](#feedback). |
| ConnectionDeviceId | Definida pelo Hub IoT nas mensagens D2C. Contém a **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId | Definida pelo Hub IoT nas mensagens D2C. Contém a **generationId** (de acordo com as [Propriedades de identidade de dispositivo](#deviceproperties)) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod | Definida pelo Hub IoT nas mensagens D2C. Informações sobre o método de autenticação usado para autenticar o dispositivo que envia a mensagem. Para saber mais, consulte [Anti-falsificação D2C](#antispoofing).|

### Escolhendo seu protocolo de comunicação <a id="amqpvshttp"></a>

O Hub IoT dá suporte para os protocolos [AMQP][lnk-amqp] e HTTP/1 para comunicações do lado do dispositivo. A seguir, uma lista de considerações sobre suas utilizações.

* **Padrão da nuvem para o dispositivo**. O HTTP/1 não tem uma maneira eficiente de implementar o envio do servidor. Assim, ao usarem HTTP/1, os dispositivos sondam o Hub IoT em busca de mensagens da nuvem para o dispositivo. Isso é muito ineficiente para o dispositivo e para o Hub IoT. As diretrizes atuais indicam que, ao usar HTTP/1, é necessário definir um intervalo de sondagem inferior a uma vez a cada 25 minutos para cada dispositivo. Por outro lado, AMQP dá suporte ao envio do servidor ao receber mensagens da nuvem para o dispositivo, e isso habilita imediatamente os envios de mensagens do Hub IoT para o dispositivo. Se a latência de entrega for uma preocupação, AMQP será a melhor opção de protocolo. Por outro lado, para dispositivos conectados raramente, o HTTP/1 funciona bem.
* **Gateways de campo**. Devido às limitações do HTTP/1 em relação ao envio do servidor, ele é inadequado para uso em [cenários de gateway de campo][lnk-azure-gateway-guidance].
* **Dispositivos com poucos recursos**. As bibliotecas HTTP/1 são consideravelmente menores do as do AMQP. Dessa forma, caso o dispositivo tenha poucos recursos (por exemplo, menos de 1 Mb de RAM), HTTP/1 poderá ser a única implementação de protocolo disponível.
* **Percurso da rede**. O padrão AMQP escuta na porta 5672. Isso pode causar problemas em redes fechadas para protocolos diferentes de HTTP.
* **Tamanho da carga**. AMQP é um protocolo binário consideravelmente mais compacto do que o HTTP/1.

Em um alto nível, recomendamos usar o AMQP sempre que possível, e usar o HTTP/1 se os recursos do dispositivo ou a configuração de rede não permitir AMQP. Além disso, ao usar HTTP/1, a frequência de sondagem deve ser definida com um valor inferior a uma vez a cada 25 minutos para cada dispositivo. Claramente, durante o desenvolvimento, é aceitável ter sondagens mais frequentes.

Como uma consideração final, é importante consultar o [Gateway de protocolo do IoT do Azure][lnk-azure-protocol-gateway], que permite a implantação de um gateway MQTT de alto desempenho que interage diretamente com o Hub IoT. O MQTT dá suporte ao envio do servidor (permitindo a entrega imediata de mensagens C2D ao dispositivo) e está disponível para dispositivos com poucos recursos. A principal desvantagem dessa abordagem é o requisito de hospedar automaticamente e gerenciar um gateway de protocolo.

### Dispositivo para a nuvem <a id="d2c"></a>

Como detalhado na seção [Pontos de extremidade](#endpoints), as mensagens do dispositivo para a nuvem são enviadas por meio de um ponto de extremidade voltado para o dispositivo (particularmente, `/devices/{deviceId}/messages/events`) e recebidas por meio de um ponto de extremidade voltado para o serviço (`/messages/events`), compatível com os [Hubs de Eventos][lnk-event-hubs]. Portanto, você pode usar a integração de Hubs de Evento Standard e os SDKs para receber mensagens.

O Hub IoT implementa o envio de mensagens do dispositivo para a nuvem de uma maneira semelhante aos [Hubs de Eventos][lnk-event-hubs], com as mensagens do dispositivo para a nuvem do Hub IoT sendo mais parecidas com os *eventos* dos Hubs de Eventos do que com as *mensagens* do [Barramento de Serviço][lnk-servicebus].

Esta implementação tem as seguintes implicações:

* Assim como nos *eventos* dos Hubs de Eventos, as mensagens do dispositivo para a nuvem são duráveis e mantidas em um hub IoT por sete dias (consulte [Opções de configuração de dispositivo para a nuvem](#d2cconfiguration)).
* As mensagens do dispositivo para a nuvem são particionadas em um conjunto fixo de partições definido no momento da criação (consulte [Opções de configuração do dispositivo para a nuvem](#d2cconfiguration)).
* Assim como nos Hubs de Eventos, os clientes que leem mensagens do dispositivo para a nuvem devem lidar com partições e pontos de verificação. Consulte [Hubs de Eventos - consumindo eventos][lnk-event-hubs-consuming-events].
* Assim como os eventos dos Hubs de Eventos, as mensagens do dispositivo para a nuvem podem ter no máximo 256 Kb e podem ser agrupadas em lotes para otimizar os envios. Os lotes podem ter no máximo 256 Kb e no máximo 500 mensagens.

No entanto, há algumas distinções importantes entre as mensagens do dispositivo para a nuvem do Hub IoT e os Hubs de Eventos:

* Como explicado na seção [Segurança](#security), o Hub IoT permite a autenticação e o controle de acesso por dispositivo,
* O Hub IoT permite que milhões de dispositivos se conectem ao mesmo tempo (consulte [Cotas e limitação](#throttling)), enquanto os Hubs de Eventos estão limitados a 5000 conexões AMQP por namespace.
* O Hub IoT não permite o particionamento arbitrário usando uma **PartitionKey**. Mensagens do dispositivo para a nuvem são particionadas com relação à **deviceId** de origem.
* O dimensionamento do Hub IoT é um pouco diferente dos Hubs de Eventos. Para saber mais, consulte [Dimensionando o Hub IoT][lnk-guidance-scale].

Observe que isso não significa que o Hub IoT substitui os Hubs de Eventos em todos os cenários. Por exemplo, em alguns cálculos de processamento de eventos, talvez seja necessário fazer a repartição de eventos com relação a um campo ou propriedade diferente antes de analisar os fluxos de dados. Nesse caso, um Hub de Eventos pode ser usado para separar duas partes do pipeline de processamento da transmissão.

Para obter detalhes sobre como usar as mensagens do dispositivo para a nuvem, consulte [SDKs e APIs do Hub IoT][lnk-apis-sdks].

#### Tráfego sem telemetria

Em muitos casos, os dispositivos não só enviam pontos de dados de telemetria para o back-end de aplicativo, mas também mensagens e solicitações *interativas* que exigem a execução e a manipulação da camada de lógica de negócios do aplicativo. Bons exemplos são alertas críticos que precisam disparar uma ação específica no back-end, ou respostas do dispositivo aos comandos.

Consulte a seção [Processamento do dispositivo para a nuvem][lnk-guidance-d2c-processing] de Diretrizes para o Hub IoT” para saber mais sobre a melhor maneira de processar esse tipo de mensagem.

#### Opções de configuração do dispositivo para a nuvem <a id="d2cconfiguration"></a>

Um Hub IoT expõe as seguintes propriedades para o controle de mensagens D2C.

* **Contagem de partição**. Defina essa propriedade no momento da criação e define o número de partições para inclusão do evento D2C.
* **Período de retenção**. Esta propriedade especifica o período de retenção das mensagens do dispositivo para a nuvem. O padrão é de um dia, mas pode ser aumentado até sete.

Além disso, de maneira semelhante aos Hubs de Eventos, o Hub IoT permite o gerenciamento de Grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem.

Você pode modificar todas essas propriedades usando o [portal do Azure][lnk-management-portal] ou programaticamente por meio do [Hub IoT do Azure - APIs do provedor de recursos][lnk-resource-provider-apis].

#### Propriedades anti-falsificação <a id="antispoofing"></a>

Para evitar a falsificação em mensagens do dispositivo para a nuvem, o Hub IoT carimba todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm a **deviceId** e a **generationId** do dispositivo de origem, conforme as [Propriedades de identidade do dispositivo](#deviceproperties).

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado com as seguintes propriedades:

    {
        "scope": "{ hub | device}",
        "type": "{ symkey | sas}",
        "issuer": "iothub"
    }

### Nuvem para o dispositivo <a id="c2d"></a>

Conforme já foi detalhado na seção [Pontos de extremidade](#endpoints), as mensagens do dispositivo para a nuvem são enviadas por meio de um ponto de extremidade voltado para o serviço (`/messages/devicebound`) e recebidas por meio de uma séria de pontos de extremidade voltados para o dispositivo (`/devices/{deviceId}/messages/devicebound`).

Cada mensagem da nuvem para o dispositivo está direcionada a um único dispositivo, definindo a propriedade **para** como `/devices/{deviceId}/messages/devicebound`.

**Importante**: cada fila de dispositivo pode conter no máximo 50 mensagens da nuvem para o dispositivo. Tentar enviar mais mensagens ao mesmo dispositivo resultará em um erro.

#### Ciclo de vida da mensagem <a id="message lifecycle"></a>

Para implementar *pelo menos uma vez* a garantia de entrega, as mensagens da nuvem para o dispositivo são mantidas em filas por dispositivo e os dispositivos devem reconhecer explicitamente a *conclusão* para que o Hub IoT os remova da fila. Isso garante a resiliência contra falhas de conectividade e do dispositivo.

A figura a seguir mostra o gráfico de estado do ciclo de vida de uma mensagem da nuvem para o dispositivo.

![Ciclo de vida de mensagens da nuvem para o dispositivo][img-lifecycle]

Quando uma mensagem é enviada pelo serviço, ela é considerada *Em fila*. Quando um dispositivo deseja *receber* uma mensagem, o Hub IoT *bloqueia* a mensagem (estado definido como **Invisível**), a fim de permitir que outros threads no mesmo dispositivo comecem a receber outras mensagens. Quando um thread de dispositivo tiver concluído o processamento de um dispositivo, ele notificará o Hub IoT *concluindo* a mensagem. Ele também pode optar por *rejeitar* a mensagem, o que a envia ao estado **Morta**, ou *abandonar*, que coloca a mensagem de volta na fila (estado **Em fila**).

Como um thread pode falhar ao processar uma mensagem sem notificar o Hub IoT, as mensagens passam automaticamente de **Invisível** para **Em fila** após um *tempo limite de visibilidade (ou bloqueio)* (padrão: 1 minuto). Uma mensagem pode transitar entre os estados **Em fila** e **Invisível** por um período máximo especificado na propriedade *contagem máxima de entrega* no Hub IoT. Após esse número de transições, a mensagem será automaticamente considerada morta. Da mesma forma, uma mensagem será automaticamente considerada morta após seu tempo de expiração (consulte [Tempo de vida](#ttl)).

Para obter um tutorial sobre mensagens da nuvem para o dispositivo, consulte [Introdução às mensagens da nuvem para o dispositivo do Hub IoT do Azure][lnk-getstarted-c2d-tutorial]. Para obter tópicos de referência sobre como as APIs e os SDKs diferentes expõem a funcionalidade de nuvem para o dispositivo, consulte [APIs e SDKs do Hub IoT][lnk-apis-sdks].

> [AZURE.NOTE]Normalmente, as mensagens da nuvem para o dispositivo devem ser concluídas sempre que a perda da mensagem não afetar a lógica do aplicativo. Isso pode acontecer em vários cenários diferentes, por exemplo, o conteúdo da mensagem foi mantido com êxito no armazenamento local, ou uma operação foi executada com êxito, ou a mensagem está carregando informações temporárias cuja perda não afeta a funcionalidade do aplicativo. Às vezes, para tarefas longas, é comum completar a mensagem da nuvem para o dispositivo depois de manter a descrição da tarefa no armazenamento local e, em seguida, notificar o back-end de aplicativo com uma ou mais mensagens do dispositivo para a nuvem em vários estágios do andamento da tarefa.

#### Vida útil <a id="ttl"></a>

Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Isso pode ser definido explicitamente pelo serviço (na propriedade **ExpiryTimeUtc**) ou pelo Hub IoT usando o *tempo de vida* padrão especificado como uma propriedade do Hub IoT. Consulte [Opções de configuração da nuvem para o dispositivo](#c2dconfiguration).

#### Comentários da mensagem <a id="feedback"></a>

Ao enviar uma mensagem da nuvem para o dispositivo, o serviço pode solicitar a entrega de um comentário por mensagem sobre o estado final dessa mensagem. Ao definir a propriedade **Ack** como **positivo**, o Hub IoT gerará uma mensagem de comentários se, e somente se, a mensagem da nuvem para o dispositivo atingir o estado **Concluído**. Ao definir a propriedade **Ack** como **negativa**, o Hub IoT gerará uma mensagem de comentários se, e somente se, a mensagem da nuvem para o dispositivo atingir o estado **Morta**. A definição a propriedade **Ack** como **total**, faz com que o Hub IoT gere uma mensagem de comentário em ambos os casos.

Como explicado em [Pontos de extremidade](#endpoints), os comentários são entregues por meio de um ponto de extremidade voltado para o serviço (`/messages/servicebound/feedback`) como mensagens. A semântica de recebimento dos comentários é a mesma para mensagens da nuvem para o dispositivo, com o mesmo [ciclo de vida da mensagem](nº do ciclo de vida da mensagem). Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato.

Cada mensagem recuperada do ponto de extremidade dos comentários tem as seguintes propriedades.

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTime | Carimbo de data e hora que indica quando o lote foi criado. |
| UserId | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz de registros serializada em JSON, cada um com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Carimbo de data e hora que indica quando ocorreu a saída da mensagem. Por exemplo, o dispositivo foi concluído ou a mensagem expirou. |
| OriginalMessageId | **MessageId** da mensagem da nuvem para o dispositivo a qual essas informações de comentários pertencem. |
| Descrição | Valores de cadeia de caracteres para os resultados anteriores. |
| DeviceId | **DeviceId** do dispositivo de destino da mensagem da nuvem para o dispositivo a qual pertence esses comentários. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da mensagem da nuvem para o dispositivo a qual pertence esses comentários. |

**Importante**. O serviço deve especificar um **MessageId** para a mensagem da nuvem para o dispositivo, a fim de poder correlacionar seus comentários com a mensagem original.

**Exemplo**. Este é um exemplo de corpo de uma mensagem de comentários.

    [
        {
            "OriginalMessageId": "0987654321",
            "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
            "Description": "Success",
            "DeviceId": "123",
            "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
        },
        {
            ...
        },
        ...
    ]

#### Opções de configuração da nuvem para o dispositivo <a id="c2dconfiguration"></a>

Cada hub IoT expõe as seguintes opções de configuração para mensagens da nuvem para o dispositivo.

| Propriedade | Descrição | Intervalo e padrão |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL padrão para mensagens da nuvem para o dispositivo. | Intervalo ISO\_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| maxDeliveryCount | Contagem máxima de entrega para filas de nuvem para o dispositivo por dispositivo. | 1 a 100. Padrão: 10. |
| feedback.ttlAsIso8601 | Retenção de mensagens informativas do serviço associado. | Intervalo ISO\_8601 de até 2D (mínimo de 1 minuto). Padrão: 1 hora. |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de comentários. | 1 a 100. Padrão: 100. |

## Cotas e limitação <a id="throttling"></a>

Cada assinatura do Azure pode ter no máximo 10 hubs IoT.

Cada hub IoT é provisionado com um determinado número de unidades em um SKU específico (para obter mais informações, veja [Preços do Hub IoT do Azure][lnk-pricing]). O SKU e o número de unidades determinam a cota máxima diária de envio de mensagens, e o número máximo de identidades de dispositivo no registro de identidade. O número de dispositivos conectados simultaneamente é limitado pelo número de identidades no registro.

Elas também determinam os limites aplicados pelo Hub IoT nas operações.

### Cota de registros de identidade do dispositivo

O Hub IoT permite até 1100 atualizações de dispositivo (criar, atualizar e excluir) por unidade (independentemente da SKU) por dia.

### Restrições de operação

As restrições de operação são limites de taxa aplicados em intervalos de minutos, e têm como objetivo evitar o abuso. O Hub IoT tenta evitar o retorno de erros sempre que possível, mas ele começará a retornar exceções se a restrição for violada por muito tempo.

A seguir, a lista de limitações impostas. Os valores referem-se a um hub individual.

| Restrição | Valor por hub |
| -------- | ------------- |
| Operações de registro de identidade (criar, recuperar, listar, atualizar e excluir), importação/exportação em massa ou individual | 100/min/unidade, até 5000/min |
| Conexões do dispositivo | 100/s/unidade |
| Envios de D2C | 120/s/unidade (para S2), 12/s/unidade (para S1). Mínimo de 100/s. |
| Operações de C2D (enviar, receber, comentários) | 100/min/unidade |

**Observação**. A qualquer momento, é possível aumentar as cotas ou restrições aumentando o número de unidades provisionadas em um Hub IoT.

## Próximas etapas

Você viu uma visão geral do desenvolvimento para Hub IoT. Siga estes links para saber mais:

- [Introdução aos Hubs IoT (tutorial)][lnk-get-started]
- [Compatibilidade de plataformas de SO e hardware][lnk-compatibility]
- [Centro de Desenvolvedores do IoT do Azure][lnk-iotdev]
- [Planeje sua implementação do IoT][lnk-guidance]

[Hubs de Eventos - Host Processador de Eventos]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx

[portal do Azure]: https://portal.azure.com

[img-summary]: ./media/iot-hub-devguide/summary.png
[img-endpoints]: ./media/iot-hub-devguide/endpoints.png
[img-lifecycle]: ./media/iot-hub-devguide/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide/eventhubcompatible.png

[lnk-compatibility]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md
[lnk-apis-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-hub-sdks]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-reference-architecture]: iot-hub-what-is-azure-iot.md

[lnk-azure-gateway-guidance]: iot-hub-guidance.md#fieldgateways
[lnk-guidance-provisioning]: iot-hub-guidance.md#provisioning
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-guidance-security]: iot-hub-guidance.md#customauth

[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-guidance]: iot-hub-guidance.md
[lnk-getstarted-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md

[lnk-amqp]: https://www.amqp.org/
[lnk-arm]: ../resource-group-overview.md
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-createuse-sas]: ../storage/storage-dotnet-shared-access-signature-part-2/
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-event-hubs]: http://azure.microsoft.com/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-guidance-d2c-processing]: iot-hub-csharp-csharp-process-d2c.md
[lnk-management-portal]: https://portal.azure.com
[lnk-rfc7232]: https://tools.ietf.org/html/rfc7232
[lnk-sas-java]: https://msdn.microsoft.com/library/azure/Hh875756.aspx
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-servicebus]: http://azure.microsoft.com/services/service-bus/
[lnk-tls]: https://tools.ietf.org/html/rfc5246
[lnk-iotdev]: https://azure.microsoft.com/develop/iot/

<!---HONumber=AcomDC_1223_2015-->