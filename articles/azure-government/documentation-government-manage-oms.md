---
title: Operations Management Suite do Azure Governamental | Microsoft Docs
description: "Este artigo descreve como o Log Analytics no Operations Management Suite é aplicável a provedores de solução e órgãos do Governo dos EUA"
services: azure-government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
ms.assetid: 
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/05/2016
ms.author: saweld
translationtype: Human Translation
ms.sourcegitcommit: 35cefe7f394e0f332b7b6fd4ec1609ae04f5fc6f
ms.openlocfilehash: 84eabe4bb79262c47f530c59e36119bacc4115d7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite"></a>Segurança cibernética de Azure Governamental: monitoramento e proteção de seus ativos com o Operations Management Suite 

## <a name="cybersecurity-in-the-cloud"></a>Segurança cibernética na nuvem
Uma preocupação essencial para nossos clientes que estão mudando para a nuvem é manter o gerenciamento de ativos e a segurança dos serviços do Azure Governamental que eles implantaram na nuvem. Os firewalls de máquinas virtuais precisam ser configurados corretamente. As redes virtuais precisam ter os grupos de segurança de rede certos aplicados a elas. O acesso aos seus ativos deve ser bloqueado no momento certo. Todos esses fluxos de trabalho necessários devem ser planejados, criados e provisionados para habilitar uma infraestrutura segura para uso pela sua agência.

A configuração desse tipo de ambiente pode ser um desafio. Carregar sua frota de servidores para qualquer serviço de monitoramento é uma operação difícil de dimensionar, sendo que atualizar o serviço de monitoramento também pode ser desafiador. É difícil monitorar a infraestrutura em diferentes provedores de nuvem, bem como entre a nuvem e o local. Por fim, manter o monitoramentos atualizado e permitir que o Azure Application Insights monitore, detecte, alerte e realize ações contra ameaças de segurança cibernética exige tempo, recursos e capacidade de computação.

## <a name="microsoft-operations-management-suite"></a>Microsoft Operations Management Suite
O Microsoft Operations Management Suite, agora disponível no Azure Governamental, é um conjunto de serviços do Azure que permite fazer tudo isso de maneira rápida e fácil. Este artigo se concentra no uso do Log Analytics, que usa a pesquisa de log de hiperescala para analisar seus dados rapidamente e expor ameaças em seu ambiente.

O Azure Log Analytics é capaz de:

* Implantar agentes para VMs individuais (Linux e Windows) no Azure, em outros provedores de nuvem e localmente.
* Conectar seus logs existentes por meio de uma conta de armazenamento do Azure Governamental ou ponto de extremidade do System Center Operations Manager com os dados de log existentes.

Vamos explorar como podemos integrar o Log Analytics à nossa frota e examinar algumas das soluções imediatas que abordam as preocupações que descrevemos aqui.

## <a name="onboarding-servers-to-log-analytics"></a>Carregar servidores de integração no Log Analytics
A primeira etapa na integração de seus ativos de nuvem ao Log Analytics é instalar o agente do Log Analytics em origens de log. Para máquinas virtuais, isso é muito simples porque você pode baixar manualmente o agente do portal do Log Analytics.

![Figura 1: servidores Windows conectados ao Operations Management Suite](./media/documentation-government-oms-figure1.png)
<p align="center">Figura 1: servidores Windows conectados ao Log Analytics</p>

Você pode conectar VMs do Azure ao Log Analytics diretamente por meio do Portal do Azure. Para obter instruções, consulte [Novas maneiras para habilitar o Log Analytics em suas VMs do Azure](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

Você também pode conectá-las por meio de programação ou configurar a extensão da máquina virtual do Log Analytics diretamente em seus modelos do Azure Resource Manager. Consulte as instruções para computadores baseados no Windows em [Conectar computadores Windows ao Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) e para computadores baseados em Linux em [Conectar computadores Linux ao Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-operations-manager-to-log-analytics"></a>Carregar contas de armazenamento e o Operations Manager no Log Analytics
O Log Analytics também pode se conectar à sua conta de armazenamento e/ou implantações existentes do System Center Operations Manager a fim de oferecer a você o gerenciamento de operações em cenários híbridos (entre provedores na nuvem ou em infraestruturas de nuvem/locais).

![Figura 2: conectar o Armazenamento do Azure e o Operations Manager ao Log Analytics](./media/documentation-government-oms-figure2.png)
<p align="center">Figura 2: conectar o Armazenamento do Azure e o Operations Manager ao Log Analytics</p>

O Log Analytics também dá suporte à coleta de informações de registro em log de outros serviços de monitoramento como Chef ou Puppet. Além disso, para implantações do Azure, temos VMs com modelos do Azure Resource Manager habilitadas para o Log Analytics, para que você possa implantar a computação e carregar no seu espaço de trabalho do Log Analytics simultaneamente.

![Figura 3: modelos do Azure Resource Manager para VMs do Azure com extensão da VM do Log Analytics](./media/documentation-government-oms-figure3a.png)
![Figura 3: modelos do Azure Resource Manager para VMs do Azure com extensão da VM do Log Analytics](./media/documentation-government-oms-figure3b.png)
<p align="center">Figura 3: modelos do Azure Resource Manager para VMs do Azure com extensão da VM do Log Analytics</p>

Informações sobre como configurar o Log Analytics com a sua implementação local existente do Operations Manager podem ser encontradas em [Conectar o Operations Manager ao Log Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="applying-intelligence-through-operations-management-suite-solution-packs"></a>Aplicando inteligência por meio de pacotes de solução do Operations Management Suite
Agora que você tem várias origens para gravação de dados em log, você precisa compreender todos esses dados.

O Log Analytics, em sua essência, é um serviço de pesquisa de log que permite a composição de consultas avançadas a fim de pesquisar rapidamente em milhares ou até mesmo milhões, de registros. No entanto, pode ser difícil descobrir os problemas para os quais você precisa escrever consultas.

Insira as soluções do Operations Management Suite. Estas são pacotes de consultas integrados nativamente com o Log Analytics para, proativamente, lhe dar percepções sobre sua frota gerenciada pelo Log Analytics.

No tema de segurança cibernética, discuto brevemente três cenários de segurança cibernética que o Log Analytics pode resolver imediatamente para você.

### <a name="antimalware-assessment"></a>Avaliação antimalware
As avaliações antimalware fornecem a você um conjunto definido de consultas, notificações e painéis de monitoramento para informar a você, rapidamente, o quão bem sua frota está protegida contra malware.

Este painel fornece uma lista de quatro itens:
* Todos os servidores com ameaças ativas e/ou corrigidas.
* Ameaças detectadas no momento.
* Computadores que não estão protegidos suficientemente. O Log Analytics localiza essas informações rastreando os logs de seus computadores, em busca de qualquer site de FWs que estejam sendo abertos ou de regras configuradas incorretamente em navegadores da Web comuns.
* Análise de como seus servidores protegidos estão sendo protegidos, por exemplo, por antivírus nativo do sistema operacional Windows ou por uma solução como o System Center Endpoint Protection.

Por exemplo, você pode ver que a ameaça abaixo foi detectada e automaticamente classificada pelo System Center:

![Figura 4: solução de avaliação de antimalware do Operations Management Suite](./media/documentation-government-oms-figure4.png)
<p align="center">Figura 4: solução de avaliação de antimalware do Operations Management Suite</p>

Mais informações sobre avaliação antimalware podem ser encontradas no artigo [Solução de Avaliação de Malware no Log Analytics](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/).

### <a name="identity-and-access"></a>Identidade e acesso
Outro cenário comum de segurança cibernética na nuvem gira em torno do comprometimento de credenciais. Além das credenciais de sua assinatura de nuvem, cada VM individual tem um usuário e/ou segredo (geralmente um certificado ou senha) associado a ela.

O Log Analytics organiza todas as tentativas de entrada em sua frota e as organiza dependendo do tipo (remota, local, nome de usuário usado, etc). No exemplo abaixo, vejo uma quantidade enorme de tentativas de entrada malsucedidas com uso de cadeias de caracteres amplamente aleatórias como nomes de usuário. Isso indica que há grande probabilidade de que meus computadores foram expostos e não adequadamente protegidos por firewalls e listas de controle de acesso.

![Figura 5: falha em 97,3% das entradas nas últimas 24 horas](./media/documentation-government-oms-figure5.png)
<p align="center">Figura 5: falha em 97,3% das entradas nas últimas 24 horas</p>

### <a name="threat-intelligence"></a>Inteligência contra ameaças
O Log Analytics também fornece proteção contra o cenários de insiders mal-intencionados – quando há um comprometimento da segurança dentro de sua organização e um usuário mal-intencionado está tentando remover dados.

A Inteligência contra ameaças do Log Analytics examina todos os Logs de rede em seu computador e automaticamente pesquisa por e notifica você sobre conexões de rede de entrada/saída para IPs mal-intencionados conhecidos (por exemplo, endereços IP em dark net não indexada).

Por exemplo, na captura de tela abaixo, posso ver que há conexões de rede de entrada e saída para a República Popular da China.

Clicando duas vezes na marcação de entrada, descubro que uma VM Linux gerenciada pelo Log Analytics está estabelecendo conexões de saída para um endereço IP de dark net conhecido na China.

Você também pode configurar alertas para soluções do Operations Management Suite como inteligência contra ameaças. Na captura de tela a seguir, configurei um alerta para que, caso o Log Analytics detecte mais de 10 conexões de saída para um endereço IP mal-intencionado conhecido, ele envie um alerta para mim por email. Em seguida, configuro esse alerta para disparar um trabalho de Automação do Azure configurado para desligar automaticamente a VM em questão.

![Figura 6: alertas do Operations Management Suite e automação](./media/documentation-government-oms-figure6.png)
<p align="center">Figura 6: alertas do Operations Management Suite e automação</p>

Esse é apenas um exemplo de uma solução imediata do Operations Management Suite que pode ser aplicada à sua frota, quer ela esteja em execução no Azure, em outro provedor de serviços de nuvem ou localmente.

O Operations Management Suite continua atualizando automaticamente seu machine learning com as ameaças mais recentes e nós também continuamos implementando novas soluções no Azure Marketplace também.

Para obter mais informações sobre o Operations Management Suite, veja a [nossa página de documentação](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/).



<!--HONumber=Jan17_HO1-->


