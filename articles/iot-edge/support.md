---
title: Sistemas operacionais compatíveis, mecanismos de contêiner – Azure IoT Edge | Microsoft Docs
description: Saiba quais sistemas operacionais podem executar o tempo de execução e o daemon do Azure IoT Edge, bem como os mecanismos de contêiner compatíveis com os seus dispositivos de produção
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/17/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: d8059ac4965ce5582b899ebc0d765e00ae9deb35
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55892789"
---
# <a name="azure-iot-edge-supported-systems"></a>Sistemas compatíveis com o Azure IoT Edge

Há várias maneiras de buscar suporte para o produto Azure IoT Edge.

**Relatar bugs** – A maioria do desenvolvimento direcionado ao produto Azure IoT Edge ocorre no projeto de software livre do IoT Edge. Bugs podem ser relatados na [página de problemas](https://github.com/azure/iotedge/issues) do projeto. As correções passam rapidamente do projeto para as atualizações do produto.

**Equipe de suporte ao Cliente Microsoft** – Usuários que tenham um [plano de suporte](https://azure.microsoft.com/support/plans/) podem acionar a equipe de suporte ao Cliente Microsoft, criando um tíquete de suporte diretamente do [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac).

**Solicitações de recursos** – O produto Azure IoT Edge rastreia solicitações de recursos por meio da [página Voz do Usuário](https://feedback.azure.com/forums/907045-azure-iot-edge).

## <a name="container-engines"></a>Mecanismos de contêiner
O Azure IoT Edge precisa de um mecanismo de contêiner para iniciar os módulos, pois eles são implementados como contêineres. A Microsoft fornece um mecanismo de contêiner, moby-engine, para atender a esse requisito. Ele é baseado no projeto de software livre Moby. Docker CE e Docker EE são outros mecanismos de contêineres populares. Também são baseados no projeto de software livre Moby e são compatíveis com Azure IoT Edge. A Microsoft fornece o melhor suporte ao esforço para sistemas que usam esses mecanismos de contêiner, no entanto, a Microsoft não tem capacidade de enviar correções para problemas inerentes a esses mecanismos. Por esse motivo, a Microsoft recomenda o uso de mecanismos móveis em sistemas de produção.

<br>
<center>
![Moby como tempo de execução do contêiner](./media/support/only-moby-for-production.png)
</center>

## <a name="operating-systems"></a>Sistemas operacionais
O Azure IoT Edge executa na maioria dos sistemas operacionais que podem executar contêineres, no entanto, todos estes igualmente não têm suporte. Os sistemas operacionais são agrupados em camadas que representam o nível de suporte que os usuários podem esperar.
* Sistemas de camada 1 podem ser considerados como oficialmente com suporte. Isso significa que a Microsoft:
    * tem esse sistema operacional em testes automatizados
    * fornece pacotes de instalação aos sistemas
* Os sistemas da Camada 2 podem ser considerados compatíveis com o Azure IoT Edge e podem ser utilizados com relativa facilidade. Isso significa que:
    * A Microsoft fez testes ad-hoc nas plataformas ou conhece um parceiro executando o Azure IoT Edge com êxito na plataforma
    * Pacotes de instalação para outras plataformas podem funcionar nessas plataformas
    
A família do sistema operacional de host sempre deverá corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Isso significa que você somente poderá usar contêineres do Linux no Linux e contêineres do Windows no Windows. Ao usar Windows, haverá suporte apenas para contêineres isolados de processo e não para contêineres isolados do Hyper-V.  

<br>
<center>
![Sistema operacional de host corresponde ao sistema operacional convidado](./media/support/edge-on-device.png)
</center>

### <a name="tier-1"></a>Camada 1
Disponível para o público geral
| Sistema operacional | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Raspbian-stretch | Não  | Sim|
| Ubuntu Server 16.04 | Sim | Não  |
| Ubuntu Server 18.04 | Sim | Não  |

Versão prévia pública
| Sistema operacional | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| Windows 10 IoT Core build 17763 | Sim | Não  |
| Windows 10 IoT Enterprise build 17763 | Sim | Não  |
| Windows Server 2019 | Sim | Não  |

Os sistemas operacionais Windows acima são os requisitos para dispositivos que executam contêineres do Windows no Windows. Essa é a única configuração com suporte para produção. Os pacotes de instalação do Azure IoT Edge para Windows permitem o uso de contêineres do Linux no Windows, no entanto, isso é apenas para desenvolvimento e teste. O uso de contêineres do Linux no Windows não é uma configuração com suporte para produção. Qualquer versão do Windows 10 build 14393 ou mais recente e do Windows Server 2016 ou mais recente pode ser usada para esse cenário de desenvolvimento.

### <a name="tier-2"></a>Camada 2

| Sistema operacional | AMD64 | ARM32v7 |
| ---------------- | ----- | ----- |
| CentOS 7.5 | Sim | Sim |
| Debian 8 | Sim | Sim |
| Debian 9 | Sim | Sim |
| RHEL 7.5 | Sim | Sim |
| Ubuntu 18.04 | Sim | Sim |
| Ubuntu 16.04 | Sim | Sim |
| Wind River 8 | Sim | Não  |
| Yocto | Sim | Não  |


## <a name="virtual-machines"></a>Máquinas Virtuais
O Azure IoT Edge pode ser executado em máquinas virtuais. Isso é comum quando os clientes desejam aumentar a infraestrutura existente com inteligência de borda. A família do sistema operacional de host da VM deve corresponder à família do sistema operacional convidado usado dentro do contêiner de um módulo. Esse é o mesmo requisito quando o Azure IoT Edge executa diretamente em um dispositivo. O Azure IoT Edge é independente da tecnologia de virtualização subjacente e funciona em VMs desenvolvidas por plataformas como Hyper-V e vSphere.

<br>
<center>
![Azure IoT Edge em uma VM](./media/support/edge-on-vm.png)
</center>

## <a name="minimum-system-requirements"></a>Requisitos mínimos do sistema
O Azure IoT Edge executa perfeitamente em dispositivos tão pequenos quanto um Raspberry Pi3 até um hardware de nível de servidor. Escolher o hardware certo para o cenário depende muito das cargas de trabalho que você quer executar. Tomar a decisão final do dispositivo pode ser complicado, no entanto, é possível criar facilmente protótipos de uma solução em laptops ou desktops tradicionais.

A experiência de prototipagem ajudará a guiar a seleção final do dispositivo. As perguntas que você deve considerar incluem: quantos módulos compõem a carga de trabalho, quantas camadas os contêineres dos módulos compartilham, em qual linguagem os módulos são escritos, quantos dados os módulos estarão processando, os módulos precisam de hardware especializado para acelerar as cargas de trabalho, quais são as características de desempenho desejadas da sua solução, qual é o seu orçamento de hardware?
