<properties
   pageTitle="Migrando do Orchestrator para a Automação do Azure | Microsoft Azure"
   description="Descreve como migrar runbooks e pacotes de integração do System Center Orchestrator para a Automação do Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/18/2015"
   ms.author="bwren" />


# Migrando do Orchestrator para a Automação do Azure

Os runbooks no [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) são baseados nas atividades de pacotes de integração que são escritos especificamente para o Orchestrator, enquanto os runbooks na Automação do Azure são baseados em Fluxos de Trabalho do Windows PowerShell. Os runbooks gráficos na Automação do Azure têm uma aparência semelhante aos runbooks do Orchestrator, com suas atividades representando cmdlets do PowerShell, runbooks filhos e ativos.

O [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo na conversão de runbooks do Orchestrator para a Automação do Azure. Além de converter os próprios runbooks, você deve converter os pacotes de integração com as atividades que eles usam para os módulos de integração com os cmdlets do Windows PowerShell.

Eis o processo básico para converter runbooks do Orchestrator para a Automação do Azure. Cada uma dessas etapas é descrita detalhadamente nas seções a seguir.

1.  Baixe o [System Center Orchestrator Migration Toolkit](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e os módulos discutidos neste artigo.
2.  Instale o [Módulo de atividades padrão](#standard-activities-module) na Automação do Azure. Isso inclui versões convertidas de atividades padrão do Orchestrator que podem ser usadas por runbooks convertidos.
2.  Instale os [Módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) na Automação do Azure para os pacotes de integração usados pelos seus runbooks.
3.  Converta os pacotes personalizados e de integração de terceiros usando o [Conversor de Pacote de Integração](#integration-pack-converter) e instale na Automação do Azure.
4.  Recrie manualmente os ativos globais no Orchestrator na Automação do Azure, já que não há nenhum método automatizado para realizar essa migração.
5.  Converta os runbooks do Orchestrator usando o [Runbook Converter](#runbook-converter-coming-soon) (em breve) e instale na Automação do Azure.
6.  Configure um [Runbook Worker Híbrido](#hybrid-runbook-worker) em seu data center local para executar runbooks convertidos.

## Automação de Gerenciamento de Serviços

O SMA ([Automação de Gerenciamento de Serviços](http://technet.microsoft.com/library/dn469260.aspx)) armazena e executa runbooks em seu data center local, como o Orchestrator, e usa os mesmo módulos de integração que a Automação do Azure. Quando estiver disponível, o [Runbook Converter](#runbook-converter-coming-soon) converterá runbooks do Orchestrator em runbooks gráficos, que no entanto não têm suporte no SMA. Você ainda pode instalar o [Módulo de atividades padrão](#standard-activities-module) e os [Módulos de integração do System Center Orchestrator](#system-center-orchestrator-integration-modules) no SMA, mas deve [reescrever seus runbooks](http://technet.microsoft.com/library/dn469262.aspx) manualmente.

## Runbook Worker Híbrido

Os runbooks do Orchestrator são armazenados em um servidor de banco de dados e executados em servidores runbook, ambos em seu data center local. Os runbooks na Automação do Azure são armazenados na nuvem do Azure e podem ser executados em seu data center local usando um [Runbook Worker Híbrido](automation-hybrid-runbook-worker.md). É dessa forma que você normalmente irá executar runbooks convertidos do Orchestrator, já que foram projetados para ser executados em servidores locais.

## Conversor de Pacote de Integração

O Conversor de Pacote de Integração converte pacotes de integração criados usando o Orchestrator Integration Toolkit (OIT) em módulos de integração baseados no Windows PowerShell que podem ser importados para a Automação do Azure ou para o SMA.

Quando você executa o Conversor de Pacote de Integração, aparece um assistente que permite que você selecione um arquivo de pacote de integração (.oip). Em seguida, o assistente lista as atividades incluídas no pacote de integração e permite que você selecione quais serão migrados. Quando você conclui o assistente, ele cria um módulo que inclui um cmdlet correspondente para cada uma das atividades no pacote de integração original.


### Parâmetros

Todas as propriedades de uma atividade no pacote de integração são convertidas em parâmetros do cmdlet correspondente no módulo de integração. Os cmdlets do Windows PowerShell têm um conjunto de [parâmetros comuns](http://technet.microsoft.com/library/hh847884.aspx) que podem ser usados com todos os cmdlets. Por exemplo, o parâmetro -Verbose faz com que um cmdlet produza informações detalhadas sobre a sua operação. Nenhum cmdlet pode ter um parâmetro com o mesmo nome de um parâmetro comum. Se uma atividade possui uma propriedade com o mesmo nome de um parâmetro comum, o assistente solicitará que você forneça outro nome para o parâmetro.

### Monitorar atividades

Monitore runbooks na inicialização do Orchestrator com uma [atividade de monitoramento](http://technet.microsoft.com/library/hh403827.aspx) e execute continuamente aguardando para ser chamada por um evento específico. A Automação do Azure não dá suporte a runbooks de monitoramento, então nenhuma atividade de monitoramento no pacote de integração será convertida. Em vez disso, um cmdlet de espaço reservado é criado no módulo de integração para a atividade de monitoramento. Esse cmdlet não tem nenhuma funcionalidade, mas permite a instalação de qualquer runbook convertido que o utilize. Esse runbook não poderá ser executado na Automação do Azure, mas pode ser instalado para que o usuário possa modificá-lo.

### Pacotes de integração que não podem ser convertidos

Os pacotes de integração que não foram criados com o OIT, incluindo alguns criados pela Microsoft, não podem ser convertidos com essa ferramenta. Os pacotes de integração fornecidos pela Microsoft foram convertidos em módulos de integração para que posam ser instalados na Automação do Azure ou no SMA.


## Módulo de atividades padrão

O Orchestrator inclui um conjunto de [atividades padrão](http://technet.microsoft.com/library/hh403832.aspx) que não está incluído em um pacote de integração, mas é usado por vários runbooks. O módulo de atividades padrão é um módulo de integração que inclui um cmdlet equivalente para cada uma dessas atividades. Você deve instalar esse módulo de integração na Automação do Azure antes de importar todos os runbooks convertidos que usam uma atividade padrão.

Além de dar suporte a runbooks convertidos, os cmdlets no módulo de atividades padrão podem ser usados por alguém que esteja familiarizado com o Orchestrator para criar novos runbooks na Automação do Azure. Embora as funcionalidades de todas as atividades padrão possam ser executadas com os cmdlets, eles podem operar de forma diferente. Os cmdlets no módulo de atividades padrão convertido funcionarão da mesma forma que as suas atividades correspondentes e usarão os mesmos parâmetros. Isso pode ajudar o criador do runbook do Orchestrator existente na sua transição para os runbooks da Automação do Azure.

## Módulos de integração do System Center Orchestrator
A Microsoft fornece [pacotes de integração](http://technet.microsoft.com/library/hh295851.aspx) para a criação de runbooks de automação de outros produtos e componentes do System Center. Atualmente, quando você baixa alguns desses pacotes de integração do [TechNet](http://www.microsoft.com/download/details.aspx?id=39622), eles não podem ser convertidos com o Conversor de Pacote de Integração devido a um problema conhecido que será corrigido com a versão RC do System Center Orchestrator Migration Toolkit. Os [Módulos de integração do System Center Orchestrator](http://www.microsoft.com/download/details.aspx?id=47324&WT.mc_id=rss_alldownloads_all) incluem versões convertidas desses pacotes de integração que podem ser importadas na Automação do Azure e no SMA antes dessa versão.

## Runbook Converter (em breve)

Essa ferramenta converterá runbooks do Orchestrator em runbooks gráficos que podem ser importados para a Automação do Azure. Mais detalhes sobre essa ferramenta serão fornecidos aqui quando ela estiver disponível.

## Artigos relacionados

- [System Center 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Automação de Gerenciamento de Serviço](https://technet.microsoft.com/library/dn469260.aspx)
- [Runbook Worker Híbrido](automation-hybrid-runbook-worker.md)
- [Atividades padrão do Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
 

<!---HONumber=August15_HO8-->