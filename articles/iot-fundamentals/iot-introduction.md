---
title: Introdução ao Azure e IoT (Internet das Coisas)
description: Visão geral da IoT do Azure e dos serviços nela contidos
author: robinsh
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 10/09/2018
ms.author: robinsh
ms.openlocfilehash: d7e5949be9ea09f94ff6ee86c9f996f2e203eac7
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49069430"
---
# <a name="introduction-to-azure-and-the-internet-of-things"></a>Introdução ao Azure e Internet das Coisas

O Azure IoT consiste em três áreas de tecnologia e soluções: soluções, serviços da plataforma e borda, todas projetadas para facilitar o desenvolvimento de ponta a ponta do seu aplicativo de IoT. Este artigo começa descrevendo as características comuns de uma solução de IoT na nuvem, seguido por uma visão geral de como o Azure IoT aborda os desafios em projetos de IoT e por que você deve considerar a adoção do Azure IoT.

## <a name="iot-solution-architecture"></a>Arquitetura da solução de IoT

Uma solução de IoT exige uma comunicação bidirecional segura entre dispositivos, possivelmente contabilizando milhões, e um back-end da solução. Por exemplo, uma solução pode usar análise preditiva automatizada para revelar insights do fluxo de eventos do dispositivo para a nuvem. 

O diagrama abaixo mostra os elementos-chave de uma arquitetura comum da solução IoT. O diagrama é independente de detalhes específicos de implementação, como os serviços do Azure usados e os sistemas operacionais do dispositivo. Nessa arquitetura, os dispositivos IoT coletam dados que eles enviam para um gateway de nuvem. O gateway de nuvem disponibiliza os dados para processamento por outros serviços de back-end. Esses serviços de back-end podem fornecer dados para:

* Outros aplicativos de linha de negócios.
* Operadores humanos por meio de um painel ou outro dispositivo de apresentação.

![Arquitetura da solução de IoT](./media/iot-introduction/iot-reference-architecture.png)

> [!NOTE]
> Para ver uma análise detalhada da arquitetura IoT, confira a [Arquitetura de Referência do IoT do Microsoft Azure](https://aka.ms/iotrefarchitecture).

### <a name="device-connectivity"></a>Conectividade do dispositivo

Em uma arquitetura de solução de IoT, dispositivos normalmente enviam telemetria para a nuvem para armazenamento e processamento. Por exemplo, em um cenário de manutenção preditiva, o back-end da solução pode usar a transmissão de dados do sensor para determinar quando uma bomba específica necessita de manutenção. Os dispositivos também podem receber e responder às mensagens de nuvem para o dispositivo lendo mensagens de um ponto de extremidade de nuvem. No mesmo exemplo, o back-end da solução pode enviar mensagens para que outras bombas na estação de bombeamento comecem a rotear de novo os fluxos antes de a manutenção devida iniciar. Esse procedimento assegura que o engenheiro de manutenção possa começar assim que chegar.

Conectar os dispositivos de modo seguro e confiável geralmente é o maior desafio em soluções de IoT. Isso porque os dispositivos IoT têm características diferentes em comparação com outros clientes, como navegadores e aplicativos móveis. Especificamente, os dispositivos IoT:

* Frequentemente são sistemas internos sem operadores humanos (diferente de um telefone).
* Eles podem ser implantados em locais remotos, nos quais o acesso físico é caro.
* Só podem ser acessados por meio do back-end da solução. Não há outra maneira de interagir com o dispositivo.
* Podem ter recursos de energia e de processamento limitados.
* Podem ter conectividade de rede intermitente, lenta ou cara.
* Talvez precisem de protocolos de aplicativo proprietários, personalizados ou específicos do setor.
* Podem ser criados usando um grande conjunto de plataformas de hardware e de software conhecidas.

Além das restrições anteriores, qualquer solução de IoT também deve ser escalonável, segura e confiável.

Dependendo do protocolo de comunicação e da disponibilidade de rede, um dispositivo pode se comunicar com a nuvem diretamente ou por meio de um gateway intermediário. Arquiteturas de IoT geralmente têm uma combinação desses dois padrões de comunicação.

### <a name="data-processing-and-analytics"></a>Processamento de dados e análise

Em soluções de IoT modernas, o processamento de dados pode ocorrer na nuvem ou no dispositivo. O processamento no dispositivo é conhecido como *Computação de borda*. A escolha de onde processar os dados depende de fatores como:

* Restrições de rede. Se a largura de banda entre os dispositivos e a nuvem for limitada, há um incentivo para realizar mais processamento de borda.
* Tempo de resposta. Se houver um requisito para agir em um dispositivo em tempo quase real, talvez seja melhor processar a resposta no próprio dispositivo. Por exemplo, um braço robô que precisa ser interrompido em caso de emergência.
* Ambiente de normas. Alguns dados não podem ser enviados para a nuvem.

Em geral, o processamento de dados na borda e na nuvem são uma combinação dos seguintes recursos:

* Receber telemetria em escala de seus dispositivos e determinar como processar e armazenar os dados.
* Analisar a telemetria para fornecer insights, sejam eles em tempo real ou após o fato.
* Enviar comandos da nuvem ou de um dispositivo de gateway para um dispositivo específico.

Além disso, um back-end de nuvem de IoT deve fornecer:

* Recursos de registro de dispositivo que permite que você:
    * Provisione dispositivos.
    * Controle quais dispositivos têm permissão para se conectar à sua infraestrutura.
* Gerenciamento do dispositivo para controlar o estado dos dispositivos e monitorar suas atividades.

Por exemplo, em cenário de manutenção preditiva, o back-end da nuvem armazena dados telemétricos históricos. A solução usa esses dados para identificar comportamentos anormais potenciais em bombas específicas antes que causem um problema real. Usando a análise de dados, é possível identificar que a solução preventiva é enviar um comando de volta para o dispositivo para realizar uma ação corretiva. Esse processo gera um loop de comentários automatizado entre o dispositivo e a nuvem, o que aumenta a eficiência da solução.

### <a name="presentation-and-business-connectivity"></a>Conectividade de negócios e apresentação

A camada de conectividade de negócios e apresentação permite que os usuários finais interajam com os dispositivos e solução IoT. Ela permite aos usuários exibir e analisar os dados coletados de seus dispositivos. Esses modos de exibição podem assumir a forma de painéis ou relatórios de BI, que podem exibir tanto os dados históricos quanto os dados quase em tempo real. Por exemplo, um operador pode verificar o status de estações de bombeamento específicas e ver quaisquer alertas gerados pelo sistema. Essa camada também permite a integração do back-end da solução de IoT com aplicativos de linhas de negócios existentes para se ligar a processos ou fluxos de trabalho empresarial. Por exemplo, uma solução de manutenção preditiva pode ser integrada a um sistema de agendamento para programar a visita de um engenheiro em uma estação de bombeamento quando identificar uma bomba que precise de manutenção.

## <a name="why-azure-iot"></a>Por que o Azure IoT?

O Azure IoT simplifica a complexidade dos projetos de IoT e aborda desafios como segurança, incompatibilidade de infraestrutura e dimensionamento da sua solução de IoT. Veja como:

### <a name="agile"></a>Agile

Acelere sua jornada de IoT.

* Escala: comece pequeno, cresça para o tamanho que quiser e em qualquer lugar — milhões de dispositivos, terabytes de dados na maioria das regiões de todo o mundo.

* Aberta: use o que tem ou modernize-se para o futuro conectando-se a qualquer dispositivo, software ou serviço.

* Híbrida: crie de acordo com suas necessidades com a implantação da sua solução de IoT na borda, na nuvem ou em qualquer outro lugar.

* Ritmo: implante com mais rapidez, acelere o tempo de colocação no mercado e mantenha-se à frente da concorrência com o líder em aceleradores de soluções e ritmo de inovação em IoT.

### <a name="comprehensive"></a>Abrangente

Gere impacto para os seus negócios.

* Completa: a Microsoft é o único provedor de soluções de IoT com uma plataforma completa que vai desde dispositivos até a nuvem, passando por big data, análises avançadas e com serviços gerenciados.

* Parceira para o sucesso: aproveite o potencial do maior ecossistema de parceria do mundo e dê vida à linha de negócios e tecnologia por toda a indústria e em todo o mundo.

* Controlada por dados: IoT diz respeito a dados e as melhores soluções de IoT reúnem todas as ferramentas necessárias para armazenar, interpretar, transformar, analisar e apresentar os dados ao usuário certo, no lugar certo, no momento certo.

* Centrada no dispositivo: a IoT da Microsoft permite se conectar a tudo, desde equipamentos herdados a um amplo ecossistema de hardwares certificados e a possibilidade de desenvolver seus próprios dispositivos em sistemas incorporados, móveis e de borda.

### <a name="secure"></a>Segurança

Vença o maior desafio de IoT: a segurança.

* Capacite: com o Microsoft IoT, você pode reunir sua visão, com a tecnologia, as práticas recomendadas e os recursos para resolver o maior problema de IoT – segurança.

* Agir: proteja seus dados de IoT e gerencie os riscos com o gerenciamento de identidade e acesso, proteção de informações e contra ameaças e gerenciamento de segurança.

* Tranquilidade: garanta a segurança de informações confidenciais em todos os dispositivos, softwares, aplicativos e serviços de nuvem, bem como em ambientes locais.

* Conformidade: a Microsoft é líder do setor na definição de requisitos de segurança que atendem a um amplo conjunto de padrões internacionais e específicos de setores para dispositivos, dados e serviços de IoT.

## <a name="next-steps"></a>Próximas etapas

Explore as seguintes áreas de tecnologias e soluções.

**Soluções**

* [Aceleradores de solução do IoT](/azure/iot-suite)
* [Centro de IoT](/azure/iot-central)

**Serviços de plataforma**

* [Hub IoT](/azure/iot-hub)
* [Serviço de Provisionamento de Dispositivos no Hub IoT](/azure/iot-dps)
* [Mapas do Azure](/azure/azure-maps/)
* [Time Series Insights](/azure/time-series-insights)

**Microsoft Edge**

* [Visão geral de IoT Edge](/azure/iot-edge)
* [O que é o IoT Edge](/azure/how-iot-edge-works)
