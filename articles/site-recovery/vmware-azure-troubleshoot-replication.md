---
title: Solucionar problemas de replicação para VM VMware e replicação de servidor físico para Azure com Azure Site Recovery | Microsoft Docs
description: Este artigo fornece solução de problemas para problemas comuns de replicação ao replicar VMs VMware e servidores físicos para Azure com Azure Site Recovery.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: asgang
ms.openlocfilehash: 9291840428c9a8d7ba5d65bc94ce5964728316f3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solução de problemas de replicação para VMs VMware e servidores físicos

ou pode receber uma mensagem de erro específica ao proteger as máquinas virtuais VMware ou servidores físicos usando o Azure Site Recovery. Este artigo descreve alguns problemas comuns que você pode encontrar ao replicar VMs VMware e servidores físicos no Azure, utilizando [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problemas de replicação inicial.

Em muitos casos, as falhas de replicação inicial que encontramos no suporte são devido a problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure. Na maioria dos casos, você pode solucionar esses problemas por conta própria, seguindo as etapas listadas abaixo.

### <a name="verify-the-source-machine"></a>Verificar o computador de origem
* Na linha de comando do computador do Servidor de Origem, use o Telnet para executar um ping no Servidor de Processo com a porta https (padrão 9443), conforme mostrado abaixo para ver se há problemas de conectividade de rede ou problemas de bloqueio de porta de firewall.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Use o Telnet, não use o PING para testar a conectividade.  Se o Telnet não estiver instalado, siga a lista de etapas [aqui](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Se não conseguir se conectar, dê permissão para a porta de entrada 9443 no Servidor de Processo e verifique se o problema ainda existe. Houve alguns casos em que o servidor de processo estava atrás da DMZ, e isso estava causando o problema.

* Verifique o status do serviço `InMage Scout VX Agent – Sentinel/OutpostStart` e se ele não está em execução e verifique se o problema ainda existe.   

## <a name="verify-the-process-server"></a>Verificar o servidor de processo

* **Verifique se o servidor de processo está ativamente enviando dados por push para o Azure**

Do computador do Servidor de Processo, abra o Gerenciador de Tarefas (pressione Ctrl-Shift-Esc). Vá para a guia Desempenho e clique no link 'Abrir o Monitor de Recursos'. No Gerenciador de Recursos, vá para a guia Rede. Verifique se cbengine.exe em 'Processos com atividade de rede' está enviando ativamente um grande volume (em Mbs) de dados.

![Habilitar a replicação](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Se isso não estiver acontecendo, siga as etapas listadas abaixo:

* **Verifique se o Servidor de Processo é capaz de se conectar de Blob do Azure**: Selecione e marque cbengine.exe para exibir as 'Conexões de TCP' para ver se há conectividade do Servidor de Processo a URL de blob do Armazenamento do Azure.

![Habilitar a replicação](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Se não, em seguida, vá para o Painel de Controle > Serviços, verifique se os seguintes serviços estão em execução:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re)Inicie qualquer serviço que não estiver em execução e verifique se o problema ainda existe.

* **Verifique se o Servidor de Processo é capaz de se conectar ao endereço IP público do Azure usando a porta 443**

Abra o CBEngineCurr.errlog mais recente do `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` e procure: 443 e a tentativa de conexão com falha.

![Habilitar a replicação](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Se houver problemas, em seguida, na linha de comando do Servidor de Processo, use o telnet para executar o ping do seu endereço IP público do Azure (mascarado na imagem acima) encontrado no CBEngineCurr.currLog usando a porta 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Se não for possível conectar-se, em seguida, verifique se o problema de acesso é devido ao firewall ou Proxy, conforme descrito na próxima etapa.


* **Verifique se o firewall baseado em endereço IP no Servidor de Processo não está bloqueando o acesso**: se você estiver usando regras de firewall baseadas em endereço IP no servidor, em seguida, baixe a lista completa de intervalos de IP do Microsoft Azure Datacenter [aqui](https://www.microsoft.com/download/details.aspx?id=41653) e adicione-os à sua configuração de firewall para garantir que eles permitam a comunicação com o Azure (e a porta HTTPS (443)).  Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).

* **Verifique se o firewall baseado em URL no Servidor de Processo não está bloqueando o acesso**: se você estiver usando regras de firewall baseada em URL no servidor, verifique se as URLs a seguir são adicionadas à configuração do firewall.

  `*.accesscontrol.windows.net:` Usado para controle de acesso e gerenciamento de identidades

  `*.backup.windowsazure.com:` Usado para transferência de dados de replicação e orquestração

  `*.blob.core.windows.net:` Usado para acessar a conta de armazenamento que armazena os dados replicados

  `*.hypervrecoverymanager.windowsazure.com:` Usado para operações de gerenciamento de replicação e orquestração

  `time.nist.gov` e `time.windows.com`: Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.

URLs para a **Nuvem do Azure Governamental**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Verifique se as Configurações de Proxy no Servidor de Processo não estão bloqueando o acesso**.  Se você estiver usando um Servidor Proxy, certifique-se de que o nome do servidor proxy é resolvido pelo servidor DNS.
Para verificar o que você forneceu no momento da instalação do Servidor de Configuração. Vá para a chave do registro

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Agora, certifique-se de que as mesmas configurações estão sendo usadas pelo agente Azure Site Recovery para enviar dados.
Procure o Backup do Microsoft Azure

Abra-o e clique em Ação > Alterar as Propriedades. Na guia de Configuração de Proxy, você deve ver o endereço de proxy, que deve ser igual ao mostrado pelas configurações do registro. Caso contrário, altere-a para o mesmo endereço.


* **Verifique se a Limitação de largura de banda não é restrita no Servidor de Processo**: Aumente a largura de banda e verifique se o problema ainda existe.

## <a name="next-steps"></a>Próximas etapas
Se precisar de mais ajuda, poste sua consulta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma comunidade ativa e um dos nossos engenheiros poderá ajudá-lo.
