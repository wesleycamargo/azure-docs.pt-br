---
title: Como usar PerfInsights no Microsoft Azure | Microsoft Docs
description: Aprenda a usar o PerfInsights para solucionar problemas de desempenho da VM do Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 2f496f906eef416b35e2e59b2db93481ce65acb1
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946257"
---
# <a name="how-to-use-perfinsights"></a>Como usar o PerfInsights

[PerfInsights](http://aka.ms/perfinsightsdownload) é uma ferramenta de diagnóstico de autoajuda que coleta e analisa os dados de diagnóstico e fornece um relatório para ajudar a solucionar problemas de desempenho de máquina virtual do Windows no Azure. O PerfInsights pode ser executado em máquinas virtuais como uma ferramenta independente ou diretamente no portal com a instalação da [Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md).

Caso esteja tendo problemas de desempenho com máquinas virtuais, antes de contatar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de solução de problemas com suporte

O PerfInsights pode coletar e analisar vários tipos de informações. As seções a seguir cobrem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise de Desempenho Rápida

Este cenário coleta a configuração do disco e outras informações importantes, incluindo:

-   Logs de eventos

-   Status da rede para todas as conexões de entrada e saída

-   Definições de configuração de firewall e rede

-   Lista de tarefas para todos os aplicativos que estão sendo executados no sistema no momento

-   Definições de configuração do banco de dados Microsoft SQL Server (se a VM for identificada como um servidor que execute o SQL Server)

-   Contadores de confiabilidade do armazenamento

-   Hotfixes importantes do Windows

-   Drivers de filtro instalados

Esta é uma coleção de passiva de informações que não devem afetar o sistema. 

>[!Note]
>Esse cenário é incluído automaticamente em cada um dos seguintes cenários:

### <a name="benchmarking"></a>Parâmetros de comparação

Este cenário executa o teste de parâmetro de comparação [Diskspd](https://github.com/Microsoft/diskspd) (IOPS e MBPS) para todas as unidades anexadas à VM. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

### <a name="slow-vm-analysis"></a>Análise de VM lenta

Este cenário executa um rastreamento de [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando os contadores especificados no arquivo RuleEngineConfig.json. Se a VM for identificada como um servidor que executa o SQL Server, um rastreamento de contador de desempenho será executado. Isso é feito com os contadores encontrados no arquivo RuleEngineConfig.json. Esse cenário também inclui dados de diagnóstico de desempenho.

### <a name="azure-files-analysis"></a>Análise de Arquivos do Azure

Este cenário executa uma captura de contador de desempenho especial junto com um rastreamento de rede. A captura inclui todos os contadores de compartilhamentos de cliente do protocolo SMB. A seguir estão alguns contadores de desempenho de compartilhamento de cliente SMB que fazem parte da captura:

| **Tipo**     | **Contador de compartilhamentos de cliente SMB** |
|--------------|-------------------------------|
| IOPS         | Solicitações de dados/s             |
|              | Solicitações de leitura/s             |
|              | Solicitações de gravação/s            |
| Latency      | Média de solicitação de dados/s         |
|              | Média de leitura/s                 |
|              | Média de gravação/s                |
| Tamanho de E/S      | Média Bytes/solicitação de dados       |
|              | Média Bytes/leitura               |
|              | Média Bytes/gravação              |
| Throughput   | Bytes de dados/s                |
|              | Bytes de leitura/s                |
|              | Bytes de gravação/s               |
| Comprimento da fila | Média Tamanho da fila de leitura        |
|              | Média Tamanho da fila de gravação       |
|              | Média Comprimento da fila de dados        |

### <a name="advanced-slow-vm-analysis"></a>Análise de VM Lenta Avançada

Ao executar uma análise de VM lenta avançada, você selecionada traços para executar em paralelo. Se desejar, execute todos eles (Contador de Desempenho, Xperf, Rede e StorPort).  

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informações é coletado pelo PerfInsights?

São coletadas informações sobre a VM do Windows, a configuração de discos ou de pools de armazenamento, os contadores de desempenho, logs e vários rastreamentos. Depende do cenário de desempenho que você está usando. A tabela a seguir fornece os detalhes:

|Dados coletados                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Análise de Desempenho Rápida | Parâmetros de comparação | Análise de VM lenta | Análise de Arquivos do Azure | Análise de VM Lenta Avançada |
| Informações de logs de eventos       | sim                        | sim                                | sim                      | sim                  | sim                  |
| Informações do sistema                | sim                        | sim                                | sim                      | sim                  | sim                  |
| Mapa de volume                        | sim                        | sim                                | sim                      | sim                  | sim                  |
| Mapa do disco                          | sim                        | sim                                | sim                      | sim                  | sim                  |
| Tarefas em execução                     | sim                        | sim                                | sim                      | sim                  | sim                  |
| Contadores de confiabilidade do armazenamento      | sim                        | sim                                | sim                      | sim                  | sim                  |
| Informações de armazenamento               | sim                        | sim                                | sim                      | sim                  | sim                  |
| Saída do fsutil                     | sim                        | sim                                | sim                      | sim                  | sim                  |
| Informações do driver de filtro                | sim                        | sim                                | sim                      | sim                  | sim                  |
| Saída de Netstat                    | sim                        | sim                                | sim                      | sim                  | sim                  |
| Configuração de rede             | sim                        | sim                                | sim                      | sim                  | sim                  |
| Configuração do firewall            | sim                        | sim                                | sim                      | sim                  | sim                  |
| Configuração do SQL Server          | sim                        | sim                                | sim                      | sim                  | sim                  |
| Rastreamentos de diagnóstico de desempenho *  | sim                        | sim                                | sim                      | sim                  | sim                  |
| Rastreamento do contador de desempenho **      |                            |                                    | sim                      |                      | sim                  |
| Rastreamento do contador SMB **              |                            |                                    |                          | sim                  |                      |
| Rastreamento do contador do SQL Server **       |                            |                                    | sim                      |                      | sim                  |
| Rastreamento de XPerf                       |                            |                                    |                          |                      | sim                  |
| Rastreamento de StorPort                    |                            |                                    |                          |                      | sim                  |
| Rastreamento de rede                     |                            |                                    |                          | sim                  | sim                  |
| Rastreamento de parâmetro de comparação de Diskspd ***       |                            | sim                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreamento de diagnóstico de desempenho (*)

Executa um mecanismo baseado em regras no segundo plano para coletar dados e diagnosticar problemas de desempenho em andamento. No momento, há suporte para regras a seguir:

- Regra de HighCpuUsage: detecta períodos de uso elevado de CPU e mostra os principais consumidores de uso da CPU durante esses períodos.
- Regra de HighDiskUsage: detecta períodos de uso elevado de disco em discos físicos e mostra os principais consumidores de uso de disco durante esses períodos.
- Regra de HighResolutionDiskMetric: mostra IOPS, taxa de transferência e métricas de latência de E/S por 50 milissegundos para cada disco físico. Isso ajuda a identificar rapidamente os períodos de limitação de disco.
- Regra de HighMemoryUsage: detecta os períodos de uso elevado de memória e mostra os principais consumidores de uso de memória durante esses períodos.

> [!NOTE] 
> Atualmente, há suporte para versões do Windows que incluem .NET Framework 3.5 ou versões posteriores.

### <a name="performance-counter-trace-"></a>Rastreamento do contador de desempenho (\*\*)

Coleta os seguintes contadores de desempenho:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk e \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/sec, \Server\Pool Nonpaged, Failures e \Server\Pool Paged Failures
- Contadores selecionados em \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments,  \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity e \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Para instâncias do SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats e \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Para Arquivos do Azure
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Rastreamento de parâmetro de comparação de Diskspd (***)
Testes de carga de trabalho de E/S do Diskspd (disco do SO [gravação] e unidades de pool [leitura/gravação])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Executar a ferramenta PerfInsights em sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que preciso saber antes de executar a ferramenta? 

#### <a name="tool-requirements"></a>Requisitos da ferramenta

-  Esta ferramenta deve ser executada na VM que tem o problema de desempenho. 

-  Há suporte para os seguintes sistemas operacionais: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2, Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Possíveis problemas ao executar a ferramenta em VMs de produção

-  Para os cenários de parâmetro de comparação ou de "Análise de VM lenta avançada" configurado para usar o Xperf ou o Diskspd, a ferramenta poderá prejudicar o desempenho da VM. Esses cenários não devem ser executados em um ambiente de produção ativo.

-  Para os cenários de parâmetro de comparação ou de "Análise de VM lenta avançada" configurado para usar o Diskspd, verifique se nenhuma outra atividade em segundo plano interfere na carga de trabalho de E/S.

-  Por padrão, a ferramenta usa a unidade de armazenamento temporário para coletar dados. Se o rastreamento permanecer habilitado por mais tempo, a quantidade de dados coletados poderá ser relevante. Isso pode reduzir a disponibilidade de espaço no disco temporário, podendo afetar, portanto, qualquer aplicativo que dependa dessa unidade.

### <a name="how-do-i-run-perfinsights"></a>Como fazer para executar o PerfInsights? 

Você pode executar o PerfInsights em uma máquina virtual instalando a [Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md). Você também pode executá-la como uma ferramenta independente. 

**Instalar e executar o PerfInsights do Portal do Azure**

Para obter mais informações sobre esta opção, confira [Instalar Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Executar o PerfInsights no modo autônomo**

Para executar a ferramenta PerfInsights, siga estas etapas:


1. Baixe [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Desbloqueie o arquivo PerfInsights.zip. Para fazer isso, clique com o botão direito do mouse no arquivo PerfInsights.zip e selecione **Propriedades**. Na guia **Geral**, selecione **Desbloquear** e, em seguida, selecione **OK**. Isso garante que a ferramenta é executada sem prompts de segurança adicionais.  

    ![Captura de tela das propriedades do PerfInsights, com opção Desbloquear destacada](media/how-to-use-perfInsights/unlock-file.png)

3.  Expanda o arquivo PerfInsights.zip compactado na unidade temporária (por padrão, ela costuma ser a unidade D). 

4.  Abra o prompt de comando do Windows como administrador e, em seguida, execute PerfInsights.exe para exibir os parâmetros de linha de comando disponíveis.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Captura de tela da saída de linha de comando do PerfInsights](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    A sintaxe básica para execução de cenários do PerfInsights é:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Use o exemplo abaixo para executar o cenário de VM lenta durante 5 minutos:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Use o seguinte exemplo para executar o cenário avançado com o Xperf e rastreamentos de Contador de desempenho durante 5 minutos:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Você pode usar o exemplo abaixo para executar o cenário de VM lento por 5 minutos e carregar o arquivo zip de resultado na conta de armazenamento.
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Pesquise todas as opções e todos os cenários disponíveis usando o comando **/list**:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Antes de executar um cenário, o PerfInsights solicita que o usuário concorde em compartilhar informações de diagnóstico e aceite os termos de licença. Use a opção **/AcceptDisclaimerAndShareDiagnostics** para ignorar esses prompts. 
    >
    >Caso você tenha um tíquete de suporte ativo com a Microsoft e esteja executando o PerfInsights de acordo com a solicitação do engenheiro de suporte com o qual está trabalhando, forneça o número do tíquete de suporte usando a opção **/sr**.
    >
    >Por padrão, o PerfInsights tentará atualizar a si próprio para a última versão, se disponível. Use o parâmetro **/SkipAutoUpdate** ou **/sau** para ignorar a atualização automática.  
    >
    >Se a chave de duração **/d** não for especificada, PerfInsights solicitará que você reproduza novamente o problema durante a execução de vmslow, azurefiles e cenários avançados. 

Quando os rastreamentos e as operações forem concluídos, um novo arquivo aparecerá na mesma pasta do PerfInsights. O nome do arquivo é **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip.** Envie esse arquivo para o agente de suporte para análise ou abra o relatório no arquivo zip para examinar as conclusões e recomendações.

## <a name="review-the-diagnostics-report"></a>Examine o relatório de diagnóstico

No arquivo **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip**, você pode localizar um relatório HTML com detalhes sobre as descobertas do PerfInsights. Para examinar o relatório, expanda o arquivo **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip** e, em seguida, abra o arquivo **PerfInsights Report.html**.

Selecione a guia **Descobertas**.

![Captura de tela do relatório PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![Captura de tela do relatório PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Constatações categorizadas como altas são problemas conhecidos que podem causar problemas de desempenho. Constatações categorizadas como médias representam configurações não adequadas que não necessariamente causam problemas de desempenho. Constatações categorizadas como lentas são declarações apenas informativas.

Analise as recomendações e links para todas as conclusões altas e médias. Saiba mais sobre como eles podem afetar o desempenho e também sobre as melhores práticas para configurações de otimização de desempenho.

### <a name="storage-tab"></a>Guia Armazenamento

A seção **Conclusões** exibe várias conclusões e recomendações relacionadas ao armazenamento.

As seções **DiskMap** e **VolumeMap** descrevem como volumes lógicos e discos físicos estão relacionados entre si.

Na perspectiva do disco físico (DiskMap), a tabela mostra todos os volumes lógicos que estão em execução no disco. No exemplo a seguir, **PhysicalDrive2** executa dois volumes lógicos criados em várias partições (J e H):

![Captura de tela da guia do disco](media/how-to-use-perfInsights/disktab.png)

Na perspectiva do volume (VolumeMap), as tabelas mostram todos os discos físicos em cada volume lógico. Observe que, para discos RAID/Dinâmicos, você poderá executar um volume lógico em vários discos físicos. No exemplo a seguir *C:\\mount* é um ponto de montagem configurado como *SpannedDisk* nos discos físicos 2 e 3:

![Captura de tela da guia de volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Guia SQL

Se a VM de destino hospedar instâncias do SQL Server, você verá uma guia adicional no relatório chamada **SQL**:

![Captura de tela da guia do SQL](media/how-to-use-perfInsights/sqltab.png)

Esta seção contém uma guia **Conclusões** e guias adicionais para cada uma das instâncias do SQL Server hospedadas na VM.

A guia **Conclusões** contém uma lista de todos os problemas de desempenho relacionados ao SQL encontrados juntamente com as recomendações.

No exemplo a seguir, **PhysicalDrive0** (executando a unidade C) é exibido. Isso ocorre porque os arquivos **modeldev** e **modellog** estão localizados na unidade C e são de tipos diferentes (como o arquivo de dados e o log de transações, respectivamente).

![Captura de tela de informações de log](media/how-to-use-perfInsights/loginfo.png)

As guias para instâncias específicas do SQL Server contêm uma seção geral que exibe informações básicas sobre a instância selecionada. As guias também contêm seções adicionais para obter informações avançadas, incluindo ajustes, configurações e opções do usuário.

### <a name="diagnostic-tab"></a>Guia Diagnóstico
A guia **Diagnóstico** contém informações sobre os principais consumidores de memória, CPU e disco no computador para a duração da execução do PerfInsights. Também é possível encontrar informações sobre patches críticos que talvez estejam ausentes no sistema, lista de tarefas e importantes eventos do sistema. 

## <a name="references-to-the-external-tools-used"></a>Referências às ferramentas externas usadas

### <a name="diskspd"></a>Diskspd

Diskspd é um gerador de carga de armazenamento e ferramenta de teste de desempenho da Microsoft. Para obter mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>XPerf

XPerf é uma ferramenta de linha de comando para capturar rastreamentos do Kit de Ferramentas de Desempenho do Windows. Para obter mais informações, consulte [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Próximas etapas

Você pode carregar relatórios e logs de diagnóstico para o Suporte da Microsoft para análise. O Suporte poderá solicitar que você transmita a saída que é gerada pelo PerfInsights para auxiliar no processo de solução de problemas.

A captura de tela a seguir mostra uma mensagem semelhante à qual você poderá receber:

![Captura de tela da mensagem de exemplo do Suporte da Microsoft](media/how-to-use-perfInsights/supportemail.png)

Siga as instruções na mensagem para acessar o espaço de trabalho de transferência de arquivo. Para mais segurança, você deve alterar sua senha no primeiro uso.

Após entrar, você encontrará uma caixa de diálogo para carregar o arquivo **CollectedData\_yyyy-MM-dd\_hh-mm-ss-fff.zip** coletado por PerfInsights.
