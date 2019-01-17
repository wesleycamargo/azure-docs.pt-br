---
title: Solucionar problemas de replicação para recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre a solução de problemas comuns de replicação durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: c53dc81da9469c0628adbd3751dc818997fa4d05
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063671"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solução de problemas de replicação para VMs VMware e servidores físicos

Você pode ver uma mensagem de erro específica ao proteger suas máquinas virtuais VMware ou servidores físicos usando o Azure Site Recovery. Este artigo descreve alguns problemas comuns que você pode encontrar ao replicar VMs VMware locais e servidores físicos no Azure utilizando o [Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problemas de replicação inicial

Falhas de replicação inicial geralmente são causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure. Na maioria dos casos, você pode solucionar esses problemas seguindo as etapas nas próximas seções.

### <a name="check-the-source-machine"></a>Verificar o computador de origem

A lista a seguir mostra maneiras para você verificar o computador de origem:

*  Na linha de comando no servidor de origem, use Telnet para executar ping no servidor de processo através da porta HTTPS (a porta HTTPS padrão é 9443), executando o comando a seguir. O comando verifica se há problemas de conectividade de rede e problemas que bloqueiam a porta do firewall.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Use Telnet para testar a conectividade. Não use `ping`. Se o Telnet não estiver instalado, conclua as etapas listadas em [instalar o cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Se você não pode se conectar ao servidor de processo, permita tráfego pela porta de entrada 9443 no servidor de processo. Por exemplo, talvez seja necessário permitir tráfego pela porta de entrada 9443 no servidor de processo, se sua rede tiver uma rede de perímetro ou sub-rede filtrada. Em seguida, verifique se o problema ainda ocorre.

*  Verifique o status do serviço **InMage Scout VX Agent – Sentinel/OutpostStart**. Se o serviço não está em execução, inicie o serviço e, em seguida, verifique se o problema ainda ocorre.   

### <a name="check-the-process-server"></a>Alterar o servidor de processo

A lista a seguir mostra maneiras para você verificar o servidor de processo:

*  **Verifique se o servidor de processo está enviando dados por push ativamente para o Azure**.

   1. No servidor de processo, abra o Gerenciador de Tarefas (pressione Ctrl+Shift+Esc).
   2. Selecione a guia **Desempenho** e clique no link **Abrir o Monitor de Recursos**. 
   3. Na página **Monitor de Recursos**, selecione a guia **Rede**. Em **Processos com Atividade de Rede**, verifique se o **cbengine.exe** está enviando ativamente grandes volumes de dados.

        ![Captura de tela que mostra os volumes em processos com atividade de rede](./media/vmware-azure-troubleshoot-replication/cbengine.png)

   Se cbengine.exe não estiver enviando um grande volume de dados, conclua as etapas nas seções a seguir.

*  **Verifique se o servidor de processo pode se conectar ao armazenamento de Blobs do Azure**.

   Selecione **cbengine.exe**. Em **Conexões TCP**, verifique se há conectividade do servidor de processo à URL do Armazenamento de Blobs do Azure.

   ![Captura de tela que mostra a conectividade entre cbengine.exe e a URL do Armazenamento de Blobs do Azure](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

   Se não houver nenhuma conectividade do servidor de processo à URL do Armazenamento de Blobs do Azure, no Painel de Controle, selecione **Serviços**. Verifique se os seguintes serviços estão em execução:

   *  cxprocessserver
   *  Agente do InMage Scout VX – Sentinel/Outpost
   *  Agente de Serviços dos Serviços de Recuperação do Microsoft Azure
   *  Serviço do Microsoft Azure Site Recovery
   *  tmansvc

   Iniciar ou reiniciar qualquer serviço que não está em execução. Verifique se o problema ainda ocorre.

*  **Verifique se o servidor de processo pode se conectar ao endereço IP público do Azure usando a porta 443**.

   Em %programfiles%\Microsoft Azure Recovery Services Agent\Temp, abra o arquivo CBEngineCurr.errlog mais recente. No arquivo, procure **443** ou a cadeia de caracteres **Falha na tentativa de conexão**.

   ![Captura de tela que mostra os logs de erros na pasta Temp](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

   Se problemas forem exibidos na linha de comando no servidor de processo, use o Telnet para o endereço IP público do Azure (o endereço IP é mascarado na imagem anterior). Você pode encontrar o endereço IP público do Azure no arquivo CBEngineCurr.currLog usando a porta 443:

   `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

   Se não for possível conectar-se, verifique se o problema de acesso é devido às configurações de firewall ou de proxy, conforme descrito na próxima etapa.

*  **Verifique se o firewall baseado em endereço IP no servidor de processo bloqueia o acesso**.

   Se você usar regras de firewall baseadas em endereço IP no servidor, baixe a lista completa dos [intervalos de IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Adicione intervalos de endereços IP à sua configuração de firewall para garantir que o firewall permita a comunicação com o Azure (e com a porta HTTPS padrão, 443). Permita os intervalos de endereço IP para a região do Azure da sua assinatura e para a região do Azure Oeste dos EUA (usados para controle de acesso e gerenciamento de identidade).

*  **Verifique se um firewall baseado em URL no servidor de processo bloqueia o acesso**.

   Se você usar uma regra de firewall baseada em URL no servidor, adicione as URLs listadas na tabela a seguir à configuração do firewall:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Verifique se as configurações de proxy no servidor de processo bloqueiam o acesso**.

   Se você estiver usando um servidor proxy, verifique se o nome do servidor proxy é resolvido pelo servidor DNS. Para verificar o valor que você forneceu ao configurar o servidor de configuração, vá para a chave do Registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

   Em seguida, verifique se as mesmas configurações estão sendo usadas pelo agente do Azure Site Recovery para enviar dados: 
      
   1. Procure o **Backup do Microsoft Azure**. 
   2. Abra **Backup do Microsoft Azure** e, em seguida, selecione **Ação** > **Alterar Propriedades**. 
   3. Na guia **Configuração de Proxy**, você deve ver o endereço de proxy. O endereço de proxy deve ser o mesmo que o endereço de proxy que é exibido nas configurações do Registro. Caso contrário, altere-a para o mesmo endereço.

*  **Verifique se a largura de banda de limitação está ou não restrita no servidor de processo**.

   Aumente a largura de banda e verifique se o problema ainda ocorre.

## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>O computador de origem não está listado no portal do Azure

Ao tentar selecionar o computador de origem para habilitar a replicação por meio do Azure Site Recovery, o computador pode não estar disponível para você por um dos seguintes motivos:

*  Se houver duas máquinas virtuais no vCenter com o mesmo UUID de instância, a primeira máquina virtual descoberta pelo servidor de configuração será mostrada no portal do Azure. Para resolver esse problema, assegure que não haja duas máquinas virtuais com o mesmo UUID de instância.
*  Verifique se você adicionou as credenciais corretas do vCenter durante a configuração do servidor de configuração, usando o modelo OVF ou a configuração unificada. Para verificar as credenciais que você adicionou durante a instalação, consulte [Modificar credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
*  Se as permissões fornecidas para acessar o vCenter não têm as permissões necessárias, pode ocorrer falha ao descobrir máquinas virtuais. Verifique se as permissões descritas em [Preparar uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) foram adicionadas à conta de usuário do vCenter.
*  Se a máquina virtual já estiver protegida por meio do Site Recovery, essa máquina não poderá ser selecionada para proteção no portal. Verifique se a máquina virtual que você está procurando no portal já não está protegida por algum outro usuário ou sob uma assinatura diferente.

## <a name="protected-virtual-machines-arent-available-in-the-portal"></a>As máquinas virtuais protegidas não estão disponíveis no portal

As máquinas virtuais que são replicadas no Site Recovery não estão disponíveis no portal do Azure se há entradas duplicadas no sistema. Para saber como excluir entradas obsoletas e resolver o problema, veja [VMware do Azure Site Recovery para o Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma comunidade ativa e um dos nossos engenheiros poderá ajudá-lo.
