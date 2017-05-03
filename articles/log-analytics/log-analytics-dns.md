---
title: "Solução Análise de DNS no Azure Log Analytics | Microsoft Docs"
description: "Configure e use a solução Análise de DNS no Log Analytics para coletar informações relacionadas à segurança, ao desempenho e às operações na infraestrutura DNS."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f44a40c4-820a-406e-8c40-70bd8dc67ae7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 4473ca68374b96f10c60282135e83f7ec390bb48
ms.lasthandoff: 04/20/2017


---

# <a name="gather-insights-about-your-dns-infrastructure-with-the-dns-analytics-preview-solution"></a>Coletar informações sobre sua infraestrutura DNS com a solução Análise de DNS (Visualização)

Este artigo descreve como configurar e usar a solução Análise de DNS no Log Analytics para coletar informações relacionadas à segurança, ao desempenho e às operações na infraestrutura DNS.

A Análise de DNS ajuda a:

- Identificar clientes que tentam resolver nomes de domínio mal-intencionados
- Identificar registros de recursos obsoletos
- Identificar os nomes de domínio consultados com frequência e clientes DNS comunicativos
- Exibir carga de solicitação em servidores DNS
- Exibir falhas de registro de DNS dinâmico

A solução coleta, analisa e correlaciona logs de auditoria e analíticos de DNS do Windows, bem como outros dados relacionados de seus servidores DNS.

## <a name="connected-sources"></a>Fontes conectadas

A tabela a seguir descreve as fontes conectadas que têm suporte dessa solução.

| **Fonte Conectada** | **Suporte** | **Descrição** |
| --- | --- | --- |
| [Agentes do Windows](log-analytics-windows-agents.md) | Sim | A solução coleta informações de DNS dos agentes do Windows. |
| [Agentes do Linux](log-analytics-linux-agents.md) | Não | A solução não coleta informações de DNS dos agentes diretos do Linux. |
| [Grupo de gerenciamento do SCOM](log-analytics-om-agents.md) | Sim | A solução coleta informações de DNS dos agentes em um grupo de gerenciamento SCOM conectado. Não é necessário ter uma conexão direta do agente SCOM com o OMS. Os dados são encaminhados do grupo de gerenciamento para o repositório do OMS. |
| [Conta de armazenamento do Azure](log-analytics-azure-storage.md) | Não | O armazenamento do Azure não é usado pela solução. |

### <a name="data-collection-details"></a>Detalhes da coleta de dados

A solução coleta dados relacionados a eventos e ao inventário DNS dos servidores DNS, onde um agente do Log Analytics está instalado. Esses dados são carregados no Log Analytics e exibidos no painel de solução. Os dados relacionados ao inventário, como número de servidores DNS, zonas e registros de recursos são coletados pela execução de cmdlets do Powershell para DNS. Os dados são atualizados uma vez a cada dois dias. Os dados relacionados a eventos são coletados quase em tempo real dos [logs de auditoria e analíticos](https://technet.microsoft.com/library/dn800669.aspx#enhanc) fornecidos pelo registro avançado de log e diagnóstico DNS no Windows Server 2012 R2.

## <a name="configuration"></a>Configuração

Use as informações a seguir para configurar a solução.

- Você deve ter um agente do [Windows](log-analytics-windows-agents.md) ou do [Operations Manager](log-analytics-om-agents.md) em cada servidor DNS que queira monitorar.
- Adicione a solução Análise de DNS ao seu espaço de trabalho do OMS do [marketplace do Azure](https://aka.ms/dnsanalyticsazuremarketplace) ou usando o processo descrito em [Adicionar soluções do Log Analytics por meio da Galeria de Soluções](log-analytics-add-solutions.md).

A solução inicia a coleta de dados sem a necessidade de configuração adicional. No entanto, você pode usar a configuração a seguir para personalizar a coleta de dados.

###  <a name="configure-the-solution"></a>Configurar a solução

No painel de solução, clique em **Configuração** para abrir a página Configuração de Análise de DNS. Há dois tipos de alteração de configuração que podem ser feitos:

- **Nomes de Domínio na Lista de Permissões** A solução não processa todas as consultas de pesquisa. Ela mantém uma lista de permissões de sufixos de nome de domínio. As consultas de pesquisa, que são resolvidas para os nomes de domínio que correspondem aos sufixos de nome de domínio na lista de permissões, não são processadas pela solução. O não processamento de nomes de domínio na lista de permissões ajuda a otimizar os dados enviados ao Log Analytics. A lista de permissões padrão inclui nomes de domínio público populares, como www.google.com e www.facebook.com. Você pode exibir a lista padrão completa usando a barra de rolagem.

É possível modificar a lista para adicionar (ou remover) qualquer sufixo de nome de domínio no qual você não esteja interessado em exibir informações de pesquisa.

- **Limite de Cliente Comunicativo** Os clientes DNS, que excedem o limite para o número de solicitações de pesquisa, são destacados na folha **Clientes DNS**. O limite padrão é 1000. Você pode editar o limite.

![nomes de domínio na lista de permissões](./media/log-analytics-dns/dns-config.png)

## <a name="management-packs"></a>Pacotes de gerenciamento

Se você estiver usando o MMA (Microsoft Monitoring Agent) para se conectar ao seu espaço de trabalho do OMS, o pacote de gerenciamento a seguir será instalado.

- Pacote de Inteligência do Coletor de Dados DNS da Microsoft (Microsft.IntelligencePacks.Dns)

Se o grupo de gerenciamento do SCOM estiver conectado ao seu espaço de trabalho do OMS, os pacotes de gerenciamento a seguir serão instalados no SCOM quando você adicionar essa solução. Não é necessária nenhuma configuração nem a manutenção desses pacotes de gerenciamento.

- Pacote de Inteligência do Coletor de Dados DNS da Microsoft (Microsft.IntelligencePacks.Dns)
- Configuração da Análise de DNS do Microsoft System Center Advisor (Microsoft.IntelligencePack.Dns.Configuration)

Para saber mais sobre como os pacotes de gerenciamento da solução são atualizados, veja [Conectar o Operations Manager ao Log Analytics](log-analytics-om-agents.md).

## <a name="use-the-solution"></a>Usar a solução

Esta seção explica todas as funções do painel e como usá-las.

Depois de adicionar a solução Análise de DNS ao seu espaço de trabalho, o bloco de solução na página Visão Geral do OMS fornecerá um resumo rápido da sua infraestrutura DNS. Ele inclui o número de servidores DNS onde os dados estão sendo coletados e o número de solicitações feitas pelos clientes para resolver domínios mal-intencionados nas últimas 24 horas. Quando você clica no bloco, ele abre o painel da solução.

![bloco Análise de DNS](./media/log-analytics-dns/dns-tile.png)

### <a name="solution-dashboard"></a>Painel da solução

O painel da solução mostra informações resumidas para os vários recursos da solução. Ele também inclui links para a exibição detalhada de diagnóstico e análise investigativa. Por padrão, os dados são mostrados para os últimos sete dias. Você pode alterar o intervalo de data e hora com o controle de seleção de data/hora, semelhante à imagem a seguir.

![controle de seleção de tempo](./media/log-analytics-dns/dns-time.png)

O painel da solução mostra as seguintes folhas:

**Segurança DNS**: relata os clientes DNS que estão tentando se comunicar com domínios mal-intencionados. Usando feeds de inteligência contra ameaças da Microsoft, a Análise de DNS pode detectar IPs de cliente que estão tentando acessar os domínios mal-intencionados. Em muitos casos, os dispositivos infectados por malware &quot;discam&quot; para o centro de &quot;comando e controle&quot; do domínio mal-intencionado resolvendo o nome de domínio do malware.

![folha Segurança DNS](./media/log-analytics-dns/dns-security-blade.png)

Quando você clica em um IP de cliente na lista, a Pesquisa de Logs é aberta mostrando os detalhes da pesquisa da respectiva consulta. No exemplo a seguir, a Análise de DNS detectou que a comunicação foi feita com um [IRCbot](https://www.microsoft.com/security/portal/threat/encyclopedia/entry.aspx?Name=Win32/IRCbot).

![resultados da pesquisa de logs mostrando ircbot](./media/log-analytics-dns/ircbot.png)

As informações ajudam a identificar:

- o IP do cliente que iniciou a comunicação
- o nome de domínio sendo resolvido para o IP mal-intencionado
- os endereços IP para os quais o nome de domínio é resolvido
- o endereço IP mal-intencionado
- a gravidade do problema
- o motivo para colocar o IP mal-intencionado na lista de bloqueios
- o tempo de detecção

**Domínios Consultados**: fornece os nomes de domínio consultados com mais frequência pelos clientes DNS no seu ambiente. Você pode exibir a lista de todos os nomes de domínio consultados e fazer uma busca minuciosa nos detalhes da solicitação de pesquisa de um nome de domínio específico na Pesquisa de Logs.

![Folha Domínios Consultados](./media/log-analytics-dns/domains-queried-blade.png)

**Clientes DNS**: relata os clientes que **violam os limites** do número de consultas no período escolhido. Você pode exibir a lista de todos os clientes DNS e os detalhes das consultas feitas por eles na Pesquisa de Logs.

![Folha Clientes DNS](./media/log-analytics-dns/dns-clients-blade.png)

**Registros de DNS Dinâmico**: relata as falhas de registro de nome. Todas as falhas de [registros de recursos](https://en.wikipedia.org/wiki/List_of_DNS_record_types) do endereço (Tipo A e AAAA) são destacadas com os IPs do cliente que fizeram as solicitações de registro. Você pode usar essas informações para encontrar a causa raiz da falha de registro:

1. Encontre a zona que é autoritativa para o nome que o cliente está tentando atualizar.
2. Use a solução para verificar as informações de inventário dessa zona.
3. Verifique se a atualização dinâmica para a zona está habilitada.
4. Verifique se a zona está configurada para atualização dinâmica segura ou não.

![folha Registros de DNS Dinâmico](./media/log-analytics-dns/dynamic-dns-reg-blade.png)

**Solicitações de registro de nome**: o bloco superior mostra uma tendência de contagem de solicitação de atualização dinâmica DNS com êxito ou falha. O bloco inferior lista os 10 principais clientes que enviam solicitações de atualização DNS com falha aos servidores DNS, classificados pelo número de falhas.

![folha Solicitações de registro de nome ](./media/log-analytics-dns/name-reg-req-blade.png)

**Exemplo de Consultas de Análise de DNS**: contém uma lista de consultas de pesquisa mais comuns que buscam dados analíticos brutos diretamente.

![exemplo de consultas](./media/log-analytics-dns/queries.png)

Você pode usar essas consultas como um ponto de partida para criar suas próprias consultas para relatórios personalizados.

- **Lista de servidores:** esse link abre a página de pesquisa Log DNS, mostrando uma lista de todos os servidores DNS com os respectivos FQDN, nome de domínio, nome de floresta e IPs de servidor associados.
- **Lista de zonas DNS:** esse link abre a página de pesquisa Log DNS, mostrando uma lista de todas as zonas DNS com o nome da zona, o status da atualização dinâmica, os servidores de nome e o status de assinatura do DNSSEC associados.
- **Registros de recursos não utilizados:** esse link abre a página de pesquisa Log DNS e mostra uma lista de todos os registros de recursos não utilizados/obsoletos. Essa lista contém o nome do registro de recurso, o tipo de registro de recurso, o servidor DNS associado, o tempo de geração de registro e o nome da zona. Você pode usar essa lista para identificar os registros de recursos DNS que não estão mais em uso. Com base nessas informações, é possível agir para remover essas entradas dos servidores DNS.
- **Carga de consulta de servidores DNS:** esse link abre a página de pesquisa Log DNS, na qual você pode obter uma perspectiva da carga DNS nos servidores DNS que ajuda no planejamento da capacidade dos servidores. Você pode passar para a guia **Métricas** a fim de alterar a exibição para uma visualização gráfica. Essa exibição ajuda a entender como a carga DNS é distribuída nos servidores DNS. Ela mostra as tendências da taxa de consulta DNS para cada servidor.
    ![resultados da pesquisa do log de consulta de servidores DNS](./media/log-analytics-dns/dns-servers-query-load.png)  
- **Carga de consulta das zonas DNS:** esse link abre a página de pesquisa Log DNS, na qual você pode ver as estatísticas de consulta da zona DNS por segundo de todas as zonas nos servidores DNS sendo gerenciadas pela solução. Clique na guia **Métricas** para alterar a exibição de registros detalhados para uma visualização gráfica dos resultados.
- **Eventos de configuração:** esse link abre a página de pesquisa Log DNS, na qual você pode ver todos os eventos de alteração de configuração DNS e mensagens associadas. Você pode filtrar esses eventos com base no horário do evento, na ID do evento, no servidor DNS ou na categoria da tarefa. Ela pode ajudar a auditar alterações feitas em servidores DNS específicos em horários específicos.
- **Log analítico de DNS:** esse link abre a página de pesquisa Log DNS, na qual você pode ver todos os eventos analíticos em todos os servidores DNS gerenciados pela solução. É possível filtrar esses eventos com base no horário do evento, na ID do evento, no servidor DNS, no IP do cliente que compôs a consulta de pesquisa e a categoria da tarefa do tipo de consulta. Os eventos analíticos do servidor DNS permitem o rastreamento da atividade no servidor DNS. Um evento analítico é registrado em log sempre que o servidor envia ou recebe informações DNS.

### <a name="dns-log-search"></a>Pesquisa de logs de DNS

Na página de Pesquisa, crie uma consulta e, em então, quando pesquisar, poderá filtrar os resultados por meio de controles da faceta. Você também pode criar consultas avançadas para transformar, filtrar e relatar sobre seus resultados. É possível começar usando as consultas a seguir.

No campo de consulta de pesquisa, digite `Type=DnsEvents` para exibir todos os eventos DNS gerados pelos servidores DNS gerenciados pela solução. Os resultados listam os dados do log de todos os eventos relacionados às consultas de pesquisa, aos registros dinâmicos e às alterações de configuração.

![pesquisa de logs de dnsevents](./media/log-analytics-dns/log-search-dnsevents.png)  

- Para exibir os dados de log da consulta de pesquisa, filtre por Subtipo, como **LookUpQuery**, no controle de faceta LHS. É exibida uma lista/tabela que contém os eventos de consulta de pesquisa para o período selecionado.
- Para exibir os dados de log de Registros Dinâmicos, filtre por Subtipo, como **DynamicRegistration**, no controle de faceta LHS. Uma lista/tabela contendo todos os eventos de Registros Dinâmicos é exibida para o período selecionado.
- Para exibir os dados de log das alterações na Configuração, filtre por Subtipo, como **ConfigurationChange**, no controle de faceta LHS. Uma lista/tabela contendo todos os eventos de alterações na Configuração é exibida para o período selecionado.

No campo de consulta de pesquisa, digite `Type=DnsInventory` para exibir todos os dados relacionados ao inventário DNS dos servidores DNS gerenciados pela solução. Os resultados listam os dados de log dos servidores DNS, das zonas DNS e dos registros de recursos.
![pesquisa de logs de dnsinventory](./media/log-analytics-dns/log-search-dnsinventory.png)

## <a name="feedback"></a>Comentários

Há duas rotas diferentes para fornecer comentários:

- **UserVoice** Poste ideias para recursos da Análise de DNS nos quais trabalhar. Visite a [página UserVoice do OMS](https://aka.ms/dnsanalyticsuservoice).
- **Ingresse em nosso coorte** Sempre estamos interessados em ter novos clientes em nossos coortes para que eles obtenham acesso antecipado a novos recursos e nos ajudem a melhorar a Análise de DNS. Se estiver interessado em participar de nossos coortes, preencha esta [pesquisa rápida](https://aka.ms/dnsanalyticssurvey).

## <a name="next-steps"></a>Próximas etapas

- [Pesquisar logs](log-analytics-log-searches.md) para exibir registros de log DNS detalhados.

