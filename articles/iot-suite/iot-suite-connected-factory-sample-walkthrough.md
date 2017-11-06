---
title: "Passo a passo da solução de fábrica conectada - Azure | Microsoft Docs"
description: "Uma descrição da fábrica conectada da solução pré-configurada de do Azure e sua arquitetura."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2017
ms.author: dobett
ms.openlocfilehash: 675a3b0fb59e449f0f76f8201d62768c03144818
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Passo a passo de solução pré-configurada de fábrica conectada

A [solução pré-configurada][lnk-preconfigured-solutions] de fábrica conectada do IoT Suite é uma implementação de uma solução industrial de ponta a ponta que:

* Conecta-se a dispositivos industriais simulados que executam servidores OPC UA em linhas de produção de fábrica simuladas e a dispositivos de servidor OPC UA reais. Para obter mais informações sobre o OPC UA, consulte as [Perguntas frequentes sobre fábrica conectada](iot-suite-faq-cf.md).
* Mostra KPIs operacionais e OEE desses dispositivos e de linhas de produção.
* Demonstra como um aplicativo baseado em nuvem pode ser usado para interagir com sistemas de servidor UA OPC.
* Permite que você conecte seus próprios dispositivos de servidor OPC UA.
* Permite procurar e modificar os dados de servidor UA OPC.
* Integra-se ao serviço TSI (Azure Time Series Insights) para fornecer exibições personalizadas de dados dos servidores UA OPC.

Você pode usar a solução como um ponto de partida para sua própria implementação e [personalizar][lnk-customize] para atender às suas próprias necessidades de negócios específicas.

Este artigo explica alguns dos principais elementos da solução de fábrica conectada para que você possa entender como ela funciona. O artigo também descreve como os dados fluem através da solução. Esse conhecimento ajuda a:

* Solucionar problemas na solução.
* Planejar como personalizar a solução para atender a seus próprios requisitos específicos.
* Criar sua própria solução IoT que usa os serviços do Azure.

Para saber mais, confira as [Perguntas frequentes sobre fábrica conectada](iot-suite-faq-cf.md).

## <a name="logical-architecture"></a>Arquitetura lógica

O seguinte diagrama descreve os componentes lógicos da solução pré-configurada:

![Arquitetura lógica de fábrica conectada][connected-factory-logical]

## <a name="communication-patterns"></a>Padrões de comunicação

A solução usa a [especificação OPC UA Pub/Sub](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) para enviar dados de telemetria do OPC UA ao Hub IoT no formato JSON. A solução usa o módulo [Publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) do IoT Edge para essa finalidade.

A solução também tem um cliente de OPC UA integrado a um aplicativo Web que pode estabelecer conexões com servidores OPC UA locais. O cliente usa um [proxy reverso](https://wikipedia.org/wiki/Reverse_proxy) e recebe ajuda do Hub IoT para fazer a conexão sem a necessidade de abrir portas no firewall local. Esse padrão de comunicação é chamado de [comunicação auxiliada por serviço](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/). A solução usa o módulo [Proxy OPC](https://github.com/Azure/iot-edge-opc-proxy/) do IoT Edge para essa finalidade.


## <a name="simulation"></a>Simulação

As estações simuladas e os sistemas MES (simulação de execução de fabricação) simulados formam uma linha de produção de fábrica. Os dispositivos simulados e o Módulo OPC Publisher se baseiam no [Padrão OPC UA .NET][lnk-OPC-UA-NET-Standard] publicado pela OPC Foundation.

O proxy OPC e o fornecedor OPC são implementados como módulos com base no [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Cada linha de produção simulada tem um gateway designado anexado.

Todos os componentes de simulação executados em contêineres Docker hospedados em uma VM do Linux do Azure. A simulação é configurada para executar oito linhas de produção simuladas por padrão.

## <a name="simulated-production-line"></a>Linha de produção simulada

Uma linha de produção fabrica peças. É composto de estações diferentes: uma estação de assembly, uma de teste e uma de empacotamento.

A simulação é executada e atualiza os dados que são expostos por meio de nós UA OPC. Todas as estações de linha de produção simulada são organizadas por MES por meio de UA OPC.

## <a name="simulated-manufacturing-execution-system"></a>Sistema de execução de fabricação simulado

O MES monitora cada estação na linha de produção por meio de UA OPC para detectar alterações de status da estação. Ele chama os métodos para controlar as estações OPC UA e passa um produto de uma estação para a próxima até a conclusão.

## <a name="gateway-opc-publisher-module"></a>Módulo de editor de gateway OPC

O Módulo OPC Publisher conecta-se aos servidores OPC UA da estação e assina os nós OPC para publicação. O módulo converte os dados de nó em formato JSON, os criptografa e os envia ao Hub IoT como mensagens OPC UA Pub/Sub.

O módulo OPC Publisher exige apenas uma porta de saída https (443) e pode trabalhar com a infraestrutura corporativa existente.

## <a name="gateway-opc-proxy-module"></a>Módulo de proxy de gateway OPC

O módulo de Proxy OPC UA de Gateway encapsula mensagens de controle e comando OPC UA binário e requer apenas uma porta de saída https (443). Pode funcionar com a infraestrutura existente da empresa, inclusive Proxies da Web.

Usa métodos de Dispositivo de Hub IoT para transferir dados TCP/IP em pacotes na camada de aplicativo e, assim, garante a confiança do ponto de extremidade, a criptografia de dados e a integridade usando SSL/TLS.

O protocolo binário OPC UA retransmitido pelo próprio proxy usa criptografia e autenticação UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

O Módulo Gateway OPC Publisher assina nós de servidor OPC UA para detectar alterações nos valores de dados. Se for detectada uma alteração de dados em um de nós, esse módulo enviará mensagens ao Hub IoT do Azure.

O Hub IoT fornece uma fonte de eventos para o Azure TSI. O TSI armazena dados por 30 dias com base em carimbos de data/hora anexados às mensagens. Os dados incluem:

* ApplicationUri UA OPC
* NodeId UA OPC
* Valor do nó
* Carimbo de data/hora de origem
* DisplayName UA OPC

Atualmente, TSI não permite aos clientes personalizar por quanto tempo desejam manter os dados.

O TSI executa consultas em relação a dados de nó usando um SearchSpan (Time.From, Time.To) e agrega por OPC UA ApplicationUri, OPC UA NodeId ou OPC UA DisplayName.

Para recuperar os dados dos medidores OEE e KPI e os gráficos de série temporal, os dados são agregados por contagem de eventos, Sum, Avg, Min e Max.

A série temporal é criada usando um processo diferente. OEE e KPIs são calculados com base nos dados de estação base e transferidos para a topologia (linhas de produção, fábricas, empresa) no aplicativo.

Além disso, a série temporal para a topologia OEE e KPI é calculada no aplicativo, sempre que um período de tempo exibido está pronto. Por exemplo, o modo de exibição de dia é atualizado a cada hora cheia.

A exibição da série temporal dos dados de nó vem diretamente de TSI usando uma agregação para o período de tempo.

## <a name="iot-hub"></a>Hub IoT
O [hub IoT][lnk-IoT Hub] recebe os dados enviados do Módulo OPC Publisher para a nuvem e os disponibiliza para o serviço Azure TSI. 

O Hub IoT na solução também:
- Mantém um registro de identidade que armazena as IDs de todos os Módulos de Editor OPC e todos os Módulos de Proxy OPC.
- É usado como o canal de transporte para a comunicação bidirecional do Módulo de Proxy OPC.

## <a name="azure-storage"></a>Armazenamento do Azure
A solução usa o armazenamento de blobs do Azure como armazenamento em disco para a VM e para armazenar dados de implantação.

## <a name="web-app"></a>Aplicativo Web
O aplicativo Web implantado como parte da solução pré-configurada é composto de um cliente UA OPC integrado, processamento de alertas e visualização de telemetria.

## <a name="telemetry-data-flow"></a>Fluxo de dados de telemetria

![Fluxo de dados de telemetria](media/iot-suite-connected-factory-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Etapas do fluxo

1. O Publicador de OPC lê os certificados X509 de agente do usuário de OPC e as credenciais de segurança de Hub IoT necessários do repositório de certificados local.
    - Se necessário, o Publicador de OPC cria e armazena os certificados ou credenciais ausentes no repositório de certificados.

2. O Publicador de OPC se registra com o Hub IoT.
    - Usa o protocolo configurado. Pode usar qualquer protocolo com suporte do SDK do cliente do Hub IoT. O padrão é MQTT.
    - A comunicação do protocolo é protegida por TLS.

3. O Publicador de OPC lê o arquivo de configuração.

4. O Publicador de OPC cria uma sessão de OPC com cada servidor de agente do usuário de OPC configurado.
    - Usa a conexão TCP.
    - O Publicador de OPC e o servidor de agente do usuário de OPC autenticam-se um ao outro usando certificados X509.
    - Todo o tráfego de agente do usuário de OPC adicional é criptografado com o mecanismo de criptografia de agente do usuário de OPC configurado.
    - O Publicador de OPC cria, na sessão OPC para cada intervalo de publicação configurado, uma assinatura de OPC.
    - Cria itens monitorados de OPC para os nós de OPC publicarem na assinatura de OPC.

5. Se um valor de nó de OPC monitorado é alterado, o servidor de agente do usuário de OPC envia atualizações para o Publicador de OPC.

6. O Publicador de OPC transcodifica o novo valor.
    - Processa em lotes várias alterações se o envio em lote estiver habilitado.
    - Cria uma mensagem de Hub IoT.

7. O Publicador de OPC envia uma mensagem para o Hub IoT.
    - Usa o protocolo configurado.
    - A comunicação é protegida por TLS.

8. O Time Series Insights (TSI) lê as mensagens do Hub IoT.
    - Usa o AMQP sobre TCP/TLS.
    - Esta etapa é interna para o datacenter.

9. Dados em repouso em TSI.

10. O WebApp do fábrica conectada ao Serviço de Aplicativo Azure consulta os dados necessários a partir do TSI.
    - Usa comunicação protegida por TCP/TLS.
    - Esta etapa é interna para o datacenter.

11. O navegador da Web se conecta ao WebApp da fábrica conectada.
    - Renderiza o painel da fábrica conectada.
    - Conecta-se via HTTPS.
    - O acesso ao Aplicativo da fábrica de conectada aplicativo requer a autenticação do usuário por meio do Azure Active Directory.
    - Qualquer chamada do WebApi para o aplicativo da fábrica conectada é protegida por Anti-Forgery-Tokens.

12. Nas atualizações de dados, o WebApp da fábrica conectada envia dados atualizados para o navegador da Web.
    - Usa o protocolo SignalR.
    - Protegido por TCP/TLS.

## <a name="browsing-data-flow"></a>Navegação de fluxo de dados

![Navegação de fluxo de dados](media/iot-suite-connected-factory-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Etapas do fluxo

1. O proxy de OPC (componente do servidor) é iniciado.
    - Lê as chaves de acesso compartilhado de um repositório local.
    - Se necessário, armazena as chaves de acesso ausentes no repositório.

2. O proxy de OPC (componente do servidor) se registra com o Hub IoT.
    - Lê todos os dispositivos conhecidos do Hub IoT.
    - Usa MQTT sobre TLS sobre Socket ou Secure Websocket.

3. O navegador da Web conecta-se ao WebApp da fábrica conectada e renderiza o painel de fábrica conectado.
    - Usa HTTPS.
    - Um usuário seleciona um servidor de agente do usuário de OPC para se conectar-se a ele.

4. O WebApp da fábrica conectada estabelece uma sessão de agente do usuário de OPC para o servidor de agente do usuário de OPC selecionado.
    - Usa a pilha de agente do usuário de OPC.

5. O transporte de proxy de OPC recebe uma solicitação da pilha de agente do usuário de OPC para estabelecer uma conexão de soquete TCP ao servidor de agente do usuário de OPC.
    - Ele apenas recupera o conteúdo de TCP e usa-o inalterado.
    - Esta etapa é interna para a WebApp da fábrica conectada.

6. O proxy de OPC (componente do cliente) procura o dispositivo de Proxy de OPC (componente do servidor) no registro de dispositivo do Hub IoT. Em seguida, chama um método de dispositivo do dispositivo do proxy de OPC (componente do servidor) no Hub IoT.
    - Usa HTTPS sobre TCP/TLS para procurar o proxy de OPC.
    - Usa HTTPS sobre TCP/TLS para estabelecer uma conexão de soquete TCP para o servidor de agente do usuário de OPC.
    - Esta etapa é interna para o datacenter.

7. O Hub IoT chama um método de dispositivo do dispositivo do proxy de OPC (componente do servidor).
    - Usa um MQTT estabelecido sobre TLS pela conexão Socket ou Secure Websocket para estabelecer uma conexão de soquete TCP para o servidor de agente do usuário de OPC.

8. O proxy de OPC (componente de servidor) envia o conteúdo TCP para a rede de chão de fábrica.

9. O servidor de agente do usuário de OPC processa o conteúdo e envia a resposta.

10. A resposta é recebida pelo soquete do proxy de OPC (componente de servidor).
    - O proxy de OPC envia os dados como o valor retornado do método de dispositivo para o Hub IoT e para o proxy de OPC (componente do cliente).
    - Esses dados são entregues para a pilha de agente do servidor de OPC no aplicativo da fábrica conectada.

11. O WebApp da fábrica conectada WebApp retorna a experiência do usuário do navegador de OPC enriquecida com as informações específicas de agente do usuário de OPC recebidas do servidor de agente do usuário de OPC para o navegador da Web para fazer a renderização.
    - Ao procurar o espaço de endereço de OPC e aplicar funções em nós de espaço de endereço de OPC, a parte do cliente da experiência de usuário do navegador de OPC usa chamadas AJAX sobre HTTPS protegidas com Anti-Forgery Tokens para obter dados do WebApp da fábrica conectada.
    - Se necessário, o cliente usa a comunicação explicada nas etapas 4 a 10 para trocar informações com o servidor de agente do usuário de OPC.

> [!NOTE]
> O proxy de OPC (componente do servidor) e o proxy de OPC Proxy (componente do cliente) concluem as etapas de 4 a 10 para todo o tráfego TCP relacionado à comunicação de agente do servidor de OPC.

> [!NOTE]
> Para o servidor de agente do usuário de OPC e a pilha de agente do usuário de OPC no WebApp da fábrica conectada, a comunicação de Proxy de OPC é transparente e todos os recursos de segurança de agente do usuário de OPC para autenticação e criptografia são aplicáveis.

## <a name="next-steps"></a>Próximas etapas

Você pode continuar a introdução ao IoT Suite lendo os seguintes artigos:

* [Permissões no site azureiotsuite.com][lnk-permissions]
* [Implantar um gateway no Windows ou Linux para a solução pré-configurada de fábrica conectada](iot-suite-connected-factory-gateway-deployment.md)
* [Implementação de referência do Publicador de OPC](iot-suite-connected-factory-publisher.md).

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
