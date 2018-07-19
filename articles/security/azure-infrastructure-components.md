---
title: Componentes e limites do sistema de informações do Azure
description: Este artigo fornece uma descrição geral da arquitetura e do gerenciamento do Microsoft Azure.
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
ms.openlocfilehash: 8db1dce5fcc56c229d1fdd746bafbd2fae2c9bad
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101274"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componentes e limites do sistema de informações do Azure
Este artigo fornece uma descrição geral da arquitetura e do gerenciamento do Microsoft Azure. O ambiente do sistema do Azure é composto por redes a seguir:

- Rede de produção do Microsoft Azure (rede do Azure)
- Microsoft Corporate network (rede de rede corporativa)

As equipes de TI separadas são responsáveis por operações e manutenção da rede do Azure e redes da rede corporativa.

## <a name="azure-architecture"></a>Arquitetura do Azure
O Microsoft Azure é uma infraestrutura para compilar, implantar, gerenciar aplicativos e serviços por meio de uma rede de datacenters gerenciada pela Microsoft e plataforma de computação em nuvem. Com base no número de recursos especificados por clientes, o Azure cria VMs com base na necessidade de recursos. Essas VMs executam em um hipervisor do Azure da Microsoft, que é projetado para usar na nuvem e não está acessíveis ao público.

Em cada nó de servidor físico do Azure, há um hipervisor executado diretamente sobre o hardware. Hipervisor divide um nó em um número variável de máquinas virtuais de convidados (VMs). Cada nó também tem uma VM raiz especial, que executa o sistema operacional host. Firewall do Windows está habilitado em cada VM. As únicas portas abertas e endereçáveis, interna ou externamente, são definidas explicitamente no arquivo de definição de serviço configurado pelo cliente. Todo o tráfego e acesso à rede e disco são mediados pelo hipervisor e do sistema operacional raiz.

Na camada do host, as VMs do Azure que executam uma versão robusta e personalizada do Windows Server mais recente. O Microsoft Azure usa uma versão do Windows Server que inclui somente os componentes necessários para hospedar as VMs. Isso é feito para melhorar o desempenho e reduzir a superfície de ataque. Limites de máquina são impostos pelo hipervisor, que não depende da segurança do sistema operacional.

**Gerenciamento do Azure por controladores de malha (FCs)**: no Azure, VMs em execução em servidores físicos (nós/folhas) são agrupados em "clusters" de aproximadamente 1000. As VMs são gerenciadas independentemente por um componente de software de plataforma expandido e redundante chamado o FC.

Cada FC gerencia o ciclo de vida de aplicativos em execução no seu cluster e provisiona e monitora a integridade do hardware sob seu controle. Ele executa as duas operações autônomas, como reincarnating instâncias de VM nos servidores íntegros quando determina que um servidor falhou. O FC também executa operações de gerenciamento de aplicativos, como implantar, atualizar e escalar horizontalmente aplicativos.

O data center é dividido em clusters. Clusters de isolar falhas no nível do FC e impedir que determinadas classes de erros que afetam os servidores, além do cluster em que elas ocorrem. FCs que servem a um cluster do Azure específico são agrupados em um Cluster de FC.

**Inventário de Hardware**: um inventário dos dispositivos de hardware e de rede do Azure é preparado durante o processo de configuração de inicialização e documentado no arquivo de configuração datacenter.xml. Qualquer novo hardware e os componentes de rede inserindo o ambiente de produção do Azure devem seguir o processo de configuração de inicialização. O FC é responsável por gerenciar o estoque inteiro listado no arquivo de configuração datacenter.xml.

**SO gerenciado pelo FC**: equipe o sistema operacional fornece imagens do sistema operacional na forma de discos rígidos virtuais (VHD) que são implantadas em todos os hosts e VMs convidadas no ambiente de produção do Azure. A equipe de sistema operacional constrói essas "Imagens de Base" por meio de um processo automatizado de compilação offline. A imagem Base é uma versão do sistema operacional em que o kernel e outros componentes básicos foram modificados e otimizados para suportar o ambiente do Azure.

Há três tipos de imagens do sistema operacional gerenciado de malha:

- SO de host – o sistema operacional do Host é um sistema operacional personalizado que é executado em VMs de Host
- Sistema operacional nativo – sistema operacional nativo que é executado em locatários (por exemplo, o armazenamento do Azure) que não tem qualquer hipervisor
- Sistema operacional convidado – sistema operacional convidado que é executado em máquinas virtuais convidadas

O Host e os sistemas gerenciados pelo FC nativo são projetados para uso na nuvem e não são acessíveis publicamente.

**Host e o sistema operacional nativo**: sistema operacional do Host e o sistema operacional nativo são imagens reforçadas do sistema operacional que hospedam os agentes de malha (FA) e executados em um nó de computação (executado como a primeira VM no nó) e nós de armazenamento. Os benefícios do uso otimizado da Base de imagens do Host e sistema operacional nativo é que ela reduz a área de superfície exposta por APIs ou componentes não utilizados que apresentam riscos de segurança alta e aumentam o volume do sistema operacional. Esses sistemas operacionais de volume reduzido incluem apenas os componentes necessários para o Azure. Isso melhora o desempenho e reduz a superfície de ataque.

**Sistema operacional convidado**: os componentes internos do Azure em execução em VMs do sistema operacional convidado não têm nenhuma oportunidade de executar o protocolo de área de trabalho remota (RDP) ao contrário de clientes externos. Todas as alterações às definições de configuração de linha de base seriam necessário para percorrer a alteração e o processo de gerenciamento de liberação.

## <a name="azure-datacenters"></a>Datacenters do Azure
A equipe de infraestrutura de nuvem da Microsoft e de operações (MCIO) gerencia datacenter e infraestrutura instalações físicas da Microsoft para todos os serviços online da Microsoft. MCIO é responsável principalmente para gerenciamento dos controles físicos e ambientais dentro dos datacenters, bem como gerenciar e dar suporte a dispositivos de rede de perímetro externo (roteadores de borda e os roteadores de Datacenter). MCIO também é responsável por configurar o hardware de servidor mínimo em racks nos data centers. Os clientes não têm nenhuma interação direta com o Azure.

## <a name="service-management--service-teams"></a>Gerenciamento de serviços e equipes de serviço
Suporte dos serviços do Azure é gerenciado por um número de grupos de engenharia, conhecido como 'As equipes de serviço'. Cada uma das equipes de serviço é responsável por uma área do suporte para o Azure. Cada equipe de serviço deve fazer um engenheiro disponível 24x7 para investigar e resolver a falha no serviço. As equipes de serviço, por padrão, têm acesso físico ao hardware operacional no Azure.

As equipes de serviço são:

- Plataforma de aplicativo
- Azure Active Directory
- Computação do Azure
- Rede do Azure
- Engenharia de serviços de nuvem
- ISSD: Segurança
- Autenticação multifator
- Banco de dados SQL
- Armazenamento

## <a name="types-of-users"></a>Tipos de usuários
Todos os usuários internos do Azure têm seus status de funcionário categorizados com um nível de confidencialidade que define o acesso aos dados do cliente (acesso ou sem acesso). Os funcionários (ou prestadores de serviço) da Microsoft são considerados usuários internos. Todos os outros usuários são considerados usuários externos. Privilégios de usuário no Azure (autorização de permissão após a autenticação) são descritos na tabela a seguir:

| Função | Interno ou externo | Nível de confidencialidade | Funções executadas e privilégios autorizados | Tipo de acesso
| --- | --- | --- | --- | --- |
| Engenheiro do datacenter do Azure | Interna | Nenhum acesso aos dados do cliente | Gerenciar a segurança física das instalações; Conduzir patrulhas para dentro e fora do datacenter e monitorar todos os pontos de entrada; Executar serviços de acompanhantes dentro e fora do datacenter para determinados funcionários não liberados que fornecem serviços gerais (refeições, limpeza) ou trabalho de TI dentro do datacenter; Realizar monitoramento e manutenção de rotina de hardware de rede; Execute o gerenciamento de incidentes e o trabalho de correção de falhas usando várias ferramentas; Realizar monitoramento e manutenção de rotina do hardware físico nos datacenters; Acesso ao ambiente sob demanda dos proprietários. Capaz de realizar investigações forenses, registrar o relatório de incidentes e exigir requisitos obrigatórios de treinamento e política de segurança; Propriedade operacional e manutenção de ferramentas de segurança críticas, como scanners e coleta de registros. | O acesso persistente ao ambiente |
| Triagem de incidentes do Microsoft Azure (engenheiros Rapid Response) | Interna | Acesso aos dados do cliente | Gerenciar as comunicações entre as equipes de operações de infraestrutura, suporte e engenharia do Azure; Incidentes de plataforma de triagem, problemas de implantação e solicitações de serviço. | Acesso just in time ao ambiente - com acesso persistente limitado para sistemas não clientes |
| Engenheiros de Implantação do Microsoft Azure | Interna | Acesso aos dados do cliente | Execute implantação/atualização de componentes da plataforma, software e alterações de configuração agendada para dar suporte ao Microsoft Azure. | Acesso just in time ao ambiente - com acesso persistente limitado para sistemas não clientes |
| Suporte de interrupção de cliente do Microsoft Azure (Locatário) | Interna | Acesso aos dados do cliente | Depure e diagnostique interrupções e falhas de plataforma para inquilinos de computação individuais e contas do Microsoft Azure; Analise falhas e direcione correções críticas para a plataforma / cliente, conduza melhorias técnicas em todo o suporte. | Acesso just in time ao ambiente - com acesso persistente limitado para sistemas não clientes |
| Incidente & engenheiros de sites ativos do Microsoft Azure (engenheiros de monitoramento) | Interna | Acesso aos dados do cliente | Responsável por diagnosticar e mitigar a saúde da plataforma usando ferramentas de diagnóstico; Direcione correções para drivers de volume, conserte itens resultantes de interrupções e ajude as ações de restauração de interrupções. | Acesso just in time ao ambiente - com acesso persistente limitado para sistemas não clientes |
|Microsoft Azure Customers | Externo | N/D | N/D | N/D |

O Azure usa identificadores exclusivos para autenticar usuários e usuários organizacionais (ou processos agindo em nome de usuários organizacionais) em todos os ativos / dispositivos que fazem parte do ambiente do Azure.

**Autenticação interna do Microsoft Azure**: as comunicações entre componentes internos do Azure são protegidas com criptografia TLS. Na maioria dos casos, os certificados x. 509 são autoassinados. As exceções são feitas para certificados com conexões que podem ser acessadas de fora da rede do Azure e para o FCs. FCs têm certificados emitidos por um Microsoft Certificate da autoridade (CA) que é apoiada por uma autoridade de certificação de raiz confiável. Isso permite que as chaves públicas de FC ser revertidas facilmente. Além disso, as chaves públicas FC são usadas pelas ferramentas de desenvolvedor da Microsoft para que, quando os desenvolvedores enviam novas imagens de aplicativos, sejam criptografadas com uma chave pública FC para proteger os segredos incorporados.

**Autenticação de dispositivo de hardware do Microsoft Azure**: o FC mantém um conjunto de credenciais (chaves e / ou senhas) usadas para se autenticar em vários dispositivos de hardware sob seu controle. O sistema usado para transportar, persistir e usar essas credenciais foi projetado para impedir que os desenvolvedores, administradores e serviços de backup do Azure acessem informações confidenciais, confidenciais ou particulares.

O tempo de criptografia com base na identidade do mestre do FC chave pública é usada na instalação do FC e reconfiguração de FC para transferir as credenciais usadas para acessar dispositivos de hardware de rede. As credenciais são recuperadas e descriptografadas pelo FC quando precisa deles.

**Dispositivos de Rede**: as contas de serviço de rede são configuradas pela equipe do Azure Networking para permitir que um cliente do Microsoft Azure autentique em dispositivos de rede (roteadores, comutadores e balanceadores de carga).

## <a name="secure-service-administration"></a>Administração segura do serviço
A equipe de operações do Microsoft Azure precisa usar estações de trabalho administrativas seguras (SAWs; os clientes podem implementar controles semelhantes usando as Estações de Trabalho de Acesso Privilegiado, ou PAWs). A abordagem SAW é uma extensão da prática recomendada bem estabelecida para usar contas de usuário e administrador separadas para a equipe administrativa. Esta prática usa uma conta administrativa individualmente atribuída que é separada da conta de usuário padrão. A SAW se baseia nessa prática de separação de conta fornecendo uma estação de trabalho confiável para essas contas confidenciais.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, confira:

- [Recursos, local e segurança física do Azure](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Recursos de segurança do Banco de Dados SQL do Microsoft Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitoramento da infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Monitoramento da integridade do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente no Azure](azure-protection-of-customer-data.md)
