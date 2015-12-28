<properties
	pageTitle="Noções básicas sobre replicação do Hyper-V com o Azure Site Recovery | Microsoft Azure" 
	description="Use este artigo para entender os conceitos técnicos que ajudam você a instalar, configurar e gerenciar com êxito o Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="anbacker" 
	manager="mkjain" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="anbacker"/>


# Noções básicas sobre replicação do Hyper-V com o Azure Site Recovery

Este artigo descreve os conceitos técnicos que ajudam você a configurar e gerenciar com êxito o site do Hyper-V ou do VMM para a proteção do Azure usando o Azure Site Recovery.

## Noções básicas sobre os componentes

### Implantação do Site do Hyper-V ou do VMM para replicação entre o local e o Azure.

Como parte da configuração de recuperação de desastres entre o local e o Azure, o Provedor do Azure Site Recovery precisa ser baixado e instalado no servidor do VMM junto com o Agente de Serviços de Recuperação do Azure que precisa ser instalado em cada host Hyper-V.

![Implantação de Site do VMM para replicação entre o local e o Azure](media/site-recovery-understanding-site-to-azure-protection/image00.png)

A implantação do Site do Hyper-V é igual à Implantação do VMM – a única diferença é que o Provedor e o Agente são instalados no próprio host do Hyper-V.

## Noções básicas sobre os fluxos de trabalho

### Habilitar proteção
Depois de proteger uma máquina virtual no portal ou localmente, um trabalho do ASR chamado *Habilitar proteção* será iniciado e pode ser monitorado na guia TRABALHOS.

![Solucionar problemas do Hyper-V no local](media/site-recovery-understanding-site-to-azure-protection/image01.png)

O trabalho *Habilitar proteção* verifica os pré-requisitos antes de invocar [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), que cria a replicação para o Azure usando as entradas configuradas durante a proteção. O trabalho *Habilitar proteção* inicia a replicação inicial localmente invocando [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx), que envia os discos virtuais da máquina virtual para o Azure.

![Solucionar problemas do Hyper-V no local](media/site-recovery-understanding-site-to-azure-protection/image02.png)

### Finalizar proteção
Um [instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a Replicação inicial é disparada. Os discos rígidos virtuais são processados individualmente, até que todos os discos sejam carregados no Azure. Isso normalmente demora um pouco para ser concluído com base no tamanho do disco e na largura de banda. Consulte [Como gerenciar o uso de largura de banda de rede da proteção local do Azure](https://support.microsoft.com/kb/3056159) para otimizar o uso da rede. Após a conclusão da replicação inicial, o trabalho *Finalizar proteção na máquina virtual* define as configurações de rede e pós-replicação. Enquanto a Replicação inicial estiver em andamento, todas as alterações aos discos são rastreadas, conforme mencionamos na seção Replicação delta abaixo. O armazenamento em disco adicional será consumido no instantâneo e nos arquivos HRL durante a Replicação inicial. Após a conclusão de uma Replicação inicial, um instantâneo da VM do Hyper-V será excluído, o que resultará na mesclagem das alterações de dados pós-Replicação inicial para o disco pai.

![Solucionar problemas do Hyper-V no local](media/site-recovery-understanding-site-to-azure-protection/image03.png)

### Replicação delta
O Rastreador de Replicação de Réplica do Hyper-V, que faz parte do Mecanismo de Replicação de Réplica do Hyper-V, rastreia as alterações para um disco rígido virtual como Logs de replicação do Hyper-V (*.hrl). Os arquivos HRL estarão localizados no mesmo diretório que os discos associados. Cada disco configurado para a replicação terá um arquivo HRL associado. Esse(s) log(s) é(são) enviado(s) para a conta de armazenamento do cliente após a conclusão da replicação inicial. Quando um log está em trânsito para o Azure, as alterações no arquivo principal são rastreadas em outro arquivo de log no mesmo diretório.

A integridade da replicação de VM durante a Replicação inicial ou a Replicação delta pode ser monitorada no modo de exibição de VM, como mencionamos em [Monitorar a integridade da replicação de máquina virtual](./site-recovery-monitoring-and-troubleshooting.md#monitor-replication-health-for-virtual-machine).

### Ressincronização 
Uma máquina virtual é marcada para ressincronização quando há uma falha na Replicação Delta e a Replicação inicial completa consome muita largura de banda de rede ou muito tempo até a conclusão de uma Replicação inicial completa. Por exemplo, quando o tamanho do arquivo HRL chega a 50% do tamanho total do disco, a máquina virtual é marcada para ressincronização. A ressincronização minimiza a quantidade de dados enviados pela rede, com o cálculo de somas de verificação dos discos da máquina virtual de Origem e de Destino e com o envio apenas do diferencial.

Depois de concluída a ressincronização, a Replicação delta normal deverá ser retomada. A ressincronização poderá ser retomada em caso de interrupção (por exemplo, interrupção de rede, falha do VMMS, etc.).

Por padrão, a *Ressincronização automaticamente agendada* é configurada durante as horas de trabalho após o expediente. Se a máquina virtual precisar ser ressincronizada manualmente, selecione a máquina virtual por meio do portal e clique em RESSINCRONIZAR. ![Solucionar problemas do Hyper-V no local](media/site-recovery-understanding-site-to-azure-protection/image04.png)

A ressincronização usa um algoritmo de agrupamento de bloco fixo em que os arquivos de Origem e de Destino são divididos em partes fixas; a soma de verificação para cada bloco é gerada e depois comparada para determinar qual(is) bloco(s) da Origem precisa(m) ser aplicado(s) ao Destino.

### Lógica de repetição
Há uma lógica de repetição interna quando ocorrem erros de replicação. Ela pode ser classificada em duas categorias, como mostrado abaixo.

| Categoria | Cenários |
|---------------------------|----------------------------------------------|
| Erro não recuperável | Nenhuma repetição será feita. O status de replicação da máquina virtual será mostrado como Crítico e é necessária uma intervenção do administrador. Alguns exemplos incluem <ul><li>Uma cadeia de VHD quebrada</li><li>A máquina virtual de réplica está em um estado inválido</li><li>Erro de autenticação de rede</li><li>Erro de autorização</li><li>Se uma máquina virtual não é encontrada no caso de um servidor Hyper-V autônomo</li></ul>|
| Erro recuperável | As tentativas ocorrem a cada intervalo da replicação usando de modo exponencial a retirada, o que aumenta o intervalo de repetição desde o início da primeira tentativa (1, 2, 4, 8 e 10 minutos). Se o erro persistir, repita a operação a cada 30 minutos. Alguns exemplos incluem <ul><li>Erro de rede</li><li>Espaço em disco insuficiente</li><li>Condição de memória insuficiente</li></ul>|

## Noções básicas sobre o ciclo de vida de recuperação e de proteção da máquina virtual do Hyper-V

![Noções básicas sobre o ciclo de vida de recuperação e a proteção da máquina virtual do Hyper-V](media/site-recovery-understanding-site-to-azure-protection/image05.png)

## Outras referências

- [Monitorar e solucionar problemas de proteção para VMware, VMM, Hyper-V e sites físicos](./site-recovery-monitoring-and-troubleshooting.md)
- [Contatando o Suporte da Microsoft](./site-recovery-monitoring-and-troubleshooting.md#reaching-out-for-microsoft-support)
- [Erros de ASR comuns e suas resoluções](./site-recovery-monitoring-and-troubleshooting.md#common-asr-errors-and-their-resolutions)

<!---HONumber=AcomDC_1217_2015-->