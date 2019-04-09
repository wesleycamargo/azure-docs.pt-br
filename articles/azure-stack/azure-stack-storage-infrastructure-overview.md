---
title: Gerenciar a infraestrutura de armazenamento para o Azure Stack | Microsoft Docs
description: Gerencie a infraestrutura de armazenamento para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: ''
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: 416d75b254d0fbe14a0b39e5ae77d09a48e548f6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271281"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Gerenciar a infraestrutura de armazenamento para o Azure Stack

*Aplicável a Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este artigo descreve a integridade e o status operacional dos recursos de infraestrutura de armazenamento do Azure Stack. Esses recursos incluem unidades de armazenamento e volumes. As informações neste tópico podem ser inestimáveis ao tentar solucionar vários problemas, como uma unidade não pode ser adicionada a um pool.

## <a name="understand-drives-and-volumes"></a>Entender os volumes e unidades

### <a name="drives"></a>Unidades

Azure Stack alimentado pelo software do Windows Server, define os recursos de armazenamento com uma combinação de espaços de armazenamento diretos (S2D) e Windows Server Failover Clustering para fornecer um alto desempenho, escalonável e de serviço de armazenamento resiliente.

Parceiros de sistema integrado de pilha do Azure oferecem diversas variações de solução, incluindo uma ampla gama de flexibilidade de armazenamento. No momento, você pode selecionar uma combinação dos três tipos de unidade: NVMe (Express de memória não volátil), SATA/SAS SSD (unidade de estado sólido), HDD (unidade de disco rígido).

Espaços de armazenamento diretos apresenta um cache para maximizar o desempenho de armazenamento. No dispositivo do Azure Stack com único ou vários tipos de unidades, espaços de armazenamento diretos usam automaticamente todas as unidades de "mais rápido" (NVMe &gt; SSD &gt; HDD) tipo para armazenar em cache. As unidades restantes são usadas para a capacidade. As unidades poderia ser agrupadas em um "todos os flash" ou "híbrida" implantação:

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image1.png)

Todos os flash aim implantações para maximizar o desempenho de armazenamento e não incluem unidades de disco rígido rotacional (HDD).

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image2.png)

Implantações híbridas ter como objetivo equilibrar o desempenho e capacidade ou para maximizar a capacidade e incluem discos rígidos rotacional (HDD).

O comportamento do cache é determinado automaticamente de acordo com os tipos de unidades que estão sendo armazenados em cache para. Ao armazenar em cache para unidades de estado sólido (como caching NVMe SSDS), apenas as gravações são armazenadas em cache. Isso reduz o desgaste de unidades de capacidade, reduzindo o tráfego cumulativo para as unidades de capacidade e estendendo o seu tempo de vida. Enquanto isso, como leituras não afete significativamente o tempo de vida do flash, e unidades de estado sólido universalmente oferecem baixa latência de leitura, leituras não estão em cache. Ao armazenar em cache para unidades de disco rígido (por exemplo, o SSDs para HDDs de cache), as leituras e gravações estão em cache, para fornecer latência de flash (geralmente ~ 10 vezes melhor) para ambos.

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image3.png)

Para obter a configuração disponível de armazenamento, você pode verificar o parceiro OEM de pilha do Azure (https://azure.microsoft.com/overview/azure-stack/partners/) para especificação detalhada.

> [!Note]  
> Dispositivo de pilha do Azure pode ser entregues em uma implantação híbrida, com unidades tanto HDD e SSD (ou NVMe). Mas as unidades do tipo mais rápido seriam usadas como unidades de cache, e todas as unidades restantes seriam usadas como unidades de capacidade como um pool. Os dados de locatário (blobs, tabelas, filas e discos) devem ser colocados em unidades de capacidade. Então, provisionamento de discos premium ou selecionando o tipo não significa que os objetos de conta de armazenamento premium têm garantia de ser alocado em SSD ou NVMe unidades e obter melhor desempenho.

### <a name="volumes"></a>Volumes

O *serviço de armazenamento* particiona o armazenamento disponível em volumes separados que são alocados para armazenar dados de sistema e de locatário. Os volumes combinam as unidades no pool de armazenamento para introduzir a tolerância a falhas, escalabilidade e benefícios de desempenho de espaços de armazenamento diretos.

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image4.png)

Há três tipos de volumes criados no pool de armazenamento do Azure Stack:

-   Infraestrutura: arquivos de host usados por máquinas virtuais de infraestrutura do Azure Stack e serviços principais.

-   Temp VM: host os discos temporários anexados às VMs do locatário e que os dados são armazenados desses discos.

-   Objeto Store: dados de locatário de host blobs, tabelas, filas e discos de VM de manutenção.

Em uma implantação de vários nó, você deverá ver três volumes de infraestrutura, enquanto o número de volumes de VM Temp e objeto Store é igual ao número de nós na implantação do Azure Stack:

-   Em uma implantação de quatro nós, há quatro volumes VM Temp iguais e quatro volumes de Store de objeto iguais.

-   Se você adicionar um novo nó ao cluster, haveria um novo volume para ambos os tipos de ser criado.

-   O número de volumes permanece igual, mesmo que um nó com defeito ou é removido.

-   Se você usar o Kit de desenvolvedor do Azure Stack, há um único volume com vários compartilhamentos.

Volumes em espaços de armazenamento diretos oferecem resiliência para proteger contra problemas de hardware, tais como falhas de disco ou servidor e para habilitar disponibilidade contínua em toda a manutenção do servidor, como atualizações de software. Implantação de pilha do Azure está usando o espelhamento de três vias para garantir a resiliência de dados. Três cópias dos dados de locatário são gravadas em diferentes servidores, em que eles chegam no cache:

![Infraestrutura de armazenamento do Azure Stack](media/azure-stack-storage-infrastructure-overview/image5.png)

Espelhamento fornece tolerância a falhas, mantendo diversas cópias de todos os dados. Como esses dados são distribuídos e colocados são não trivial (consulte este blog para saber mais), mas é absolutamente True para dizer que todos os dados armazenados usando o espelhamento são gravados em sua totalidade, várias vezes. Cada cópia é gravada para físicas diferentes hardware (unidades diferentes em diferentes servidores) que são considerados para falha de modo independente. Espelhamento de três vias com segurança pode tolerar pelo menos dois problemas de hardware (disco ou servidor) por vez. Por exemplo, se a reinicialização de um servidor quando, de repente, outra unidade ou um servidor falhar, todos os dados permanecem seguros e acessados continuamente.

## <a name="volume-states"></a>Estados de volume

Para descobrir quais volumess de estado estão em, use os seguintes comandos do PowerShell:

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

Get-AzsVolume - ScaleUnit \$scaleunit\_nome - StorageSubSystem \$subsistema\_nome | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, descrição, ação, TotalCapacityGB, RemainingCapacityGB

Aqui está um exemplo de saída, mostrando um volume separado e um volume degradado/incompletas:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Desconhecido | Desanexado |
| ObjStore_2 | Aviso | {Degradado, incompleta} |

As seções a seguir listam a integridade e estados operacionais.

### <a name="volume-health-state-healthy"></a>Estado de integridade do volume: Healthy

| Estado operacional | DESCRIÇÃO |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | O volume está íntegro. |
| Abaixo do ideal | Dados não for escritos uniformemente entre unidades.<br> <br>**Ação:** Entre em contato com o suporte para otimizar o uso da unidade no pool de armazenamento. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. Talvez você precise restaurar do backup depois que a conexão com falha é restaurado. |


### <a name="volume-health-state-warning"></a>Estado de integridade do volume: Aviso

Quando o volume está em um estado de integridade de aviso, isso significa que uma ou mais cópias de seus dados não estão disponíveis, mas o Azure Stack ainda pode ler a pelo menos uma cópia dos seus dados.

| Estado operacional | DESCRIÇÃO |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| No serviço | O Azure Stack está reparando o volume, como após a adição ou remoção de uma unidade. Quando o reparo for concluído, o volume deve retornar para o estado de integridade Okey.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o reparo do volume e verificar o status posteriormente. |
| Não Concluído | A resiliência do volume é reduzida porque uma ou mais unidades de falharam ou estão ausentes. No entanto, as unidades ausentes contêm cópias atualizadas dos seus dados.<br> <br>**Ação:** Reconectar-se a todas as unidades ausentes, substitua todas as unidades com falha e colocar online todos os servidores que estão offline. |
| Degradado | A resiliência do volume é reduzida porque uma ou mais unidades de falharam ou estão ausentes e há desatualizadas cópias de seus dados nessas unidades.<br> <br>**Ação:** Reconectar-se a todas as unidades ausentes, substitua todas as unidades com falha e colocar online todos os servidores que estão offline. |

 

### <a name="volume-health-state-unhealthy"></a>Estado de integridade do volume: Não Íntegro

Quando um volume está em um estado de integridade não íntegro, alguns ou todos os dados no volume está inacessíveis no momento.

| Estado operacional | DESCRIÇÃO |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nenhuma redundância | O volume perdeu dados porque muitas unidades falhou.<br> <br>**Ação:** Entre em contato com o suporte. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Estado de integridade do volume: Desconhecido

O volume também pode ficar no estado de integridade desconhecido se o disco virtual se tornam destacado.

| Estado operacional | DESCRIÇÃO |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Desanexado | Uma falha de dispositivo de armazenamento ocorreu que pode fazer com que o volume seja inacessível. Alguns dados podem ser perdidos.<br> <br>**Ação:** <br>1. Verifique o físico e a conectividade de todos os dispositivos de armazenamento de rede.<br>2. Se todos os dispositivos estão conectados corretamente, entre em contato com o suporte ao cliente. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. Talvez você precise restaurar do backup depois que a conexão com falha é restaurado. |

## <a name="drive-states"></a>Estados da unidade

Use os seguintes comandos do PowerShell para monitorar o estado de unidades:

 

\$scaleunit\_name = (Get-AzsScaleUnit)\[0\].name

\$subsystem\_name = (Get-AzsStorageSubSystem -ScaleUnit \$scaleunit\_name)\[0\].name

, SerialNumber

 

Get-AzsDrive - ScaleUnit \$scaleunit\_nome - StorageSubSystem \$subsistema\_nome | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, descrição, ação, uso, CanPool, CannotPoolReason, SerialNumber, modelo, MediaType, CapacityGB

As seções a seguir descrevem os estados de integridade, uma unidade pode ser no.

### <a name="drive-health-state-healthy"></a>Estado de integridade da unidade: Healthy

| Estado operacional | DESCRIÇÃO |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | O volume está íntegro. |
| No serviço | A unidade é executar algumas operações de manutenção interna. Quando a ação for concluída, a unidade deve retornar para o estado de integridade Okey. |

### <a name="drive-health-state-healthy"></a>Estado de integridade da unidade: Healthy

Uma unidade em que a lata de estado de aviso ler e gravar dados com êxito, mas tem um problema.

| Estado operacional | DESCRIÇÃO |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Perda de comunicação | Conectividade for perdida na unidade.<br> <br>**Ação:** Traga todos os servidores online novamente. Se isso não resolver a ele, reconecte o disco. Se isso continuar acontecendo, substitua a unidade para garantir a resiliência completa. |
| Falha preditiva | Uma falha da unidade está prevista para ocorrer em breve.<br> <br>**Ação:** Substitua unidade assim que possível para garantir a resiliência completa. |
| Erro de e/s | Ocorreu um erro temporário ao acessar a unidade.<br> <br>**Ação:** Se isso continuar acontecendo, substitua a unidade para garantir a resiliência completa. |
| Erro transitório | Ocorreu um erro temporário com a unidade. Isso geralmente significa que a unidade não estava respondendo, mas ele também pode significar que os espaços de armazenamento diretos partição protetora inadequadamente foi removida da unidade. <br> <br>**Ação:** Se isso continuar acontecendo, substitua a unidade para garantir a resiliência completa. |
| Latência Anormal | A unidade é, às vezes, responder e mostra sinais de falha.<br> <br>**Ação:** Se isso continuar acontecendo, substitua a unidade para garantir a resiliência completa. |
| Remover do pool | O Azure Stack está no processo de remoção da unidade de seu pool de armazenamento.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a remoção da unidade e verificar o status posteriormente.<br>Se o status permanecer, entre em contato com o suporte ao cliente. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. |
|  |  |
| Modo de manutenção inicial | O Azure Stack está no processo de colocar a unidade no modo de manutenção. Este é um estado temporário - a unidade em breve deve estar no estado de modo de manutenção In.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o processo e verificar o status posteriormente. |
| No modo de manutenção | A unidade está no modo de manutenção, interrompendo as leituras e gravações da unidade. Isso geralmente significa que tarefas de administração do Azure Stack, como PNU ou FRU está operando a unidade. Mas o administrador também pode colocar a unidade no modo de manutenção.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a tarefa de administração e verificar o status posteriormente.<br>Se o status permanecer, entre em contato com o suporte ao cliente. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. |
|  |  |
| Parar modo de manutenção | Azure Stack está no processo de colocar a unidade online novamente. Este é um estado temporário - a unidade em breve estará no outro estado - o ideal é íntegra.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o processo e verificar o status posteriormente. |

 

### <a name="drive-health-state-unhealthy"></a>Estado de integridade da unidade: Não Íntegro

Uma unidade no estado não íntegro atualmente não pode ser gravada ou acessada.

| Estado operacional | DESCRIÇÃO |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Divisão | A unidade tem se tornam separada do pool.<br> <br>**Ação:** Substitua a unidade com um novo disco. Se você precisar usar esse disco, remova o disco do sistema, verifique se que não há nenhum dado útil no disco, apagar o disco e, em seguida, reconecte o disco. |
| Não pode ser usado | O disco físico é colocado em quarentena porque não é suportado pelo seu fornecedor de solução. Há suporte para apenas os discos que são aprovados para a solução e tem o firmware de disco correta.<br> <br>**Ação:** Substitua a unidade com um disco que tenha uma fabricante aprovado e o número do modelo para a solução. |
| Metadados obsoletos | O disco de substituição foi usado anteriormente e pode conter dados de um sistema de armazenamento desconhecido. O disco está em quarentena.        <br> <br>**Ação:** Substitua a unidade com um novo disco. Se você precisar usar esse disco, remova o disco do sistema, verifique se que não há nenhum dado útil no disco, apagar o disco e, em seguida, reconecte o disco. |
| Metadados não reconhecidos | Metadados não reconhecidos encontrado na unidade, que normalmente significa que a unidade tem metadados de um pool diferente nele.<br> <br>**Ação:** Substitua a unidade com um novo disco. Se você precisar usar esse disco, remova o disco do sistema, verifique se que não há nenhum dado útil no disco, apagar o disco e, em seguida, reconecte o disco. |
| Falha na mídia | A unidade de falha e não será usada por espaços de armazenamento mais.<br> <br>**Ação:** Substitua unidade assim que possível para garantir a resiliência completa. |
| Falha de hardware do dispositivo | Houve uma falha de hardware nessa unidade. <br> <br>**Ação:** Substitua unidade assim que possível para garantir a resiliência completa. |
| Atualização do firmware | O Azure Stack está atualizando o firmware da unidade. Esse é um estado temporário que, geralmente, dura menos de um minuto e durante o qual o tempo de outras unidades no pool de lidar com todas as leituras e gravações.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a atualização e verificar o status posteriormente. |
| Iniciando | A unidade está se preparando para a operação. Isso deve ser um estado temporário - uma vez concluído, que a unidade deve fazer a transição para um estado operacional diferente.<br> <br>**Ação:** Aguarde para o Azure Stack concluir a operação e verifique o status posteriormente. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Motivos de que uma unidade não pode ser colocado em pool

Algumas unidades apenas não estiverem prontas para ser no pool de armazenamento do Azure Stack. Você pode descobrir por que uma unidade não é elegível ao pooling examinando a propriedade CannotPoolReason de uma unidade. A tabela a seguir fornece mais detalhes sobre cada um dos motivos.

| Motivo | DESCRIÇÃO |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware não está em conformidade | A unidade não está na lista de modelos de armazenamento aprovadas especificados usando o serviço de integridade.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Firmware não estão em conformidade | O firmware da unidade física não está na lista de revisões de firmware aprovados por meio do serviço de integridade.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Em uso por cluster | Atualmente, a unidade é usada por um Cluster de Failover.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Mídia removível | A unidade é classificada como uma unidade removível. <br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Não íntegro | A unidade não está em um estado íntegro e talvez precise ser substituído.<br> <br>**Ação:** Substitua a unidade com um novo disco. |
| Capacidade insuficiente | Há partições ocupando o espaço livre na unidade.<br> <br>**Ação:** Substitua a unidade com um novo disco. Se você precisar usar esse disco, remova o disco do sistema, verifique se que não há nenhum dado útil no disco, apagar o disco e, em seguida, reconecte o disco. |
| Verificação em andamento | O serviço de integridade está verificando se a unidade ou o firmware da unidade é aprovado para uso.<br> <br>**Ação:** Aguarde para o Azure Stack concluir o processo e verificar o status posteriormente. |
| Falha na verificação | O serviço de integridade não pôde verificar se a unidade ou o firmware da unidade é aprovado para uso.<br> <br>**Ação:** Entre em contato com o suporte. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. |
| Off-line | A unidade está offline. <br> <br>**Ação:** Entre em contato com o suporte. Antes de começar, o processo de coleta de arquivo de log usando as diretrizes de https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Próxima etapa

[Gerenciar a capacidade de armazenamento](https://docs.microsoft.com/azure/azure-stack/azure-stack-manage-storage-shares) 