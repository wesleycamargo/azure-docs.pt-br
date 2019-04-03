---
title: Ajuste de desempenho de TCP/IP para VMs do Azure | Microsoft Docs
description: Aprenda várias TCP/IP desempenho ajustes técnicas comuns e suas relações com VMs do Azure.
services: virtual-network
documentationcenter: na
author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
manager: paragk
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/30/2019
ms.author:
- rimayber
- dgoddard
- stegag
- steveesp
- minale
- btalb
- prachank
ms.openlocfilehash: c5d4f67e9c1e4e983133675c440b8c5b64183227
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851680"
---
# <a name="tcpip-performance-tuning-for-azure-vms"></a>TCP/IP ajuste de desempenho para VMs do Azure

A finalidade deste artigo é discutir comuns técnicas de ajuste de desempenho de TCP/IP e suas considerações para máquinas virtuais em execução no Microsoft Azure. É importante primeiro ter uma compreensão básica dos conceitos e, em seguida, discutir como eles podem ser ajustados.

## <a name="common-tcpip-tuning-techniques"></a>Técnicas comuns de ajuste de TCP/IP

### <a name="mtu-fragmentation-and-large-send-offload-lso"></a>MTU, fragmentação e enviar LSO (descarregamento grande)

#### <a name="explanation-of-mtu"></a>Explicação de MTU

A unidade de transmissão máxima (MTU) é o maior tamanho quadro (pacote), especificado em bytes, que podem ser enviados por um adaptador de rede. A MTU é uma definição configurável e o padrão que MTU usado em VMs do Azure e a configuração padrão na maioria dos dispositivos de rede globalmente, é 1500 bytes.

#### <a name="explanation-of-fragmentation"></a>Explicação de fragmentação

A fragmentação ocorre quando um pacote é enviado, que excede a MTU de uma interface de rede. O TCP/IP stack interromperá o pacote em partes menores (fragmentos) que estão em conformidade com as interfaces de MTU. A fragmentação ocorre na camada de IP e é independente do protocolo subjacente (como TCP). Quando um pacote de 2000 bytes é enviado por um adaptador de rede com uma MTU de 1500, em seguida, ele será ser dividido em um pacote de 1500 bytes e um pacote de 500 bytes.

Dispositivos de rede no caminho entre a origem e destino têm a opção para ignorar pacotes que excedem o MTU ou fragmentar o pacote em partes menores.

#### <a name="the-dont-fragment-df-bit-in-an-ip-packet"></a>O bit de "não Fragmentar" em um pacote IP

O bit de fragmento não é um sinalizador no cabeçalho de protocolo IP. Quando o bit DF estiver definido, ele indica que os dispositivos de rede intermediários no caminho entre o remetente e o receptor não devem fragmentar o pacote. Há muitas razões por que esse bit pode ser definido (consulte a seção de descoberta de caminho abaixo para obter um exemplo). Quando um dispositivo de rede recebe um pacote com o conjunto de bits de fragmento não e esse pacote ultrapassar a MTU da interface de dispositivos, o comportamento padrão é para o dispositivo remover o pacote e enviar um pacote de "ICMP fragmentação necessária" volta para a fonte original das pacote.

#### <a name="performance-implications-of-fragmentation"></a>Implicações de desempenho de fragmentação

Fragmentação pode ter implicações de desempenho negativo. Uma das principais razões para o impacto no desempenho é o impacto de CPU/memória de fragmentação e reagrupamento de pacotes. Quando um dispositivo de rede precisa de um pacote do fragmento, ele precisa alocar recursos de CPU/memória para executar a fragmentação. O mesmo deve acontecer quando o pacote é reorganizado. O dispositivo de rede deve armazenar todos os fragmentos até que elas são recebidas para que ele pode remontá-las para o pacote original. Esse processo de fragmentação/remontagem também pode causar a latência devido ao processo de fragmentação/remontagem.

A outra implicação de desempenho negativo possíveis de fragmentação é que os pacotes fragmentados podem chegar fora de ordem. Pacotes fora de ordem podem fazer com que certos tipos de dispositivos de rede para descartar os pacotes fora de ordem – que, em seguida, exige o pacote inteiro para ser retransmitida. Os cenários típicos para descartar fragmentos incluem dispositivos de segurança, como firewalls de rede ou quando um dispositivo de rede de recebimento do buffers são esgotados. Quando um dispositivo de rede de recebimento do buffers são esgotados, um dispositivo de rede está tentando remontar um pacote fragmentado, mas não tem os recursos para armazenar e reassume o pacote.

Fragmentação pode ser percebida como uma operação negativa, mas o suporte para fragmentação é necessária para conectar-se a diversas redes pela Internet.

#### <a name="benefits-and-consequences-of-modifying-the-mtu"></a>Benefícios e as consequências de modificar a MTU

Como uma instrução geral, aumentando a MTU pode criar uma rede mais eficiente. Todos os pacotes que são transmitidos tem informações adicionais de cabeçalho que são adicionadas ao pacote original. Pacote mais significa cabeçalho mais sobrecarga e a rede é menos eficiente como resultado.

Por exemplo, o tamanho do cabeçalho Ethernet é 14 bytes mais um 4 bytes Frame Check Sequence (FCS) para garantir a consistência do quadro. Se um pacote de byte de 2000 é enviado, 18 bytes de sobrecarga de Ethernet é adicionado na rede. Se o pacote é fragmentado em um pacote de 1500 bytes e um pacote de 500 bytes, cada pacote terá 18 bytes do cabeçalho de Ethernet - ou 36 bytes. Enquanto um único pacote de byte de 2000 teria apenas um cabeçalho de Ethernet de 18 bytes.

É importante observar que aumentar o MTU por si só não necessariamente criará uma rede mais eficiente. Se um aplicativo envia somente pacotes de 500 bytes, a sobrecarga de cabeçalho mesmo existirá se o MTU é 1500 bytes ou 9000 bytes. Em ordem para a rede para ser mais eficiente, em seguida, ele também deve usar tamanhos maiores de pacotes que são relativas a MTU.

#### <a name="azure-and-vm-mtu"></a>O Azure e MTU de VM

A MTU para VMs do Azure padrão é 1500 bytes. A pilha de rede Virtual do Azure tentará do fragmento de um pacote em bytes 1400. No entanto, a pilha de rede Virtual do Azure permitirá pacotes até 2006 bytes quando o bit "Não fragmentar" é definido no cabeçalho IP.

É importante observar que essa fragmentação não implica que a pilha de rede Virtual do Azure é inerentemente ineficiente porque ele pacotes em 1400 bytes de fragmentos, enquanto as VMs têm uma MTU de 1500. A realidade é que uma grande porcentagem de pacotes de rede é muito menor do que 1.500 bytes ou de 1400 bytes.

#### <a name="azure-and-fragmentation"></a>Azure e a fragmentação

Hoje, pilha de rede Virtual do Azure está configurada para descartar "Fora de ordem fragmentos" – que significa a pacotes fragmentados que não chegam na ordem original fragmentado. Esses pacotes são descartados, principalmente devido a uma vulnerabilidade de segurança de rede anunciada em novembro de 2018 chamado FragmentStack.

FragmentSmack é um defeito a maneira como o kernel do Linux manipulados reagrupamento de pacotes IPv4 e IPv6 fragmentados. Um invasor remoto pode usar essa falha às operações de remontagem fragmento caro disparador, levar a uma negação de serviço e de aumento de CPU no sistema de destino.

#### <a name="tune-the-mtu"></a>Ajustar a MTU

VMs do Azure dão suporte a uma MTU configurável assim como qualquer outro sistema operacional. No entanto, a fragmentação que ocorre dentro do Azure e é detalhada acima, deve ser considerada ao configurar a MTU.

Azure não incentivar os clientes a aumentar sua MTU de VM. Esta discussão destina-se a explicar em detalhes como o Azure implementa o MTU e executa a fragmentação hoje mesmo.

> [!IMPORTANT]
>Aumentando a MTU não tenha mostrado para melhorar o desempenho e pode ter um efeito negativo no desempenho do aplicativo.
>
>

#### <a name="large-send-offload-lso"></a>Descarregamento de envio grande LSO)

Enviar LSO (descarregamento grande) pode melhorar o desempenho de rede ao descarregar a segmentação de pacotes para o adaptador de Ethernet. Com LSO habilitado, o TCP/IP stack criará um pacote grande de TCP e, em seguida, enviar para o adaptador Ethernet para segmentação antes de encaminhá-lo. O benefício de LSO é que ele pode liberar a CPU da segmentação de pacotes no tamanho dos pacotes que estão em conformidade com a MTU e que o processamento para a interface de Ethernet em que ele é executado em hardware de descarregamento. Mais informações sobre os benefícios de LSO podem ser encontradas no [desempenho na documentação do adaptador de rede Microsoft](https://docs.microsoft.com/windows-hardware/drivers/network/performance-in-network-adapters#supporting-large-send-offload-lso).

Quando LSO estiver habilitada, os clientes do Azure poderão ver tamanhos grandes de quadro quando a captura de pacote de desempenho. Esses tamanhos de quadro grande podem levar alguns clientes a acreditar que a fragmentação ou um jumbo que MTU está sendo usado quando não for. Com LSO, o adaptador ethernet pode anunciar um MSS maior para a pilha de TCP/IP para criar um pacote maior de TCP. Todo esse quadro não segmentado é então encaminhado para o adaptador de Ethernet e deve ser visível em uma captura de pacotes executada na VM. No entanto, o pacote será dividido em muitas estruturas menores pelo adaptador Ethernet de acordo com o MTU do adaptador de Ethernet.

### <a name="tcpmss-window-scaling-and-pmtud"></a>Dimensionamento de janela TCP/MSS e PMTUD

#### <a name="explanation-of-tcp-mss"></a>Explicação de MSS TCP

Tamanho máximo do segmento do TCP (MSS) é uma configuração que se destina a definir o tamanho máximo de segmento TCP como para evitar a fragmentação de pacotes TCP. Sistemas operacionais geralmente definirá MSS como MSS = MTU - tamanho IP & cabeçalho de TCP (20 bytes ou total de 40 bytes). Portanto, uma interface com uma MTU de 1500 terá um MSS de 1460. No entanto, o MSS é configurável.

Essa configuração foi acordada no handshake TCP de três vias, quando uma sessão TCP é configurada entre uma origem e destino. Ambos os lados enviar um valor de MSS e o menor dos dois é usado para a conexão TCP.

Dispositivos de rede intermediários, como Gateways de VPN, incluindo o Gateway de VPN do Azure, têm a capacidade de ajustar a MTU independentemente da origem e destino para garantir um desempenho ideal de rede. Portanto, deve observar que a MTU da origem e destino sozinho não é os fatores exclusivos no valor de MSS real.

#### <a name="explanation-of-path-mtu-discovery-pmtud"></a>Explicação da descoberta MTU de caminho (PMTUD)

Enquanto o MSS é negociada, pode não indicar o MSS real que pode ser usado como outros dispositivos de rede no caminho entre a origem e destino podem ter um valor MTU menor do que a origem e destino. Nesse caso, o dispositivo cujo MTU é menor do que o pacote será descartar o pacote e devolver uma mensagem de controle mensagem ICMP (Internet Protocol) fragmentação necessária (tipo 3, 4 de código) que contém seu MTU. Esta mensagem ICMP permite que o host de origem reduzir seu MTU de caminho adequadamente. O processo é chamado de descoberta de MTU de caminho.

O processo de PMTUD é inerentemente ineficiente e tem implicações no desempenho da rede. Quando os pacotes são enviados que exceder uma MTU de caminhos de rede, esses pacotes devem ser retransmitidas com um menor MSS. Se o remetente não receber o pacote ICMP fragmentação necessária, talvez devido a um firewall de rede no caminho (conhecido como blackhole PMTUD), em seguida, o remetente não sabe que ele precisa reduzir o MSS e será continuamente retransmita o pacote. Por esse motivo, não recomendamos aumentar o MTU de VM do Azure.

#### <a name="vpn-considerations-with-mtu"></a>Considerações sobre VPN com MTU

Os clientes que usam as VMs que executam o encapsulamento (como VPNs IPSec) podem ter implicações adicionais para o tamanho do pacote e MTU. VPNs adicionar cabeçalhos adicionais serão adicionados ao pacote original, portanto, aumentar o tamanho do pacote e exigindo um MSS menores.

A recomendação atual para o Azure é definir a fixação de MSS TCP para 1350 bytes e a interface de túnel MTU para 1400. Mais informações podem ser encontradas na [VPN dispositivos e a página de parâmetros de IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

### <a name="latency-round-trip-time-and-tcp-window-scaling"></a>Latência, tempo de ida e volta e dimensionamento de janela TCP

#### <a name="latency-and-round-trip-time"></a>Tempo de latência e de ida e volta

Latência de rede é regida pela velocidade da luz em uma rede de fibra óptica. A realidade é, taxa de transferência de rede de TCP também é controlada com eficiência (práticos limites máximos) por causa do tempo de ida e volta (RTT) entre os dois dispositivos de rede.

| | | | |
|-|-|-|-|
|Rota|Distância|Tempo unidirecional|Tempo de ida e volta (RTT)|
|Nova Iorque para San Francisco|4,148 km|21 ms|42 ms|
|Nova York para Londres|5,585 km|28 ms|56 ms|
|Nova York para Sydney|15,993 km|80 ms|160 ms|

Esta tabela mostra a distância linear entre dois locais, no entanto, em redes, a distância é geralmente mais do que a distância em linha reta. É uma fórmula simples para calcular o mínimo de RTT conforme regido pela velocidade da luz: RTT mínimo = 2 * (quilômetros de distância / velocidade de propagação).

Um valor padrão de 200 pode ser usado para velocidade de propagação - valor é que a distância em metros luz viaja em 1 milissegundo.

No exemplo de Nova Iorque para San Francisco, é 4,148 km distância em linha reta. RTT mínimo = 2 * (4,148 / 20). A saída da equação será em milissegundos.

Como a distância física entre dois locais é uma realidade fixa, se o desempenho de rede máxima for necessário, é a opção mais lógica selecionar destinos com a menor distância entre eles. Em segundo lugar, as decisões de design dentro da rede virtual podem ser feitas para o caminho do tráfego de otimizar e reduzir a latência. Essas considerações de rede virtual são descritas na seção considerações de Design de rede abaixo.

#### <a name="latency-and-round-trip-time-effects-on-tcp"></a>Efeitos de tempo de latência e de ida e volta sobre TCP

Ida e volta RTT (tempo) tem um efeito direto na taxa de transferência máxima de TCP. O protocolo TCP tem um conceito de tamanho da janela. O tamanho da janela é a quantidade máxima de tráfego que pode ser enviado por uma conexão TCP antes que o remetente deve receber a confirmação do receptor. Se o MSS TCP é definido como 1460 e o tamanho da janela TCP é definido como a 65535, em seguida, o remetente pode enviar 45 pacotes antes que ele deve receber a confirmação do receptor. Se não for recebida a confirmação, em seguida, o remetente retransmitirá. Neste exemplo, o tamanho de janela TCP / MSS TCP = pacotes enviados. Ou 65535 / 1460 é arredondado até 45.

Esse estado de "aguardando confirmação", como um mecanismo para criar uma entrega confiável de dados, é o que causa o RTT afetar a taxa de transferência TCP com eficiência. Quanto mais tempo o remetente aguarda confirmação, quanto mais tempo também deve aguardar antes de enviar mais dados.

A fórmula para calcular a taxa de transferência máxima de uma única conexão de TCP é da seguinte maneira: Tamanho da janela / (latência RTT em milissegundos / 1000) = máximo de bytes/segundo. A tabela a seguir é formatada em Megabytes para facilitar a leitura e mostra o máximo em megabytes por segundo taxa de transferência de uma única conexão de TCP.

| | | | |
|-|-|-|-|
|Tamanho de janela TCP em Bytes|Latência RTT<br/>em milissegundos|Máximo<br/>Megabytes por segundo taxa de transferência|Máximo<br/> Megabits por segundo taxa de transferência|
|65535|1|65.54|524.29|
|65535|30|2.18|17.48|
|65535|60|1.09|8.74|
|65535|90|.73|5.83|
|65535|120|.55|4.37|

Se houver alguma perda de pacotes, em seguida, isso pode reduzir a taxa de transferência máxima de uma conexão TCP enquanto o remetente retransmite dados que já foram enviados.

#### <a name="explanation-of-tcp-window-scaling"></a>Explicação de dimensionamento de janela TCP

Dimensionamento de janela TCP é um conceito que aumenta dinamicamente o tamanho da janela TCP permitindo que mais dados a serem enviados antes que uma confirmação é necessária. Em nosso exemplo anterior, 45 pacotes seriam enviados antes de uma confirmação foi necessária. Se o número de pacotes que são enviados antes de uma confirmação é aumentada, em seguida, a taxa de transferência máxima de TCP também é aumentada, reduzindo o número de vezes que um remetente aguarda o reconhecimento.

Taxa de transferência TCP é demonstrada em uma tabela simple a seguir:

| | | | |
|-|-|-|-|
|Tamanho de janela TCP<br/>in Bytes|RTT latência em milissegundos|Máximo<br/>Megabytes por segundo taxa de transferência|Máximo<br/> Megabits por segundo taxa de transferência|
|65535|30|2.18|17.48|
|131,070|30|4.37|34.95|
|262,140|30|8.74|69.91|
|524,280|30|17.48|139.81|

No entanto, o valor do cabeçalho TCP para o tamanho de janela TCP é apenas 2 bytes de comprimento, que significa que o valor máximo para uma janela de recepção é 65535. Para aumentar o tamanho máximo da janela, um fator de dimensionamento de janela TCP foi introduzido.

O fator de escala também é uma configuração que pode ser configurada em um sistema operacional. A fórmula para calcular o tamanho da janela TCP usando os fatores de escala é da seguinte maneira: Tamanho de janela TCP = tamanho da janela TCP em Bytes \* (2 ^ fator de dimensionamento). Se o fator de dimensionamento de janela é 3 e o tamanho da janela de 65535, o cálculo é da seguinte maneira: 65535 \* (2 ^ 3) = 262,140 bytes. Um fator de escala de 14 resulta em um tamanho de janela TCP de 14 (o deslocamento máximo permitido), o tamanho da janela TCP será 1,073,725,440 bytes (8,5 gigabits).

#### <a name="support-for-tcp-window-scaling"></a>Suporte para dimensionamento de janela TCP

Windows tem a capacidade de definir diferente fatores de dimensionamento em uma base de tipo de conexão - existem várias classes de conexões (datacenter, internet e assim por diante). Você pode ver a classificação de conexão de dimensionamento de janela com o comando do powershell Get-NetTCPConnection.

```powershell
Get-NetTCPConnection
```

Você pode ver os valores de cada classe com o comando do powershell Get-NetTCPSetting.

```powershell
Get-NetTCPSetting
```

O tamanho de janela TCP inicial e o fator de dimensionamento de TCP podem ser definido no Windows por meio do comando do powershell Set-NetTCPSetting. Mais informações podem ser encontradas no [NetTCPSetting do conjunto de página](https://docs.microsoft.com/powershell/module/nettcpip/set-nettcpsetting?view=win10-ps)

```powershell
Set-NetTCPSetting
```

As configurações de TCP em vigor para AutoTuningLevel são da seguinte maneira.

| | | | |
|-|-|-|-|
|AutoTuningLevel|Fator de escala|Multiplicador de escala|Fórmula para<br/>calcular o tamanho máximo da janela|
|Desabilitado|Nenhum|Nenhum|Tamanho da janela|
|Restrito|4|2^4|Tamanho da janela * (2 ^ 4)|
|Altamente restrito|2|2^2|Tamanho da janela * (2 ^ 2)|
|Normal|8|2^8|Tamanho da janela * (2 ^ 8)|
|Experimental|14|2^14|Tamanho da janela * (2 ^ 14)|

Embora essas configurações são os mais prováveis afetar o desempenho de TCP, deve-se observar que muitos outros fatores através da Internet, fora do controle do Azure, também podem afetar o desempenho de TCP.

#### <a name="increase-mtu-size"></a>Aumentar o tamanho MTU

Uma lógica pergunta é "pode aumentar a MTU aumentar o desempenho de TCP como uma MTU maior significa uma maior MSS"? A resposta simples é – provavelmente não. Conforme discutido, há prós e contras em relação a tamanho do pacote que são aplicáveis, além de apenas o tráfego de TCP. Como discutido acima, os fatores mais importantes que afetam o desempenho de taxa de transferência TCP é RTT, perda de pacotes e tamanho de janela TCP.

> [!IMPORTANT]
> O Azure não recomenda que os clientes do Azure modifique o valor MTU padrão em máquinas virtuais.
>
>

### <a name="accelerated-networking-and-receive-side-scaling"></a>Rede acelerada e Receive Side Scaling

#### <a name="accelerated-networking"></a>Rede Acelerada

Funções de rede da máquina virtual foram historicamente intensivo sobre o convidado da VM e Host do hipervisor/de CPU. Todos os pacotes que transite por meio do host é processado no software pelo host de CPU – incluindo todos os a rede Virtual encapsulamento/de-capsulation. Assim, mais o tráfego que passa por host, em seguida, maior será a carga de CPU. E, se a CPU do host estiver ocupada com outras operações, em seguida, que também afeta taxa de transferência de rede e latência. Esse problema foi resolvido por meio da rede acelerada.

A rede acelerada fornece a latência de rede Ultra baixas consistentes por meio de hardware programável interno do Azure e tecnologias como SR-IOV. Movendo grande parte da pilha de rede definida pelo software do Azure desativar as CPUs e em SmartNICs baseada em FPGA, computação ciclos são recuperados por aplicativos de usuário final, colocando menos carga na VM, diminuindo a tremulação e a inconsistência na latência. Em outras palavras, o desempenho pode ser mais determinista.

A rede acelerada atinge melhorias de desempenho, permitindo que a VM convidada ignorar o host e estabelecer um caminho de dados diretamente com SmartNIC do host. Benefícios da rede acelerada são:

- **Latência menor / maior pacotes por segundo (pps)**: remover o comutador virtual do caminho de dados elimina o tempo que os pacotes gastam no host para processamento da política e aumenta o número de pacotes que podem ser processados dentro da VM.

- **Tremulação reduzida**: o processamento de comutador virtual depende da quantidade de política que precisa ser aplicada e da carga de trabalho da CPU que está fazendo o processamento. O descarregamento da imposição de política para o hardware remove essa variabilidade ao entregar pacotes diretamente à VM, removendo a comunicação do host para a VM e todas as interrupções e mudanças de contexto de software.

- **Menor utilização da CPU**: ignorar o comutador virtual no host resulta em menor utilização da CPU para processar o tráfego de rede.

A rede acelerada deve ser habilitada explicitamente em uma base por VM. Instruções para habilitar a rede acelerada em uma VM estão disponíveis na [criar uma máquina virtual Linux com rede acelerada página](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).

#### <a name="receive-side-scaling-rss"></a>Receive Side Scaling (RSS)

RSS é uma tecnologia de driver de rede que distribui o recebimento do tráfego de rede com mais eficiência com a distribuição de processamento de recebimento entre várias CPUs em um sistema com vários processadores. Em termos simples, o RSS permite que um sistema processe uma maior quantidade de tráfego recebido, pois ela usa todas as CPUs disponíveis, em vez de apenas um. Uma discussão mais técnica de RSS pode ser encontrada na [Introdução ao Receive Side Scaling página](https://docs.microsoft.com/windows-hardware/drivers/network/introduction-to-receive-side-scaling).

RSS é necessária para alcançar desempenho máximo quando a rede acelerada está habilitada em uma máquina virtual. Também pode haver benefícios em usar o RSS em VMs que não têm a rede acelerada habilitada. Uma visão geral de como determinar se o RSS está habilitado e configuração para habilitá-la pode ser encontrada na [otimizar a taxa de transferência de rede para máquinas virtuais do Azure página](http://aka.ms/FastVM).

### <a name="tcp-time-wait-and-time-wait-assassination"></a>Tempo e espera de tempo TCP esperam Assassination

Outro problema comum que afeta o desempenho de rede e de aplicativo é a configuração de espera de tempo de TCP. Em VMs ocupadas que são de abertura e fechamento muitos soquetes, como um cliente ou servidor (fonte IP:Source porta + porta IP:Destination de destino), durante a operação normal do TCP, um soquete determinado pode terminar em um estado de espera de tempo para uma quantidade significativa de tempo. Esse estado de "tempo de espera", destina-se para permitir que todos os dados adicionais a serem entregues em um soquete antes de fechá-lo. Portanto, as pilhas de TCP/IP geralmente evitar a reutilização de um soquete soltando silenciosamente o pacote TCP SYN de clientes.

Essa quantidade de tempo de um soquete está em estado de espera é configurável, mas pode variar de 30 segundos para 240 segundos de tempo. Soquetes são um recurso finito, e o número de soquetes que pode ser usado em um determinado momento é configurável (o número geralmente se encontra 30.000 em torno de soquetes possíveis). Se esse número é esgotado, ou os clientes e servidores têm configurações de espera de hora incompatíveis e uma VM tentar reutilizar um soquete em estado de espera de tempo, em seguida, novas conexões falhará, pois pacotes TCP SYN são descartadas silenciosamente.

Normalmente, o valor de intervalo de portas para soquetes, bem como as configurações de TCP tempo aguardar e reutilização de soquete são configuráveis na pilha de TCP/IP de um sistema operacional. Alterar esses números podem potencialmente melhorar a escalabilidade, mas, dependendo do cenário, poderia introduzir problemas de interoperabilidade e deve ser alterada com cuidado.

Um recurso chamado tempo de espera Assassination foi introduzido para resolver isso, limitação de dimensionamento. Assassination de espera de tempo permite que um soquete ser reutilizado em determinados cenários, como quando o número de sequência no pacote IP de que a nova conexão excede o número de sequência do último pacote da conexão anterior. Nesse caso, o sistema operacional permitirá que a nova conexão seja estabelecida (aceitar o novo SYN ACK) e feche o força a conexão anterior que foi no tempo de espera estado. Esse recurso tem suporte em VMs do Windows no Azure hoje mesmo e suporte dentro de outras VMs deve ser investigado pelos clientes do Azure com os respectivos fornecedores de sistema operacional.

Documentação sobre como configurar as configurações de TCP tempo aguardar e intervalo de porta de origem está disponível na [as configurações que podem ser modificadas para página de melhorar o desempenho da rede](https://docs.microsoft.com/biztalk/technical-guides/settings-that-can-be-modified-to-improve-network-performance).

## <a name="virtual-network-factors-that-can-affect-performance"></a>Fatores de rede virtuais que podem afetar o desempenho

### <a name="vm-maximum-outbound-throughput"></a>Produtividade de saída máxima de VM

O Azure oferece uma variedade de tamanhos e tipos de VM, cada um com uma combinação diferente de funcionalidades de desempenho. Um tal recurso de desempenho é rede taxa de transferência (ou largura de banda), medida em megabits por segundo (Mbps). Como as máquinas virtuais são hospedadas em um hardware compartilhado, a capacidade de rede deve ser compartilhada adequadamente entre as máquinas virtuais que compartilham o mesmo hardware. Máquinas virtuais maiores recebem relativamente mais largura de banda que máquinas virtuais menores.

A largura de banda de rede alocada a cada máquina virtual é limitada no tráfego de saída (saída) da máquina virtual. Todo o tráfego de rede que deixa a máquina virtual é contado para o limite alocado, independentemente do destino. Por exemplo, se uma máquina virtual tem um limite de 1.000 Mbps, esse limite se aplica se o tráfego de saída destina-se a outra máquina virtual na mesma rede virtual ou fora do Azure.
A entrada não é limitada diretamente. No entanto, há outros fatores, como limites de CPU e armazenamento, que podem afetar a capacidade de uma máquina virtual de processar dados de entrada.

Rede acelerada é um recurso projetado para melhorar o desempenho de rede, incluindo latência, taxa de transferência e utilização da CPU. Embora a rede acelerada possa melhorar a taxa de transferência de uma máquina virtual, poderá fazê-lo somente até a máquina de virtual alocados a largura de banda.

As máquinas virtuais do Azure devem ter um, mas podem ter vários adaptadores de rede anexados a elas. A largura de banda alocada a uma máquina virtual é a soma de todo o tráfego de saída em todos os adaptadores de rede anexados a uma máquina virtual. Em outras palavras, a largura de banda alocada é por máquina virtual, independentemente de quantos adaptadores de rede estão anexados à máquina virtual.
 
Taxa de transferência de saída esperada e o número de interfaces de rede cada tamanho de VM com suporte é detalhada aqui. Para ver a taxa de transferência máxima, selecione um tipo, como de uso geral e uma série de tamanho na página resultante, como a série Dv2. Cada série tem uma tabela com as especificações na última coluna intitulada, máximo de NICs de rede / desempenho de rede (Mbps) esperado.

O limite de taxa de transferência se aplica à máquina virtual. A taxa de transferência não é afetada pelos seguintes fatores:

- **Número de adaptadores de rede**: O limite de largura de banda é cumulativo de todo o tráfego de saída da máquina virtual.

- **Rede acelerada**: Embora o recurso pode ser útil para alcançar o limite publicado, ele não altera o limite.

- **Destino do tráfego**: Todos os destinos contam para o limite de saída.

- **Protocolo**: Todo o tráfego de saída em todos os protocolos conta para o limite.

Um [tabela de largura de banda máxima por tipo de VM pode ser encontrada ao visitar esta página](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e clicando no respectivo tipo VM. Em cada página de tipo, uma tabela mostrará as NICs máximo e a largura de banda máxima esperada da rede.

Para obter mais informações sobre a largura de banda de rede de VM podem ser encontradas no [largura de banda de rede de máquina Virtual](http://aka.ms/AzureBandwidth).

### <a name="internet-performance-considerations"></a>Considerações sobre o desempenho da Internet

Como discutido neste artigo, fatores na Internet e fora do controle do Azure podem afetar o desempenho da rede. Esses fatores são:

- **Latência**: O tempo de ida e volta entre dois destinos pode ser afetado por problemas em redes intermediários, o tráfego não levando a distância "mais curta" caminhos de emparelhamento possíveis e abaixo do ideal caminho

- **Perda de pacotes**: Perda de pacotes pode ser causada pelo congestionamento de rede, problemas de caminho físico e dispositivos de rede em execução

- **Fragmentação da tamanho MTU**: A fragmentação ao longo do caminho pode levar a atrasos na chegada de dados ou pacotes que chegam fora de ordem, que podem afetar a entrega de pacotes

Rastreamento de rotas é uma boa ferramenta para medir as características de desempenho de rede (como perda de pacotes e latência) ao longo de cada caminho de rede entre um dispositivo de origem e de destino.

### <a name="network-design-considerations"></a>Considerações de design de rede

Junto com as considerações acima, a topologia de uma rede Virtual pode afetar o desempenho de rede Virtual. Por exemplo, um hub e spoke design que tráfego backhauls globalmente para uma rede virtual de hub único apresentará latência de rede e, portanto, afetar o desempenho geral da rede. Da mesma forma, o número de dispositivos de rede que passa do tráfego de rede também pode afetar a latência geral. Por exemplo, em um design de hub e spoke, se o tráfego está passando por meio de um spoke dispositivo de rede Virtual e um dispositivo de Hub Virtual antes de trânsito com a Internet, em seguida, latência pode ser introduzida por dispositivos de rede Virtual.

### <a name="azure-regions-virtual-networks-and-latency"></a>Regiões do Azure, redes virtuais e latência

Regiões do Azure são compostas de vários data centers que existem dentro de uma área geográfica geral. Esses datacenters não estejam fisicamente próximos uns dos outros e, em alguns casos, pode ser separados por até 10 km. A rede Virtual é uma sobreposição lógica na parte superior da rede do Centro de dados físicos do Azure e uma rede Virtual não implica em qualquer topologia de rede específico dentro do data center. Por exemplo, A VM e B de VM estão na mesma rede Virtual e sub-rede, mas podem estar em diferentes racks, linhas ou até mesmo datacenters. Eles podem ser separados por metros de cabo de fibra ótica ou quilômetros do cabo de fibra óptica. Essa realidade pode introduzir latência variável (diferença de alguns milissegundos) entre diferentes VMs.

Esse posicionamento geográfico e, portanto, a latência entre duas VMs, podem ser influenciados por meio da configuração de zonas de disponibilidade e conjuntos de disponibilidade, no entanto, a distância entre data centers em uma região é específico da região e predominantemente influenciou por topologia de data center na região.

### <a name="source-nat-port-exhaustion"></a>Esgotamento de porta NAT de origem

Uma implantação no Azure pode se comunicar com pontos de extremidade fora do Azure na Internet pública e/ou espaço de IP público. Quando uma instância inicia essa conexão de saída, o Azure mapeia dinamicamente o endereço IP privado para um endereço IP público. Depois que esse mapeamento é criado, o tráfego de retorno para esse fluxo originado de saída também pode alcançar o endereço IP privado onde o fluxo foi originado.

Para cada conexão de saída, o balanceador de carga do Azure deve manter esse mapeamento para um determinado período de tempo. Com a natureza de vários locatário do Azure, manter esse mapeamento para cada fluxo de saída para cada VM pode consumir muitos recursos. Portanto, há limites que são definidos e com base na configuração da rede Virtual do Azure. Ou declarados com mais precisão - uma VM do Azure só pode fazer um determinado número de conexões de saída em um determinado momento. Quando esses limites são esgotados, a VM do Azure será impedida de fazer quaisquer conexões de saída ainda mais.

Esse comportamento, entretanto, é configurável. Para obter mais informações sobre [SNAT e SNAT da porta esgotamento], consulte [deste artigo](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections).

## <a name="measure-network-performance-on-azure"></a>Medir o desempenho de rede no Azure

Um número dos limites máximos de desempenho neste artigo está relacionado à latência de rede / ida e volta (RTT) de tempo entre duas VMs. Esta seção fornece algumas sugestões sobre como testar a latência/RTT, bem como o desempenho de TCP e desempenho da rede VM. Os valores de TCP/IP & rede discutidos acima podem ser ajustados e o desempenho testado usando as técnicas descritas abaixo. Os valores de latência, a MTU, MSS e tamanho da janela podem ser usados nos cálculos listados acima e máximos teóricos podem ser comparados com os valores reais observados durante o teste.

### <a name="measure-round-trip-time-and-packet-loss"></a>Medir o tempo de ida e volta e perda de pacotes

Desempenho de TCP depende intensamente RTT e a perda de pacotes. A maneira mais simples para medir o RTT e a perda de pacotes está usando o utilitário ping disponível no Windows e Linux. A saída do ping mostrará max/min/média latência entre uma origem e destino, bem como perda de pacotes. Por padrão, o ping usa o protocolo ICMP. Para testar TCP RTT, em seguida, PsPing pode ser usado. Saiba mais sobre o PsPing está disponível em [esse link](https://docs.microsoft.com/sysinternals/downloads/psping).

### <a name="measure-actual-throughput-of-a-tcp-connection"></a>Taxa de transferência real de uma conexão TCP medida

NTttcp é uma ferramenta que é usada para testar o desempenho de TCP de uma VM Linux ou Windows. Várias configurações de TCP podem ser ajustadas e os benefícios testado usando o NTttcp. Para obter mais informações sobre o NTttcp podem ser encontradas nos links abaixo.

- [Largura de banda/taxa de transferência (NTttcp) de teste](https://aka.ms/TestNetworkThroughput)

- [Utilitário NTttcp](https://gallery.technet.microsoft.com/NTttcp-Version-528-Now-f8b12769)

### <a name="measure-actual-bandwidth-of-a-virtual-machine"></a>Medida largura de banda real de uma máquina virtual

Teste de desempenho de diferentes tipos de VM, a rede acelerada e assim por diante, pode ser testado usando uma ferramenta chamada Iperf, também está disponível no Linux e Windows. O Iperf pode usar o TCP ou UDP para testar a taxa de transferência de rede geral. Testes de taxa de transferência TCP usando o Iperf são influenciados pelos fatores discutidos neste artigo (latência, tempo de resposta e assim por diante). Portanto, UDP pode produzem resultados melhores para simplesmente a taxa de transferência máxima de teste.

Informações adicionais podem ser encontradas abaixo:

- [Solucionando problemas de desempenho de rede de Expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-network-performance)

- [Como validar a taxa de transferência VPN para uma rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-validate-throughput-to-vnet)

### <a name="detect-inefficient-tcp-behaviors"></a>Detectar comportamentos TCP ineficientes

Os clientes do Azure podem ver os pacotes TCP com sinalizadores de TCP (SACK, DUP ACK, retransmissão e rápida de retransmissão) em capturas de pacotes que podem indicar problemas de desempenho de rede. Esses pacotes indicam especificamente ineficiências de rede como resultado de perda de pacotes. No entanto, perda de pacotes não é necessariamente devido a problemas de desempenho do Azure. Problemas de desempenho pode ser o resultado de aplicativo, sistema operacional ou outros problemas que podem não estar diretamente relacionados com a plataforma do Azure. Também é importante observar que alguns retransmissão ou ACKs duplicados em uma rede é normal – protocolos TCP foram criados para ser confiável. E, evidência desses pacotes de TCP em uma captura de pacote não indica necessariamente um problema de rede sistêmicos a menos que sejam excessivas.

No entanto, ele deve ser declarado com clareza que esses tipos de pacote são indicações de que a taxa de transferência TCP não está alcançando seu desempenho máximo – pelos motivos discutidos em outras seções.
