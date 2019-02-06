---
title: Monitorar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Como monitorar a Sincronização de Arquivos do Azure.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/28/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 032b39846d19e34f2eb87c1311feeb4bb890cb24
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467451"
---
# <a name="monitor-azure-file-sync"></a>Monitorar a Sincronização de Arquivos do Azure

Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo descreve como monitorar sua implantação da Sincronização de Arquivos do Azure usando o portal do Azure e o Windows Server.

As opções de monitoramento a seguir estão disponíveis atualmente:

## <a name="azure-portal"></a>Portal do Azure

No portal do Azure, você pode exibir a integridade do servidor registrado, a integridade do ponto de extremidade do servidor (integridade de sincronização) e as métricas.

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para exibir a integridade do ponto de extremidade de servidor e do servidor registrado, vá para o Serviço de Sincronização de Armazenamento no portal do Azure. A integridade do servidor registrado pode ser vista na folha Servidores registrados. A integridade do ponto de extremidade de servidor pode ser vista na folha de grupos de sincronização.

Integridade do servidor registrado
- Se o estado do servidor registrado é Online, o servidor está se comunicando com sucesso com o serviço.
- Se o estado do servidor registrado é Aparece Offline, verifique se o processo do Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) no servidor está em execução. Se o servidor está atrás de um firewall ou proxy, configure o firewall e o proxy segundo a [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Integridade do ponto de extremidade do servidor
- A integridade do ponto de extremidade do servidor no portal baseia-se nos eventos de sincronização registrados no log de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório (por exemplo, erro cancelado), a sincronização ainda poderá se mostrar íntegra no portal desde que a sessão de sincronização atual esteja progredindo (a ID do Evento 9302 é usada para determinar se os arquivos estão sendo aplicados). Para saber mais, confira a seguinte documentação: [Integridade da Sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Progresso da Sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se o portal mostra um erro de sincronização devido à ausência de progresso na sincronização, verifique a [Documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter diretrizes.

### <a name="azure-monitor"></a>Azure Monitor

Use o [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para monitorar a sincronização, as camadas de nuvem e conectividade do servidor. As métricas para a Sincronização de Arquivos do Azure são habilitadas por padrão e são enviadas para o Azure Monitor a cada 15 minutos.

Para exibir as métricas da Sincronização de Arquivos do Azure no Azure Monitor, selecione o tipo de recurso dos Serviços de Sincronização de Armazenamento.

As métricas a seguir para a Sincronização de Arquivos do Azure estão disponíveis no Azure Monitor:

| Nome da métrica | DESCRIÇÃO |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (upload e download).<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |
| Recall da camada de nuvem | Tamanho dos dados em recall.<br><br>Unidade: Bytes<br>Tipo de agregação: Soma<br>Dimensão aplicável: Nome do Servidor |
| Arquivos não sincronizando | Contagem de arquivos que estão falhando em sincronizar.<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |
| Arquivos sincronizados | Contagem de arquivos carregados e baixados.<br><br>Unidade: Contagem<br>Tipo de agregação: Soma<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |
| Pulsação do servidor | Contagem de pulsações recebidas do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do Servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = sessão de sincronização bem-sucedida; 0 = sessão de sincronização com falha)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |

## <a name="windows-server"></a>Windows Server

No Windows Server, você pode exibir a integridade das camadas de nuvem, do servidor registrado e da sincronização.

### <a name="event-logs"></a>Logs de eventos

Use o log de eventos de telemetria no servidor para monitorar a integridade do servidor registrado, da sincronização e das camadas de nuvem. No Visualizador de Eventos, o log do evento de Telemetria está localizado em Aplicativos e Serviços\Microsoft\FileSync\Agent.

Integridade de Sincronização
- A ID do evento 9102 é registrada quando uma sessão de sincronização é concluída. Esse evento deve ser usado para determinar se as sessões de sincronização estão sendo concluídas com êxito (HResult = 0) e se há erros de sincronização por item. Para saber mais, confira a seguinte documentação: [Integridade da Sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Erros por Item](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Às vezes, as sessões de sincronização falham no geral ou possuem um PerItemErrorCount diferente de zero, mas ainda fazem avançar o progresso, com alguns arquivos sendo sincronizados com êxito. Isso pode ser visto nos campos Applied (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), que informam quanto da sessão está sendo bem-sucedida. Se você vir várias sessões de sincronização em uma fila que estão falhando, mas com uma contagem Applied crescente, forneça o tempo de sincronização para tentar novamente antes de abrir um tíquete de suporte.

- A ID do evento 9302 é registrada a cada 5 a 10 minutos, se há uma sessão de sincronização ativa. Esse evento deve ser usado para determinar se a sessão de sincronização atual está progredindo (AppliedItemCount > 0). Se a sincronização não estiver progredindo, a sessão de sincronização deverá eventualmente falhar e uma ID do evento 9102 será registrada com o erro. Para saber mais, confira a seguinte documentação: [Progresso da Sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Integridade do servidor registrado
- A ID do evento 9301 é registrada a cada 30 segundos quando um servidor consulta o serviço em busca de trabalhos. Se GetNextJob é concluído com status = 0, o servidor é capaz de se comunicar com o serviço. Se GetNextJob é concluído com um erro, verifique a [Documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter diretrizes.

Integridade das Camadas de Nuvem
- Para monitorar a atividade em camadas em um servidor, use as IDs do evento 9003, 9016 e 9029 no log de eventos da Telemetria (localizado no Visualizador de Eventos, em Aplicativos e Serviços\Microsoft\FileSync\Agent).

  - A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo, Contagem total de erros, ErrorCode, etc. Observe que um evento é registrado por código de erro.
  - A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo, o percentual de espaço livre é, Número de arquivos fantasmados na sessão, Número de arquivos que falharam no fantasma etc.
  - ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo, Número de arquivos tentados na sessão, Número de arquivos em camadas na sessão, Número de arquivos já em camadas, etc.
  
- Para monitorar a atividade de rechamada em um servidor, use as IDs do evento 9005, 9006, 9009 e 9059 no log de eventos da Telemetria (localizado no Visualizador de Eventos, em Aplicativos e Serviços\Microsoft\FileSync\Agent).

  - A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo, Total de arquivos exclusivos acessados, Total de arquivos exclusivos com acesso com falha, etc.
  - ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo, Total de solicitações com falha, ErrorCode etc. Observe que um evento é registrado por código de erro.
  - A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
  - A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo, ShareId, Nome do Aplicativo e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>contadores de desempenho

Use os contadores de desempenho da Sincronização de Arquivos do Azure no servidor para monitorar a atividade de sincronização.

Para exibir os contadores de desempenho da Sincronização de Arquivos do Azure no servidor, inicie o Monitor de Desempenho (Perfmon.exe). Os contadores podem ser encontrados nos objetos Bytes da AFS Transferidos e Operações de Sincronização da AFS.

Os seguintes contadores de desempenho para a Sincronização de Arquivos do Azure estão disponíveis no Monitor de Desempenho:

| Objeto de Desempenho\Nome do Contador | DESCRIÇÃO |
|-|-|
| Bytes de AFS Transferidos\Bytes Baixados/s | Número de bytes baixados por segundo. |
| Bytes de AFS Transferidos\Bytes Carregados/s | Número de bytes carregados por segundo. |
| Bytes de AFS Transferidos\Total de Bytes/s | Total de bytes por segundo (upload e download). |
| Operações de Sincronização de AFS\Arquivos de Sincronização Baixados/s | Número de arquivos baixados por segundo. |
| Operações de Sincronização de AFS\Arquivos de Sincronização Carregados/s | Número de arquivos carregados por segundo. |
| Operações de Sincronização de AFS\Total de Operações de Arquivo de Sincronização/s | Número total de arquivos sincronizados (upload e download). |

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
