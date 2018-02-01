---
title: Monitorar e solucionar problemas do Azure Site Recovery | Microsoft Docs
description: "Monitorar e solucionar problemas de replicação do Azure Site Recovery e operações usando o portal"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 01/06/2017
ms.author: bsiva
ms.openlocfilehash: 6dca032c8611ac4f7e66eb6f7e22e53f49209143
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="monitoring-and-troubleshooting-azure-site-recovery"></a>Monitoramento e solução de problemas do Azure Site Recovery

Neste artigo, você aprenderá como usar os recursos internos de monitoramento do Azure Site Recovery para o monitoramento e a solução de problemas. Saiba como:
> [!div class="checklist"]
> - Usar o painel do Azure Site Recovery (página de visão geral do cofre)
> - Monitorar e solucionar problemas de replicação
> - Monitorar Operações/Trabalhos do Azure Site Recovery
> - Assinar as notificações por email

## <a name="using-the-azure-site-recovery-dashboard"></a>Usar o painel do Azure Site Recovery

O painel do Azure Site Recovery na página de visão geral do cofre consolida todas as informações de monitoramento do cofre em um local único. Inicie no painel do cofre e aprofunde-se para obter mais detalhes, navegando pelas partes do painel. As principais partes do painel do Azure Site Recovery são as seguintes:

### <a name="1-switch-between-azure-backup-and-azure-site-recovery-dashboards"></a>1. Alternar entre os painéis do Backup do Azure e o Azure Site Recovery

A switch de alternância na parte superior da página de visão geral permite alternar entre as páginas do painel do Site Recovery e Backup. Essa seleção, uma vez feita, será relembrada e padronizada para a próxima vez que você abrir a página de visão geral do cofre. Selecione a opção Site Recovery para visualizar o painel do Site Recovery. 

As várias partes da página do painel do Azure Site Recovery atualizam automaticamente a cada 10 minutos, para que o painel indique as informações mais recentes disponíveis.

![Recursos de monitoramento na página de visão geral do Azure Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-overview-page.png)

### <a name="2-replicated-items"></a>2. Itens replicados

A seção de itens replicados do painel apresenta uma visão geral da integridade de replicação de servidores protegidos no cofre. 

<table>
<tr>
    <td>Healthy</td>
    <td>A replicação está progredindo normalmente para esses servidores e nenhum erro ou sintomas de aviso foram detectados.</td>
</tr>
<tr>
    <td>Aviso </td>
    <td>Um ou mais sintomas de aviso que podem afetar a replicação ou indicar que a replicação não está progredindo normalmente foi detectado para esses servidores.</td>
</tr>
<tr>
    <td>Crítico</td>
    <td>Um ou mais sintomas de erro de replicação críticos foram detectados para esses servidores. Esses sintomas de erro geralmente são indicadores de que a replicação está paralisada ou não está progredindo tão rápido quanto a taxa de alteração de dados para esses servidores.</td>
</tr>
<tr>
    <td>Não aplicável</td>
    <td>Servidores que atualmente não estão previstos para serem replicados, como servidores que falharam.</td>
</tr>
</table>

Para consultar uma lista de servidores protegidos filtrados por integridade de replicação, clique na descrição da integridade de replicação ao lado da rosca. O link Exibir Tudo próximo ao título da seção é um atalho para a página de itens replicados para o cofre. Use o link Exibir Tudo para consultar a lista de todos os servidores no cofre.

### <a name="3-failover-test-success"></a>3. Teste de failover com êxito

A seção de teste de failover com êxito do painel apresenta uma separação de máquinas virtuais no cofre com base no status do failover de teste. 

<table>
<tr>
    <td>Teste recomendado</td>
    <td>Máquinas virtuais que não tiveram um failover de teste com êxito desde o momento em que atingiram um estado protegido.</td>
</tr>
<tr>
    <td>Realizado com êxito</td>
    <td>Máquinas virtuais que tiveram um ou mais failovers de teste com êxito.</td>
</tr>
<tr>
    <td>Não aplicável</td>
    <td>Máquinas virtuais que atualmente não são elegíveis para um failover de teste. Os exemplos são: servidores com falha, servidores para os quais a replicação inicial está em andamento, servidores para os quais um failover está em andamento, servidores para os quais um failover de teste já está em andamento.</td>
</tr>
</table>

Clique no status do failover de teste ao lado da rosca e consulte a lista de servidores protegidos com base no status de failover de teste.
 
> [!IMPORTANT]
> Como melhor prática, é recomendável que você execute um failover de teste nos servidores protegidos pelo menos uma vez a cada seis meses. Realizar um failover de teste é uma maneira não interruptiva de testar o failover dos servidores e aplicativos para um ambiente isolado e ajudá-lo a avaliar a preparação de continuidade do seu negócio.

 Uma operação de failover de teste em um servidor ou um plano de recuperação será considerada com êxito somente depois que a operação de failover de teste e a operação de failover de teste de limpeza forem concluídas com êxito.

### <a name="4-configuration-issues"></a>4. Problemas de configuração

A seção Problemas de Configuração mostra uma lista de problemas que podem impactar na capacidade das máquinas virtuais de failover com êxito. As classes dos problemas listados nesta seção são:
 - **Configurações ausentes:** servidores protegidos cujas configurações necessárias estão faltando, como uma rede de recuperação ou um grupo de recursos de recuperação.
 - **Recursos ausentes:** recursos de recuperação/destino configurados não encontrados ou não disponíveis na assinatura. Por exemplo, o recurso foi excluído ou migrado para uma assinatura ou grupo de recursos diferente. As seguintes configurações de recuperação/destino são monitoradas quanto à disponibilidade: grupo de recursos de destino, rede virtual de destino e sub-rede, conta de armazenamento de destino/log, conjunto de disponibilidade de destino, endereço IP de destino.
 - **Cota de assinatura:** o saldo da cota de recurso de assinatura disponível é comparado com o saldo necessário para poder fazer failover em todas as máquinas virtuais no cofre. Se for encontrado saldo disponível insuficiente, o saldo da cota insuficiente será relatado. As cotas para os seguintes recursos do Azure são monitoradas: contagem principal da máquina virtual, contagem principal de família da máquina virtual, contagem da placa de interface de rede (NIC).
 - **Atualizações de software:** a disponibilidade de novas atualizações de software, versões de software que expiram.

Os problemas de configuração (além da disponibilidade de atualizações de software) são detectados por uma operação de validação periódica executada a cada 12 horas por padrão. É possível forçar a operação do validador a ser executada imediatamente, clicando no ícone de atualização próximo ao cabeçalho da seção *Problemas de configuração*.

Clique nos links para obter mais detalhes sobre os problemas listados e as máquinas virtuais impactadas por eles. Para problemas que afetam máquinas virtuais específicas, é possível obter mais detalhes clicando no link **requer atenção** na coluna de configurações de destino para a máquina virtual. Os detalhes incluem recomendações sobre como é possível corrigir os problemas detectados.

### <a name="5-error-summary"></a>5. Resumo dos erros

A seção de resumo dos erros mostra os sintomas de erro de replicação atualmente ativos que podem afetar a replicação de servidores no cofre, juntamente com o número de entidades afetadas devido a cada erro.

Os sintomas de erro de replicação para servidores em um estado crítico ou de aviso de integridade da replicação podem ser consultados na tabela de resumo de erros. 

- Erros que afetam os componentes da infraestrutura local, como o não recebimento de uma pulsação do Provedor do Azure Site Recovery em execução no Servidor de Configuração local, servidor VMM ou host Hyper-V são listados no início da seção de resumo de erros
- Os sintomas de erro de replicação que afetam os servidores protegidos são listados a seguir. As entradas da tabela de resumo dos erros são classificadas por ordem decrescente da severidade do erro e, depois, por ordem decrescente da contagem dos servidores afetados.
 

> [!NOTE]
> 
>  Sintomas de erro de replicação múltipla podem ser observados em um servidor único. Se houvesse vários sintomas de erro em um servidor único, cada sintoma de erro seria contado para esse servidor na lista de seus servidores afetados. Quando o problema subjacente resultante de um sintoma de erro for corrigido, os parâmetros de replicação melhorarão e o erro será apagado da máquina virtual.
>
> > [!TIP]
> A contagem de servidores afetados é uma maneira útil de entender se um problema subjacente único pode afetar múltiplos servidores. Por exemplo, uma falha de rede pode afetar potencialmente todos os servidores replicando de um site local para o Azure. Essa exibição rapidamente transmite se a correção de um problema subjacente corrigirá a replicação para vários servidores.
>

### <a name="6-infrastructure-view"></a>6. Modo de exibição de infraestrutura

O modo de exibição de infraestrutura fornece uma representação visual do cenário consistente dos componentes infraestruturais envolvidos na replicação. Além disso, descreve visualmente a integridade da conectividade entre os vários servidores e entre os servidores e os serviços do Azure envolvidos na replicação. 

Uma linha verde indica que a conexão está em estado íntegro, enquanto uma linha vermelha com o ícone de erro sobreposto indica a existência de um ou mais sintomas de erro que afetam a conectividade entre os componentes envolvidos. Focalizar o ponteiro do mouse sobre o ícone de erro na linha mostra o erro e o número de entidades afetadas. 

Clicando no ícone de erro, mostra uma lista filtrada de entidades afetadas pelo(s) erro(s).

![Modo de exibição de infraestrutura do Site Recovery (cofre)](media/site-recovery-monitor-and-troubleshoot/site-recovery-vault-infra-view.png)

> [!TIP]
> Certifique-se de que os componentes da infraestrutura local (Servidor de Configuração, servidores de processo adicionais, máquinas virtuais VMware replicantes, hosts Hyper-V, servidores VMM) estão executando a versão mais recente do software do Azure Site Recovery. Para poder usar todos os recursos do modo de exibição de infraestrutura, é necessário estar executando o [Pacote cumulativo de atualizações 22](https://support.microsoft.com/help/4072852) ou mais recente do Azure Site Recovery

Para usar o modo de exibição de infraestrutura, selecione o cenário de replicação apropriado (máquinas virtuais do Azure, máquinas virtuais VMware/servidor físico ou Hyper-V) dependendo do seu ambiente de origem. O modo de exibição de infraestrutura apresentado na página de visão geral do cofre é uma exibição agregada para o cofre. É possível pode fazer busca detalhada dos componentes individuais clicando nas caixas.

Um modo de exibição de infraestrutura com escopo para o contexto de uma única máquina de replicação está disponível na página de visão geral do item replicado. Para ir à página de visão geral de um servidor de replicação, vá para os itens replicados no menu do cofre e selecione o servidor para visualizar os detalhes.

### <a name="infrastructure-view---faq"></a>Modo de exibição de infraestrutura - Perguntas frequentes

**P.** Por que não vejo o modo de exibição de infraestrutura para minha VM? </br>
**A.** O recurso de modo de exibição de infraestrutura somente está disponível para máquinas virtuais que estão replicando para o Azure. O recurso atualmente não está disponível para máquinas virtuais que estão replicando entre sites locais.

**P.** Por que a contagem de máquinas virtuais no modo de exibição de infraestrutura do cofre é diferente da contagem total mostrada na rosca dos itens replicados?</br>
**A.** O modo de exibição de infraestrutura do cofre é com escopo por cenários de replicação. Somente máquinas virtuais que participam do cenário de replicação atualmente selecionado estão incluídas na contagem de máquinas virtuais mostradas no modo de exibição de infraestrutura. Além disso, para o cenário selecionado, apenas as máquinas virtuais atualmente configuradas para replicar para o Azure estão incluídas na contagem de máquinas virtuais mostradas no modo de exibição de infraestrutura (por exemplo, máquinas virtuais com failover, e máquinas virtuais replicando de volta para uma site local não estão incluídas no modo de exibição de infraestrutura.)

**P.** Por que a contagem de itens replicados mostrada na gaveta essencial na página de visão geral é diferente da contagem total de itens replicados mostrada no gráfico de rosca no painel de controle?</br>
**A.** Somente as máquinas virtuais para as quais a replicação inicial foi concluída estão incluídas na contagem mostrada na gaveta essencial. O total de rosca de itens replicados contém todas as máquinas virtuais no cofre, incluindo servidores para os quais a replicação inicial está atualmente em andamento.

**P.** Em quais cenários de replicação está disponível o modo de exibição de infraestrutura? </br>
**A.**
>[!div class="mx-tdBreakAll"]
>|Cenário de replicação  | Estado VM  | Modo de exibição de infraestrutura disponível  |
>|---------|---------|---------|
>|Máquinas virtuais replicando entre dois sites locais     | -        | Não       |
>|Todos     | Com failover         |  Não        |
>|Máquinas virtuais replicando entre duas regiões do Azure     | Replicação inicial em andamento ou Protegida         | sim         |
>|Máquinas virtuais VMware replicando para Azure     | Replicação inicial em andamento ou Protegida        | sim        |
>|Máquinas virtuais VMware replicando para Azure     | Máquinas virtuais com failover sendo replicadas de volta para um site VMware local         | Não         |
>|Máquinas virtuais Hyper-V replicando para Azure     | Replicação inicial em andamento ou Protegida        | sim       |
>|Máquinas virtuais Hyper-V replicando para Azure     | Com failover/Failback em andamento        |  Não        |


### <a name="7-recovery-plans"></a>7. Planos de recuperação

A seção de planos de Recuperação mostra a contagem dos planos de recuperação no cofre. Clique no número para consultar a lista dos planos de recuperação, criar novos planos de recuperação ou editar os existentes. 

### <a name="8-jobs"></a>8. Trabalhos

Os trabalhos do Azure Site Recovery rastreiam o status das operações do Azure Site Recovery. A maioria das operações no Azure Site Recovery são executadas de forma assíncrona, com um trabalho de rastreamento usado para rastrear o progresso da operação.  Para saber como monitorar o status de uma operação, consulte a seção [Monitorar Operações/Trabalhos do Azure Site Recovery](#monitor-azure-site-recovery-jobsoperations).

Esta seção de Trabalhos do painel exibe as seguintes informações:

<table>
<tr>
    <td>Com falha</td>
    <td>Trabalhos do Azure Site Recovery com falha nas últimas 24 horas</td>
</tr>
<tr>
    <td>Em andamento</td>
    <td>Trabalhos do Azure Site Recovery que atualmente estão em andamento</td>
</tr>
<tr>
    <td>Aguardando entrada</td>
    <td>Trabalhos do Azure Site Recovery que atualmente estão em pausa aguardando uma entrada do usuário.</td>
</tr>
</table>

O link Exibir Tudo próximo ao cabeçalho da seção é um atalho para acessar a página da lista de trabalhos.

## <a name="monitor-and-troubleshoot-replication-issues"></a>Monitorar e solucionar problemas de replicação

Além das informações disponíveis na página do painel do cofre, é possível obter os detalhes adicionais e as informações de solução de problemas na página da lista de máquinas virtuais e na página de detalhes da máquina virtual. É possível exibir a lista de máquinas virtuais protegidas no cofre, selecionando a opção **Itens replicados** do menu do cofre. Alternativamente, é possível acessar uma lista filtrada dos itens protegidos clicando em qualquer um dos atalhos de escopo disponíveis na página do painel do cofre.

![Exibição da lista de itens replicados do Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-list-view.png)

A opção de filtro na página da lista de itens replicados permite que você aplique vários filtros, como a integridade de replicação e a política de replicação. 

A opção do seletor de coluna permite especificar colunas adicionais a serem exibidas, como RPO, problemas de configuração de destino e erros de replicação. Você pode iniciar operações em uma máquina virtual ou visualizar erros que afetam a máquina virtual clicando com o botão direito do mouse em uma determinada linha da lista de máquinas.

Para detalhar ainda mais, selecione uma máquina virtual clicando na máquina. Isso abrirá a página de detalhes da máquina virtual. A página de visão geral em detalhes da máquina virtual contém um painel onde você encontrará informações adicionais relativas à máquina. 

Na página de visão geral da máquina de replicação, você encontrará:
- RPO (objetivo de ponto de recuperação): RPO atual para a máquina virtual e o tempo em que o RPO foi calculado pela última vez.
- Pontos de recuperação mais recentes disponíveis para a máquina
- Problemas de configuração, se houver, que possam afetar a prontidão de failover da máquina. Clique no link para obter mais detalhes.
- Detalhes do erro: lista dos sintomas de erro de replicação atualmente observados na máquina, juntamente com possíveis causas e correções recomendadas
- Eventos: uma lista cronológica de eventos recentes que afetam a máquina. Enquanto os detalhes do erro mostram os sintomas de erro atualmente observáveis na máquina, os eventos são um registro histórico de vários eventos que podem ter afetado a máquina, incluindo sintomas de erro que podem ter sido notados anteriormente para a máquina.
- Modo de exibição de infraestrutura para máquinas que replicam para Azure

![Visão geral/detalhes do item replicado do Site Recovery](media/site-recovery-monitor-and-troubleshoot/site-recovery-virtual-machine-details.png)

O menu de ação na parte superior da página fornece opções para executar várias operações, como failover de teste na máquina virtual. O botão de detalhes do erro no menu de ação permite visualizar todos os erros atualmente ativos, incluindo erros de replicação, problemas de configuração e avisos baseados nas melhores práticas de configuração para a máquina virtual.

> [!TIP]
> Como o RPO ou o objetivo de ponto de recuperação são diferentes do ponto de recuperação mais recente disponível?
> 
>O Azure Site Recovery usa um processo assíncrono de várias etapas para replicar máquinas virtuais para o Azure. Na penúltima etapa de replicação, as alterações recentes na máquina virtual juntamente com os metadados são copiados para uma conta de armazenamento em cache/log. Quando essas alterações juntamente com a marca para identificar um ponto de recuperação forem gravadas na conta de armazenamento na região de destino, o Azure Site Recovery terá as informações necessárias para gerar um ponto de recuperação para a máquina virtual. Neste ponto, o RPO foi encontrado para as alterações carregadas na conta de armazenamento até o momento. Em outras palavras, o RPO para a máquina virtual nesse momento expresso em unidades de tempo, é igual à quantidade de tempo decorrido do carimbo de data/hora correspondente ao ponto de recuperação.
>
>O serviço do Azure Site Recovery, que opera em tela de fundo, escolhe os dados carregados da conta de armazenamento e aplica nos discos de réplica criados para a máquina virtual. Em seguida, gera um ponto de recuperação e torna esse ponto disponível para recuperação no failover. O ponto de recuperação mais recente disponível indica o carimbo de data/hora correspondente ao ponto de recuperação mais recente que já foi processado e aplicado aos discos de réplica.
>> [!WARNING]
> Um relógio distorcido ou um tempo de sistema incorreto na máquina de origem de replicação ou os servidores de infraestrutura locais impedirá o valor RPO calculado. Para garantir um relatório dos valores de RPO preciso, assegure-se de que o relógio do sistema nos servidores envolvidos na replicação também esteja preciso. 
>

## <a name="monitor-azure-site-recovery-jobsoperations"></a>Monitorar Operações/Trabalhos do Azure Site Recovery

O Azure Site Recovery executa as operações que você especifica de forma assíncrona. Os exemplos de operações que você pode executar são habilitar replicação, criar plano de recuperação, failover de teste, configurações de replicação de atualização e etc. Cada operação tem um trabalho correspondente criado para rastrear e auditar a operação. O objeto de trabalho tem todas as informações necessárias para que você acompanhe o estado e o progresso da operação. É possível rastrear o status das várias operações do Site Recovery para o cofre na página de Trabalhos. 

Para consultar a lista de trabalhos do Site Recovery para o cofre, acesse a seção **Monitoramento e Relatórios** do menu do cofre e selecione Trabalhos > Trabalhos do Site Recovery. Selecione um trabalho na lista de trabalhos na página, clicando nele para obter mais detalhes sobre o trabalho especificado. Se um trabalho não foi concluído com êxito ou tiver erros, você poderá visualizar mais informações sobre o erro e possível correção, clicando no botão de detalhes do erro na parte superior da página de detalhes do trabalho (também acessível pela página da lista de Trabalhos, clicando com o botão direito do mouse no trabalho sem êxito.) Você pode usar a opção de filtro no menu de ação na parte superior da página da lista de trabalhos para filtrar a lista com base em critérios específicos e usar o botão Exportar para exportar os detalhes das tarefas selecionadas para o Excel. Além disso, é possível acessar a exibição da lista de trabalhos a partir do atalho disponível na página de painel do Site Recovery. 

 Para as operações executadas pelo Portal do Azure, o trabalho criado e seu status atual também podem ser rastreados a partir da seção de notificações (o ícone de sino no canto superior direito) do Portal do Azure.

## <a name="subscribe-to-email-notifications"></a>Assinar as notificações por email

O recurso interno de notificação por email permite que você assine para receber notificações por email para eventos críticos. Se assinado, as notificações por email serão enviadas para os seguintes eventos:
- Integridade de replicação de uma máquina de replicação degradante para crítica.
- Não há conectividade entre os componentes da infraestrutura local e o serviço do Azure Site Recovery. A conectividade com o serviço do Site Recovery a partir dos componentes da infraestrutura local, como o Servidor de Configuração (VMware) ou o System Center Virtual Machine Manager (Hyper-V) registrado no cofre, é detectada usando um mecanismo de pulsação.
- Falhas de operação de failover, se houver.

Para assinar e receber notificações por email para o Azure Site Recovery, vá para a seção **Monitoramento e Relatórios** do menu do cofre e:
1. Selecione Alertas e Eventos > Eventos do Site Recovery.
2. Selecione "Notificações por email" no menu na parte superior da página de eventos que está aberta.
3. Use o assistente de notificação por email para ativar ou desativar as notificações por email e selecionar os destinatários para as notificações. Você pode especificar que todos os administradores de assinaturas sejam enviados para notificações e/ou fornecer uma lista de endereços de email para enviar notificações. 