<properties
 pageTitle="Orientação do Hub IoT do Azure | Microsoft Azure"
 description="Uma coleção de tópicos de orientação para soluções que usam o Hub IoT do Azure."
 services="iot-hub"
 documentationCenter=".net"
 authors="fsautomata"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="elioda"/>

# Hub IoT do Azure - Orientação

## Comparação: Hub IoT e Hubs de Eventos
Um dos principais casos de uso do Hub IoT do Azure é coletar a telemetria dos dispositivos. Por esse motivo, o Hub IoT é frequentemente comparado aos [Hubs de Eventos], que são serviços de processamento de eventos que fornecem a entrada em grande escala de telemetria e de eventos na nuvem, com baixa latência e alta confiabilidade.

Os serviços, no entanto, apresentam muitas diferenças, detalhadas nas seções a seguir.

| Área | Hub IoT | Hubs de Eventos |
| ---- | ------- | ---------- |
| Padrões de comunicação | Entrada de evento do dispositivo para a nuvem e sistema de mensagens da nuvem para o dispositivo. | Somente a entrada de evento (geralmente considerada para cenários do dispositivo para a nuvem). |
| Segurança | Identidade por dispositivo e controle de acesso revogável. Consulte [Guia do desenvolvedor do Hub IoT - Segurança]. | [Políticas de Acesso Compartilhado][Event Hub - security] em todo o Hub de Eventos, com suporte limitado a revogação por meio do uso de [políticas do editor][Event Hub publisher policies]. No contexto das soluções IoT, normalmente é necessário implementar uma solução personalizada a fim de dar suporte a credenciais de acordo com o dispositivo e medidas anti-falsificação. |
| Escala | Os Hubs IoT são otimizados para fornecerem suporte a milhões de dispositivos conectados simultaneamente. | Os Hubs de Eventos podem dar suporte a uma quantidade mais limitada de conexões simultâneas: até 5.000 conexões AMQP, de acordo com as [Cotas do Barramento de Serviço]. Por outro lado, os Hubs de Eventos permitem que os usuários especifiquem a partição de cada mensagem enviada. |
| SDKs de dispositivo | O Hub IoT fornece [SDKs de dispositivo][Azure IoT Hub SDKs] para uma grande variedade de plataformas e idiomas | Os Hubs de Eventos têm suporte do .NET, de C e fornecem interfaces de envio AMQP e HTTP. |

Concluindo, mesmo que o único caso de uso seja a entrada de telemetria do dispositivo para a nuvem, o Hub IoT fornece um serviço desenvolvido especificamente para a conectividade do dispositivo IoT, e continuará a expandir as propostas de valor para esses cenários com recursos específicos do IoT. Os Hubs de Eventos são projetados para a entrada de evento em grande escala, no contexto de cenários de datacenter interno e externo. Não é incomum usar o Hub IoT e os Hubs de Eventos em conjunto, permitindo que o primeiro lide com a comunicação do dispositivo para a nuvem, e o último com a entrada de evento de estágio posterior em mecanismos de processamento em tempo real.

## Provisionamento de dispositivo <a id="provisioning"></a>
As soluções do IoT mantêm informações sobre o dispositivo em muitos sistemas e repositórios diferentes, o [registro de identidade do Hub IoT][IoT Hub Developer Guide - identity registry] é um dentre outros repositórios que mantêm informações de dispositivo específicas ao aplicativo. Chamamos de *provisionamento* o processo de criação das informações necessárias sobre o dispositivo em todos esses sistemas.

Há muitos requisitos e estratégias para o provisionamento de dispositivo (consulte a [orientação sobre gerenciamento de dispositivo do Hub IoT] para saber mais). O [registro de identidade do Hub IoT][IoT Hub Developer Guide - identity registry] fornece as APIs necessárias para integrar o Hub IoT ao processo de provisionamento.

## Gateways de campo <a id="fieldgateways"></a>
Um gateway de campo é um dispositivo especializado ou um software de finalidade geral que age como um habilitador de comunicação e, possivelmente, como um sistema de controle de dispositivo local e hub de processamento de dados do dispositivo. Um gateway de campo pode executar funções locais de processamento e controle para os dispositivos; por outro lado, ele pode filtrar ou agregar a telemetria de dispositivo e, portanto, reduzir a quantidade de dados transferidos para o back-end.

O escopo de um gateway de campo inclui o próprio gateway de campo e todos os dispositivos conectados a ele. Como o nome sugere, os gateways de campo atuam fora de instalações de processamento de dados dedicadas e costumam ser associados ao local. Um gateway de campo é diferente de um mero roteador de tráfego, pois tem uma função ativa no gerenciamento de fluxo de informações e de acesso. Isso significa que é um terminal de sessão ou de conexão de rede e de entidade endereçada ao aplicativo ou terminal de sessão (por exemplo, os gateways nesse contexto podem auxiliar no provisionamento do dispositivo, na transformação de dados, na conversão de protocolo e no processamento de regras de evento). Os dispositivos NAT ou firewalls, por outro lado, não se qualificam como gateways de campo, pois não são terminais explícitos de conexão ou de sessão, mas em vez disso, roteiam (ou negam) conexões ou sessões feitas por meio deles.

### Gateways de campo transparentes versus opacos
Com relação às identidades de dispositivo, os gateways de campo serão chamados de *transparentes* se outros dispositivos em seu escopo tiverem entradas de identidade do dispositivo no registro de identidade do Hub IoT. Eles são chamados de *opacos* no caso de a única identidade no registro de identidade do Hub IoT ser a identidade do gateway de campo.

É importante observar que o uso de gateways *opacos* impede que o Hub IoT forneça recursos de [anti-falsificação de identidade do dispositivo][IoT Hub Developer Guide - Anti-spoofing]. Além disso, como todos os dispositivos no escopo do gateway de campo são representados como um único dispositivo no Hub IoT, eles estarão sujeitos a cotas e a limites como um único dispositivo.

### Outras considerações

Ao implementar um gateway de campo, você poderá usar os SDKs de dispositivo IoT do Azure. Alguns SDKs oferecem funcionalidades específicas ao gateway, como a habilidade de multiplexar a comunicação de vários dispositivos na mesma conexão com o Hub IoT. Como explicado no [Guia do desenvolvedor do Hub IoT - escolhendo seu protocolo de comunicação], você deve evitar o uso de HTTP/1 como protocolo de transporte para gateways de campo.

## Usando serviços/esquemas personalizados de autenticação de dispositivo. <a id="customauth"></a>
O Hub IoT do Azure permite a configuração de credenciais de segurança por dispositivo e controle de acesso por meio do uso do [registro de identidade do dispositivo][IoT Hub Developer Guide - identity registry]. Se uma solução IoT já tiver um investimento considerável em um registro de identidade de dispositivo personalizado e/ou em um esquema de autenticação, ainda poderá aproveitar os outros recursos do Hub IoT por meio da criação de um *serviço de token* para o Hub IoT.

O serviço de token é um serviço de nuvem implantado automaticamente e que usa uma Política de Acesso Compartilhado do Hub IoT com permissões **DeviceConnect** para criar tokens *no escopo do dispositivo*.

  ![][img-tokenservice]

Estas são as principais etapas do padrão de serviço do token.

1. Crie uma [Política de Acesso Compartilhado do Hub IoT][IoT Hub Developer Guide - Security] com permissões **DeviceConnect** para seu Hub IoT. Essa política será usada pelo serviço de token para assinar os tokens.
2. Quando um dispositivo quiser acessar o Hub IoT, ele solicitará um token assinado ao serviço de token. O dispositivo pode usar o registro de identidade/esquema de autenticação do dispositivo personalizado.
3. O serviço de token retorna um token, criado de acordo com o [Guia do desenvolvedor do Hub IoT - Segurança], usando `/devices/{deviceId}` como `resourceURI`, com `deviceId` como o dispositivo que está sendo autenticado.
4. O dispositivo usa o token diretamente com o Hub IoT.

O serviço de token pode definir a expiração do token como desejado. Ao expirar, o Hub IoT atuará como o servidor da conexão e o dispositivo terá que solicitar um novo token ao serviço de token. Claramente, um tempo de expiração curto aumentará a carga no dispositivo e no serviço de token.

Vale a pena especificar que a identidade do dispositivo ainda precisa ser criada no Hub IoT para que o dispositivo possa se conectar. Isso também significa que o controle de acesso por dispositivo (desabilitando as identidades do dispositivo de acordo com o [Guia do desenvolvedor do Hub IoT - registro de identidade]), ainda está funcionando, mesmo se o dispositivo se autenticar com um token. Isso reduz a existência de tokens de longa duração.

### Comparação com um gateway personalizado
O padrão do serviço do token é a maneira recomendada de implementar um registro de identidade/ esquema de autenticação personalizado com o Hub IoT, pois permite que o Hub IoT lide com a maior parte do tráfego de solução. No entanto, há casos em que o esquema de autenticação personalizado está tão entremeado com o protocolo (por exemplo, [TLS-PSK]) que um serviço de processamento de todo o tráfego (*gateway personalizado*) se torna necessário. Consulte o artigo [Gateway do protocolo] para saber mais.

## Dimensionamento do Hub IoT
O Hub IoT pode dar suporte a até um milhão de dispositivos conectados simultaneamente, aumentando para 2.000 o número de unidades S1 ou S2 do Hub IoT. Consulte [Preços do Hub IoT][lnk-pricing] para saber mais.

Cada unidade do Hub IoT permite um certo número de identidades de dispositivo no registro, que podem estar simultaneamente conectadas, e um número de mensagens diárias.

Para dimensionar corretamente sua solução, será necessário considerar o uso específico do Hub IoT. Em particular, considere a taxa de transferência de pico necessária para as seguintes categorias de operações:

* Mensagens do dispositivo para a nuvem
* Mensagens da nuvem para o dispositivo
* Operações de registro de identidade

Além dessas informações sobre a taxa de transferência, lembre-se de consultar as [Cotas e limites do Hub IoT] e crie sua solução adequadamente.

### Taxa de transferência de mensagens do dispositivo para a nuvem, e da nuvem para o dispositivo
A melhor maneira de dimensionar uma solução do Hub IoT é avaliar o tráfego de acordo com o dispositivo.

As mensagens do dispositivo para a nuvem seguem estas diretrizes de taxa de transferência sustentada.

| Camada | Taxa de transferência sustentada | Taxa de envio sustentada |
| ---- | -------------------- | ------------------- |
| S1 | até 8 kb/hora por dispositivo | média de quatro mensagens por hora por dispositivo |
| S2 | até 4 kb/minuto por dispositivo | média de duas mensagens por minuto por dispositivo |

Ao receber mensagens do dispositivo para a nuvem, o back-end de aplicativo pode esperar taxa de transferência máxima a seguir (entre todos os leitores).

| Camada | Taxa de transferência sustentada |
| ---- | -------------------- |
| S1 | até 120 Kb/minuto por unidade com 2 Mb/s, no mínimo. |
| S2 | até 4 Mb/minuto por unidade com 2 Mb/s, no mínimo. |

O desempenho de mensagens da nuvem para o dispositivo é dimensionado por dispositivo, e cada dispositivo recebe até cinco mensagens por minuto.

### Taxa de transferência de operações de registro de identidade
As operações de registro de identidade do Hub IoT não devem ser operações em tempo de execução, pois estão relacionadas principalmente com provisionamento do dispositivo. Consulte [Cotas e limites do Hub IoT] para obter números de desempenho de intermitência específicos.

### Fragmentação
Enquanto um único Hub IoT pode ser dimensionado para milhões de dispositivos, às vezes sua solução exigirá características específicas de desempenho que um único Hub IoT não pode garantir. Nesse caso, recomendamos o particionamento de seus dispositivos em vários Hubs IoT, a fim de suavizar os picos de tráfego e obter as taxas de transferência ou de operação necessárias.

## Alta disponibilidade e recuperação de desastres
Por ser um serviço do Azure, o Hub IoT fornece alta disponibilidade (HA) com redundâncias no nível de região do Azure, sem a necessidade de qualquer trabalho adicional da solução. Além disso, o Azure oferece vários recursos que ajudam a criar soluções com recursos de recuperação de desastres ou disponibilidade entre regiões, se isso for necessário. As soluções devem ser criadas e preparadas para aproveitar esses recursos a fim de fornecer alta disponibilidade global e entre regiões para dispositivos ou usuários. O artigo [Orientação técnica de continuidade de negócios do Azure] descreve os recursos internos do Azure para recuperação de desastres e continuidade de negócios. O documento [Recuperação de desastres e alta disponibilidade para aplicativos do Azure] fornece uma orientação de arquitetura sobre estratégias para que os aplicativos do Azure obtenham HADR.

## Failover regional com o Hub IoT
Um tratamento completo das topologias de implantação em soluções IoT está fora do escopo desta seção, mas, para fins de alta disponibilidade e de recuperação de desastres, consideraremos o modelo de implantação de *failover regional*.

Em um modelo de failover regional, o back-end da solução será executado principalmente em um datacenter, mas um Hub IoT e back-end adicionais serão implantados em uma região adicional de datacenter para fins de failover, no caso de o Hub IoT no datacenter principal sofrer uma interrupção ou no caso de a conectividade de rede do dispositivo para o datacenter primário ser interrompida. Os dispositivos usarão um ponto de extremidade de serviço secundário sempre que o gateway primário não puder ser alcançado. Com um recurso de failover entre regiões, é possível melhorar a disponibilidade da solução ultrapassando a alta disponibilidade de uma única região.

Em um alto nível, a fim de implementar um modelo de failover regional com Hub IoT, você precisará do seguinte.

* **Um Hub IoT secundário e lógica de roteamento do dispositivo** - no caso de uma interrupção do serviço em sua região primária, os dispositivos precisarão iniciar a conexão com a região secundária. Com base no status da maioria dos serviços envolvidos, é comum os administradores de solução acionarem o processo de failover entre regiões. A melhor maneira de comunicar o novo ponto de extremidade para os dispositivos, enquanto mantém o controle do processo, é fazer com que eles verifiquem regularmente um serviço de *concierge* para o ponto de extremidade ativo atual. O serviço de concierge pode ser um aplicativo Web simples que é replicado e acessível usando técnicas de redirecionamento de DNS (por exemplo, usando o [Gerenciador de Tráfego do Azure]).
* **Duplicação do registro de identidade** - para ser usado, o Hub IoT secundário precisará conter todas as identidades de dispositivo que precisam se conectar à solução. A solução deve manter backups replicados geograficamente das identidades dos dispositivos e carregá-los no Hub IoT secundário antes de mudar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é muito útil nesse contexto, [Guia do desenvolvedor do Hub IoT - registro de identidade].
* **Mesclando a lógica** - quando a região primária fica disponível novamente, todo o estado e os dados criados no site secundário precisam ser migrados de volta para a região primária. Isso está relacionado principalmente às identidades de dispositivo e metadados de aplicativo, que precisarão ser mesclados com o Hub IoT principal e, provavelmente, com outros armazenamentos específicas ao aplicativo na região primária. Para simplificar esta etapa, normalmente recomendamos o uso de operações idempotente. Isso minimiza os efeitos colaterais não apenas para a distribuição eventual e consistente de eventos, mas também de duplicatas ou a entrega de eventos fora de ordem. Além disso, a lógica do aplicativo deve ser criada a fim de poder tolerar possíveis inconsistências ou estados "ligeiramente" atualizados, devido ao tempo adicional necessário para que o sistema seja "reparado" ou com base em objetivos de ponto de recuperação (RPO). O artigo a seguir fornece mais orientações sobre este tópico: [À prova de falhas: orientação para arquiteturas resilientes na nuvem].




[img-tokenservice]: media/iot-hub-guidance/tokenservice.png

[IoT Hub Developer Guide - identity registry]: iot-hub-devguide.md#identityregistry
[Guia do desenvolvedor do Hub IoT - registro de identidade]: iot-hub-devguide.md#identityregistry
[Guia do desenvolvedor do Hub IoT - escolhendo seu protocolo de comunicação]: iot-hub-devguide.md#amqpvshttp
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Guia do desenvolvedor do Hub IoT - Segurança]: iot-hub-devguide.md#security
[IoT Hub Developer Guide - Anti-spoofing]: iot-hub-devguide.md#antispoofing
[Gateway do protocolo]: iot-hub-protocol-gateway.md
[Cotas e limites do Hub IoT]: iot-hub-devguide.md#throttling

[orientação sobre gerenciamento de dispositivo do Hub IoT]: iot-hub-device-management.md
[Hubs de Eventos]: ../event-hubs/event-hubs-what-is-event-hubs/
[Gerenciador de Tráfego do Azure]: https://azure.microsoft.com/documentation/services/traffic-manager/
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview/
[Event Hub publisher policies]: ../event-hubs-overview/#common-publisher-tasks
[Cotas do Barramento de Serviço]: ../service-bus/service-bus-quotas/
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md

[TLS-PSK]: https://tools.ietf.org/html/rfc4279

[Orientação técnica de continuidade de negócios do Azure]: https://msdn.microsoft.com/library/azure/hh873027.aspx
[Recuperação de desastres e alta disponibilidade para aplicativos do Azure]: https://msdn.microsoft.com/library/azure/dn251004.aspx
[À prova de falhas: orientação para arquiteturas resilientes na nuvem]: https://msdn.microsoft.com/library/azure/jj853352.aspx

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub

<!---HONumber=Oct15_HO1-->