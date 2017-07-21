---
title: "Suporte para vários locatários para replicação de VM do VMware no Azure (programa CSP) | Microsoft Docs"
description: "Descreve como implantar o Azure Site Recovery em um ambiente de multilocatário para orquestrar a replicação, o failover e a recuperação de máquinas virtuais VMware locais no Azure por meio do Programa CSP usando o Portal do Azure"
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
ms.date: 06/23/2017
ms.author: manayar
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 801eb19a2c1601653f229a5175fc71d6551ebe08
ms.contentlocale: pt-br
ms.lasthandoff: 06/23/2017


---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-the-csp-program"></a>Suporte de multilocatário no Azure Site Recovery para replicar máquinas virtuais do VMware no Azure por meio do Programa CSP

O Azure Site Recovery oferece suporte a ambientes multilocatários para assinaturas de locatários. Também há suporte para multilocação para assinaturas de locatários criadas e gerenciadas por meio do programa CSP. Este artigo detalha as diretrizes para implementar e gerenciar cenários multilocatários do VMware para o Azure. Também há detalhes sobre a criação e gerenciamento de assinaturas de locatário por meio do CSP.

Observe que este guia usa intensamente a documentação existente para replicar máquinas virtuais VMware no Azure. Este guia deve ser usado em conjunto com essa [documentação](site-recovery-vmware-to-azure.md).

## <a name="multi-tenant-environments"></a>Ambientes multilocatário
Há três modelos principais de multilocatários:

1.  **Provedor de Serviços de Hospedagem (HSP) Compartilhado** – Aqui, o parceiro possui a infraestrutura física e usa os recursos compartilhados (vCenter, datacenters, armazenamento físico etc.) para hospedar VMs de vários locatários na mesma infraestrutura. O gerenciamento de recuperação de desastres pode ser fornecido pelo parceiro como um serviço gerenciado ou pertencer ao locatário como uma solução de recuperação de desastres de autoatendimento.
2.  **Provedor de Serviços de Hospedagem Dedicado** – Aqui, o parceiro possui a infraestrutura física, mas usa recursos dedicados (vários vCenters, armazenamentos de dados físicos etc.) para hospedar VMs de cada locatário em uma infraestrutura separada. Novamente, o gerenciamento de recuperação de desastres pode ser gerenciado por parceiro ou autoatendimento pelo locatário.
3.  **Provedor de Serviços Gerenciado (MSP)** – Aqui, o cliente possui a infraestrutura física que hospeda as VMs, e os parceiros fornecem habilitação e gerenciamento de recuperação de desastres.

## <a name="shared-hosting-multi-tenant-guidance"></a>Diretriz de multilocatário de hospedagem compartilhada
Este guia aborda com detalhes o cenário de hospedagem compartilhado. Os outros dois cenários são subconjuntos do cenário de hospedagem compartilhado e usam os mesmos princípios. As diferenças são descritas ao final do guia de hospedagem compartilhado.

O requisito básico em um cenário multilocatário é isolar os locatários diferentes: um locatário não deve ser capaz de observar o que outro locatário tem hospedado. Em um ambiente totalmente gerenciado por parceiros, esse requisito não é tão importante quanto para um ambiente de autoatendimento, no qual ele pode ser fundamental. Este guia presume que o isolamento de locatários é necessário.

A arquitetura tem a seguinte aparência:

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)

**Figura 1: Cenário de hospedagem compartilhada com um vCenter**

Conforme visto na representação acima, cada cliente terá um Servidor de Gerenciamento separado. Isso limita o acesso do locatário a VMs de locatário específicas e habilita o isolamento de locatários. O cenário de replicação de máquina virtual do VMware usa o servidor de configuração para gerenciar contas a fim de descobrir VMs e instalar agentes. Seguimos os mesmos princípios para ambientes multilocatários, mas acrescentando a restrição à descoberta de VM por meio do controle de acesso do vCenter.

O requisito de isolamento de dados exige que todas as informações confidenciais da infraestrutura (como credenciais de acesso) continuem sendo divulgadas pelos locatários. Por esse motivo, recomendamos que todos os componentes do servidor de gerenciamento (CS [Servidor de Configuração], PS [Servidor de Processo] e MT [Servidor de Destino Mestre]) permaneçam sob o controle exclusivo do parceiro. Isso inclui PS de expansão.

### <a name="every-cs-under-the-multi-tenant-scenario-uses-two-accounts"></a>Cada CS no cenário de multilocatário usa duas contas:

- **Conta de acesso do vCenter**: essa conta é usada para descobrir VMs do locatário e é a conta que tem as permissões de acesso do vCenter atribuídas (descritas na próxima seção). Recomendamos que o parceiro insira essas credenciais por conta própria na ferramenta de configuração para evitar vazamentos acidentais de acesso.
- **Conta de acesso à máquina virtual**: essa conta é usada para instalar o agente de mobilidade em VMs do locatário por meio de envio por push automático. Normalmente, essa é uma conta de domínio que um locatário pode fornecer ao parceiro ou que pode ser gerenciada, como alternativa, diretamente por parceiro. Caso o locatário não queira compartilhar os detalhes diretamente para o parceiro, o locatário poderá ter permissão para inserir as credenciais por meio de um acesso de tempo limitado ao CS ou, opcionalmente, os locatário podem instalar agentes de mobilidade manualmente com a assistência do parceiro.

### <a name="requirements-for-vcenter-access-account"></a>Requisitos para contas de acesso do vCenter

Conforme detalhado na seção anterior, o CS exige a configuração com uma conta que tem uma função especial atribuída. É importante observar que essa atribuição de função precisa ser feita para a conta de acesso do vCenter para cada objeto do vCenter, e não propagada para os objetos filhos. Isso garante o isolamento do locatário, pois a propagação de acesso também pode resultar em acesso acidental a outros objetos

![permissões sem propagação](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

A alternativa é atribuir a conta de usuário e a função no objeto do Datacenter e propagar para objetos filho; depois disso, essas contas precisam receber uma função ‘Sem acesso’ para cada objeto (como VMs de outros locatários) que não deve estar acessível a um locatário específico. Isso é trabalhoso e expõe controles de acesso acidentais, pois cada objeto filho novo criado também recebe automaticamente o acesso herdado do pai. Portanto, recomendamos a primeira abordagem.

O procedimento de acesso da conta do vCenter é o seguinte:

1.  Crie uma nova função clonando a função 'Somente leitura' predefinida e dê a ela um nome conveniente (como Azure_Site_Recovery usado neste exemplo).
2.  Atribua as seguintes permissões a essa função:
 *  Armazenamento de dados -> alocar espaço, procurar armazenamento de dados, operações de arquivo de baixo nível, remover arquivo, atualizar arquivos de máquina virtual
 *  Rede -> Atribuição de rede
 *  Recurso -> Atribuir VM ao pool de recursos, migrar VM desligada, migrar VM ligada
 *  Tarefas -> Criar tarefa, atualizar tarefa
 *  Máquina virtual -> Configuração
 *  Máquina virtual -> Interagir -> Responder pergunta, conexão de dispositivo, configurar mídia de CD, configurar mídia de disquete, desligar, ligar, instalação de ferramentas de VMware
 *  Máquina virtual -> Inventário -> Criar, registrar, cancelar registro
 *  Máquina virtual -> Provisionamento -> Permitir download de máquina virtual, permitir upload de arquivos de máquina virtual
 *  Máquina virtual -> Instantâneos -> Remover instantâneos.

    ![permissões de função](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3.  Atribua o nível de acesso à conta do vCenter (usada no CS do locatário) para objetos diferentes da seguinte maneira:

| **Objeto** | **Função** | **Comentários** |
| --- | --- | --- |
| vCenter | Somente leitura | Isso é necessário apenas para permitir o acesso do vCenter para gerenciar objetos diferentes. Essa permissão pode ser removida se a conta nunca for fornecida a um locatário ou usada para operações de gerenciamento no vCenter. |
| Datacenter | Azure_Site_Recovery |  |
| Host e Cluster de Host | Azure_Site_Recovery | Verifique novamente se o acesso está no nível de objeto, assim somente os hosts com VMs do locatário serão acessíveis antes do failover e após o failback. |
| Repositório de dados e Cluster de Repositório de dados | Azure_Site_Recovery | O mesmo que o descrito acima |
| Rede | Azure_Site_Recovery |  |
| Servidor de Gerenciamento | Azure_Site_Recovery | Isso inclui acesso a todos os componentes – CS, PS e MT – se algum estiver fora da máquina CS. |
| VMs do locatário | Azure_Site_Recovery | Certifique-se de que quaisquer VMs de locatário novas de um locatário específico também recebam esse acesso, ou não serão descobertas por meio do Portal do Azure. |

O acesso à conta do vCenter está concluído. Isso preenche o requisito mínimo de permissões para concluir as operações de failback. Essas permissões de acesso também podem ser usadas com as políticas existentes. Basta modificar suas permissões existentes definidas a fim de incluir permissões do ponto 2 detalhado acima.

Para restringir operações de recuperação de desastres até o estado de failover, ou seja, sem os recursos de failback, siga o procedimento acima, mas em vez de atribuir a função 'Azure_Site_Recovery' à conta de acesso do vCenter, atribua apenas uma função 'Read Only' a essa conta. Esse conjunto de permissões permite failover e replicação de VM e não permite failback. O resto do processo acima permanece como está. Cada permissão ainda é atribuída apenas no nível do objeto e não propagada aos objetos filhos, a fim de garantir o isolamento do locatário e restringir a descoberta da VM.

## <a name="other-multi-tenant-environments"></a>Outros ambientes multilocatários

A orientação descreveu detalhadamente acima como configurar um ambiente de multilocatário para uma solução de hospedagem compartilhada. As outras duas soluções principais são dedicadas à hospedagem e ao serviço gerenciado. A arquitetura delas é a seguinte:

### <a name="dedicated-hosting-solution"></a>Solução de hospedagem dedicada

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)

**Figura 2: Cenário de hospedagem dedicada com vários vCenters**

A diferença de arquitetura aqui é que a infraestrutura de cada locatário é provisionada apenas para esse locatário. O provedor de hospedagem ainda precisa seguir as etapas de CSP detalhadas para hospedagem compartilhada, mas não precisa se preocupar com o isolamento do locatários, pois os locatários são isolados por meio de vCenters separados. O provisionamento de CSP permanece inalterado.

### <a name="managed-service-solution"></a>Solução de serviço gerenciado

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)

**Figura 3: Cenário de serviço gerenciado com vários vCenters**

A diferença de arquitetura aqui é que a infraestrutura de cada locatário também é fisicamente separada de outros locatários. Esse cenário normalmente existe quando o locatário possui a infraestrutura e quer apenas um provedor de solução para gerenciar a recuperação de desastres. O parceiro ainda precisa seguir as etapas de CSP detalhadas para hospedagem compartilhada, mas não precisa se preocupar com o isolamento do locatários, pois os locatários são isolados fisicamente por meio de infraestruturas diferentes. O provisionamento de CSP permanece inalterado.


## <a name="csp-program-overview"></a>Visão geral do programa CSP
O [programa](https://partner.microsoft.com/en-US/cloud-solution-provider) CSP (Provedor de Soluções de Nuvem) da Microsoft promove histórias de trabalho em conjunto com parceiros a fim de oferecer todos os serviços de nuvem da Microsoft, incluindo O365, EMS e o Microsoft Azure. Ele permite que nossos parceiros controlem a relação de ponta a ponta com clientes e se tornem o ponto de contato principal do relacionamento. Por meio do CSP, um parceiro pode implantar as assinaturas do Azure para os clientes e combinar essas assinaturas com suas próprias ofertas personalizadas de valor agregado.

Com o Azure Site Recovery, os parceiros podem gerenciar a solução completa de Recuperação de desastres para os clientes diretamente do CSP, ou usar o CSP para configurar os ambientes do Azure Site Recovery e permitir que os clientes gerenciem suas próprias necessidades de recuperação de desastres por meio do autoatendimento. Nos dois cenários, o parceiro é o elo entre o Azure Site Recovery e os clientes finais, e o parceiro realiza o relacionamento com os clientes e os cobra pelo uso do Azure Site Recovery.

## <a name="creating-and-managing-tenant-accounts"></a>Criar e gerenciar contas de locatário

### <a name="step-0-prerequisite-check"></a>Etapa 0: Verificação de pré-requisitos

Os pré-requisitos de VM são iguais aos descritos na [documentação](site-recovery-vmware-to-azure.md) do Azure Site Recovery. Além desses pré-requisitos, os controles de acesso acima devem estar ativos antes de prosseguir com o gerenciamento de locatário por meio do CSP. Crie um Servidor de Gerenciamento separado para cada locatário que possa se comunicar com as VMs do locatário com o vCenter do parceiro. Somente o parceiro tem direitos de acesso a esse servidor.

### <a name="step-1-create-tenant-account"></a>Etapa 1: Criar a conta de locatário

1.  Por meio do [Centro de parceiros](https://partnercenter.microsoft.com/) faça logon em sua conta do CSP. No menu Painel à esquerda, selecione a opção 'Clientes'.

    ![csp-dashboard](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

2.  Na página aberta, clique no botão 'Adicionar cliente'.

    ![add-customer](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

3.  Na página Novo Cliente, preencha todos os detalhes de informações da conta para o locatário e clique em 'Avançar: Assinaturas'.

    ![fill-details](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

4.  Na página de seleção de assinaturas, role a tela para baixo para adicionar a assinatura 'Microsoft Azure'. Outras assinaturas podem ser adicionadas agora ou a qualquer outro momento posterior.

    ![add-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

5.  Continue em frente e, na próxima página, revise todos os detalhes do locatário e clique no botão Enviar.

    ![customer-summary](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)

6.  Após a criação do cliente, você obtém uma página de confirmação com os detalhes da conta e senha padrão dessa assinatura. Salve as informações e altere a senha posteriormente, conforme necessário, por meio do logon no Portal do Azure. Essas informações podem ser compartilhadas como estão com o locatário, ou também é possível criar e compartilhar uma conta separada, se for necessário.

### <a name="step-2-access-tenant-account"></a>Etapa 2: Acessar a conta de locatário

1.  Você pode acessar a assinatura do locatário na página 'Clientes' em seu Painel, conforme descrito na etapa 1. Navegue até aqui e clique no nome da conta de locatário criada.
2.  Isso abrirá a seção Assinaturas da conta de locatário e, a partir daqui, você pode monitorar as assinaturas existentes da conta e adicionar outras assinaturas conforme o necessário. Para gerenciar as operações de recuperação de desastres do locatário, selecione a opção Todos os recursos (Portal do Azure), no lado direito da página.

    ![all-resources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)

3.  Clicar no botão 'Todos os recursos' concede a você acesso às assinaturas do Azure do locatário, e você poderá confirmar verificar o mesmo verificando o AAD exibido no canto superior direito do Portal do Azure.

    ![aad-admin](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

Agora você pode executar todas as operações do Site Recovery do locatário por meio do Portal do Azure e gerenciar as operações de recuperação de desastres. O processo detalhado acima deve ser seguido sempre para acessar a assinatura do locatário por meio do CSP para recuperação de desastres gerenciada.

### <a name="step-3-deploy-resources-to-tenant-subscription"></a>Etapa 3: Implantar recursos na assinatura de locatário
1.  No Portal do Azure, crie um grupo de recursos e implante um cofre dos Serviços de Recuperação de acordo com o processo normal. Baixe a chave do registro do cofre.
2.  Registre o CS para o locatário usando a chave de registro do cofre.
3.  Insira as credenciais das duas contas de acesso – conta de acesso do vCenter e conta de acesso da VM.

    ![config-accounts](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-recovery-services-vault"></a>Etapa 4: Registrar a infraestrutura do Site Recovery no cofre dos Serviços de Recuperação
1.  Abra o Portal do Azure e, no cofre criado anteriormente, registre o servidor do vCenter no CS registrado na etapa anterior. Use a conta de acesso do vCenter para essa finalidade.
2.  Conclua o processo de 'Preparação da infraestrutura' para o Site Recovery de acordo com o processo normal.
3.  Agora, as VMs estão prontas para serem replicadas. Verifique se apenas as VMs do locatário estão visíveis na folha de seleção da VM, sob a opção Replicar.

    ![tenant-vms](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-subscription"></a>Etapa 5: Atribuir acesso do locatário à assinatura

No caso de recuperação de desastres de autoatendimento, os detalhes da conta devem ser fornecidos para o locatário conforme mencionado no item 6 da etapa 1. Isso deve ser feito após o parceiro configurar a infraestrutura de recuperação de desastres. Independentemente do tipo de recuperação de desastres (gerenciado ou por autoatendimento), o parceiro precisa acessar as assinaturas de locatários por meio do portal do CSP e configurar o cofre e registrar a infraestrutura pertencente ao parceiro para as assinaturas de locatários.

Um parceiro também pode adicionar um novo usuário à assinatura de locatário por meio do portal do CSP da seguinte maneira:

1.  Acesse a página de inscrição do CSP do locatário específico e selecione a opção 'Usuários e licenças'.

    ![user-licenses](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    Agora, você pode criar um novo usuário inserindo os detalhes relevantes e selecionando as permissões ou, como alternativa, carregando a lista de usuários por um arquivo CSV.
2.  Após a criação dos usuários, volte para o Portal do Azure e na folha Assinatura, selecione a assinatura relevante.
3.  Na nova folha que é aberta, selecione o IAM (Controle de Acesso) e clique em +Adicionar para adicionar um usuário com o nível de acesso relevante. Os usuários criados por meio do Portal do CSP aparecerão automaticamente na folha que abre após você clicar em um nível de acesso.

    ![user-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    Para a maioria das operações de gerenciamento, a função de Colaborador é suficiente. Um usuário com esse nível de acesso pode fazer tudo em uma assinatura, exceto alterar níveis de acesso (para o qual um nível de acesso Proprietário é necessário). Você também pode ajustar os níveis de acesso conforme o necessário.

