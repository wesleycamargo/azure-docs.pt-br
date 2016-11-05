---
title: Solução de Avaliação de Atualização do Sistema no Log Analytics | Microsoft Docs
description: Você pode usar a solução Atualizações do Sistema no Log Analytics para ajudá-lo a aplicar as atualizações ausentes aos servidores em sua infraestrutura.
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
ms.date: 08/11/2016
ms.author: banders

---
# <a name="system-update-assessment-solution-in-log-analytics"></a>Solução de Avaliação de Atualização do Sistema no Log Analytics
Você pode usar a solução Atualizações do Sistema no Log Analytics para ajudá-lo a aplicar as atualizações ausentes aos servidores em sua infraestrutura. Depois que a solução for instalada, você poderá exibir as atualizações ausentes em seus servidores monitorados usando o bloco **Avaliação da Atualização do Sistema** na página **Visão Geral** no OMS.

Se forem encontradas atualizações ausentes, os detalhes serão mostrados no painel **Atualizações** . Você pode usar o painel **Atualizações** para trabalhar com as atualizações ausentes e desenvolver um plano para aplicá-las aos servidores que precisam delas.

## <a name="installing-and-configuring-the-solution"></a>Instalando e configurando a solução
Use as informações a seguir para instalar e configurar a solução.

* Adicione a solução Avaliação da Atualização do Sistema ao seu espaço de trabalho do OMS usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).  Não é necessária nenhuma configuração.

## <a name="system-update-data-collection-details"></a>Detalhes da coleta de dados da Atualização do Sistema
A Avaliação de Atualização do Sistema coleta dados de estado e metadados estado usando os agentes que você tiver habilitado.

A tabela a seguir mostra os métodos de coleta de dados e outros detalhes sobre como os dados são coletados para a Avaliação da Atualização do Sistema.

| plataforma | Agente direto | Agente SCOM | Armazenamento do Azure | SCOM necessário? | Os dados do agente SCOM enviados por meio do grupo de gerenciamento | frequência de coleta |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![Sim](./media/log-analytics-system-update/oms-bullet-green.png) |![Sim](./media/log-analytics-system-update/oms-bullet-green.png) |![Não](./media/log-analytics-system-update/oms-bullet-red.png) |![Não](./media/log-analytics-system-update/oms-bullet-red.png) |![Sim](./media/log-analytics-system-update/oms-bullet-green.png) |Pelo menos, 2 vezes por dia e 15 minutos após a instalação de uma atualização |

A tabela a seguir mostra exemplos de tipos de dados coletados pela Avaliação de Atualização do Sistema:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Metadados |BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, IP Address, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Estado |StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

### <a name="to-work-with-updates"></a>Para trabalhar com atualizações
1. Na página **Visão Geral**, clique no bloco **Avaliação de Atualização do Sistema**.  
    ![Avaliação de atualização do sistema](./media/log-analytics-system-update/sys-update-tile.png)
2. No painel **Atualizações** , exiba as categorias de atualização.  
    ![Painel Atualizações](./media/log-analytics-system-update/sys-updates02.png)
3. Role para a direita da página para exibir a folha **Atualizações Críticas/de Segurança do Windows** e, em **Classificação**, clique em **Atualizações de segurança**.  
    ![Atualizações de Segurança](./media/log-analytics-system-update/sys-updates03.png)
4. Na página Pesquisa de Log, são mostradas diversas informações sobre as atualizações de segurança ausentes dos servidores na sua infraestrutura. Clique em **Lista** para exibir informações detalhadas sobre as atualizações.  
    ![Resultados da Pesquisa - Lista](./media/log-analytics-system-update/sys-updates04.png)
5. Na página de Pesquisa de Log, informações detalhadas sobre cada atualização são mostradas. Ao lado do número KBID, clique em **Visualizar** para exibir o artigo correspondente no site de suporte da Microsoft.  
    ![Resultados da Pesquisa de Log - Visualizar KB](./media/log-analytics-system-update/sys-updates05.png)
6. Seu navegador da web abre a página de suporte da Microsoft para a atualização em uma nova guia. Exiba as informações sobre a atualização que está ausente.  
    ![Página da Web de Suporte da Microsoft](./media/log-analytics-system-update/sys-updates06.png)
7. Usando as informações encontradas, você pode criar um plano para aplicar manualmente a atualização ausente ou você pode continuar seguindo as etapas restantes para aplicar automaticamente a atualização.
8. Se você deseja aplicar automaticamente a atualização ausente, vá até o painel **Atualizações** e, em **Atualizar execuções**, clique em **Clique para agendar uma execução de atualização**.  
    ![Execuções de atualização - guia Agendado](./media/log-analytics-system-update/sys-updates07.png)
9. Na página **Atualizar execuções** na guia **Agendado**, clique em **Adicionar** para criar uma nova execução de atualização.  
    ![Guia Agendado - Adicionar](./media/log-analytics-system-update/sys-updates08.png)
10. Sobre a página **Nova execução de atualização**, digite um nome para a execução de atualização, adicionar computadores individuais ou grupos de computadores, defina uma agenda e, em seguida, clique em **Salvar**.  
    ![Nova execução de atualização](./media/log-analytics-system-update/sys-updates09.png)
11. A guia **Agendado** na página **Execuções de atualizações** mostra a nova execução de atualização que você agendou.  
    ![Guia Agendado](./media/log-analytics-system-update/sys-updates10.png)
12. Quando a execução de atualização for iniciada, você verá informações na guia **Executando**.  
    ![Guia Executando](./media/log-analytics-system-update/sys-updates11.png)
13. Após a conclusão da execução da atualização, a guia **Concluído** exibe o status.
14. Se as atualizações foram aplicadas a partir da execução da atualização, a folha **Atualizações Críticas/de Segurança do Windows**, você verá que o número de atualizações é reduzido.  
    ![Folha de Atualizações Críticas/de Segurança do Windows - contagem de atualização reduzida](./media/log-analytics-system-update/sys-updates12.png)

## <a name="next-steps"></a>Próximas etapas
* [Pesquise nos logs](log-analytics-log-searches.md) para exibir dados detalhados da atualização do sistema.

<!--HONumber=Oct16_HO2-->


