---
title: Solucionar problemas do servidor de processo do Azure Site Recovery
description: Este artigo descreve como solucionar problemas com o servidor de processo do Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: rayne
ms.openlocfilehash: 926e5b685369f8660daf6221f818734f6f12d2b5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928401"
---
# <a name="troubleshoot-the-process-server"></a>Solucionar problemas do servidor de processo

O [recuperação de Site](site-recovery-overview.md) servidor de processo é usado quando você configurar a recuperação de desastres para o Azure para VMs do VMware locais e servidores físicos. Este artigo descreve como solucionar problemas com o servidor de processo, incluindo problemas de conectividade e replicação.

[Saiba mais](vmware-physical-azure-config-process-server-overview.md) sobre o servidor de processo.

## <a name="before-you-start"></a>Antes de começar

Antes de iniciar a solução de problemas:

1. Verifique se você entendeu como [monitorar os servidores de processo](vmware-physical-azure-monitor-process-server.md).
2. Examine as práticas recomendadas a seguir.
3. Certifique-se de que você siga [considerações sobre capacidade](site-recovery-plan-capacity-vmware.md#capacity-considerations)e use as diretrizes de dimensionamento para o [servidor de configuração](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) ou [servidores de processo autônomo](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Práticas recomendadas para implantação de servidor de processo

Para um melhor desempenho dos servidores de processo, estamos resumidos por um número de práticas recomendadas gerais.

**Prática recomendada** | **Detalhes**
--- |---
**Uso** | Verifique se o servidor de processo do servidor/autônomo de configuração são usadas apenas para a finalidade pretendida. Não execute qualquer outra coisa no computador.
**Endereço IP** | Certifique-se de que o servidor de processo tem um endereço IPv4 estático e não tem o NAT configurado.
**Controlar o uso de memória/CPU** |Manter o uso de CPU e memória em 70%.
**Certifique-se de espaço livre** | Espaço livre refere-se para o espaço em disco de cache no servidor de processo. Dados de replicação são armazenados no cache antes de serem carregado no Azure.<br/><br/> Manter o espaço livre acima de 25%. Se ele estiver abaixo de 20%, a replicação é limitada para computadores replicados associados com o servidor de processo.

## <a name="check-process-server-health"></a>Verificar integridade do servidor de processo

A primeira etapa na solução de problemas é verificar a integridade e o status do servidor de processo. Para fazer isso, examine todos os alertas, verifique se os serviços necessários estão em execução e verifique se há uma pulsação do servidor de processo. Essas etapas são resumidas no gráfico a seguir, seguido pelos procedimentos para ajudá-lo a executar as etapas.

![Solucionar problemas de integridade do servidor de processo](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Etapa 1: Solucionar problemas de alertas de integridade do servidor de processo

O servidor de processo gera um número de alertas de integridade. Esses alertas e ações recomendadas, são resumidas na tabela a seguir.

**Tipo de alerta** | **Erro** | **Solucionar problemas**
--- | --- | --- 
![Healthy][green] | Nenhum  | Servidor de processo está conectado e íntegro.
![Aviso][yellow] | A serviços especificados não estão em execução. | 1. Verifique se os serviços estão em execução.<br/> 2. Se os serviços estão sendo executados conforme o esperado, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Aviso][yellow]  | CPU utilização > 80% nos últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs usando o servidor de processo se alinha à [definidos limites](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  CPU utilização > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs usando o servidor de processo se alinha à [definidos limites](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute as [Planejador de implantação](http://aka.ms/asr-v2a-deployment-planner) para replicação de servidor do VMware/físicos.
![Aviso][yellow] | Memória uso > 80% nos últimos 15 minutos. |  1. Não adicione novas máquinas.<br/>2. Verifique se o número de VMs usando o servidor de processo se alinha à [definidos limites](site-recovery-plan-capacity-vmware.md#capacity-considerations)e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>3. Siga as instruções associadas ao aviso.<br/> 4. Se o problema persistir, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] | Memória uso > 95% nos últimos 15 minutos. | 1. Não adicione novas máquinas e considerando a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/> 2. Siga as instruções associadas ao aviso.<br/> 3. 4. Se o problema persistir, siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 4. Se o problema persistir, execute as [Planejador de implantação](http://aka.ms/asr-v2a-deployment-planner) para problemas de replicação de servidor do VMware/físicos.
![Aviso][yellow] | Cache pasta espaço livre < 30% nos últimos 15 minutos. | 1. Não adicionar novas máquinas e considere a configuração de um [servidor de processo adicional](vmware-azure-set-up-process-server-scale.md).<br/>2. Verifique se o número de VMs usando o servidor de processo se alinha à [diretrizes](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).
![Crítico][red] |  Espaço livre < 25% para os últimos 15 minutos | 1. Siga as instruções associadas com o aviso para esse problema.<br/> 2. 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).<br/> 3. Se o problema persistir, execute as [Planejador de implantação](http://aka.ms/asr-v2a-deployment-planner) para replicação de servidor do VMware/físicos.
![Crítico][red] | Não há pulsação do servidor de processo para 15 minutos ou mais. O serviço tmansvs não está se comunicando com o servidor de configuração. | 1) Verifique se o servidor de processo está em execução.<br/> 2. Verifique se o tmassvc está em execução no servidor de processo.<br/> 3. Siga as instruções abaixo para [solucionar problemas de conectividade e replicação](#check-connectivity-and-replication).


![Chave de tabela](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Etapa 2: Serviços de servidor de processo de verificação

Serviços que devem estar em execução no servidor de processo são resumidos na tabela a seguir. Há pequenas diferenças nos serviços, dependendo de como o servidor de processo é implantado. 

Para todos os serviços, exceto o agente de serviços de recuperação do Microsoft Azure (obengine), verifique se o StartType está definido como **automáticas** ou **automática (início atrasado)**.
 
**Implantação** | **Serviços em execução**
--- | ---
**Servidor de processo no servidor de configuração** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de Upload de log (LogUpload); Serviço de aplicativo InMage Scout; Agente de serviços de recuperação do Microsoft Azure (obengine); InMage Scout VX Agent – Sentinela/posto avançado (svagents); tmansvc; World Wide Web (W3SVC); do serviço de publicação MySQL; Serviço do Microsoft Azure Site Recovery (dra)
**Servidor de processo em execução como um servidor autônomo** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de Upload de log (LogUpload); Serviço de aplicativo InMage Scout; Agente de serviços de recuperação do Microsoft Azure (obengine); InMage Scout VX Agent – Sentinela/posto avançado (svagents); tmansvc.
**Servidor de processo implantado no Azure para failback** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Serviço de Upload de log (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Etapa 3: Verifique a pulsação do servidor de processo

Se não houver nenhuma pulsação do servidor de processo (código de erro 806), faça o seguinte:

1. Verifique se a VM do servidor de processo está em execução.
2. Verifique esses logs de erros.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Verifique a conectividade e replicação

 Falhas de replicação inicial e contínua geralmente são causadas por problemas de conectividade entre os computadores de origem e o servidor de processo ou entre o servidor de processo e o Azure. Essas etapas são resumidas no gráfico a seguir, seguido pelos procedimentos para ajudá-lo a executar as etapas.

![Solucionar problemas de conectividade e replicação](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Etapa 4: Verifique se a sincronização de horário na máquina de origem

Certifique-se de que a data/hora do sistema para o computador replicado está em sincronia. [Saiba mais](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Etapa 5: Verifique se o software antivírus no computador de origem

Verifique se nenhum software antivírus no computador replicado está bloqueando o Site Recovery. Se você precisar excluir o Site Recovery de outros programas antivírus, examine [deste artigo](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Etapa 6: Verifique a conectividade do computador de origem


1. Instalar o [o cliente Telnet](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) no computador de origem, se você precisar. Não use o Ping.
2. Da máquina de origem, execute ping no servidor de processo na porta HTTPS com o Telnet. Por padrão 9443 é a porta HTTPS para tráfego de replicação.

    `telnet <process server IP address> <port>`

3. Verificar se a conexão foi bem-sucedida.


**Conectividade** | **Detalhes** | **Ação**
--- | --- | ---
**Bem-sucedida** | Telnet mostra uma tela em branco e o servidor de processo está acessível. | Nenhuma outra ação é necessária.
**Unsuccessful** | Você não pode se conectar. | Certifique-se de que a porta 9443 de entrada é permitida no servidor de processo. Por exemplo, se você tiver uma rede de perímetro ou uma sub-rede filtrada. Verifique a conectividade.
**Parcialmente bem-sucedida** | Você pode se conectar, mas o computador de origem informa que o servidor de processo não pode ser acessado. | Continue com o próximo procedimento de solução de problemas.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Etapa 7: Solucionar problemas de um servidor de processo inacessível

Se o servidor de processo não pode ser acessado da máquina de origem, o erro 78186 será exibido. Se não tiver sido abordado, esse problema nos leva a ambos consistentes com o aplicativo e pontos de recuperação consistentes com falhas não seja gerado conforme o esperado.

Solução de problemas verificando se o computador de origem pode alcançar o endereço IP do servidor de processo e executar a ferramenta de cxpsclient no computador de origem, para verificar a conexão de ponta a ponta.


### <a name="check-the-ip-connection-on-the-process-server"></a>Verifique a conexão de IP no servidor de processo

Se o telnet é bem-sucedida, mas o computador de origem informa que o servidor de processo não pode ser acessado, verifique se você consegue acessar o endereço IP do servidor de processo.

1. Em um navegador da web, tente alcançar IP endereço https://<PS_IP>:<PS_Data_Port>/.
2. Se essa verificação mostrará um erro de certificado HTTPS, que é normal. Se você ignorar o erro, você deverá ver um 400 - Solicitação incorreta. Isso significa que o servidor não pode servir a solicitação do navegador e que a conexão HTTPS padrão é bom.
3. Se essa verificação não funcionar, em seguida, observe a mensagem de erro do navegador. Por exemplo, um erro de 407 indicará um problema com a autenticação de proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Verifique a conexão com cxpsclient

Além disso, você pode executar a ferramenta cxpsclient para verificar a conexão de ponta a ponta.

1. Execute a ferramenta da seguinte maneira:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. No servidor de processo, verifique os logs gerados nessas pastas:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Verifique os logs da VM de origem para carregar falhas (erro 78028)

Problema com carregamentos de dados bloqueado máquinas de origem para o serviço de processos pode resultar em ambos os pontos de recuperação consistentes com falhas e consistente com o aplicativo não seja gerados. 

1. Para solucionar problemas de falhas de carregamento de rede, você pode procurar por erros neste log:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Use o restante dos procedimentos neste artigo pode ajudar a resolver problemas de carregamento de dados.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Etapa 8: Verifique se o servidor de processo é enviando dados por push

Verifique se o servidor de processo está ativamente enviando dados por push para o Azure.

  1. No servidor de processo, abra o Gerenciador de Tarefas (pressione Ctrl+Shift+Esc).
  2. Selecione o **desempenho** guia > **Monitor abrir recurso**.
  3. Na **o Monitor de recursos** página, selecione o **rede** guia. Sob **processos com atividade de rede**, verifique se cbengine.exe está enviando ativamente um vNotolume grande de dados.

       ![Volumes em processos com atividade de rede](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Se cbengine.exe não estiver enviando um grande volume de dados, conclua as etapas nas seções a seguir.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Etapa 9: Verifique a conexão de servidor de processo para o armazenamento de BLOBs do Azure

1. No Monitor de recursos, selecione **cbengine.exe**.
2. Sob **conexões TCP**, verifique se há conectividade do servidor de processo para o armazenamento do Azure.

  ![Conectividade entre cbengine.exe e a URL de armazenamento de BLOBs do Azure](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Serviços de verificação

Se não houver nenhuma conectividade do servidor de processo para a URL de armazenamento de BLOBs do Azure, verifique se os serviços estão em execução.

1. No painel de controle, selecione **Services**.
2. Verifique se os seguintes serviços estão em execução:

    - cxprocessserver
    - Agente do InMage Scout VX – Sentinel/Outpost
    - Agente de Serviços dos Serviços de Recuperação do Microsoft Azure
    - Serviço do Microsoft Azure Site Recovery
    - tmansvc

3. Iniciar ou reiniciar qualquer serviço que não está em execução.
4. Verifique se o servidor de processo está conectado e acessível. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Etapa 10: verificar a conexão de servidor de processo para o endereço IP público do Azure

1. No servidor de processo, na **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, abra o arquivo ErrLog mais recente.
2. No arquivo, procure **443**, ou para a cadeia de caracteres **Falha na tentativa de conexão**.

  ![Logs de erros na pasta Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Se você encontrar problemas, localizado seu endereço IP público do Azure no arquivo Currlog usando a porta 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Na linha de comando no servidor de processo, use o Telnet para o endereço IP público do Azure.
6. Se você não pode se conectar, siga o procedimento a seguir.

## <a name="step-11-check-process-server-firewall-settings"></a>Etapa 11: Verifique as configurações de firewall do servidor de processo. 

Verifique se o firewall baseado em endereço IP no servidor de processo está bloqueando o acesso.

1. Para regras de firewall baseadas em endereço IP:

    a) Baixar a lista completa dos [intervalos de IP de datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

    b) adicione intervalos de endereços IP na sua configuração de firewall, para garantir que o firewall permita a comunicação para o Azure (e para a porta HTTPS padrão 443).

    Permitir c) intervalos de endereços IP para a região do Azure de sua assinatura e para a região do Azure Oeste dos EUA (usados para gerenciamento de identidade e controle de acesso).

2. Para firewalls baseados em URL, adicione as URLs listadas na tabela a seguir à configuração do firewall.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Etapa 12: Verifique se as configurações de proxy de servidor de processo 

1. Se você estiver usando um servidor proxy, verifique se o nome do servidor proxy é resolvido pelo servidor DNS. Verifique o valor que você forneceu ao configurar o servidor de configuração na chave do registro **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Certifique-se de que as mesmas configurações são usadas pelo agente de recuperação de Site do Azure para enviar dados.

    a) pesquise **Backup do Microsoft Azure**.

    b) abra **Backup do Microsoft Azure**e selecione **ação** > **alterar propriedades**.

    c) na **configuração de Proxy** guia, o endereço de proxy deve ser o mesmo como o endereço de proxy que é exibido nas configurações do registro. Caso contrário, altere-a para o mesmo endereço.

## <a name="step-13-check-bandwidth"></a>Etapa 13: Verificar a largura de banda

Aumentar a largura de banda entre o servidor de processo e o Azure e, em seguida, verifique se o problema ainda ocorrer.


## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png