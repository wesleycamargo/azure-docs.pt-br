<properties
 pageTitle="Introdução à biblioteca de gerenciamento de dispositivos | Microsoft Azure"
 description="Biblioteca do cliente de gerenciamento de dispositivos (DM) do Hub IoT do Azure"
 services="iot-hub"
 documentationCenter=""
 authors="CarlosAlayo"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="carlosa"/>

# Introdução à biblioteca do cliente de DM (gerenciamento de dispositivos) do Hub IoT do Azure

## Visão geral

A biblioteca do cliente do gerenciamento de dispositivos do Hub IoT do Azure permite que você gerencie seus dispositivos IoT com o IoT Hub do Azure. "Gerenciar" inclui as ações de reinicialização, a redefinição de fábrica e a atualização de firmware. Hoje, oferecemos uma biblioteca C independente de plataforma, mas adicionaremos suporte a outras linguagens em breve. Como descrito na [Visão geral do gerenciamento de dispositivos do Hub IoT do Azure][lnk-dm-overview], há três conceitos principais no gerenciamento de dispositivos do Hub IoT:

- Dispositivos gêmeos
- Trabalhos de dispositivo
- Consultas do dispositivo

Se você não estiver familiarizado com eles, convém ler a visão geral antes de continuar. Todos estão intimamente relacionados à biblioteca do cliente.

A biblioteca do cliente de DM terá duas responsabilidades principais no gerenciamento de dispositivos:

- Sincronizar propriedades no dispositivo físico com seu dispositivo gêmeo correspondente no Hub IoT
- Coreografar os trabalhos de dispositivo enviados pelo Hub IoT para o dispositivo

As propriedades do dispositivo físico (por exemplo, o nível da bateria e o número de série) são periodicamente sincronizadas com o dispositivo gêmeo baseado em nuvem de forma que o Hub IoT tenha sempre uma exibição atualizada de cada um dos dispositivos físicos (desde que o dispositivo esteja conectado).

A principal maneira como o serviço interage com o dispositivo físico é por meio de trabalhos de dispositivo e do dispositivo gêmeo. Os seguintes tipos de trabalho são fornecidos no Hub IoT. A biblioteca do cliente do DM ocupa-se de grande parte da orquestração para trabalhos de dispositivo, mas cabe a você, o desenvolvedor, implementar os retornos de chamada necessários em seu dispositivo para dar suporte a cada tipo de trabalho.

1.	**Atualização de firmware**: atualiza o firmware (ou a imagem do sistema operacional) no dispositivo físico
2.	**Reinicializar**: reinicia o dispositivo físico
3.	**Redefinição de fábrica**: reverte o firmware (ou a imagem do sistema operacional) do dispositivo físico para uma imagem de backup fornecida de fábrica armazenada no dispositivo.
4.	**Atualização da configuração**: configura o agente de cliente do Hub IoT Hub em execução no dispositivo físico
5.	**Ler propriedade do dispositivo**: obtém o valor de uma propriedade de dispositivo mais recente no dispositivo físico
6.	**Gravar propriedade do dispositivo**: altera uma propriedade do dispositivo no dispositivo físico

As seções a seguir guiarão você pela arquitetura da biblioteca do cliente e fornecerão diretrizes sobre como implementar os objetos de dispositivos diferentes em seu dispositivo.

## Princípios e conceitos funcionais de design da biblioteca do cliente do gerenciamento de dispositivos
A biblioteca do cliente do DM foi projetada pensando na portabilidade e na integração entre plataformas. Isso foi obtido por meio das seguintes decisões de design:

1.	Baseado no LWM2M sobre o protocolo padrão COAP para acomodar a extensibilidade para uma variedade de dispositivos diferentes.
2.	Gravado em ANSI C99 para facilitar a portabilidade para uma ampla variedade de plataformas.
3.	Protegido por meio de autenticação TLS/TCP e do Hub IoT do Azure (tokens SAS) para que possa ser usado em cenários de alta segurança.
4.	Com base no projeto OSS [Wakaama Eclipse][lnk-Wakaama] para aproveitar o código existente e contribuir com a comunidade.

### Conceitos relevantes de LWM2M
Escolhemos o LWM2M padrão para acomodar a extensibilidade para uma variedade de dispositivos diferentes. Para simplificar a experiência de desenvolvimento, abstraímos a maior parte do protocolo. No entanto, é importante entender os princípios fundamentais da biblioteca, principalmente o modelo de dados e como os dados são transmitidos.

#### Objetos e recursos: modelo de dados no LWM2M
O modelo de dados LWM2M introduz o conceito de objetos e recursos:

- **Objetos** descrevem um conjunto de entidades funcionais coerentes no sistema, como as atualizações de firmware e do dispositivo.
- **Recursos** descrevem atributos ou ações incluídos nesses objetos, como informações de nível da bateria e a ação de reinicialização.

Observe que há dois relacionamentos “um para muitos” neste modelo:

- **Dispositivo e objetos**: cada dispositivo pode ter vários objetos. Por exemplo, um dispositivo da Contoso deve ter um "objeto Dispositivo" e um "objeto Servidor" (veremos descrições detalhadas dos objetos na próxima seção).
- **Objetos e recursos**: cada objeto pode ter vários recursos. Por exemplo, um objeto pode conter os recursos de atualização do firmware de dispositivo da Contoso, como o pacote URI onde a nova imagem é armazenada.

#### Padrão de observação/notificação: como os dados são transmitidos no LWM2M
Além desses conceitos, é importante entender como os dados fluem do dispositivo para o serviço. Para fazer isso, o LWM2M define o padrão de "observação/notificação". Quando o dispositivo físico se conecta ao serviço, o Hub IoT inicia a “observação” nas propriedades de dispositivo selecionadas. Em seguida, o dispositivo físico "notifica" o serviço de alterações para as propriedades do dispositivo.

Em nossa biblioteca do cliente, implementamos o padrão de observação/notificação como a forma de enviar dados de gerenciamento de dispositivo do dispositivo para o Hub IoT. O padrão é controlado por dois parâmetros:

- **Período mínimo**: o período de tempo em que o dispositivo atrasa ao enviar uma atualização para uma propriedade observada. Isso é definido como cinco minutos. Portanto, o dispositivo envia uma atualização para uma propriedade observada no máximo a cada cinco minutos, mesmo se o valor for alterado com mais frequência. Isso significa que se a propriedade for alterada a cada minuto, o serviço só verá a última alteração no quinto minuto.

- **Período máximo**: o período de tempo em que o dispositivo, independente de a propriedade observada ser alterada ou não, envia uma atualização para o valor da propriedade. É definido como seis horas. Portanto, o dispositivo envia uma atualização para o valor para uma propriedade observada no máximo a cada seis horas, mesmo se o valor não tiver sido alterado.

> [AZURE.NOTE]  A única exceção a esses parâmetros é que as propriedades usadas para o trabalho de atualização do firmware têm um período mínimo definido como 30 segundos. Isso acontece porque essas propriedades mudam com muita frequência durante a execução do trabalho e, portanto, reduzimos o período mínimo para acelerarmos o processo de atualização.

## Funções e arquitetura da biblioteca do cliente
Como vimos na visão geral, há duas funções principais das quais as bibliotecas do cliente cuidam:

- Sincronizar o dispositivo físico com seu dispositivo gêmeo correspondente no Hub IoT
- Coreografar os trabalhos de dispositivo enviados pelo Hub IoT para o dispositivo.

Nesta seção, detalharemos cada uma delas.

### Sincronização do dispositivo físico e do dispositivo gêmeo
A biblioteca do cliente usa o padrão de observação/notificação para manter o dispositivo gêmeo atualizado. Lembre-se de que o dispositivo gêmeo é a representação do lado do serviço do dispositivo físico. Para a execução dessa sincronização, ocorre o seguinte processo:

1. O dispositivo registra-se no serviço, normalmente durante a inicialização. Exemplo: "eu sou um dispositivo, tenho ID de dispositivo Contoso com o token de acesso Y”.
2. O serviço observa recursos nos objetos. Exemplo: "mantenha-me atualizado sobre o nível da bateria do meu dispositivo Contoso”.
3. O dispositivo notifica o serviço sobre o valor do recurso. A frequência das notificações baseia-se nos períodos mínimo e máximo. Exemplo: "17:00 nível de bateria 99%, 17:05 bateria o nível de bateria 90%, etc."

### Coreografar trabalhos de dispositivo: como o dispositivo gêmeo e os trabalhos de dispositivo funcionam juntos
Para agir em um dispositivo físico, o serviço deve localizar o dispositivo gêmeo associado. Isso pode ser feito por meio da consulta de propriedades ou da pesquisa de uma ID específica. Sabendo a ID do gêmeo (e, portanto, a ID correspondente do dispositivo físico), o serviço estará pronto para iniciar um trabalho de dispositivo no dispositivo físico.

O trabalho de dispositivo representa um conjunto de comandos diferentes que representam o processo específico que queremos executar (por exemplo: as etapas de atualização de firmware). Um trabalho de dispositivo pode ser composto por várias etapas:

1.	O dispositivo gêmeo tem propriedades que representam o estado de um trabalho de dispositivo.
2.	Para avançar entre os diferentes processos no trabalho de dispositivo, as propriedades no dispositivo gêmeo devem ter um determinado valor e, portanto, o dispositivo físico deve definir a propriedade correta para poder ser sincronizado ao dispositivo gêmeo e continuar o trabalho.

Essa sequência será repetida se o processo for composto por várias etapas (por exemplo: durante uma atualização de firmware, o dispositivo gêmeo mudará suas propriedades várias vezes durante as diversas etapas antes que o trabalho seja considerado como concluído).

## Diretrizes para implementar o gerenciamento de dispositivos em seu cliente
Nas seções anteriores, aprendemos sobre as funções das bibliotecas do cliente de gerenciamento de dispositivos, seus princípios de design e como elas se relacionam ao LWM2M. Agora, nos concentraremos em explicar como as peças se encaixam no tempo de execução.

Essencialmente, a biblioteca de cliente lida com a comunicação entre o dispositivo e o serviço e, portanto, tudo o que resta é a implementação da lógica específica do dispositivo. Isso consiste em duas partes:

1.	**Implementar os detalhes específicos do dispositivo**: envolve gravar lógica específica do dispositivo para executar as ações solicitadas pelo serviço (por exemplo: baixar o pacote de firmware) nos retornos de chamada apropriados. A seguir, um exemplo desse retorno de chamada para o pacote de atualização de firmware. Você pode encontrar os retornos de chamada nos arquivos .c da pasta [aqui][lnk-github1].

            object_firmwareupdate *obj = get_firmwareupdate_object(0);
            obj->firmwareupdate_packageuri_write_callback =     start_firmware_download;
            // platform specific code
            obj->firmwareupdate_update_execute_callback = start_firmware_update;
            //platform specific code


2.	**Informar à biblioteca do cliente quando as propriedades forem alteradas**: faça isso chamando as funções definidas correspondentes no arquivo .h da pasta [aqui][lnk-github2].

        IOTHUB_CLIENT_RESULT set_firmwareupdate_state(uint16_t instanceId, int value);

### Objetos que você precisa implementar na biblioteca do cliente do DM

Acabamos de explicar como você implementaria a lógica específica do dispositivo para executar trabalhos de dispositivo. Agora, explicaremos quais objetos estão disponíveis para uso.

Alguns desses objetos são necessários, o que significa que você precisa implementar a lógica específica do dispositivo para que ela faça parte do gerenciamento de dispositivos do Hub IoT. Outros são opcionais e, portanto, você pode escolher dependendo das necessidades do seu serviço (por exemplo, você pode optar por não fazer atualizações de firmware usando o Hub IoT). Veja uma descrição de cada um:

- **Objeto de dispositivo (obrigatório)**: fornece informações específicas do dispositivo, como informações do fabricante, número do modelo, número de série, hora do dispositivo. O serviço pode ler essas informações e, em alguns casos, atualizá-las. Ele também define duas ações que o serviço pode executar em um dispositivo: reinicialização e redefinição de fábrica.
- **Objeto de servidor (necessário)**: contém os parâmetros de conexão e as configurações usadas para se conectar ao Hub IoT, como a vida útil do registro e a associação de transporte. O serviço só pode ler essas informações.
- **Objeto de configuração (opcional)**: contém informações sobre a configuração definida pelo usuário que podem ser consultadas no dispositivo ou enviadas por push a um dispositivo a partir do serviço para facilitar a configuração do dispositivo. O serviço pode ler e atualizar essas informações.
- **Objeto de atualização de firmware (opcional)**: fornece uma ação de atualização de firmware que o serviço pode invocar. Também fornece informações como o local do pacote de firmware e o status de uma operação de atualização de firmware em andamento.

Cada um desses objetos tem um conjunto de recursos associados a ele (lembre-se da associação um para muitos). A lista dos objetos LWM2M e de todos os recursos associados ao suporte no gerenciamento de dispositivos do Hub IoT do Azure foi incluída ao final deste artigo.

> [AZURE.NOTE] As propriedades personalizadas do dispositivo, as várias instâncias de recurso e as várias instâncias de objeto não têm suporte na versão atual do sistema.

### Juntando as peças

A seguir, veja um diagrama que reúne todas essas peças. No lado direito, em azul, você pode ver os diferentes componentes da biblioteca do cliente de gerenciamento de dispositivos: objetos, manipuladores e métodos de notificação. O lado esquerdo, em verde, representa a lógica necessária para gravar no nível do aplicativo do dispositivo. A biblioteca do cliente conecta-se à lógica do aplicativo com o Hub IoT, garantindo que a comunicação e a coreografia sejam manipuladas adequadamente.

A figura abaixo mostra os componentes da biblioteca do cliente do DM.

![][img-library-overview]

## Próximas etapas: adquira experiência prática
Este artigo abordou as noções básicas do uso da biblioteca do cliente de gerenciamento de dispositivos do Hub IoT para C.

Para adquirir experiência prática, você pode acessar os seguintes recursos:

- Exemplo de atualização do firmware Intel Edison: exemplo com recursos de gerenciamento de dispositivos habilitados usando um dispositivo Intel Edison. Confira [iotdm\_edison\_sample][lnk-edison-sample].
- Exemplo de dispositivos simulados: um exemplo de dispositivos independentes de plataforma executado em dispositivos Linux e Windows. Veja [iotdm\_simple\_sample][lnk-simple-sample]
- Para saber mais sobre objetos LWM2M, confira [objeto e registro de recursos LWM2M de OMA][lnk-oma]

## Apêndice: Objetos e recursos LWM2M com suporte no momento

### Objeto de dispositivo

| Nome do Recurso | Operação remota permitida no recurso | Tipo | Intervalo e Unidades | Descrição |
|-----------------|--------------------------------------|---------|-----------------|-------------|
| Fabricante | Ler | Cadeia de caracteres | | Nome do fabricante |
| ModelNumber | Ler | Cadeia de caracteres | | Um identificador de modelo (cadeia de caracteres especificada pelo fabricante) |
| DeviceType | Ler | Cadeia de caracteres | | Tipo de dispositivo (cadeia de caracteres especificada pelo fabricante)<br/>Observação: é mapeado para a API do lado servidor **SystemPropertyNames.DeviceDescription** |
| SerialNumber | Ler | Cadeia de caracteres | | Número de série do dispositivo |
| FirmwareVersion | Ler | Cadeia de caracteres | | Versão atual do firmware do dispositivo |
| HardwareVersion | Ler | Cadeia de caracteres | | Versão atual do hardware do dispositivo. |
| Reboot | Executar | | | Reinicie o dispositivo. |
| FactoryReset | Executar | | | Execute a redefinição de fábrica do dispositivo para que o dispositivo tenha a mesma configuração que a implantação inicial. |
| CurrentTime | Leitura<br/>Gravação | Hora | | Hora atual do UNIX do dispositivo. O cliente deve ser responsável por aumentar esse valor de tempo à medida que cada segundo decorre.<br/>O servidor do DM é capaz de gravar nesse recurso para sincronizar o cliente com o horário no servidor. |
| UTCOffset | Leitura<br/>Gravação | Cadeia de caracteres | | Diferença UTC em vigor. |
| Fuso horário | Leitura<br/>Gravação | Cadeia de caracteres | | Indica em qual fuso horário o dispositivo está localizado. |
| MemoryFree | Ler | Número inteiro | KB | Memória estimada disponível atualmente de espaço de armazenamento que pode armazenar dados e software no dispositivo |
| MemoryTotal | Ler | Número inteiro | KB | Quantidade total de espaço de armazenamento que pode armazenar dados e software no dispositivo |
| BatteryLevel | Ler | Número inteiro | 0 a 100% | Nível de bateria atual como uma porcentagem (de 0 a 100) |
| BatteryStatus | Ler | Número inteiro | 0 a 6 | **0**: a bateria está funcionando corretamente e não está na alimentação.<br/>**1**: a bateria está sendo carregada.<br/>**2**: bateria está totalmente carregada e está conectada à rede elétrica.<br/>**3**: bateria está danificada.<br/>**4**: a bateria está quase descarregada.<br/>**5**: a bateria não está presente.<br/> **6**: informações sobre a bateria não estão disponíveis. |

### Objeto de Atualização de Firmware

| Nome do Recurso | Operação | Tipo | Intervalo e Unidades | Descrição |
|----------------|-----------|---------|-----------------|-------------|
| Pacote | Gravar | Opaco | | Pacote de firmware em formato binário.<br/>É mapeado para a API de serviço:<br/>**SystemPropertyNames.FirmwarePackage** |
| PackageURI | Gravar | Cadeia de caracteres | 0 a 255 bytes | URI de onde o dispositivo pode baixar o pacote de firmware.<br/>É mapeado para a API de serviço: **SystemPropertyNames.FirmwarePackageUri** |
| Atualização | Executar | | | Atualiza o firmware usando o pacote de firmware armazenado no Pacote ou usando o firmware baixado do URI do pacote.<br/>É mapeado para a API de serviço:<br/>**ScheduleFirmwareUpdateAsync** |
| Estado | Ler | Número inteiro | 1-3 | Status do processo de atualização de firmware:<br/>**1**: Ocioso. Isso poderia ocorrer antes do download do pacote de firmware ou após a aplicação do pacote de firmware.<br/>**2**: baixando o pacote de firmware.<br/>**3**: pacote de firmware baixado.<br/> É mapeado para a API de serviço: **SystemPropertyNames.FirmwareUpdateState** |
| UpdateResult | Ler | Número inteiro | 0 a 6 | O resultado do download ou a atualização do firmware<br/>**0**: valor padrão.<br/>**1**: atualização do firmware bem-sucedida.<br/>**2**: não há armazenamento suficiente para o novo pacote de firmware.<br/>**3**: sem memória durante o download do pacote de firmware.<br/>**4**: conexão perdida durante o download do pacote de firmware.<br/>**5**: falha de verificação CRC para o novo pacote baixado.<br/>**6**: tipo de pacote de firmware sem suporte.<br/>**7**: URI inválido. É mapeado para a API de serviço: **SystemPropertyNames.FirmwareUpdateResult** |
| PkgName | Ler | Cadeia de caracteres | 0 a 255 bytes | Nome descritivo do pacote de firmware referenciado pelo recurso **Pacote** <br/>É mapeado para a API do serviço:<br/>**SystemPropertyNames.FirmwarePackageName** |
| PackageVersion | Ler | Cadeia de caracteres | 0 a 255 bytes | Versão do pacote de firmware referenciado pelo recurso **Pacote** <br/>É mapeado para a API do serviço:<br/>**SystemPropertyNames.FirmwarePackageVersion** |

### Objeto do Servidor LWM2M

| Nome do Recurso | Operação | Tipo | Intervalo e Unidades | Descrição |
|------------------------|------------|---------|-----------------|---------------|
| Período Mínimo Padrão | Leitura Gravação | Número inteiro | Segundos | O período de tempo em que o dispositivo atrasa ao enviar uma atualização para uma propriedade observada. Por exemplo, dado um **DefaultMinPeriod** de cinco minutos, o dispositivo enviará uma atualização para uma propriedade observada no máximo a cada cinco minutos, mesmo se o valor for alterado com mais frequência. É mapeado para a API de serviço: **SystemPropertyNames.DefaultMinPeriod** |
| Período máximo padrão | Leitura Gravação | Número inteiro | Segundos | O período de tempo (em segundos) em que o dispositivo, independente de a propriedade observada ser alterada, envia uma atualização para o valor da propriedade. Por exemplo, dado um **DefaultMaxPeriod** de seis horas, uma propriedade observada envia uma atualização para o valor da propriedade pelo menos a cada seis horas, independentemente das alterações do recurso.<br/>É mapeado para a API de serviço:<br/>**SystemPropertyNames.DefaultMaxPeriod** |
| Tempo de vida | Leitura Gravação | Número inteiro | Segundos | O tempo de vida de registro do dispositivo. Um novo registro ou uma solicitação de atualização precisa ser recebida por meio do dispositivo no seu tempo de vida, caso contrário o dispositivo terá o registro cancelado no serviço.<br/>É mapeado para a API de serviço:<br/>**SystemPropertyNames.RegistrationLifetime** |

### Objeto de Configuração

| Nome do Recurso | Operação | Tipo | Intervalo e Unidades | Descrição |
|---------------|------------|--------|-----------------|-------------|
| Nome | Leitura Gravação | Cadeia de caracteres | | Identifica exclusivamente o nome da configuração do dispositivo para ler ou atualizar. |
| Valor | Leitura Gravação | Cadeia de caracteres | | Identifica exclusivamente o valor da configuração para ler ou atualizar. |
| Aplicar | Executar | | | Aplica-se à alteração da configuração no dispositivo. |

## Próximas etapas

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]
- [Usar o Portal do Azure para gerenciar o Hub IoT][lnk-portal]

[img-library-overview]: media/iot-hub-device-management-library/library.png
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-simple-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_simple_sample
[lnk-edison-sample]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample
[Azure IoT Hub device SDK]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c
[Azure IoT Hub]: Link%20to%20DM%20Overview
[Lightweight M2M]: http://openmobilealliance.org/about-oma/work-program/m2m-enablers/
[CoAP]: https://tools.ietf.org/html/rfc7252
[Wakaama]: https://github.com/eclipse/wakaama
[OMA LWM2M Object and resource registry]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-Wakaama]: https://github.com/eclipse/wakaama
[lnk-github1]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-github2]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/lwm2m_objects
[lnk-oma]: http://technical.openmobilealliance.org/Technical/technical-information/omna/lightweight-m2m-lwm2m-object-registry

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->