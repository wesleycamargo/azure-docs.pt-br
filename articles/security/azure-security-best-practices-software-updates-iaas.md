<properties
   pageTitle="Práticas recomendadas para atualizações de Software no Microsoft Azure IaaS | Microsoft Azure"
   description="O artigo fornece um conjunto de práticas recomendadas para atualizações de software em um ambiente do Microsoft Azure IaaS. Destina-se a profissionais de TI e a analistas de segurança que lidam diariamente com controle de alterações, atualização de software e gerenciamento de ativos de informação, incluindo as pessoas responsáveis por iniciativas de segurança e de conformidade da organização."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/18/2016"
   ms.author="yurid"/>

#Práticas recomendadas para atualizações de software no Microsoft Azure IaaS

Antes de entrar em qualquer tipo de discussão sobre as práticas recomendadas para um ambiente de [IaaS](https://azure.microsoft.com/overview/what-is-iaas/) do Azure, é importante entender quais são os cenários que farão com que você gerencie atualizações de software. O diagrama abaixo deve explicar melhor:

![Modelos de nuvem e responsabilidades](./media/azure-security-best-practices-software-updates-iaas/sec-cloudstack.png)

No modelo de data center tradicional, em que toda a infraestrutura está localizada no local, você é totalmente responsável por gerenciar atualizações de sistemas operacionais, de aplicativos, de rede, de dispositivos (roteadores, opções, etc.) e de hardware (firmware). Em um cenário de IaaS, você ainda terá que gerenciar atualizações para sistemas operacionais e aplicativos. No entanto, toda a infraestrutura dos sistemas operacionais e dos aplicativos é gerenciada pela Microsoft. Nesses modelos, os clientes ainda são proprietários dos dados e os responsáveis pela proteção no nível do ponto de extremidade.

Em um cenário de PaaS, você terá ainda menos responsabilidade sobre as atualizações de software, já que o gerenciamento de atualizações para o sistema operacional é de responsabilidade da Microsoft. Em um cenário de SaaS, a responsabilidade pelas atualizações de software para toda a pilha é da Microsoft.

Esses mesmos princípios se aplicam a um cenário híbrido em que sua empresa esteja usando as VMs de IaaS do Azure que se comunicam com recursos locais, conforme mostrado no diagrama a seguir.

![Cenário híbrido típico com o Microsoft Azure](./media/azure-security-best-practices-software-updates-iaas/sec-azconnectonpre.png)

## Avaliação inicial

Mesmo que sua empresa já esteja usando um sistema de gerenciamento de atualização e você já tenha políticas de atualização de software em vigor, é importante rever avaliações de política anteriores com frequência e atualizá-las com base nas necessidades atuais. Isso significa que você precisa estar familiarizado com o estado atual dos recursos em sua empresa. Para atingir esse estado, você precisa conhecer:

-   Os computadores físicos e virtuais em sua empresa.

-   Os sistemas operacionais e versões em execução em cada um desses computadores físicos e virtuais.

-   As atualizações de software instaladas atualmente em cada computador (versões de service pack, atualizações de software e outras modificações).

-   A função que cada computador executa em sua empresa.

-   Os aplicativos e programas em execução em cada computador.

-   A quem pertence e as informações de contato para cada computador.

-   Os ativos presentes no seu ambiente e o valor relativo deles determinam quais áreas precisam de mais atenção e proteção.

-   Problemas de segurança conhecidos e os processos da sua empresa para a identificação novos problemas de segurança ou alterações no nível de segurança.

-   Contramedidas que foram implantadas para proteger o seu ambiente.

Você deve atualizar essas informações regularmente, e elas devem estar prontamente disponíveis para os envolvidos em seu processo de gerenciamento de atualizações de software.

## Estabelecer uma linha de base

Uma parte importante do processo de gerenciamento de atualização de software é criar instalações padrão iniciais de versões de sistemas operacionais, aplicativos e hardware para os computadores da sua empresa. Isso é chamado de linha de base. Uma linha de base é a configuração de um produto ou sistema estabelecido em um ponto específico no tempo. Um aplicativo ou linha de base do sistema operacional, por exemplo, permite recriar um computador ou serviço em um estado específico.

As linhas de base fornecem a base para encontrar e corrigir problemas em potencial e simplificam o processo de gerenciamento de atualizações de software, tanto reduzindo o número de atualizações de software que você deve implantar em sua empresa quanto aumentando a capacidade de monitorar a conformidade.

Depois de executar a auditoria inicial de sua empresa, você deve usar as informações obtidas da auditoria para definir uma linha de base operacional para os componentes de TI dentro de seu ambiente de produção. Uma quantidade de linhas de base pode ser solicitada dependendo dos tipos diferentes de hardware e software implantados na produção.

Por exemplo, alguns servidores exigem uma atualização de software para impedir que fiquem pendentes quando iniciam o processo de desligamento na execução do Windows Server 2012. Uma linha de base para esses servidores deve incluir essa atualização de software.

Em grandes organizações, geralmente é útil dividir os computadores da sua empresa em categorias de ativo e manter cada categoria em uma linha de base padrão usando as mesmas versões e atualizações de software. Você pode usar essas categorias de ativo na priorização de uma distribuição de atualização de software.

## Inscrever-se nos serviços de notificação de atualização de software apropriados

Depois de realizar uma auditoria inicial do software em uso na sua empresa, você deve determinar o melhor método para receber notificações de novas atualizações de software para cada produto e versão de software. Dependendo do produto de software, o melhor método de notificação pode ser notificações por email, sites ou publicações de computador.

Por exemplo, o MSRC (Microsoft Security Response Center) responde a todas as questões de segurança sobre os produtos Microsoft e fornece o Boletim de Segurança da Microsoft, uma notificação de email gratuito de vulnerabilidades recentemente identificadas e atualizações de software lançadas para lidar com essas vulnerabilidades. Você pode assinar este serviço em http://www.microsoft.com/technet/security/bulletin/notify.mspx

## Considerações sobre a atualização de software

Depois de realizar uma auditoria inicial do software em uso na sua empresa, você deve determinar os requisitos para configurar o sistema de gerenciamento de atualização de software, que depende do sistema de gerenciamento de atualização de software que está usando. Para o WSUS, leia [Práticas recomendadas para o Windows Server Update Services](https://technet.microsoft.com/library/Cc708536); para o System Center, leia [Planejando atualizações de Software no Configuration Manager](https://technet.microsoft.com/library/gg712696).

No entanto, há algumas considerações gerais e práticas recomendadas que você pode aplicar independentemente da solução que estiver usando, conforme mostrado nas seções a seguir.

### Configurando o ambiente

Considere as práticas a seguir ao planejar o ambiente de gerenciamento de atualização de software:

-   **Criar coleções de atualização de software baseadas em critérios estáveis de produção**: em geral, o uso de critérios estáveis para criar coleções para seu inventário de atualização de software e distribuição ajudará a simplificar todas as fases do processo de gerenciamento de atualização de software. Os critérios estáveis podem incluir a versão do sistema operacional cliente instalado e o nível de service pack, a função do sistema ou a organização de destino.

-   **Criar coleções de pré-produção que incluem computadores de referência**: a coleção de pré-produção deve incluir configurações representativas de versões do sistema operacional, linha de software de negócios e outros softwares em execução na sua empresa.

Você também deve considerar onde o servidor de atualização de software estará localizado, se na infraestrutura do Azure IaaS na nuvem ou se será local. Isso é uma decisão importante porque você precisa avaliar a quantidade de tráfego entre os recursos locais e a infraestrutura do Azure. Leia [Conectar uma rede local a uma Rede Virtual do Microsoft Azure](https://technet.microsoft.com/library/Dn786406.aspx) para saber mais sobre como conectar sua infraestrutura local ao Azure.

As opções de design que determinam onde o servidor de atualização estará localizado também varia de acordo com sua infraestrutura atual e com o sistema de atualização de software que você está usando atualmente. Para o WSUS, leia [Implantar o Windows Server Update Services em sua organização](https://technet.microsoft.com/library/hh852340.aspx) e para o System Center Configuration Manager, leia [Planejando sites e hierarquias no Configuration Manager](https://technet.microsoft.com/library/Gg712681.aspx).

### Backup

Backups regulares são importantes não apenas para a plataforma de gerenciamento de atualização de software em si, mas também para os servidores que serão atualizados. As organizações que possuem um [processo de gerenciamento de alteração](https://technet.microsoft.com/library/cc543216.aspx) local precisarão que o departamento de TI justifique a necessidade de atualização do servidor, o tempo de inatividade estimado e o possível impacto. Para garantir que você tenha uma configuração de reversão para uso no caso de falha de uma atualização, faça o backup do sistema regularmente.

Algumas opções de backup do Azure IaaS incluem:

-   [Proteção de carga de trabalho do Azure IaaS usando o Data Protection Manager](https://azure.microsoft.com/blog/2014/09/08/azure-iaas-workload-protection-using-data-protection-manager/)

-   [Fazer backup de máquinas virtuais do Azure](../backup/backup-azure-vms.md)

### Monitoramento

Você deve executar relatórios regulares para monitorar o número de atualizações faltantes, instaladas ou com status incompleto para cada atualização de software autorizada. Da mesma forma, relatar atualizações de software que ainda não foram autorizadas pode facilitar as decisões de implantação.

Você também deve considerar as seguintes tarefas:

-   Realizar uma auditoria das atualizações de segurança aplicáveis e instaladas em todos os computadores em sua empresa.

-   Autorizar e implantar as atualizações nos computadores apropriados.

-   Acompanhar o inventário e atualizar o status e progresso da instalação para todos os computadores em sua empresa.

Além das considerações gerais que foram explicadas neste artigo, você também deve considerar as práticas recomendadas de cada produto, por exemplo, se você tiver uma máquina virtual no Azure com SQL Server, não deixe de seguir a recomendação de atualizações de software para o produto.

## Próximas etapas

Use as diretrizes descritas neste artigo para ajudar a determinar as melhores opções para as atualizações de software de máquinas virtuais no Azure IaaS. Existem muitas semelhanças entre as práticas recomendadas de atualização de software em um datacenter tradicional versus o IaaS do Azure; portanto, é recomendável que você avalie suas diretivas de atualização de software atual para incluir as VMs do Azure e as práticas recomendadas relevantes deste artigo em seu processo geral de atualização de software.

<!---HONumber=AcomDC_0727_2016-->