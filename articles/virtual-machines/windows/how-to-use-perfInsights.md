---
title: Como usar PerfInsights no Microsoft Azure | Microsoft Docs
description: Aprenda a usar o PerfInsights para solucionar problemas de desempenho da VM do Windows.
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>Como usar o PerfInsights 

[PerfInsights](http://aka.ms/perfinsightsdownload) é um script automatizado que coleta informações de diagnóstico úteis. Ele também executa as cargas de estresse de E/S e fornece um relatório de análise para ajudar a solucionar problemas de desempenho de máquina virtual do Windows no Azure. Isso pode ser executado nas máquinas virtuais como um script autônomo ou diretamente no Portal instalando a [Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md).

Se você estiver tendo problemas de desempenho com máquinas virtuais, antes de entrar em contato com o suporte, execute este script.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de solução de problemas com suporte

O PerfInsights pode coletar e analisar vários tipos de informações. As seções a seguir cobrem cenários comuns.

### <a name="collect-basic-configuration"></a>Coletar configuração básica 

Este cenário coleta a configuração do disco e outras informações importantes, incluindo:

-   Logs de eventos

-   Status da rede para todas as conexões de entrada e saída

-   Definições de configuração de firewall e rede

-   Lista de tarefas para todos os aplicativos que estão sendo executados no sistema no momento

-   Arquivo de informações criado pelo msinfo32 para a máquina virtual

-   Definições de configuração do banco de dados Microsoft SQL Server (se a VM for identificada como um servidor que execute o SQL Server)

-   Contadores de confiabilidade do armazenamento

-   Hotfixes importantes do Windows

-   Drivers de filtro instalados

Esta é uma coleção de passiva de informações que não devem afetar o sistema. 

>[!Note]
>Esse cenário é incluído automaticamente em cada um dos cenários a seguir.

### <a name="benchmarking"></a>Parâmetros de comparação

Este cenário executa o teste de parâmetro de comparação [Diskspd](https://github.com/Microsoft/diskspd) (IOPS e MBPS) para todas as unidades anexadas à VM. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

### <a name="slow-vm-analysis"></a>Análise de VM lenta 

Este cenário executa um rastreamento de [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando os contadores especificados no arquivo Generalcounters.txt. Se a VM for identificada como um servidor que executa o SQL Server, ela executará um rastreamento do contador de desempenho. Isso é feito usando os contadores que são encontrados no arquivo Sqlcounters.txt, e também inclui dados de diagnóstico de desempenho.

### <a name="slow-vm-analysis-and-benchmarking"></a>Análise de VM lenta e padrão de referência

Este cenário executa um rastreamento do [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) que é seguido pelo teste de parâmetro de comparação [Diskspd](https://github.com/Microsoft/diskspd). 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

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

### <a name="custom-slow-vm-analysis"></a>Análise de VM lenta personalizada 

Ao executar uma análise de VM lenta personalizada, selecione rastreamentos para executar em paralelo. Se você quiser, é possível executá-los todos (Contador de Desempenho, Xperf, Rede e StorPort). Após o rastreamento estar concluído, a ferramenta executará o parâmetro de comparação Diskspd, se ele estiver selecionado. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado em um sistema de produção ativo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um teste de parâmetro de comparação ou rastreamento pode afetar adversamente o desempenho da VM.
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>Que tipo de informações é coletado pelo script?

São coletadas informações sobre a VM do Windows, a configuração de discos ou de pools de armazenamento, os contadores de desempenho, logs e vários rastreamentos. Depende do cenário de desempenho que você está usando. A tabela a seguir fornece os detalhes:

|Dados coletados                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | Coletar configuração básica | Parâmetros de comparação | Análise de VM lenta | Análise de VM lenta e padrão de referência | Análise de Arquivos do Azure | Análise de VM lenta personalizada |
| Informações de logs de eventos      | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Informações do sistema               | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Mapa de volume                       | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Mapa do disco                         | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Tarefas em execução                    | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Contadores de confiabilidade do armazenamento     | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Informações de armazenamento              | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Saída do fsutil                    | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Informações do driver de filtro               | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Saída de Netstat                   | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Configuração de rede            | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Configuração do firewall           | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Configuração do SQL Server         | sim                        | sim                                | sim                      | sim                            | sim                  | sim                  |
| Rastreamentos de diagnóstico de desempenho * | sim                        | sim                                | sim                      |                                | sim                  | sim                  |
| Rastreamento do contador de desempenho **     |                            |                                    |                          |                                |                      | sim                  |
| Rastreamento do contador SMB **             |                            |                                    |                          |                                | sim                  |                      |
| Rastreamento do contador do SQL Server **      |                            |                                    |                          |                                |                      | sim                  |
| Rastreamento de XPerf                      |                            |                                    |                          |                                |                      | sim                  |
| Rastreamento de StorPort                   |                            |                                    |                          |                                |                      | sim                  |
| Rastreamento de rede                    |                            |                                    |                          |                                | sim                  | sim                  |
| Rastreamento de parâmetro de comparação de Diskspd ***      |                            | sim                                |                          | sim                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreamento de diagnóstico de desempenho (*)

Executa um mecanismo baseado em regras no segundo plano para coletar dados e diagnosticar problemas de desempenho em andamento. No momento, há suporte para regras a seguir:

- Regra de HighCpuUsage: detecta períodos de uso elevado de CPU e mostra os principais consumidores de uso da CPU durante esses períodos.
- Regra de HighDiskUsage: detecta períodos de uso elevado de disco em discos físicos e mostra os principais consumidores de uso de disco durante esses períodos.
- Regra de HighResolutionDiskMetric: mostra IOPS, taxa de transferência e métricas de latência de E/S por 50 milissegundos para cada disco físico. Isso ajuda a identificar rapidamente os períodos de limitação de disco.
- Regra de HighMemoryUsage: detecta os períodos de uso elevado de memória e mostra os principais consumidores de uso de memória durante esses períodos.

> [!NOTE] 
> Atualmente, há suporte para versões do Windows que incluem .NET Framework 3.5 ou versões posteriores.

### <a name="performance-counter-trace-"></a>Rastreamento do contador de desempenho (**)

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

## <a name="run-the-perfinsights-script-on-your-vm"></a>Execute o script PerfInsights na sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>O que eu preciso saber antes de executar o script? 

#### <a name="script-requirements"></a>Requisitos de script

-  Esse script deve ser executado na VM que tem o problema de desempenho. 

-  Há suporte para os seguintes sistemas operacionais: Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2, Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>Possíveis problemas ao executar o script em VMs de produção

-  Para cenários de parâmetro de comparação ou o cenário de "Análise de VM lenta personalizada" configurado para usar XPerf ou DiskSpd, o script poderá afetar negativamente o desempenho da VM. Você não deve executar esses cenários em um ambiente de produção.

-  Para cenários de parâmetro de comparação ou o cenário de "Análise de VM lenta personalizada" configurado para usar o DiskSpd, verifique se nenhuma outra atividade em segundo plano interfere na carga de trabalho de E/S.

-  Por padrão, o script usa a unidade de armazenamento temporário para coletar dados. Se o rastreamento permanecer habilitado por mais tempo, a quantidade de dados coletados poderá ser relevante. Isso pode reduzir a disponibilidade de espaço no disco temporário, podendo afetar, portanto, qualquer aplicativo que dependa dessa unidade.

### <a name="how-do-i-run-perfinsights"></a>Como fazer para executar o PerfInsights? 

Você pode executar o PerfInsights em uma máquina virtual instalando a [Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md). Você também pode executá-lo como um script autônomo. 

**Instalar e executar o PerfInsights do Portal do Azure**

Para obter mais informações sobre esta opção, confira [Instalar Extensão de VM de Diagnóstico de Desempenho do Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Executar o script do PerfInsights no modo autônomo**

Para executar o script do PerfInsights, siga estas etapas:


1. Baixe [PerfInsights.zip](http://aka.ms/perfinsightsdownload).

2. Desbloqueie o arquivo PerfInsights.zip. Para fazer isso, clique com o botão direito do mouse no arquivo PerfInsights.zip e selecione **Propriedades**. Na guia **Geral**, selecione **Desbloquear** e, em seguida, selecione **OK**. Isso garantirá que o script seja executado sem prompts de segurança adicionais.  

    ![Captura de tela das propriedades do PerfInsights, com opção Desbloquear destacada](media/how-to-use-perfInsights/unlock-file.png)

3.  Expanda o arquivo PerfInsights.zip compactado na unidade temporária (por padrão, ela costuma ser a unidade D). O arquivo compactado deve conter os seguintes arquivos e pastas:

    ![Captura de tela de arquivos na pasta zip](media/how-to-use-perfInsights/file-folder.png)

4.  Abra o Windows PowerShell como administrador e execute o script PerfInsights.ps1.

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    Poderá ser necessário digitar "y" para confirmar se você deseja alterar a política de execução.

    Na caixa de diálogo **Aviso e Consentimento**, você tem a opção de compartilhar informações de diagnóstico com o Suporte da Microsoft. Você também deve concordar com o contrato de licença para continuar. Faça suas seleções e, em seguida, selecione **Executar Script**.

    ![Captura de tela da caixa de diálogo Aviso e Consentimento](media/how-to-use-perfInsights/disclaimer.png)

5.  Envie o número do caso, se ele estiver disponível, ao executar o script. Depois, selecione **OK**.
    
    ![Captura de tela da caixa de diálogo ID de suporte](media/how-to-use-perfInsights/enter-support-number.png)

6.  Selecione a unidade de armazenamento temporário. O script pode detectar automaticamente a letra da unidade. Se houver problemas nesse estágio, você poderá ser solicitado a selecionar a unidade (a unidade padrão é D). Os logs gerados são armazenados aqui na pasta de\_coleção de log. Depois de inserir ou aceitar a letra da unidade, selecione **OK**.

    ![Captura de tela da caixa de diálogo da unidade temporária](media/how-to-use-perfInsights/enter-drive.png)

7.  Selecione um cenário de solução de problemas na lista fornecida.

       ![Captura de tela da lista de cenários de solução de problemas](media/how-to-use-perfInsights/select-scenarios.png)

Você também pode executar PerfInsights sem interface do usuário. O comando a seguir executa o cenário de solução de problemas “Análise de VM lenta” sem uma interface do usuário. Ele solicita que você concorde com o mesmo aviso de isenção de responsabilidade e EULA mencionados na etapa 4.

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

Se você quiser que o PerfInsights seja executado no modo sem confirmação, use o parâmetro **-AcceptDisclaimerAndShareDiagnostics**. Por exemplo, use o seguinte comando:

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>Como fazer para solucionar os problemas durante a execução do script?

Se o script terminar de maneira anormal, você poderá retornar para um estado consistente executando o script em conjunto com a opção limpeza, da seguinte maneira:

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

Se houver problemas durante a detecção automática da unidade temporária, você poderá ser solicitado a selecionar a unidade (a unidade padrão é a D).

![Captura de tela da caixa de diálogo da unidade temporária](media/how-to-use-perfInsights/enter-drive.png)

O script desinstala as ferramentas do utilitário e remove as pastas temporárias.

### <a name="troubleshoot-other-script-issues"></a>Solução de outros problemas de script 

Se ocorrerem problemas quando você executar o script, pressione Ctrl+C para interromper o script. Se você continuar encontrando falhas de script mesmo após várias tentativas, execute o script no modo de depuração usando a opção de parâmetro "-Debug" na inicialização.

Após a falha ocorrer, copie a saída completa do console do PowerShell e envie-a ao agente de Suporte da Microsoft que o está ajudando a solucionar o problema.

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>Como fazer para executar o script no modo de “Análise de VM lenta personalizada”?

Ao selecionar a **Análise de VM lenta personalizada**, é possível habilitar vários rastreamentos em paralelo (para selecionar vários rastreamentos, use a tecla Shift):

![Captura de tela da lista de cenários](media/how-to-use-perfInsights/select-scenario.png)

Quando você selecionar os cenários Rastreamento do Contador de Desempenho, Rastreamento de XPerf, Rastreamento de Rede ou Rastreamento de Storport, siga as instruções nas caixas de diálogo seguintes. Tente reproduzir o problema de desempenho lento depois de iniciar os rastreamentos.

Você pode iniciar um rastreamento através da caixa de diálogo a seguir:

![Captura de tela da caixa de diálogo Iniciar Rastreamento do Contador de Desempenho](media/how-to-use-perfInsights/start-trace-message.png)

Para interromper os rastreamentos, você precisa confirmar o comando em uma segunda caixa de diálogo.

![Captura de tela da caixa de diálogo Interromper Rastreamento do Contador de Desempenho](media/how-to-use-perfInsights/stop-trace-message.png)
![Captura de tela da caixa de diálogo Interromper Todos os Rastreamentos](media/how-to-use-perfInsights/ok-trace-message.png)

Quando os rastreamentos e as operações forem concluídos, um novo arquivo aparecerá na unidade D: \\log\_coleção (ou na unidade temporária). O nome do arquivo é **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip.** Você pode enviar este arquivo para o agente de suporte para análise.

## <a name="review-the-diagnostics-report"></a>Examine o relatório de diagnóstico

No arquivo **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip,**, você pode localizar um relatório HTML com detalhes sobre as descobertas do PerfInsights. Para examinar o relatório, expanda o arquivo **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** e, em seguida, abra o arquivo **PerfInsights Report.html**.

Selecione a guia **Descobertas**.

![Captura de tela do relatório PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![Captura de tela do relatório PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> As conclusões categorizadas como críticas são problemas conhecidos que podem causar problemas de desempenho. As conclusões categorizadas como importantes representam não ideais que não causam necessariamente problemas de desempenho. As conclusões categorizadas como informativas são apenas instruções informativas.

Analise as recomendações e links para todas as conclusões críticas e importantes. Saiba mais sobre como eles podem afetar o desempenho e também sobre as melhores práticas para configurações de otimização de desempenho.

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

Após entrar, você encontrará uma caixa de diálogo para carregar o arquivo **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** coletado por PerfInsights.
