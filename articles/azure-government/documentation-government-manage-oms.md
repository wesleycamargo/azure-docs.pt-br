---
title: OMS do Azure Governamental | Microsoft Docs
description: "Este artigo descreve o cenário do OMS aplicável às agências e provedores de solução do governo dos EUA"
services: Azure-Government
cloud: gov
documentationcenter: 
author: sacha
manager: jobruno
editor: 
translationtype: Human Translation
ms.sourcegitcommit: 1f44321bc0ec33362c64da9a19d3309c45783ed2
ms.openlocfilehash: fb38649d6f40e24849ba5a8de371b1c911089fc7

---

# <a name="azure-government-cybersecurity-monitoring-and-securing-your-assets-with-operations-management-suite-oms"></a>Segurança cibernética de Azure Governamental: monitoramento e proteção de seus ativos com o Operations Management Suite (OMS)

## <a name="cybersecurity-in-the-cloud"></a>Segurança cibernética na Nuvem
Um aspecto essencial para nossos clientes que estão mudando para a Nuvem é manter o gerenciamento de ativos e a segurança de seus serviços do Azure Governamental implantados na Nuvem. Os firewalls de Máquinas virtuais precisam ser configurados corretamente. As Redes virtuais precisam ter os Grupos de segurança de certos aplicados a elas. O acesso aos seus ativos devem ser restritos às pessoas certas, no momento certo. Tudo isso representa os fluxos de trabalho que você precisa planejar, criar e provisionar para habilitar uma infraestrutura segura para sua agência.

A configuração de um ambiente desse tipo pode ser um desafio. Integrar sua frota de servidores a qualquer serviço de monitoramento é difícil de dimensionar, bem como de atualizar. É difícil monitorar a infraestrutura em diferentes provedores de nuvem, bem como entre a Nuvem e o local. Por fim, manter o monitoramentos atualizado e permitir informações para o monitoramento, detecção, alerta e ação contra ameaças de segurança cibernética exige tempo, recursos e capacidade de computação.

## <a name="microsoft-operations-management-suite-oms"></a>Microsoft Operations Management Suite (OMS)
O Microsoft Operations Management Suite – agora disponível no Azure Governamental – é um serviço que permite a você fazer tudo isso, aproveitando o potencial do map reduce e o machine learning como serviço. O OMS pode:
* Implantar agentes para VMS individuais (Linux e Windows) no Azure, outros provedores de nuvem e/ou locais.
* Conectar seus logs existentes por meio de uma conta de armazenamento do Azure Governamental ou ponto de extremidade SCOM com os dados de log existentes.
* Executar imediatamente serviços de machine learning evergreen e map reduce capacitados pela pesquisa de log de grande escala para expor ameaças em seu ambiente.

Vamos explorar como podemos integrar o OMS à sua frota, e algumas das soluções imediatas que podem atender aos pontos acima.

## <a name="onboarding-servers-to-oms"></a>Servidores de integração para OMS
A primeira coisa ao integrar seus ativos de Nuvem ao Operations Management Suite é instalar o agente do OMS em suas fontes de logs.
Para máquinas virtuais, isso é muito simples, você pode baixar manualmente o agente de seu espaço de trabalho do OMS.

![Figura 1: Servidores Windows conectados ao OMS](./media/documentation-government-oms-figure1.png)
<p align="center">Figura 1: Servidores Windows conectados ao OMS</p>

Você pode conectar VMs do Azure ao OMS diretamente por meio do Portal. Instruções [aqui](https://blogs.technet.microsoft.com/momteam/2016/02/10/new-ways-to-enable-log-analytics-oms-on-your-azure-vms/).

Você também pode conectá-los por meio de programação e/ou configurar a extensão do OMS diretamente em seus modelos do Azure Resource Manager. Instruções para computador com base no Windows [aqui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents) e com base no Linux [aqui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-linux-agents).

## <a name="onboarding-storage-accounts-and-scom-to-oms"></a>Integrar contas de armazenamento e SCOM ao OMS
O OMS também pode se conectar à sua conta de armazenamento e/ou implantações existentes do System Center Operations Manager a fim de oferecer a você o gerenciamento de operações em cenários híbridos (entre provedores na nuvem ou em infraestruturas de nuvem/locais).

![Figura 2: Conexão do Armazenamento do Azure e o SCOM ao OMS](./media/documentation-government-oms-figure2.png)
<p align="center">Figura 2: Conexão do Armazenamento do Azure e o SCOM ao OMS</p>

O OMS também oferece suporte a informações de registro em log de outros serviços de monitoramentos como Chef e/ou Puppet. Além disso, para implantações do Azure – também temos VMs com modelos do Azure Resource Manager habilitadas para OMS, para que você possa implantar a Computação e integrar ao seu espaço de trabalho do OMS simultaneamente. 

![Figura 3: Modelos do Azure Resource Manager para VMs do Azure com extensão do OMS](./media/documentation-government-oms-figure3a.png)
![Figura 3: Modelos do Azure Resource Manager para VMs do Azure com extensão do OMS](./media/documentation-government-oms-figure3b.png)
<p align="center">Figura 3: Modelos do Azure Resource Manager para VMs do Azure com extensão do OMS</p>

Encontre informações sobre como configurar o OMS com sua implementação local existente do SCOM [aqui](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-om-agents).

## <a name="leveraging-intelligence-through-oms-solution-packs"></a>Como aproveitar a inteligência por meio de Pacotes de solução do OMS.
Agora que você tem várias fontes de dados de registro em log, surge um problema separado – dar sentido a todos esses dados.
O OMS, em sua base, é um serviço de pesquisa de log que permite a composição de consultas avançadas a fim de pesquisar rapidamente em milhares, ou até mesmo milhões, de registros. No entanto, é muito difícil descobrir os problemas para os quais você precisa escrever consultas.

É aí que entram as soluções do OMS – pacotes de consultas integrados nativamente à tecnologia de map reduce e machine Learning do OMS, com o objetivo de obter informações de forma proativa sobre sua frota gerenciada por OMS.

Junto com o tema de segurança cibernética, abordarei brevemente três cenários de segurança cibernética que o OMS pode resolver imediatamente para você.

###<a name="antimalware-assessment"></a>Avaliação antimalware
As Avaliações antimalware fornecem um conjunto predefinido de consultas, notificações e painéis de monitoramento para informar, rapidamente, o quão bem sua frota está protegida contra malware.

Esse painel oferece 4 coisas:
* Todos os servidores com ameaças ativas e/ou corrigidas.
* Ameaças detectadas no momento
* Computadores que não estão protegidos suficientemente. O OMS faz isso rastreando os logs de seus computadores e procurando por qualquer site de FWs que estão sendo abertos e/ou regras configuradas incorretamente em navegadores da Web comuns.
* Análise de como seus servidores protegidos estão sendo protegidos. Por exemplo – pela proteção antivírus nativa do sistema operacional Windows e/ou algo parecido com o System Center Endpoint Protection.

Por exemplo, abaixo, você pode ver que uma ameaça foi detectada e automaticamente classificada pelo Systems Center:

![Figura 4: Solução de avaliação antimalware do OMS](./media/documentation-government-oms-figure4.png)
<p align="center">Figura 4: Solução de avaliação antimalware do OMS</p>

Para saber mais sobre a Avaliação antimalware, confira: [https://azure.microsoft.com/pt-br/documentation/articles/log-analytics-malware/](https://azure.microsoft.com/en-us/documentation/articles/log-analytics-malware/)

### <a name="identity-and-access"></a>Identidade e Acesso
Outro cenário comum de segurança cibernética na Nuvem é o comprometimento de credenciais. Além das credenciais de sua assinatura de nuvem, cada VM individual tem um usuário e/ou segredo (geralmente um certificado e/ou senha) associado a ela.

O OMS somará e organizará todas as tentativas de logon em sua frota e as organizará dependendo do tipo (remoto, local, nome de usuário usado etc.) No exemplo abaixo, vejo uma quantidade enorme de tentativas de logon sem sucesso a partir de cadeias de caracteres amplamente aleatórias como nomes de usuário. Isso provavelmente indica que meus computadores estão expostos e não adequadamente protegidos por firewalls e listas de controle de acesso.

![Figura 5: 97,3% logon sem sucesso nas últimas 24 horas](./media/documentation-government-oms-figure5.png)
<p align="center">Figura 5: 97,3% logon sem sucesso nas últimas 24 horas</p>

### <a name="threat-intelligence"></a>Inteligência contra ameaças
O OMS também fornece proteção contra o cenários de insiders mal-intencionados – quando há um comprometimento da segurança dentro de sua organização, e um usuário mal-intencionado está tentando remover dados.

A Inteligência contra ameaças do OMS examina todos os Logs de rede em seu computador, pesquisa e notifica automaticamente sobre conexões de rede de entrada/saída para IPs mal-intencionados conhecidos (por exemplo, endereços IP em dark net não indexada).
Por exemplo, abaixo, vejo que há conexões de rede de entrada e saída para a República Popular da China. 

Clicando duas vezes na marca de entrada, descubro que uma VM com Linux gerenciada por meu OMS está estabelecendo conexões de saída para um IP de dark net conhecido na China.

Você também pode configurar Alertas para Soluções do OMS como a Inteligência contra ameaças. Abaixo, configurei um Alerta para que se o OMS detectar mais de 10 conexões de saída para um IP mal-intencionados conhecido, ela enviará um alerta para mim por email. Em seguida, configuro esse alerta para disparar um trabalho de Automação do Azure configurado para desligar automaticamente a VM.

![Figura 6: Alertas e automação do OMS](./media/documentation-government-oms-figure6.png)
<p align="center">Figura 6: Alertas e automação do OMS</p>

Esse é apenas um exemplo de uma solução imediata do OMS que pode ser aplicada à sua frota, quer ela esteja em execução no Azure, em outro provedor de serviços de nuvem ou localmente.
O OMS continuará atualizando automaticamente seu aprendizado de máquina com as ameaças mais recentes, e nós também continuaremos implementando novas Soluções na Galeria de Soluções do OMS.

Para saber mais sobre o OMS, veja nossa página de documentação: [https://azure.microsoft.com/pt-br/documentation/articles/documentation-government-overview/](https://azure.microsoft.com/en-us/documentation/articles/documentation-government-overview/) 



<!--HONumber=Dec16_HO1-->


