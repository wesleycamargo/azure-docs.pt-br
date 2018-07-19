---
title: Recursos, local e segurança física do Azure | Microsoft Docs
description: O artigo descreve os datacenters do Azure, incluindo as ofertas de conformidade, segurança e infra-estrutura física.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: a6a9b1d6e12dabb09cde684c34481b4b3442c1b8
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101210"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Recursos, local e segurança física do Azure
A nuvem da Microsoft é composta por uma [infraestrutura de datacenter globalmente distribuída](https://azure.microsoft.com/global-infrastructure/) que suporta milhares de serviços online e abrange mais de 100 instalações altamente seguras em todo o mundo.

A infraestrutura é projetada para aproximar os aplicativos aos usuários em todo o mundo, preservando a residência dos dados e oferecendo opções abrangentes de conformidade e resiliência para os clientes. Azure tem 52 regiões em todo o mundo e está disponível em 140 países.

Uma região é um conjunto de datacenters interconectados através de uma rede grande e resiliente. A rede inclui a distribuição de conteúdo, o balanceamento de carga, redundância e criptografia por padrão. Com mais regiões globais do que qualquer outro provedor de nuvem, o Azure dá aos clientes a flexibilidade para implantar aplicativos no local em que eles precisam.

As regiões do Azure são organizadas em geografias. Uma geografia do Azure garante que os requisitos de residência, de soberania, de conformidade e de resiliência de dados sejam respeitados dentro de limites geográficos.

As geografias permitem que os clientes com necessidades de conformidade e de residência de dados específicas mantenham seus dados e aplicativos próximos. As geografias são tolerantes a falhas para resistir a falhas de região completas por meio de sua conexão com nossa infraestrutura de rede dedicada de alta capacidade.

As Zonas de disponibilidade são locais separados fisicamente dentro de uma região do Azure. Cada Zona de disponibilidade é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. As Zonas de Disponibilidade permitem que os clientes executem aplicativos críticos com alta disponibilidade e replicação de baixa latência.

A figura a seguir mostra como a infraestrutura global do Azure associa região e zonas de disponibilidade ao mesmo limite de residência de dados para alta disponibilidade, recuperação de desastre e backup.

![Limite de residência de dados][1]

Um grande espaço geográfico distribuído de datacenters permite que a Microsoft esteja próxima dos clientes, a fim de reduzir a latência da rede e permitir backup e failover geo-redundantes.

## <a name="physical-security"></a>Segurança física
A Microsoft projeta, cria e opera datacenters de uma maneira que controla rigorosamente o acesso físico às áreas onde os dados do cliente são armazenados. A Microsoft entende a importância de proteger os dados do cliente e está comprometida em ajudar a proteger os datacenters que contêm seus dados. Temos uma divisão inteira na Microsoft dedicada a projetar, construir e operar as instalações físicas que suportam o Azure. Essa equipe está investida em manter a segurança física de última geração.

A Microsoft adota uma abordagem em camadas à segurança física para reduzir o risco de usuários não autorizados obterem acesso físico aos dados e aos recursos do datacenter. Os centros de dados gerenciados pela Microsoft têm extensas camadas de proteção: aprovação de acesso no perímetro da instalação, no perímetro do prédio, dentro do prédio e no datacenter. Camadas de segurança física são:

- Solicitação de acesso e aprovação – você deve solicitar acesso antes de chegar ao data center. Você precisa fornecer uma justificativa comercial válido para sua visita, como conformidade ou fins de auditoria. Todas as solicitações são aprovadas em uma base de necessidade de acesso por funcionários da Microsoft. Uma base de necessidade de acesso ajuda a manter o número de indivíduos necessários para concluir uma tarefa em data centers para o mínimo necessário. Depois que a permissão é concedida, o usuário individual só tem acesso à área de discretos do datacenter com base na justificativa de negócios aprovados. Permissões são limitadas a um determinado período de tempo e expiram após o período de tempo permitido.

- Perímetro da instalação - quando você chega a um datacenter, é necessário passar por um ponto de acesso bem definido. Normalmente, cercas altas feitas de aço e concreto abrangem cada centímetro do perímetro. Há câmeras em volta dos datacenters, com uma equipe de segurança monitorando seus vídeos 24 horas por dia, 7 dias por semana e 365 dias por ano.

- Entrada do edifício - A entrada do datacenter é composta por funcionários de segurança profissionais que passaram por rigorosos treinamentos e verificação de antecedentes. Esses agentes de segurança também patrulham rotineiramente o datacenter enquanto monitoram os vídeos das câmeras dentro do datacenter 24 horas por dia, 7 dias por semana e 365 dias por ano.

- Dentro do edifício - Depois de entrar no prédio, você deve passar a autenticação de dois fatores com dados biométricos para continuar a movimentação pelo datacenter. Se sua identidade for validada, você poderá inserir a parte do datacenter para o qual você aprovou o acesso. Você pode permanecer lá somente durante a duração do tempo aprovado.

- Andar do datacenter - Você só tem permissão para entrar no andar em que você está autorizado a entrar. É necessário para passar uma triagem de detecção de metal corpo completo. Para reduzir o risco de dados não autorizados entrarem ou saírem do datacenter sem o nosso conhecimento, somente dispositivos aprovados podem entrar no chão do datacenter. Além disso, monitor de câmeras de vídeo parte frontal e traseira de todos os servidores em rack. Triagem de detecção de metal corpo completo é repetida quando você sai do andar de datacenter. Para sair do datacenter, você precisa passar por uma verificação de segurança adicional.

Os visitantes terão de devolver os crachás após a saída de qualquer instalação da Microsoft.

## <a name="physical-security-reviews"></a>Revisões de segurança física
Revisões de segurança física das instalações são realizadas periodicamente para garantir que os datacenters atendam adequadamente aos requisitos de segurança do Microsoft Azure. A equipe do provedor de hospedagem de data center não fornece o gerenciamento de serviço do Microsoft Azure. O pessoal não tem acesso de logon aos sistemas do Azure ou acesso físico à sala de disposição e às gaiolas do Azure.

## <a name="data-bearing-devices"></a>Dispositivos de influência de dados
A Microsoft usa os procedimentos de práticas recomendados e uma solução de apagamento de dados que seja [NIST 800-88 compatíveis](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Para discos rígidos que não podem ser limpos, um processo de destruição que destrói e torna impossível a recuperação de informações é usado. O processo de destruição pode ser desintegrar, destruir, pulverizar ou incinerar. O meio apropriado de descarte é determinado pelo tipo de ativo. Registros da destruição são retidos.  

## <a name="equipment-disposal"></a>Descarte de equipamentos
O Microsoft Azure implementa esse princípio em nome dos clientes. Após o fim da vida útil do sistema, a equipe operacional da Microsoft segue rigorosos manuseios de dados e processos de descarte de hardware para ajudar a garantir que nenhum hardware que possa conter dados de cliente seja disponibilizado a pessoas não confiáveis. Uma abordagem de fácil eliminação é seguida (por meio do firmware de disco rígido) para as unidades que dão suporte a ele. Para discos rígidos que não podem ser limpos, um processo de destruição que destrói e torna impossível a recuperação de informações é usado. O processo de destruição pode ser desintegrar, destruir, pulverizar ou incinerar. O meio apropriado de descarte é determinado pelo tipo de ativo. Registros da destruição são retidos. Todos os serviços do Microsoft Azure usam os serviços aprovados de gerenciamento de armazenamento e descarte de mídia.

## <a name="compliance"></a>Conformidade
A infraestrutura do Azure foi projetada e gerenciada para atender a um amplo conjunto de padrões de conformidade internacionais e específicos do setor, como ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Normas específicas também são atendidas, incluindo o IRAP da Austrália, o G-Cloud do Reino Unido e o MTCS de Cingapura. Rigorosas auditorias de terceiros, tais como aquelas realizadas pelo British Standards Institute, confirmam a adesão do Azure aos rígidos controles de segurança exigidos por tais normas.

Consulte as [ofertas de conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings) para uma lista completa dos padrões de conformidade atendidas pelo Azure.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
