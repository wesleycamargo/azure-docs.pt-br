---
title: Solução de Monitoramento de VMware no Log Analytics | Microsoft Docs
description: Saiba mais sobre como a solução de Monitoramento de VMware pode ajudar a gerenciar logs e monitorar hosts ESXi.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: banders

---
# <a name="vmware-monitoring-(preview)-solution-in-log-analytics"></a>Solução de Monitoramento de VMware (visualização) no Log Analytics
A solução de monitoramento de VMware no Log Analytics é uma solução que ajuda você a criar uma abordagem centralizada de registro em log e monitoramento para logs do VMware de tamanho grande. Este artigo descreve como você pode solucionar problemas, capturar e gerenciar os hosts ESXi em uma única localização usando a solução. Com a solução, você pode ver os dados detalhados de todos os seus hosts ESXi em uma única localização. Você pode ver os principais eventos contagens, status e tendências de VM e hosts ESXi fornecidos por meio dos logs de host ESXi. Você pode solucionar problemas exibindo e pesquisando logs de host ESXi centralizados. Além disso, você pode criar alertas com base em consultas de pesquisa de log.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

* Adicione a solução de Monitoramento VMware ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics da Galeria de Soluções](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Hosts VMware ESXi com suporte
Host vSphere ESXi 5.5 e 6.0

#### <a name="prepare-a-linux-server"></a>Preparar um servidor Linux
Crie um sistema operacional do Linux VM para receber todos os dados syslog de hosts ESXi. O [Agente do OMS para Linux](log-analytics-linux-agents.md) é o ponto de coleta para todos os dados syslog de hosts ESXi. Você pode usar vários hosts ESXi para encaminhar logs para um único servidor Linux, como no exemplo a seguir.  

   ![fluxo syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar coleção syslog
1. Configure o encaminhamento syslog para VSphere. Para obter informações detalhadas ajudar a configurar o encaminhamento syslog, veja [Configurar syslog em ESXi 5. x e 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Vá para **Configuração de Host ESXi** > **Software** > **Configurações Avançadas** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. No campo *Syslog.global.logHost*, adicione o servidor Linux e o número da porta *1514*. Por exemplo, `tcp://hostname:1514` ou `tcp://123.456.789.101:1514`
3. Abra o firewall do host ESXi para syslog. **Configuração do Host ESXi** > **Software** > **Perfil de Segurança** > **Firewall** e abra **Propriedades**.  
   
    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  
   
    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Marque o Console vSphere para confirmar que esse syslog está configurado corretamente. Confirme no host ESXI que a porta **1514** está configurada.
5. Teste a conectividade entre o servidor Linux e o host ESXi usando o comando `nc` no Host ESXi. Por exemplo:
   
    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```
6. Baixe e instale o agente do OMS para Linux no servidor Linux. Veja a [Documentação do Agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) para obter mais informações.
7. Depois que o agente do OMS para Linux estiver instalado, vá para o diretório /etc/opt/microsoft/omsagent/sysconf/omsagent.d directory and copy the vmware_esxi.conf file to the /etc/opt/microsoft/omsagent/conf/omsagent.d e altere o proprietário/grupo e as permissões do arquivo. Por exemplo:
   
    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
8. Reinicie o Agente do OMS para Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart`.
9. No Portal do OMS, execute uma pesquisa de log de `Type=VMware_CL`. Quando o OMS coleta os dados syslog, ele retém o formato syslog. No portal, alguns campos específicos são capturados, como *Hostname* e *ProcessName*.  
   
    ![type](./media/log-analytics-vmware/type.png)  
   
    Se seus resultados da pesquisa de log de exibição forem semelhantes àqueles da imagem acima, você estará pronto para usar o painel de solução de Monitoramento de VMWare do OMS.  

## <a name="vmware-data-collection-details"></a>Detalhes da coleta de dados do VMware
A solução de monitoramento do VMware coleta vários dados de log e métricas de desempenho de hosts ESXi usando os Agentes do OMS para Linux que você tenha habilitado.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados.

| plataforma | Agente do OMS para Linux | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![Sim](./media/log-analytics-vmware/oms-bullet-green.png) |![Não](./media/log-analytics-vmware/oms-bullet-red.png) |![Não](./media/log-analytics-vmware/oms-bullet-red.png) |![Não](./media/log-analytics-containers/oms-bullet-red.png) |![Não](./media/log-analytics-vmware/oms-bullet-red.png) |a cada 3 minutos |

A tabela a seguir mostram exemplos de campos de dados coletados pela solução de Monitoramento de VMware:

| nome do campo | Descrição |
| --- | --- |
| Device_s |dispositivos de armazenamento do VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |horário em que o evento ocorreu |
| HostName_s |nome de host ESXi |
| Operation_s |criar VM ou excluir VM |
| ProcessName_s |nome do evento |
| ResourceId_s |nome do host do VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Status de SCSI do VMware |
| SyslogMessage_s |Dados syslog |
| UserName_s |usuário que criou ou excluiu a VM |
| VMName_s |Nome da VM |
| Computador |computador host |
| TimeGenerated |horário em que os dados foram gerados |
| DataCenter_s |Datacenter do VMware |
| StorageLatency_s |latência de armazenamento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Visão geral da solução de Monitoramento de VMware
O bloco VMware aparece no portal do OMS. Ele fornece uma visão ampla de eventuais falhas. Quando você clica no bloco, você entra em um modo de exibição de painel.

![bloco](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegar pelo modo de exibição de painel
No modo de exibição de painel **VMware**, as folhas são organizadas por:

* Contagem de Status de Falha
* Contagens de hosts principais por evento
* Contagens de eventos principais
* Atividades da Máquina Virtual
* Eventos de Disco do Host ESXi

![solução1](./media/log-analytics-vmware/solutionview1-1.png)

![solução2](./media/log-analytics-vmware/solutionview1-2.png)

Clique em qualquer folha para abrir o painel de pesquisa do Log Analytics que mostra informações detalhadas específicas para essa folha.

Desse ponto em diante, você pode editar a consulta de pesquisa para modificá-lo para algo específico. Para obter um tutorial sobre os conceitos básicos da pesquisa do OMS, confira o [Tutorial de pesquisa de log do OMS.](log-analytics-log-searches.md)

#### <a name="find-esxi-host-events"></a>Encontrar eventos do host ESXi
Um único host ESXi gera vários logs, com base em seus processos. A solução de Monitoramento do VMware centraliza-os e resume as contagens de eventos. Essa exibição centralizada ajuda a entender qual host ESXi tem um alto volume de eventos e quais eventos ocorrem com mais frequência em seu ambiente.

![evento](./media/log-analytics-vmware/events.png)

Você pode aprofundar-se ainda mais, clicando em um host ESXi ou um tipo de evento.

Quando você clica em um nome de host ESXi, as informações desse host são exibidas. Se você quiser restringir os resultados com o tipo de evento, deverá adicionar `“ProcessName_s=EVENT TYPE”` em sua consulta de pesquisa. Você pode selecionar **ProcessName** no filtro de pesquisa. Isso restringe as informações para você.

![aprofundar-se](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar atividades altas de VM
Uma máquina virtual pode ser criada em e excluída de qualquer host ESXi. Isso é útil para um administrador identificar a quantidade de VMs criadas por um host ESXi. Isso, por sua vez, ajuda a entender o planejamento de capacidade e desempenho. Manter o controle dos eventos de atividade de VM é essencial ao gerenciar seu ambiente.

![aprofundar-se](./media/log-analytics-vmware/vmactivities1.png)

Se você quiser ver dados adicionais de criação de VMs de host ESXi, clique em um nome de host ESXi.

![aprofundar-se](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Consultas de pesquisa comuns
A solução inclui outras consultas úteis que podem ajudá-lo a gerenciar seus hosts ESXi, como espaço de armazenamento de alta latência de armazenamento e falha do caminho.

![consultas](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Salvar consultas
Salvar consultas de pesquisa é um recurso padrão do OMS e pode lhe ajudar a manter todas as consultas que você considerar úteis. Depois de criar uma consulta você considerar útil, salve-a clicando em **Favoritos**. Uma consulta salva permite que você reutilize-a posteriormente com facilidade usando a página [Meu Painel](log-analytics-dashboards.md), na qual você pode criar seus próprios painéis personalizados.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas com base em consultas
Depois de criar as consultas, talvez você queira usá-las para alertá-lo quando eventos específicos ocorrem. Veja [Alertas no Log Analytics](log-analytics-alerts.md) para obter informações sobre como criar alertas. Para obter exemplos de consultas de alertas e outros exemplos de consultas, veja a postagem de blog [Monitorar VMware usando o OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics).

## <a name="next-steps"></a>Próximas etapas
* Usar [Pesquisas de Log](log-analytics-log-searches.md) no Log Analytics para exibir dados detalhados de hosts do VMware.
* [Criar seus próprios painéis](log-analytics-dashboards.md) mostrando os dados de host do VMware.
* [Criar alertas](log-analytics-alerts.md) quando ocorrem eventos específicos de host do VMware.

<!--HONumber=Oct16_HO2-->


