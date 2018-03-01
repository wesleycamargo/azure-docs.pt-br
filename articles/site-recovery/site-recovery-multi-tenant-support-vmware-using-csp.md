---
title: "Suporte para vários locatários para replicação de VM do VMware no Azure (programa CSP) | Microsoft Docs"
description: "Descreve como implantar o Azure Site Recovery em um ambiente de multilocatário para orquestrar a replicação, o failover e a recuperação de VMs (máquinas virtuais) VMware locais no Azure por meio do Programa CSP usando o Portal do Azure"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 532dd399d2d5fcbab616744dd02f4a95078cbb1b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-csp"></a>Suporte a multilocatários no Azure Site Recovery para replicar máquinas virtuais do VMware no Azure por meio do CSP

O Azure Site Recovery oferece suporte a ambientes multilocatários para assinaturas de locatários. Ele também dá suporte à multilocação para assinaturas de locatários que são criadas e gerenciadas por meio do programa CSP (Provedor de Soluções na Nuvem da Microsoft). Este artigo detalha as diretrizes para implementar e gerenciar cenários multilocatários do VMware para o Azure. Para obter detalhes sobre como criar e gerenciar assinaturas de locatários, consulte [Gerenciar multilocação com CSP](site-recovery-manage-multi-tenancy-with-csp.md) .

Essa diretriz é amplamente na documentação existente de replicação de máquinas virtuais VMware para o Azure. Para obter mais informações, consulte [Replicar máquinas virtuais VMware para o Azure com o Site Recovery](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Ambientes multilocatário
Há três modelos principais de multilocatários:

* **HSP (Provedor de Serviços de Hospedagem) Compartilhado**: o parceiro tem a infraestrutura física e usa os recursos compartilhados (vCenter, datacenters, armazenamento físico e assim por diante) para hospedar VMs de vários locatários na mesma infraestrutura. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode ter recuperação de desastre como uma solução de autoatendimento.

* **Provedor de Serviços de Hospedagem Dedicado**: o parceiro tem a infraestrutura física, mas usa recursos dedicados (vários vCenters, repositórios de dados físicos e assim por diante) para hospedar VMs de cada locatário em uma infraestrutura separada. O parceiro pode fornecer gerenciamento de recuperação de desastre como um serviço gerenciado ou o locatário pode já tê-la como uma solução de autoatendimento.

* **MSP (Provedor de Serviços Gerenciado)**: o cliente tem a infraestrutura física que hospeda as VMs e o parceiro fornece a habilitação e gerenciamento de recuperação de desastre.

## <a name="shared-hosting-multi-tenant-guidance"></a>Diretriz de multilocatário de hospedagem compartilhada
Esta seção aborda com detalhes o cenário de hospedagem compartilhada. Os outros dois cenários são subconjuntos do cenário de hospedagem compartilhada e usam os mesmos princípios. As diferenças são descritas ao final da diretriz de hospedagem compartilhada.

O requisito básico em um cenário multilocatário é isolar os vários locatários. Um locatário não deve ser capaz de observar o que é hospedado por outro locatário. Em um ambiente gerenciado por parceiros, esse requisito não é tão importante quanto em um ambiente de autoatendimento, no qual ele pode ser crucial. Este guia presume que o isolamento de locatários é necessário.

A arquitetura é apresentada no diagrama a seguir:

![HSP compartilhado com um vCenter](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)  
**Cenário de hospedagem compartilhada com um vCenter**

Conforme visto no diagrama anterior, cada cliente tem um servidor de gerenciamento separado. Essa configuração limita o acesso do locatário a VMs de locatários específicos e habilita o isolamento de locatários. Um cenário de replicação de máquina virtual do VMware usa o servidor de configuração para gerenciar contas a fim de descobrir VMs e instalar agentes. Os mesmos princípios se aplicam para ambientes multilocatários, com a adição da restrição à descoberta de VM por meio do controle de acesso do vCenter.

O requisito de isolamento de dados precisa que todas as informações confidenciais de infraestrutura (como credenciais de acesso) continuem não sendo reveladas para os locatários. Por esse motivo, é recomendável que todos os componentes do servidor de gerenciamento permaneçam sob controle exclusivo do parceiro. Os componentes de servidor de gerenciamento são:
* CS (Servidor de configuração)
* PS (Servidor de processo)
* MT (Servidor de destino mestre)

Um PS de expansão também está sob controle do parceiro.

### <a name="every-cs-in-the-multi-tenant-scenario-uses-two-accounts"></a>Cada CS no cenário de multilocatários usa duas contas

- **Conta de acesso do vCenter**: use essa conta para descobrir VMs do locatário. Ela recebe a atribuição de permissões de acesso do vCenter (conforme descrito na próxima seção). Para ajudar a evitar vazamentos de acesso, é recomendável que os próprios parceiros insiram essas credenciais na ferramenta de configuração.

- **Conta de acesso à máquina virtual**: use essa conta para instalar o agente de mobilidade em VMs do locatário por meio de envio por push automático. Essa normalmente é uma conta de domínio que um locatário pode fornecer ao parceiro, ou uma que pode ser gerenciada diretamente pelo parceiro. Se o locatário não quiser compartilhar os detalhes diretamente com o parceiro, poderá receber permissão para inserir as credenciais por meio de um acesso por tempo limitado ao CS ou, com a assistência do parceiro, instalar agentes de mobilidade manualmente.

### <a name="requirements-for-a-vcenter-access-account"></a>Requisitos para uma conta de acesso do vCenter

Conforme mencionado na seção anterior, você deve configurar o CS com uma conta atribuída com uma função especial. A atribuição de função deve ser aplicada à conta de acesso do vCenter para cada objeto do vCenter e não propagada para os objetos filhos. Essa configuração garante o isolamento do locatário, pois a propagação de acesso também pode resultar em acesso acidental a outros objetos.

![A opção Propagar para Filho](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

A alternativa é atribuir a conta de usuário e a função no objeto do datacenter e propagá-las para os objetos filhos. Em seguida, conceda à conta uma função *Sem acesso* para cada objeto (como VMs de outros locatários) que deve ficar inacessíveis para um locatário específico. Essa configuração é trabalhosa e expõe controles de acesso acidentais, pois cada novo objeto filho criado também recebe automaticamente o acesso herdado do pai. Portanto, é recomendável usar a primeira abordagem.

O procedimento de acesso da conta do vCenter é o seguinte:

1. Crie uma nova função clonando a função *Somente leitura* predefinida e dê a ela um nome conveniente (como Azure_Site_Recovery usado neste exemplo).

2. Atribua as seguintes permissões a essa função:

    * **Repositório de dados**: Alocar espaço, Procurar repositório de dados, Operações de arquivo de baixo nível, Remover arquivo, Atualizar arquivos de máquina virtual

    * **Rede**: Atribuição de rede

    * **Recurso**: Atribuir VM ao pool de recursos, Migrar VM desligada, Migrar VM ligada

    * **Tarefas**: Criar tarefa, Atualizar tarefa

    * **Máquina virtual**:
        * Configuração > todas
        * Interação > Responder perguntas, Conexão do dispositivo, Configurar mídia de CD, Configurar mídia de disquete, Desligar, Ligar, Instalação de ferramentas VMware
        * Inventário > Criar com base em existente, Criar novo, Registrar, Cancelar registro
        * Provisionamento > Permitir download de máquina virtual, Permitir upload de arquivos de máquina virtual
        * Gerenciamento de instantâneo > Remover instantâneos

    ![A caixa de diálogo Editar Função](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3. Atribua os níveis de acesso à conta do vCenter (usada no CS do locatário) a diferentes objetos da seguinte maneira:

>| Objeto | Função | Comentários |
>| --- | --- | --- |
>| vCenter | Somente leitura | É necessário apenas para permitir o acesso do vCenter para gerenciar diferentes objetos. Você pode remover essa permissão se a conta nunca será fornecida a um locatário ou usada para operações de gerenciamento no vCenter. |
>| Datacenter | Azure_Site_Recovery |  |
>| Host e cluster de host | Azure_Site_Recovery | Verifique novamente se o acesso está no nível de objeto, de forma que somente os hosts acessíveis tenham VMs do locatário antes do failover e após o failback. |
>| Repositório de dados e cluster de repositório de dados | Azure_Site_Recovery | Mesmo que o anterior. |
>| Rede | Azure_Site_Recovery |  |
>| Servidor de gerenciamento | Azure_Site_Recovery | Inclui o acesso a todos os componentes (CS, PS e MT) fora do computador do CS. |
>| VMs do locatário | Azure_Site_Recovery | Verifique se as novas VMs de locatário de um locatário específico também recebem esse acesso ou elas não poderão ser descobertas por meio do Portal do Azure. |

O acesso à conta do vCenter está concluído. Essa etapa cumpre o requisito de permissões mínimas para concluir operações de failback. Você também pode usar essas permissões de acesso com as políticas existentes. Basta modificar seu conjunto de permissões existentes para incluir permissões de função da etapa 2, como detalhado anteriormente.

Para restringir as operações de recuperação de desastre até o estado de failover (ou seja, sem os recursos de failback), siga o procedimento anterior, com uma exceção: em vez de atribuir a função *Azure_Site_Recovery* à conta de acesso do vCenter, atribua apenas uma função *Somente Leitura* a essa conta. Esse conjunto de permissões permite failover e replicação de VM, mas não permite failback. Todo o resto do processo anterior permanece igual. Para garantir o isolamento do locatário e a restringir a descoberta de VM, cada permissão ainda é atribuída apenas no nível do objeto e não propagada aos objetos filhos.

## <a name="other-multi-tenant-environments"></a>Outros ambientes multilocatários

A seção anterior descreveu detalhadamente como configurar um ambiente multilocatário para uma solução de hospedagem compartilhada. As outras duas soluções principais são dedicadas à hospedagem e ao serviço gerenciado. A arquitetura dessas soluções é descrita nas seções a seguir.

### <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

Como mostrado no diagrama a seguir, a diferença de arquitetura em uma solução de hospedagem dedicada é que a infraestrutura de cada locatário está configurada somente para esse locatário. Como os locatários são isolados por meio de vCenters separados, o provedor de hospedagem ainda precisa seguir as etapas de CSP fornecidas para hospedagem compartilhada, mas não precisa se preocupar com o isolamento do locatário. A configuração do CSP permanece inalterada.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)  
**Cenário de hospedagem dedicada com vários vCenters**

### <a name="managed-service-solution"></a>Solução de serviço gerenciado

Conforme mostrado no diagrama a seguir, a diferença de arquitetura em uma solução de serviço gerenciado é que a infraestrutura de cada locatário também é fisicamente separada da infraestrutura de outros locatários. Esse cenário normalmente existe quando o locatário tem a infraestrutura e busca um provedor de solução para gerenciar a recuperação de desastre. Novamente, como os locatários são isolados fisicamente por meio de infraestruturas diferentes, o parceiro ainda precisa seguir as etapas de CSP fornecidas para hospedagem compartilhada, mas não precisa se preocupar com o isolamento do locatário. O provisionamento de CSP permanece inalterado.

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)  
**Cenário de serviço gerenciado com vários vCenters**

## <a name="next-steps"></a>Próximas etapas
[Saiba mais](site-recovery-role-based-linked-access-control.md) sobre o controle de acesso baseado em função para gerenciar implantações do Azure Site Recovery.

[Gerenciar multilocação com CSP](site-recovery-manage-multi-tenancy-with-csp.md)
