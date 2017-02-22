---
title: "Visão geral do DoD do Azure Governamental | Microsoft Docs"
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/12/2017
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 71e8742d71ce6ebab08988751a886df4df000f8a
ms.openlocfilehash: 4e815c5fce563155f7a747262a3695a5180706e3


---
# <a name="department-of-defense-dod-in-azure-government"></a>Departamento de Defesa (DoD) no Azure Governamental
## <a name="overview"></a>Visão geral
O Azure Governamental é usado por entidades do Departamento de Defesa (DoD) para implantar uma ampla variedade de cargas de trabalho e soluções, incluindo as cargas de trabalho cobertas pelo<a href="http://iasecontent.disa.mil/cloud/SRG/index.html"> O Guia de Requisitos de Segurança de Computação em Nuvem do DoD, Versão 1, Liberação 2</a> no Impacto Nível 4 (L4) e Impacto Nível 5 (L5).

O Azure Governamental é o primeiro e único serviço de nuvem comercial em hiperescala ao qual foi concedida uma autorização temporária do DoD para Informações com Impacto Nível 5 pela Defense Information Systems Agency. Além disso, as regiões do Azure Governamental dedicadas a cargas de trabalho de cliente do Departamento de Defesa dos EUA agora estão disponíveis.

Um dos principais fatores para a mudança do DoD para a nuvem é permitir que as organizações se concentram em suas missões e minimizar as distrações da compilação e gerenciamento de soluções de TI internas.

As arquiteturas de nuvem baseadas no Azure Governamental permitem que os funcionários do DoD se concentrem em objetivos da missão, e no gerenciamento de serviços de consumo de TI, como o SharePoint e outras cargas de trabalho do aplicativo.  Isso permite o realinhamento de recursos essenciais de TI para se concentrar no desenvolvimento de aplicativos, análise e segurança cibernética.

A elasticidade e a flexibilidade fornecidas pelo Azure proporcionam benefícios incríveis aos clientes do DoD. É mais simples, mais rápido e mais econômico ampliar uma carga de trabalho na nuvem do que percorrer os processos tradicionais de aquisição de hardware e serviços ao trabalhar localmente, ou em data centers do DoD. Por exemplo, adquirir um novo hardware multisservidores, até mesmo para um ambiente de teste, pode demorar muitos meses e exigir a aprovação de despesas consideráveis de capital . Por outro lado, com o Azure, uma migração de teste de uma carga de trabalho existente pode ser configurada em semanas ou até mesmo dias, e de forma econômica (quando o teste terminar, o ambiente poderá ser interrompido sem custos contínuos).

Essa flexibilidade é considerável. Ao mudar para o Azure, os clientes do DoD não apenas economizam dinheiro; a nuvem oferece novas oportunidades. Por exemplo, é fácil criar um ambiente de teste para obter ideias sobre novas tecnologias, você pode migrar um aplicativo e testá-lo no Azure antes de realizar uma implantação de produção na nuvem. Os proprietários de missão podem explorar opções mais econômicas com mais facilidade e sem riscos.

A segurança é outra área fundamental e, embora qualquer implantação de nuvem exija planejamento adequado a fim de garantir uma entrega de serviços segura e confiável, na realidade, as cargas de trabalho com base na nuvem configuradas mais adequadamente (incluindo cargas de trabalho até no nível L4) no Azure Governamental serão mais seguras do que muitas implantações tradicionais em locais e data centers do DoD . Isso ocorre porque os órgãos de defesa têm a experiência e o conhecimento para proteger fisicamente todos os ativos; no entanto, as áreas de superfície de TI apresentam desafios diferentes. A segurança cibernética é um espaço em rápida mutação, o que exige habilidades de especialista e a capacidade de desenvolver e implantar rapidamente contramedidas conforme o necessário. A plataforma Azure, tanto comerciais quando Governamental, oferece suporte a centenas de milhares de clientes, e essa escala permite que a Microsoft detecte rapidamente os vetores de ataque em evolução e direcione seus recursos para o desenvolvimento e implementação rápida das defesas apropriadas.

## <a name="dod-region-qa"></a>Perguntas e respostas sobre regiões do DoD

### <a name="what-are-the-azure-government-dod-regions"></a>O que são as regiões do DoD do Azure Governamental? 
As regiões do US DoD Leste e US DoD Central são regiões fisicamente separadas do Microsoft Azure, projetadas para atender aos requisitos de segurança do DoD (Departamento de Defesa) para a computação em nuvem, especificamente para dados designados como Impacto Nível 5 do DoD segundo o SRG (Guia de Requisitos de Segurança) de Computação em Nuvem do DoD.   

### <a name="what-is-the-difference-between-azure-government-and-the-azure-government-dod-regions"></a>Qual é a diferença entre o Azure Governamental e as Regiões do DoD do Azure Governamental? 
O Azure Governamental é uma nuvem de comunidade do governo dos EUA que fornece serviços para clientes governamentais nas esferas Federal, Estadual e Local, entidades tribais sujeitas a ITAR e provedores de soluções, executando trabalho em seu nome. Todas as regiões do Azure Governamental são desenvolvidas e operadas para atender aos requisitos de segurança de dados com Impacto Nível 5 do DoD e padrões FedRAMP elevados.

As regiões do DoD do Azure Governamental são projetadas para dar suporte a requisitos de separação física para dados com Impacto Nível 5 por meio do fornecimento de infraestrutura de armazenamento e computação dedicada para uso somente por clientes do DoD.  

#### <a name="what-is-the-difference-between-impact-level-4-and-impact-level-5-data"></a>Qual é a diferença entre dados com Impacto Nível 4 e Impacto Nível 5?  
Dados com Impacto Nível 4 são CUI (informações controladas não classificadas) que podem incluir dados sujeitos a controle de exportação, informações de integridade protegidas por informações de privacidade e outros dados que exigem a designação CUI explícita (por exemplo, Somente Para Uso Oficial, Confidenciais Para Efeitos da Aplicação da Lei, Informações de Segurança Confidenciais).

Dados com Impacto Nível 5 incluem CUI (informações controladas não classificadas) que requerem um nível mais alto de proteção conforme considerado necessário pelo proprietário das informações, pelo poder público ou normas governamentais.  Dados com Impacto Nível 5 incluem Sistemas de Segurança Nacional não classificados.  Para obter mais informações sobre o sobre os níveis de impacto dos SRG, seus requisitos e características específicas estão disponíveis na seção 3 do Guia de Requisitos de Segurança de Computação em Nuvem do DoD.  

### <a name="what-data-is-categorized-as-impact-level-5"></a>Quais dados são categorizados como Impacto Nível 5? 
O Nível 5 engloba CUI (informações controladas não classificadas), que requerem um nível mais alto de proteção que aquele oferecido pelo Nível 4 conforme considerado necessário pelo proprietário das informações, pelo poder público ou outras normas governamentais. O Nível 5 também dá suporte a NSSs (sistemas de segurança nacional) não classificados.  Esse nível acomoda categorizações de informações NSS e CUI com base em CNSSI-1253 até confidencialidade moderada e integridade moderada (M-M-x).

### <a name="what-is-microsoft-doing-differently-to-support-impact-level-5-data"></a>O que a Microsoft está fazendo de diferente para dar suporte a dados com Impacto Nível 5? 
Dados com Impacto Nível 5, por definição, só podem ser processados em uma infraestrutura dedicada que garante a separação física dos clientes do DoD de locatários que não são do Governo Federal.  Ao fornecer as regiões do US DoD Leste e US DoD Central, a Microsoft fornece um serviço exclusivo para os clientes do DoD que cumpre um nível ainda maior do que os requisitos definidos pelo DoD e excede o nível de proteção e capacidade oferecido por qualquer outra solução de nuvem comercial em hiperescala.

### <a name="do-these-regions-support-classified-data-requirements"></a>Essas regiões dão suporte a requisitos de dados confidenciais? 
Essas regiões do DoD do Azure Governamental dão suporte somente a dados não classificados até e incluindo o Impacto Nível 5.  Dados de Impacto Nível 6 são definidos como informações confidenciais até Secretas.

### <a name="what-organizations-in-the-dod-can-use-the-azure-government-dod-regions"></a>Quais as organizações no DoD podem usar as Regiões do DoD do Azure Governamental? 
As regiões do US DoD Leste e US DoD Central são criadas para dar suporte à base de clientes do Departamento de Defesa.  Isso inclui:
* O Escritório do Secretário de Defesa
* Os Chefes do Estado-Maior Conjunto dos EUA
* O Estado-Maior Conjunto
* As Agências de Defesa
* Atividades de Campo do Departamento de Defesa
* O Departamento do Exército
* O Departamento da Marinha (incluindo o Corpo de Fuzileiros Navais dos Estados Unidos)
* O Departamento da Força Aérea
* A Guarda Costeira dos Estados Unidos
* Os comandos de combate unificados
* Outros escritórios, agências, atividades e comandos no controle ou supervisão de qualquer entidade aprovada citada acima

### <a name="are-the-dod-regions-more-secure"></a>As regiões do DoD são mais seguras? 
Microsoft opera em todos os seus datacenters do Azure e infraestrutura de suporte de acordo com os padrões locais e internacionais de segurança e conformidade – liderando todas as plataformas de nuvem comercial em investimentos em conformidade e conquistas.  Essas novas regiões do DoD oferecerão garantias específicas e compromissos para atender aos requisitos definidos no SRG do DoD para a Computação em Nuvem.

### <a name="why-are-there-multiple-dod-regions"></a>Por que existem várias regiões do DoD? 
Por ter várias regiões do DoD, a Microsoft fornece aos clientes a oportunidade de arquitetar as soluções para cenários de recuperação de desastre entre regiões para garantir a continuidade dos negócios e atender aos requisitos de capacitação do sistema.  Além disso, os clientes podem otimizar o desempenho ao implantar soluções na geografia mais próxima possível da sua localização física.

### <a name="are-these-dod-regions-connected-to-the-niprnet"></a>Essas regiões do DoD estão conectadas à NIPRNet? 
O DoD determina que os serviços de nuvem comerciais usados para CUI devem estar conectados aos clientes por meio de um CAP (ponto de acesso de nuvem).  Portanto, as regiões do DoD do Azure estão conectadas à NIPRNet por meio de conexões redundantes com vários CAPs distribuídos geograficamente.  Um CAP do DoD é um sistema de proteção de limites de rede e dispositivos de monitoramento que oferecem proteção aos serviços e à rede do sistema de informações do DoD.

### <a name="what-does-general-availability-mean"></a>O que significa disponibilidade geral? 
Disponibilidade geral significa que as regiões do DoD do Azure Governamental podem ser usadas para dar suporte a cargas de trabalho de produção e que haverá suporte para SLAs com apoio financeiro para todos os serviços implantados nas regiões e também disponíveis ao público em geral.

### <a name="how-does-a-dod-customer-acquire-azure-government-dod-services"></a>Como um cliente DoD adquire os serviços DoD do Azure Governamental? 
Os serviços DoD do Azure Governamental podem ser adquiridos por entidades qualificadas por meio dos canais de revendedor mesmo que o governo do Azure.  Para manter o compromisso da Microsoft de simplificar o planejamento de aquisição e estimativa de custo de serviços de nuvem, os preços para as regiões do DoD do Azure Governamental serão incluídos na Calculadora de preços do Azure quando houver disponibilidade geral.  Os serviços DoD do Azure Governamental podem ser rapidamente escalados ou reduzidos verticalmente para acompanhar a demanda, para que você pague somente pelo que usar.
Nenhuma modificação contratual será necessária para os clientes do Enterprise Agreement já usando o Azure Governamental.  

### <a name="how-are-the-dod-regions-priced"></a>Quais os preços das regiões do DoD? 
As regiões do DoD utilizam preço com base na região.  Isso significa que os custos de serviço para clientes do DoD validados serão baseados na região do Azure Governamental na qual você executar suas cargas de trabalho.  Para obter informações mais específicas sobre preços, consulte o seu Executivo de Conta da Microsoft.  Os preços para as regiões do DoD serão fornecidos por meio da calculadora do Azure.com em uma data futura.

### <a name="how-does-a-dod-organization-get-validated-for-the-azure-government-dod-regions"></a>Como uma organização do DoD é validada para as regiões do DoD do Azure Governamental? 
Para acessar as regiões do DoD do Azure, os clientes devem concluir um processo de qualificação prévia para verificar seu uso pretendido e organização do ambiente do DoD do Azure.  Após a conclusão bem-sucedida do processo de qualificação prévia, a Microsoft fornecerá ao candidato organizacional mais instruções para criar uma assinatura, acessar o ambiente e fornecer controle de acesso baseado em função para outros membros da organização.

### <a name="can-independent-software-vendors-and-solution-providers-building-on-azure-deploy-solutions-in-the-azure-government-dod-regions"></a>Fornecedores independentes de software e os provedores de soluções com base no Azure podem implantar soluções nas regiões do DoD do Azure Governamental? 
Provedores de soluções com ofertas de serviço de nuvem criados no Azure podem operar soluções somente do DoD de locatário único e multilocatário nas regiões do DoD do Azure Governamental.  Esses provedores primeiro devem demonstrar qualificação fornecendo evidência documentada de um contrato com uma entidade aprovada pelo DoD ou ter uma carta de responsável de uma entidade aprovada pelo DoD.  Provedores oferecendo serviços nas regiões do DoD do Azure Governamental devem incluir a defesa da rede de computadores, o relatório de incidentes e pessoal triado para a operação de soluções que manipulam informações com Impacto Nível 5 em sua oferta.  Diretrizes adicionais para provedores de soluções podem ser encontradas no Guia de Requisitos de Segurança de Computação em Nuvem do DoD.

### <a name="will-office-365-or-microsoft-dynamics-365-be-a-part-of-this-offering"></a>O Office 365 ou Microsoft Dynamics 365 farão parte desta oferta? 
A Microsoft está fornecendo serviços do Office 365 para o DoD com Impacto Nível 5 em conjunto com esta oferta.  O Dynamics 365 está planejando oferecer serviços com Impacto Nível 5 das regiões do DoD do Azure em uma data futura.

### <a name="how-do-i-connect-to-the-dod-regions-once-i-have-a-subscription"></a>Como me conecto a regiões do DoD depois de obter uma assinatura? 
As regiões do DoD para o Azure Governamental estão disponíveis por meio do Portal de Gerenciamento do Azure Governamental.  Os clientes do DoD aprovados para uso verão as regiões listadas como opções disponíveis ao implantar serviços disponíveis.  Para obter diretrizes gerais sobre como gerenciar suas assinaturas do Azure Governamental, consulte nossa documentação.

### <a name="what-services-are-part-of-your-impact-level-5-accreditation-scope"></a>Quais serviços são parte de seu escopo de capacitação de Impacto Nível 5? 
O Azure é um serviço perene em que novos serviços e recursos estão sendo adicionados a cada semana e o número de serviços no escopo está se expandindo regularmente.  Para obter as informações mais atualizadas, visite nossa<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Central de Confiabilidade da Microsoft.


## <a name="next-steps"></a>Próximas etapas:
<a href="https://www.microsoft.com/en-us/TrustCenter/Compliance/DISA"> Central de Confiabilidade da Microsoft - página da Web do DoD </a>

<a href="http://iasecontent.disa.mil/cloud/SRG/index.html"> O Guia de requisitos de segurança de computação na nuvem do DoD, Versão 1, Liberação 2 </a>

<a href="https://azure.microsoft.com/en-us/offers/azure-government/"> Canais de revendedor do Azure Governamental

<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Governamental. </a>




<!--HONumber=Jan17_HO3-->


