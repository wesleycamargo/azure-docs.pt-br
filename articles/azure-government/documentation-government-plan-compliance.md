---
title: Conformidade com o Azure Governamental | Microsoft Docs
description: "Fornece uma visão geral do serviços de conformidade disponíveis para o Azure Governamental"
services: azure-government
cloud: gov
documentationcenter: 
author: jomolesk
manager: zakramer
ms.assetid: 1d2e0938-482f-4f43-bdf6-0a5da2e9a185
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/07/2016
ms.author: jomolesk
translationtype: Human Translation
ms.sourcegitcommit: 474ee83dfc8532f8410b79c6426af3cad6f3d320
ms.openlocfilehash: cd54456c8f9e0f37f53973fca80b8bd3514b62d1


---
# <a name="azure-government-compliance"></a>Conformidade com o Azure Governamental

## <a name="azure-blueprint"></a>Especificações Técnicas do Azure

O programa de Especificações Técnicas do Azure é projetado para facilitar o uso seguro e em conformidade do Azure para órgãos governamentais e provedores de terceiros, criando em nome do governo. Clientes do Azure Governamental podem aproveitar a Autoridade Provisória JAB do FedRAMP do Azure Governamental para operar (P-ATO) ou a Autorização Provisória DoD (PA), reduzindo o escopo dos controles de segurança de responsabilidade do cliente em sistemas baseados no Azure. Herdar implementações de controle de segurança do Azure Governamental permite que os clientes se concentrem em implementações de controle específicas em seus ambientes IaaS, PaaS ou SaaS criados no Azure.

> [!NOTE]
> No contexto de Especificações Técnicas do Azure, "cliente" faz referência à organização que cria diretamente no Azure. Os clientes do Azure podem incluir os prédios de ISVs terceirizados criando em nome do governo ou dos órgãos do governo diretamente no Azure.

## <a name="blueprint-customer-responsibilities-matrix"></a>Matriz de Responsabilidades do Cliente de Especificações Técnicas

A CRM (Matriz de Responsabilidades do Cliente) de Especificações Técnicas do Azure foi projetada para ajudar os clientes do Azure Governamental a implementar e documentar controles de segurança específicos do sistema implementados no Azure. A CRM lista explicitamente todos os requisitos de controle de segurança [NIST SP 800-53](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-53r4.pdf) para linhas de base FedRAMP e DISA que incluem um requisito de implementação do cliente. Isso inclui controles com uma responsabilidade compartilhada entre o Azure e os clientes do Azure, bem como controles que devem ser totalmente implementados pelos clientes do Azure. Quando apropriado, os controles são delimitados com granularidade de sub-requisito de controle para fornecer orientação mais específica.

O formato de CRM destina-se ao utilitário e promove a documentação concentrada apenas nas partes do cliente dos controles de segurança implementados.

Por exemplo, o controle AC-1 requer procedimentos e políticas de controle de acesso documentadas para o sistema que busca um ATO. Para esse controle, a Microsoft tem políticas e procedimentos internos específicos do Azure relacionados a mecanismos de controle de acesso usados para gerenciar a plataforma e a infraestrutura do Azure. Os clientes também devem criar suas próprias políticas e procedimentos do controle de acesso usados no sistema específico criado no Azure. Os documentos da CRM controlam partes AC-1a, o que exige que as políticas e os procedimentos incluam conteúdo específico, bem como AC-1b, que exige que os clientes examinem e atualizem esses documentos anualmente.

A CRM de Especificações Técnicas está disponível como pasta de trabalho do Microsoft Excel para as linhas de base FedRAMP Moderada e Alta e as linhas de base DISA Cloud Computing SRG L4 e L5.

Para solicitar uma cópia da CRM de Especificações Técnicas do Azure ou fornecer comentários, envie um email a [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="blueprint-system-security-plan"></a>Plano de Segurança de Sistema de Especificações Técnicas

O modelo de SSP (Plano de Segurança do Sistema) de Especificações Técnicas do Azure é voltado para o cliente e projetado para ser usado no desenvolvimento de um SSP que documenta as implementações de controle de segurança do cliente, bem como controles herdados do Azure. Controles que incluem uma responsabilidade do cliente, contendo orientações para a documentação da implementação de controle com uma resposta completa e em conformidade. As seções sobre herança do Azure documentam como os controles de segurança são implementados pelo Azure em nome do cliente.

O SSP de Especificações Técnicas do Azure está disponível para as linhas de base FedRAMP Moderada e Alta e as linhas de base DISA Cloud Computing SRG L4 e L5. 

Para solicitar uma cópia do SSP de Especificações Técnicas do Azure ou fornecer comentários, envie um email a [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

## <a name="azure-blueprint-implementation-guidance"></a>Diretrizes de implementação de Especificações Técnicas do Azure

As diretrizes de implementação de Especificações Técnicas do Azure foram projetadas para ajudar arquitetos de soluções de nuvem e a equipe de segurança a entender como os recursos e serviços do Azure Governamental podem ser implantados para implementar um subconjunto dos controles de segurança FedRAMP e DoD de responsabilidade do cliente. Uma matriz de documentação, ferramentas, modelos e outros recursos está disponível para orientar a implantação segura de recursos e serviços do Azure. Os recursos do Azure podem ser implantados usando os [blocos de construção](https://github.com/mspnp/template-building-blocks) do modelo do Azure Resource Manager, [modelos Início Rápido](https://azure.microsoft.com/resources/templates/) do Azure fornecidos pela comunidade ou por meio do uso de modelos do Resource Manager com base em JSON [criados pelo cliente](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). A arquitetura de uma implantação básica no Azure inclui recursos de computação, rede e armazenamento. Essa orientação de implementação aborda como esses recursos podem ser implantados de forma a ajudar a atender aos requisitos de implementação de controle de segurança.

### <a name="virtual-machines"></a>Máquinas Virtuais

Os modelos do Azure Resource Manager podem ser usados para implantar máquinas virtuais pré-configuradas que atendem aos STIGs (guias de implementação técnica de segurança do governo e do setor) dos EUA e a parâmetros de comparação de segurança. Máquinas virtuais do Azure personalizadas podem ser criadas usando uma máquina pré-configurada existente ou implantando uma nova máquina virtual e aplicando uma política de segurança usando o Active Directory, para máquinas que ingressaram no domínio ou o Utilitário de Objeto de Política de Grupo Local, para máquinas autônomas. A extensão de Script Personalizado de máquina virtual do Azure pode ser usada para gerenciar essas tarefas de configuração pós-implantação.

A configuração de máquina virtual do Windows pode incluir implementações de controle de segurança para:

- O sistema usa notificação e confirmação [controle NIST SP 800-53 AC-8]

  > Instrução de implementação de controle de exemplo AC-8: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização, incluindo o uso de uma notificação de uso do sistema apropriada, que os usuários devem reconhecer antes do logon (ref. parâmetro de comparação CIS, seções 2.3.7.4, 2.3.7.5).*

- Restrições de conta de computador local, incluindo bloqueio de conta [controle NIST SP 800-53 AC-7], controle de sessão simultânea [controle NIST SP 800-53 AC-10], bloqueio de sessão [controle NIST SP-800-53 AC-11], gerenciamento de autenticador [controle NIST SP 800-53 IA-5] e outros

  > Instrução de implementação de controle de exemplo AC-7: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação do CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização, incluindo o início de um bloqueio de conta depois de tentativas de logon com falha consecutivas (ref. Seções de parâmetro de comparação CIS 1.2.1, 1.2.2, 1.2.3; observação: valores de parâmetro de comparação CIS devem ser alterados para atender aos requisitos de parâmetro FedRAMP e DoD).*

  > Instrução de implementação de controle de exemplo AC-10: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização, incluindo a restrição dos usuários a uma sessão de serviços de área de trabalho remota única que atenda aos requisitos FedRAMP e DoD. (ref. parâmetro de comparação CIS, seção 18.9.48.3.2).*

  > Instrução de implementação de controle de exemplo AC-11: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação do CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização, incluindo o início de um bloqueio de sessão após 900 segundos (15 minutos) de inatividade que seja mantido até que o usuário seja autenticado novamente (ref. parâmetro de comparação CIS, seções 2.3.7.3, 19.1.3.1, 19.1.3.3).*

  > Instrução de implementação de controle de exemplo IA-5: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização, incluindo restrições sobre senhas de contas locais que impõem restrições mínimas (1 dia) e máximas (60 dias), condições de reutilização (24 senhas), comprimento (14 caracteres) e requisitos de complexidade, que atendem aos requisitos FedRAMP (ref. parâmetro de comparação CIS, seções 1.1.1, 1.1.2., 1.1.3., 1.1.4, 1.1.5; observação: valores de parâmetro de comparação CIS devem ser alterados para atender aos requisitos de parâmetro DoD).*

- Definições de configuração, incluindo menor funcionalidade [controles NIST SP 800-53 CM-6, CM-7]

  > Instrução de implementação de controle de exemplo CM-6: *um objeto de GPO (política de grupo) controlado por configuração é mantido para todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação do CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização específicos às necessidades operacionais do sistema. Este objeto de política de grupo é estabelecido, além de outros recursos de configuração, para documentar e garantir uma implementação consistente de definições de configuração.*

  > Instrução de implementação de controle de exemplo CM-7: *um objeto de GPO (política de grupo) controlado por configuração é mantido para todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é configurado de acordo com o parâmetro de comparação do CIS para Windows Server 2012 R2 e personalizado para atender aos requisitos da organização específicos às necessidades operacionais do sistema. O objeto de política de grupo é usado para estabelecer uma imagem de máquina virtual de linha de base, que é mantida para todas as máquinas do Windows no Azure e reflete a configuração de máquinas do Windows para fornecer somente os recursos essenciais para a operação do sistema.*

  Ferramentas de correção de linha de base de segurança, que incluem GPOs (objetos de política de grupo) pré-configurados para máquinas do Windows e scripts do shell, para máquinas Linux, estão disponíveis na [Microsoft](https://technet.microsoft.com/itpro/windows/keep-secure/windows-security-baselines), para máquinas Windows e no [Center for Internet Security](https://benchmarks.cisecurity.org/) e no [DISA (Defense Information Systems Agency)](http://iase.disa.mil/stigs), para máquinas Windows e Linux.  

Máquinas virtuais do Azure podem ser criptografadas para atender aos requisitos de controle de segurança para proteger as informações em repouso [controle NIST SP 800-53 SC-28]. Modelos de Início Rápido do Azure estão disponíveis para a implantação de criptografia em máquinas virtuais [novas](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image) e existentes do [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm) e do [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm). 

> Instrução de implementação de controle de exemplo SC&28;: *máquinas virtuais controladas pelo cliente no Azure implementam a criptografia de disco para proteger a confidencialidade e a integridade das informações em repouso. A criptografia de disco do Azure para Windows é implementada usando o recurso BitLocker do Windows; a criptografia de disco para máquinas virtuais Linux é implementada usando o recurso DM-Crypt do Linux.

Várias extensões de máquina virtual do Azure podem ser implantadas para atender aos requisitos de controle de segurança. A [extensão da máquina virtual Microsoft Antimalware](https://docs.microsoft.com/azure/security/azure-security-antimalware) pode ser implantada em máquinas virtuais novas e existentes. A extensão Antimalware pode habilitar a proteção em tempo real, bem como verificações periódicas agendadas [controle NIST SP 800-53 SI-3].

> Instrução de implementação de controle de exemplo SI&3;: *proteções antimalware baseadas em host para máquinas virtuais Windows controladas pelo cliente no Azure são implementadas usando a extensão da máquina virtual Antimalware da Microsoft. Essa extensão é configurada para executar verificações periódicas e em tempo real (semanalmente), atualizar automaticamente as assinaturas de proteção e mecanismo antimalware, executar ações de correção automática e fornecer notificações por meio de Diagnóstico do Azure.*

*Recursos e ferramentas adicionais*

[Blocos de construção](https://github.com/mspnp/template-building-blocks) de modelo do Azure Resource Manager que podem ser personalizados e usados para implantar recursos do Azure, incluindo a implantação de [máquinas virtuais](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/multi-vm-n-nic-m-storage) e [extensões de máquina virtual](https://github.com/mspnp/template-building-blocks/tree/master/templates/buildingBlocks/virtualMachine-extensions). 

### <a name="virtual-network"></a>Rede Virtual

A VNet (Rede Virtual do Azure) permite o controle completo de roteamento e políticas de segurança em arquiteturas de rede virtual por meio de implantação e configuração de sub-redes, grupos de segurança de rede e rotas definidas pelo usuário. Grupos de segurança de rede podem ser aplicados a sub-redes ou máquinas individuais, separando logicamente recursos por carga de trabalho, com base em uma arquitetura multicamada, para qualquer outra finalidade. Na arquitetura de referência abaixo, os recursos são agrupados em sub-redes separadas para as camadas da Web, de negócios e de dados e sub-redes para recursos e gerenciamento do Active Directory. Grupos de segurança de rede são aplicados a cada sub-rede para restringir o tráfego de rede na rede virtual. 
 
![Arquitetura de N camadas usando o Microsoft Azure](./media/documentation-government-plan-compliance/compute-n-tier.png)

Grupos de segurança de rede habilitam controle total dos caminhos de comunicação entre recursos [controle NIST SP 800-53 AC-4].

> Instrução de implementação de controle de exemplo AC-4: *máquinas virtuais controladas pelo cliente na Web implementando funções do Azure, de negócios e de camadas de dados são separadas por sub-rede. Grupos de segurança de rede são definidos e aplicados a cada sub-rede, restringindo o fluxo de informações na camada de rede ao mínimo necessário para a funcionalidade do sistema de informações.*

Grupos de segurança de rede podem ser aplicados a comunicações de saída de sub-redes e máquinas virtuais. Isso permite controle total da comunicação entre componentes do sistema de informações em sistemas de informações do Azure e externos [controle NIST SP 800-53 AC-3 (5)]. O processamento de regras de grupo de segurança de rede é implementado como uma função deny-all, permit-by-exception. Além disso, rotas definidas pelo usuário podem ser configuradas para rotear comunicações de entrada e saída de sub-redes específicas e máquinas virtuais por meio de um dispositivo virtual, como um firewall ou IDS/IPS, gerenciando ainda mais as comunicações do sistema.

> Instrução de implementação de controle de exemplo AC-3 (5): *toda a comunicação de saída proveniente de recursos controlados pelo cliente no Azure é restrita por meio da implementação de grupos de segurança de rede, que são configurados com um conjunto de regras de saída que nega todo o tráfego, exceto aquele explicitamente permitido para dar suporte à funcionalidade de sistema de informações, que atende aos requisitos FedRAMP e DoD L4. Além disso, as rotas definidas pelo usuário são configuradas para rotear todo o tráfego de saída por meio de um dispositivo de firewall virtual, que é configurado com um conjunto de regras que permite a comunicação somente com sistemas de informações externos aprovados.*

A arquitetura de referência acima demonstra como recursos do Azure podem ser agrupados logicamente em sub-redes separadas com conjuntos de regras de grupo de segurança de rede aplicados para garantir que as funções de segurança e não segurança sejam isoladas. Nesse caso, as três camadas de aplicativos Web são isoladas da sub-rede do Active Directory, bem como da sub-rede de gerenciamento, que pode hospedar o sistema de informações e ferramentas de gerenciamento de segurança e recursos [controle NIST SP 800-53 SC-3].

> Instrução de implementação de controle de exemplo SC-3: *funções de segurança são isoladas de funções de não segurança no ambiente do Azure controlado pelo cliente por meio da implementação de sub-redes e grupos de segurança de rede que são aplicados a essas sub-redes. Os recursos do sistema de informações dedicados para as camadas da Web, de negócios e de dados do aplicativo Web são separados logicamente da sub-rede de gerenciamento, em que são executadas tarefas relacionadas à segurança do sistema de informações.*

A arquitetura de referência também implementa pontos de controle de acesso gerenciados para acesso remoto ao sistema de informações [controle NIST SP 800-53 AC-17 (3)]. Um balanceador de carga para a Internet é implantado para distribuir o tráfego da Internet para o aplicativo Web e a sub-rede de gerenciamento inclui um jumpbox ou host bastião, por meio do qual é controlado todo o acesso remoto relacionadas ao gerenciamento para o sistema. Grupos de segurança de rede restringem o tráfego na rede virtual, garantindo que o tráfego externo seja roteado somente para os recursos voltados para o público.

> Instrução de implementação de controle de exemplo AC-17 (3): *o acesso remoto a componentes controlados pelo cliente do sistema de informações no Azure é restrito a dois pontos de controle de acesso de rede gerenciado. 1) O tráfego de Internet designado para o aplicativo Web é gerenciado por meio de um balanceador de carga para a Internet, que distribui o tráfego para os recursos da camada da Web. 2) o acesso remoto ao gerenciamento é gerenciado por meio de um host bastião em uma sub-rede segregada no ambiente. O grupo de segurança de rede aplicado à sub-rede de gerenciamento, no qual o jumpbox reside, permite conexões apenas de endereços IP públicos de lista branca e está restrito somente ao tráfego da área de trabalho remota.*

Grupos de segurança de rede permitem controle total das comunicações entre sistemas e host externo e recursos do Azure, bem como entre sub-redes internas e hosts, separando os componentes do sistema de informações que são designados como publicamente acessíveis e aqueles que não o são. Além das soluções de arquitetura de referência acima, o Azure habilita a implantação de dispositivos virtuais, como firewall e as soluções IDS/IPS que, quando usados em conjunto com rotas definidas pelo usuário, protegem ainda mais e gerenciam as conexões entre os recursos do Azure e redes externas e sistemas de informações [controle NIST SP 800-53 SC-7].

> Instrução de implementação de controle de exemplo SC-7: *recursos controlados pelo cliente no Azure são protegidos por meio de vários mecanismos de proteção de limite implantados. Grupos de segurança de rede são definidos e aplicados a sub-redes que restringem o tráfego no limite do sistema de informações e na rede virtual. Grupos de segurança de rede incluem regras de ACL (lista de controle de acesso) que controlam o tráfego entre sub-redes e máquinas virtuais. Balanceadores de carga são implantados para distribuir o tráfego da Internet para recursos específicos. Sub-redes garantem que os componentes do sistema de informações publicamente acessíveis sejam separados logicamente de recursos não públicos.*

Conjuntos de regras para grupos de segurança de rede habilitam restrições para protocolos e portas de rede específicas, o que é um componente fundamental para assegurar que os sistemas de informações sejam implementados de maneira a fornecer somente recursos essenciais [controle NIST SP 800-53 CM-7].

> Instrução de implementação de controle de exemplo CM-7: *os grupos de segurança de rede aplicados a sub-redes e máquinas virtuais com o ambiente do Azure controlado pelo cliente são implementados usando uma abordagem deny-all, permit-by-exception. Isso garante que somente o tráfego de rede em protocolos e portas explicitamente aprovados seja permitido, dando suporte ao conceito de menor funcionalidade.*

*Recursos e ferramentas adicionais*

A documentação da arquitetura de referência acima está disponível no [site de documentação](https://docs.microsoft.com/azure/guidance/guidance-compute-n-tier-vm) do Azure. Modelos do Azure Resource Manager para implantar a arquitetura de referência estão incluídos na mesma página. O site de documentação do Azure também contém informações detalhadas sobre [grupos de segurança de rede](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) e [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

### <a name="storage"></a>Armazenamento

Os dados armazenados no armazenamento do Azure são replicados para garantir a alta disponibilidade. Várias opções de replicação estão disponíveis. GRS (armazenamento com redundância geográfica) e RA-GRS (armazenamento com redundância geográfica de acesso de leitura) garantem que os dados sejam replicados para uma região secundária. Regiões primárias e secundárias são combinadas para garantir a distância necessária entre data centers e assegurar a disponibilidade em caso de desastre ou interrupção em toda a área [controle NIST SP 800-53 CP-9]. Para o armazenamento de alta disponibilidade com redundância geográfica, selecione GRS (armazenamento com redundância geográfica) ou RA-GRS (armazenamento com redundância geográfica de acesso de leitura) ao criar uma nova conta de armazenamento. 

> Instrução de implementação de controle de exemplo CP-9: *todas as contas de armazenamento controladas pelo cliente no Azure implementam o armazenamento com redundância geográfica, garantindo que seis cópias de todos os dados sejam mantidas em nós separados em dois data centers.*

A SSE (Criptografia do Serviço de Armazenamento) do Azure protege dados em repouso em contas de armazenamento do Azure [controles NIST SP 800-53 SC-28, SC-28 (1)]. Quando habilitada, o Azure criptografa automaticamente os dados antes de persistir no armazenamento. Os dados são criptografados usando a criptografia AES de 256 bits. O SSE dá suporte a criptografia de blobs de bloco, blobs de acréscimo e blobs de páginas.

> Instrução de implementação de controle de exemplo SC 28-28-SC(1): *a confidencialidade e a integridade de todos os blobs de armazenamento com o ambiente controlado pelo cliente do Azure são protegidas por meio do uso do Azure SSE, que usa a criptografia AES de 256 bits para todos os dados em repouso.*

*Recursos e ferramentas adicionais*

O site de documentação do Azure tem informações detalhadas sobre [criptografia do serviço de armazenamento](https://docs.microsoft.com/azure/storage/storage-service-encryption) e [replicação de armazenamento](https://docs.microsoft.com/azure/storage/storage-redundancy). 

### <a name="azure-active-directory"></a>Azure Active Directory

O Azure Active Directory oferece recursos de identidade e acesso para sistemas de informações em execução no Microsoft Azure. Com o uso de serviços de diretório, grupos de segurança e política de grupo, os clientes podem ajudar a controlar as políticas de acesso e segurança das máquinas que fazem uso do Azure Active Directory. Contas e grupos de segurança podem ser usados para ajudar a gerenciar o acesso ao sistema de informações. A política de grupo pode ajudar a garantir que os requisitos de configuração sejam atendidos.

Os serviços de diretório e grupos de segurança do Azure Active Directory podem ajudar a implementar esquemas RBAC (controle de acesso baseado em função) e controlar o acesso ao sistema de informações. Isso pode incluir implementações de controle de segurança para:

- Gerenciamento de conta [controle NIST SP 800-53 AC-2]

  > Instrução de implementação de controle de exemplo AC-2: *o Azure Active Directory é usado para gerenciar o acesso implementando o controle de acesso baseado em função com o uso de grupos do Active Directory. Há requisitos estabelecidos para gerenciamento de contas e acesso a contas de domínios com suporte no AAD. O acesso a servidores membros do domínio tem suporte apenas em grupos de segurança. Cada grupo tem um proprietário principal e um secundário. Esses proprietários são responsáveis por manter a precisão da associação de grupo, permissão e descrição.*

  > Instrução de implementação de controle de exemplo AC-2.c: *quando um usuário solicita acesso a qualquer grupo de segurança, a solicitação deve ser aprovada pelo proprietário do grupo com base em critérios definidos para a associação. Condições de conta são determinadas por proprietários do grupo de segurança.*

  > Instrução de implementação de controle de exemplo AC-2.f: *o Azure Active Directory é usado para gerenciar o controle de acesso a sistemas de informações. Os administradores de contas criam, habilitam, modificam, desabilitam ou removem contas do sistema de informações conforme necessário, seguindo a política de gerenciamento de conta.*

  > Instrução de implementação de controle de exemplo AC-2 (1): *o Azure Active Directory é usado para gerenciar o acesso implementando o controle de acesso baseado em função com o uso de grupos do Active Directory. Há requisitos estabelecidos para gerenciamento de contas e acesso a contas de domínios com suporte no AAD. O acesso a servidores membros do domínio tem suporte apenas em grupos de segurança. Cada grupo tem um proprietário principal e um secundário. Esses proprietários são responsáveis por manter a precisão da associação de grupo, permissão e descrição.*

  > Instrução de implementação de controle de exemplo AC-2 (2): *o Azure Active Directory é usado para gerenciar o controle de acesso a sistemas de informações. Os administradores de contas criam contas temporárias de acordo com a política de gerenciamento de conta. Essas contas temporárias precisam ser definidas para expirar conforme os requisitos da política.*

  > Instrução de implementação de controle de exemplo AC-2 (3): *o Azure Active Directory gerencia o controle de acesso a sistemas de informações. Os administradores de contas criam contas temporárias de acordo com a política de gerenciamento de conta. Essas contas temporárias precisam ser definidas para expirar conforme os requisitos da política.*

  >Instrução de implementação de controle de exemplo AC-2 (7): *o Azure Active Directory gerencia o acesso de controle a sistemas de informações de acordo com um esquema de acesso baseado em função, que organiza os privilégios do sistema de informações em funções que são atribuídas a grupos de segurança. Administradores de Grupos de Segurança são responsáveis por conceder acesso a usuários para atribuir usuários ao grupo de segurança correto. São atribuídas permissões a cada grupo de segurança com o acesso mínimo apropriado para realizar adequadamente suas tarefas.*

  > Instrução de implementação de controle de exemplo AC-2 (11): *o Azure Active Directory gerencia o acesso de controle a sistemas de informações no Azure. Restrições de uso requeridas pelas políticas de segurança são definidas e aplicadas no Azure Active Directory por Administradores de Contas.*

- Imposição de Acesso [controle NIST SP 800-53 AC-3]

  > Instrução de implementação de controle de exemplo AC-3: *o Azure Active Directory impõe autorizações aprovadas para o ambiente do Cliente usando o controle de acesso baseado em função. O acesso a grupos de segurança do Azure Active Directory é gerenciado por Administradores do Grupo de Segurança. Os usuários são colocados em grupos de segurança apropriadas de acordo com suas funções, usando os princípios de menor privilégio.*

- Privilégio mínimo [controle NIST SP 800-53 AC-6]

  > Instrução de implementação de controle de exemplo AC-6 (10): *o Azure Active Directory impõe autorizações aprovadas para o ambiente do Cliente usando o controle de acesso baseado em função. O acesso a grupos de segurança do Azure Active Directory é gerenciado por Administradores do Grupo de Segurança. Usuários não privilegiados não recebem acesso a grupos de segurança que permitem acessar funções privilegiadas, incluindo quaisquer permissões que permitam desabilitar, contornar ou alterar proteções de segurança.*

- Acesso remoto [controle NIST SP 800-53 AC-17]

  > Instrução de implementação de controle de exemplo AC-17 (1): *o Azure Active Directory é usado para monitorar e controlar todo o acesso remoto. O Azure Active Directory inclui relatórios de auditoria, atividade e segurança para seu diretório.*

- Proteção de Informações de Auditoria [controle NIST SP 800-53 AU-9]

  > Instrução de implementação de controle de exemplo AU-9: *controles de acesso totalmente gerenciados são usados para proteger informações e ferramentas de auditoria contra acesso, modificação e exclusão não autorizados. O Azure Active Directory impõe o acesso lógico aprovado aos usuários usando uma abordagem em camadas de autorizações de acesso e controles lógicos com políticas do Active Directory e associações de grupo baseadas em funções. A capacidade de exibir informações de auditoria e usar as ferramentas de auditoria é limitada aos usuários que requerem essas permissões.*

  > Instrução de implementação de controle de exemplo AU-9 (4): *o Azure Active Directory restringe o gerenciamento da funcionalidade de auditoria aos membros dos grupos de segurança apropriados. Apenas funcionários com uma necessidade específica para acessar a funcionalidade de auditoria de gerenciamento recebem essas permissões.*

- Restrições de Acesso para Alteração [controle NIST SP 800-53 CM-5]

  > Instrução de implementação de controle de exemplo CM-5: *o RBAC imposto pelo Azure Active Directory é usado para definir, documentar, aprovar e impor restrições de acesso lógico associadas a alterações. Todas as contas criadas no sistema são baseados em função. Os funcionários solicitam acesso a Administradores de Conta e, se aprovado, são colocados em grupos de segurança apropriados, de acordo com suas funções. O acesso ao ambiente de produção só é permitido para membros de grupos de segurança específicos depois da aprovação.*

  > Instrução de implementação de controle de exemplo CM-5 (1): *o Azure Active Directory impõe restrições de acesso lógico por meio de associações de grupo de segurança. Isso exige que os proprietários do grupo de segurança concedam acesso a um grupo de segurança específico.*

- Identificação de Usuário e Autenticação [controle NIST SP 800-53 IA-2]

  > Instrução de implementação de controle de exemplo IA-2: *os funcionários que acessam o ambiente de informações do sistema são identificados exclusivamente por seu nome de usuário e autenticados usando o Azure Active Directory. A autenticação do Azure Active Directory é necessária para acessar o sistema de informações.*

  > Instrução de implementação de controle de exemplo IA-2 (8): *o acesso ao ambiente de produção é protegido contra ataques de repetição pela funcionalidade interna Kerberos do Azure Active Directory. Na autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, como uma lista de IP criptografada, carimbos de data/hora do cliente e vida útil do tíquete. Se um pacote é repetido, o carimbo de data/hora é verificado. Se o carimbo de data/hora for anterior ou igual a um autenticador anterior, o pacote será rejeitado.*

  > Instrução de implementação de controle de exemplo IA-2 (9): *todas as contas são protegidas contra ataques de repetição pela funcionalidade interna Kerberos do Azure Active Directory. Na autenticação Kerberos, o autenticador enviado pelo cliente contém dados adicionais, como uma lista de IP criptografada, carimbos de data/hora do cliente e vida útil do tíquete. Se um pacote é repetido, o carimbo de data/hora é verificado. Se o carimbo de data/hora for anterior ou igual a um autenticador anterior, o pacote será rejeitado.*

- Gerenciamento de Identificador [controle NIST SP 800-53 IA-4]

  > Instrução de implementação de controle de exemplo IA-4.b: *identificadores de conta do Azure Active Directory são usados para identificar usuários. Esses identificadores exclusivos não são reutilizados.*

  > Instrução de implementação de controle de exemplo IA-4.c: *o Azure Active Directory é o repositório de contas central usado para fornecer acesso ao ambiente de serviço. Quando contas são criadas no Azure Active Directory, o identificador exclusivo do usuário é atribuído à pessoa.*

  > Instrução de implementação de controle de exemplo IA-4.d: *identificadores de usuário exclusivos nunca são reutilizados. Isso é imposto pelo Azure Active Directory.*

  > IA-4.e instrução de implementação de controle de exemplo: *todas as contas no Azure Active Directory são configuradas para ser desabilitadas automaticamente após 35 dias de inatividade.*

  > Instrução de implementação de controle de exemplo IA-4 (4): *o Azure Active Directory denota contratados e fornecedores usando convenções de nomenclatura aplicadas às credenciais exclusivas do Azure Active Directory.*

- Gerenciamento de Autenticador [controle NIST SP 800-53 IA-5]

  > Instrução de implementação de controle de exemplo IA-5.b: *o Azure Active Directory atribui uma identificação exclusiva e uma senha temporária aleatória que atende aos requisitos da política no momento da criação da conta inicial. O Azure Active Directory mantém a identificação exclusiva associada à conta durante a vida útil da conta. A identificação de conta nunca é repetida no Azure Active Directory*

  > Instrução de implementação de controle de exemplo IA-5.c: *o Azure Active Directory garante que as senhas emitidas atendam aos requisitos de política de complexidade de senha.*

  > Instrução de implementação de controle de exemplo IA-5.d: *os procedimentos de distribuição de autenticador inicial são tratados por Administradores de Contas. Modificações de acesso às contas de usuário passam pelo processo de gerenciamento de conta conforme definido em AC-2. Se um autenticador é perdido ou comprometido, os administradores de contas seguem os processos definidos para redefinir, emitir novamente ou revogar o autenticador, conforme necessário.*

  > Instrução de implementação de controle de exemplo IA-5 (1).c: *o Azure Active Directory é usado para garantir que todas as senhas sejam protegidas por criptografia enquanto armazenados e transmitidas. As senhas armazenadas pelo Azure Active Directory são automaticamente transformadas em hash como parte da funcionalidade do Azure Active Directory.*

  > Instrução de implementação de controle de exemplo IA-5 (1).f: *o Azure Active Directory é usado para gerenciar o controle de acesso ao sistema de informações. Sempre que uma conta é criada inicialmente ou uma senha temporária é gerada, o Azure Active Directory é utilizado para exigir que o usuário altere a senha no próximo logon.*

  > Instrução de implementação de controle de exemplo IA-5 (4): *o Azure Active Directory é a ferramenta automatizada adotada para determinar se há autenticadores de senha suficientemente fortes para satisfazer às restrições de comprimento, complexidade, rotação e tempo de vida de senha estabelecidas em IA-5 (1). O Azure Active Directory garante que a intensidade de autenticador de senha no momento da criação atenda a esses padrões.*

A configuração de política de grupo do Azure Active Directory pode ser usada para implantar políticas de segurança de cliente que atendam a requisitos de segurança específicos. Máquinas virtuais cliente do Azure podem ter essas políticas aplicadas pelo Azure Active Directory.
As configurações de política de grupo do Azure Active Directory podem incluir implementações de controle de segurança para:

- Privilégio mínimo [controle NIST SP 800-53 AC-6]

  > Instrução de implementação de controle de exemplo AC-6 (9): *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo auditoria para a execução de funções privilegiadas.*

- Tentativas de logon malsucedidas [controle NIST SP 800-53 AC-7]

  > Instrução de implementação de controle de exemplo AC-7: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo o início de um bloqueio de conta após tentativas de logon com falhas consecutivas.*

- Controle de Sessões Simultâneas [controle NIST SP 800-53 AC-10], bloqueio da sessão [controle NIST SP 800-53 AC-11], término de sessão [controle NIST SP 800-53 AC-12]

  > Instrução de implementação de controle de exemplo AC-10: *um objeto de GPO (política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo a restrição dos usuários a uma sessão de serviços de área de trabalho remota única que atenda aos requisitos FedRAMP e DoD.*

  > Instrução de implementação de controle de exemplo AC-11.a: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo o início de um bloqueio de sessão após 900 segundos (15 minutos) de inatividade que seja mantido até que o usuário seja autenticado novamente.*

  > Instrução de implementação de controle de exemplo AC-12: *o Windows interrompe automaticamente sessões da área de trabalho remota ao receber uma solicitação de logoff do usuário cliente. Além disso, um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo o encerramento de uma sessão de área de trabalho remota após 900 segundos (15 minutos) de inatividade.*

- Gerenciamento de Autenticador [controle NIST SP 800-53 IA-5]

  > Instrução de implementação de controle de exemplo IA-5.f: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo restrições sobre senhas de contas locais que impõem restrições mínimas (um dia) e máximas (60 dias), condições de reutilização (24 senhas), comprimento (14 caracteres) e requisitos de complexidade, que atendem aos requisitos FedRAMP.*

  > Instrução de implementação de controle de exemplo IA-5.g: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender às necessidades da organização, incluindo restrições sobre senhas de contas locais que impõem restrições de tempo de vida máximo (60 dias), que atendem aos requisitos FedRAMP.*

  > Instrução de implementação de controle de exemplo IA-5 (1).a: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo requisitos de complexidade, que atendem aos requisitos FedRAMP.*

  > Instrução de implementação de controle de exemplo IA-5 (1).b: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo requisitos de complexidade, que atendem aos requisitos FedRAMP.*

  > Instrução de implementação de controle de exemplo IA-5 (1).d: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender às necessidades da organização, incluindo restrições sobre senhas de contas locais que impõem restrições de tempo de vida mínimo (1 dia) e máximo (60 dias), que atendem aos requisitos FedRAMP."

  > IA-5 (1).e Instrução de implementação de controle de exemplo: *um GPO (objeto de política de grupo) controlado por configuração é aplicado a todas as máquinas Windows controladas pelo cliente no Azure. O objeto de política de grupo é personalizado para atender aos requisitos da organização, incluindo restrições para condições de reutilização (24 senhas), que atendem aos requisitos FedRAMP.*

*Recursos e ferramentas adicionais*

Documentação, ferramentas, modelos e outros recursos estão disponíveis para orientar a implantação segura de recursos e serviços do Azure. Para obter uma introdução ao Azure Active Directory, acesse [Microsoft Azure Docs](https://docs.microsoft.com/azure/active-directory/).

### <a name="key-vault"></a>Cofre da Chave

O Cofre da Chave do Azure oferece proteção para chaves criptográficas e segredos usados por aplicativos e serviços em nuvem. Com o uso do Azure Key Vault, os clientes podem criar, gerenciar e proteger chaves e segredos. Contêineres seguros (cofres) podem ser usados para armazenar e gerenciar chaves criptográficas e segredos com segurança. O Azure Key Vault pode ser usado para gerar chaves de criptografia usando HSMs que têm FIPS 140-2 Nível 2 validado.

Os contêineres do Azure Key Vault podem ajudar a armazenar chaves de criptografia com segurança e alta disponibilidade. Isso pode incluir implementações de controle de segurança para:

-   Proteção de autenticadores [controle NIST SP 800-53 IA-5 (7)]

  > Instrução de implementação de controle de exemplo IA-5 (7): *o uso de autenticadores estáticos não criptografados incorporados a aplicativos, scripts de acesso ou teclas de função é explicitamente proibido. Qualquer script ou aplicativo que usa um autenticador faz uma chamada para um contêiner do Azure Key Vault antes de cada uso. O acesso aos contêineres do Azure Key Vault é auditado, o que permite a detecção de violações dessa proibição se uma conta de serviço é usada para acessar um sistema sem uma chamada correspondente para o contêiner do Azure Key Vault.*

- Estabelecimento de Chave de Criptografia e gerenciamento [controle NIST SP 800-53 SC-12 (1)]

  > Instrução de implementação de controle de exemplo SC-12 (1): *o Azure Key Vault é usado para armazenar chaves criptográficas e segredos. Esse serviço rastreia e monitora o acesso a segredos. Esse serviço é usado para garantir que segredos não sejam perdidos.*

O Azure Key Vault pode ser usado para criar chaves de criptografia usando HSMs que atendem ao FIPS 140-2 Nível 2 validado. As configurações de política de grupo do Azure Active Directory podem incluir implementações de controle de segurança para:

- Estabelecimento de Chave de Criptografia e Gerenciamento [controle NIST SP 800-53 SC-12 (2)]

  > Instrução de implementação de controle de exemplo SC-12 (2): *o Azure Key Vault é usado para produzir, controlar e distribuir chaves criptográficas. Essas chaves são geradas usando HSMs com FIPS 140-2 Nível 2 validado. Essas chaves são armazenadas e gerenciadas em contêineres criptografados com segurança no Azure Key Vault.*

*Recursos e ferramentas adicionais*

Documentação, ferramentas, modelos e outros recursos estão disponíveis para orientar a implantação segura de recursos e serviços do Azure. Para obter uma introdução ao Azure Key Vault, acesse [Microsoft Azure Docs](https://docs.microsoft.com/azure/key-vault/).

### <a name="operations-management-suite"></a>Operations Management Suite

O OMS (Microsoft Operations Management Suite) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger a infraestrutura local e de nuvem. A Segurança e Conformidade ajuda a identificar, avaliar e atenuar os riscos de segurança à infraestrutura. Esses recursos do OMS são implementados por meio de várias soluções, incluindo a Log Analytics, que analisa dados de log e monitora configurações de segurança.

Serviços de Auditoria e Segurança habilitados para OMS podem ajudar a implementar o monitoramento de contas e o registro em log. Isso pode incluir implementações de controle de segurança para:

- Gerenciamento de Conta [controle NIST SP 800-53 AC-2]

  > Instrução de implementação de controle de exemplo AC-2.g: *o OMS (Operation Management Suite) da Microsoft é usado para monitorar o uso de contas do sistema. O OMS cria logs de auditoria para contas do sistema que podem ser examinadas com análises e alertar com base em um conjunto definido de critérios do OMS.*

  > Instrução de implementação de controle de exemplo AC-2 (4): *o OMS (Operation Management Suite) da Microsoft é usado para gerar logs de auditoria para funções de gerenciamento de conta, como ações de criação, modificação, habilitação, desabilitação e remoção de conta. O OMS é usada para alertar o proprietário do sistema se qualquer uma das condições acima for executada.*

  > Instrução de implementação de controle de exemplo AC-2 (12): *o OMS (Operation Management Suite) da Microsoft é usado para monitorar o uso atípico de contas do sistema. O OMS cria logs de auditoria para contas do sistema que podem ser examinadas com análises e alertar os funcionários apropriados com base em um conjunto definido de critérios do OMS.*

- Privilégio mínimo [controle NIST SP 800-53 AC-6]

  > Instrução de implementação de controle de exemplo AC-6 (9): *o OMS (Operation Management Suite) da Microsoft é usado para monitorar a execução de funções privilegiadas. O OMS cria logs de auditoria para uma lista de funções especificadas que podem ser examinados com análises e alertar com base em um conjunto definido de critérios do OMS.*

 - Acesso remoto [controle NIST SP 800-53 AC-17]

  > Instrução de implementação de controle de exemplo AC-17 (1): *o OMS (Operation Management Suite) da Microsoft é usado para monitorar e controlar o acesso remoto. O OMS inclui relatórios de segurança, atividade e auditoria para seu diretório.*

- Eventos de Auditoria [controle NIST SP 800-53 AU-2]

  > Instrução de implementação de controle de exemplo AU-2: *o OMS (Operation Management Suite) da Microsoft é usado para realizar auditoria: eventos de logon de conta com e sem êxito, eventos de gerenciamento de conta, acesso a objetos, alteração de política, funções de privilégio, rastreamento de processos e eventos do sistema. Para aplicativos Web: todas as atividades do administrador, verificações de autenticação, verificações de autorização, exclusões de dados, acesso a dados, alterações de dados e alterações de permissão. Assim como qualquer outro conjunto de eventos definido pelo cliente.*

- Conteúdo de Registros de Auditoria [controle NIST SP 800-53 AU-3]

  > Instrução de implementação de controle de exemplo AU-3 (2): *o OMS (Operation Management Suite) da Microsoft é usado como gerenciamento centralizado e configuração para todos os registros gerados por equipamentos rede, armazenamento e computação.*

- Capacidade de Armazenamento de Auditoria [controle NIST SP 800-53 AU-4]

  > Instrução de implementação de controle de exemplo AU-4: *o OMS (Operation Management Suite) da Microsoft é usado para gerenciamento centralizado e configuração de todos os registros de auditoria gerados por equipamentos de rede, armazenamento e computação. A ferramenta de gerenciamento centralizado é usada para configurar a capacidade de armazenamento de registros de auditoria.*

- Resposta a Falhas de Processamento de Auditoria [controle NIST SP 800-53 AU-5]

  > Instrução de implementação de controle de exemplo AU-5: *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria para alertar profissionais definidos pela organização sobre falhas no processamento de auditoria.*

  > Instrução de implementação de controle AU-5 (1): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria para alertar profissionais definidos pela organização no período de tempo definido pela organização de que o armazenamento de registros de auditoria atingiu uma porcentagem definida pela organização do volume máximo do repositório.*

  > Instrução de implementação de controle AU-5 (2): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria para alertar profissionais definidos pela organização em tempo real quando eventos auditáveis definidos na política da organização falham.*

- Revisão de Auditoria, análise e Relatórios [controle NIST SP 800-53 AU-6]

  > Instrução de implementação de controle de exemplo AU-6 (3): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria e a solução de Log Analytics para analisar diferentes repositórios de log e promover a conscientização situacional em toda a organização. A ferramenta de análise é usada para gerar relatórios sobre os logs de auditoria e promover a conscientização situacional.*

  > Instrução de implementação de controle de exemplo AU-6 (4): *o OMS (Operation Management Suite) da Microsoft é usado para gerenciamento centralizado e configuração de todos os registros de auditoria. A ferramenta de gerenciamento centralizado permite que registros de auditoria de todas as fontes sejam examinados e analisados.*

  > Instrução de implementação de controle de exemplo AU-6 (5): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria e a solução de Log Analytics para analisar diferentes dados relacionados à segurança gerados pela verificação de vulnerabilidade, monitoramento do sistema de informações e dados relacionados a desempenho. A análise desses diferentes recursos fornece a capacidade aprimorada de identificar atividades suspeitas.*

- Redução de Auditoria e Geração de Relatórios [controle NIST SP 800-53 AU-7]

  > Instrução de implementação de controle de exemplo AU-7: *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria e a solução de Log Analytics para gerar relatórios sob demanda legíveis para humanos que permitem investigações posteriores de incidentes de segurança. O uso da ferramenta de Log Analytics da Microsoft não altera de forma permanente ou irreversível o conteúdo original de registro de auditoria ou a ordem de tempo.*

  > Instrução de implementação de controle de exemplo AU-7 (1): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria para consultar eventos auditáveis definidos pela organização.*

- Proteção de Informações de Auditoria [controle NIST SP 800-53 AU-9]

  > Instrução de implementação de controle de exemplo AU-9: *o OMS (Operation Management Suite) da Microsoft é usado com grupos de segurança para gerenciar o acesso a informações e ferramentas para impedir o acesso não autorizado, a modificação e a exclusão de registros de auditoria. A capacidade de exibir informações de auditoria e usar as ferramentas de auditoria é limitada aos usuários que requerem essas permissões.*

  > Instrução de implementação de controle de exemplo AU-9 (2): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria, bem como o Backup do Azure para fazer backup de logs de auditoria no Azure, que então replica os dados para fornecer disponibilidade e confiabilidade de dados. O Backup do Azure fornece um local seguro para que os logs de auditoria sejam armazenados em um sistema diferente daquele que está sendo auditado.* 

  > Instrução de implementação de controle de exemplo AU-9 (4): *o OMS (Operation Management Suite) da Microsoft é usado com grupos de segurança para gerenciar o acesso a informações e ferramentas de auditoria. Apenas funcionários com uma necessidade específica para acessar a funcionalidade de auditoria de gerenciamento recebem essas permissões.*

- Retenção de Registros de Auditoria [controle NIST SP 800-53 AU-11]

  > Instrução de implementação de controle de exemplo AU-11: *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria para configurar a retenção de logs de auditoria. A retenção de logs de auditoria é configurada para reter os dados de auditoria pelo menos durante 90 dias.*

- Geração de Auditoria [controle NIST SP 800-53 AU-12]

  > Instrução de implementação de controle de exemplo AU-12: *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria para coletar eventos auditáveis definidos no AU-02 de componentes do sistema de informações. A solução de Segurança e Auditoria do OMS é usada pelos funcionários definidos pela organização para definir quais eventos auditáveis são coletados de dispositivos específicos.*

  > Instrução de implementação de controle de exemplo AU-12 (1): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria e a solução de Log Analytics para compilar registros de auditoria de componentes do sistema que podem ser classificados por carimbos de data/hora para criar uma trilha de auditoria de todo o sistema.*

- Restrições de Acesso para Alteração [controle NIST SP 800-53 CM-5]

  > Instrução de implementação de controle de exemplo CM-5 (1): *o OMS (Operation Management Suite) da Microsoft é usado com solução de Segurança e Auditoria e o Active Directory para aplicar restrições de acesso lógicas por meio de associações de grupo de segurança. As ações executadas por esses grupos de segurança são auditadas pelo OMS.*

- Inventário de Componentes do Sistema de Informações [controle NIST SP 800-53 CM-8]

  > Instrução de implementação de controle de exemplo CM-8 (3): *o OMS (Operation Management Suite) da Microsoft é usado com a solução de Segurança e Auditoria e a solução Antimalware para detectar a presença de software não autorizado. Após a detecção, o OMS desabilita o componente infectado e envia um alerta aos funcionários definidos pela empresa.*

Documentação, ferramentas, modelos e outros recursos estão disponíveis para orientar a implantação segura de recursos e serviços do Azure. Para obter uma introdução ao Operation Management Suite, acesse [Microsoft Azure Docs](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview/).

### <a name="additional-implementation-guidance"></a>Diretrizes de implementação adicional

Além dos principais serviços do Azure descritos acima, vários recursos internos podem ajudar as organizações a atender aos requisitos de conformidade de controle de segurança. Todos os recursos do Azure são organizados em grupos de recursos. Os ativos podem ser organizados por meio da aplicação de marcas, que são pares de chave-valor que podem ser selecionados pelo cliente para identificar recursos por categoria ou qualquer outra propriedade. Os grupos de recursos do Azure garantem identificação completa e rastreamento de todos os recursos do cliente implantados no Azure [controle NIST SP 800-53 controle CM-8]. 

> Instrução de implementação de controle de exemplo CM-8: *todos os recursos controlados pelo cliente implantados no grupo de recursos "Recursos do cliente". No portal do Azure, todos os ativos implantados são identificados na folha do grupo de recursos, garantindo um inventário preciso e atual. Marcas são aplicadas aos recursos, conforme necessário, para atender aos requisitos de rastreamento e emissão de relatórios.*

Modelos do Azure Resource Manager, permitem que os clientes definam a configuração de recursos em sua implantação do Azure. Os modelos do Resource Manager são baseados em JSON e usam sintaxe declarativa para recursos de documentos, parâmetros de configuração e outros aspectos de um ambiente do Azure. Os modelos do Resource Manager são gerados automaticamente quando soluções do Azure são implantadas no portal do Azure e também podem ser criadas e editados manualmente para atender a necessidades específicas do cliente. Esses modelos do Resource Manager podem servir como uma representação da configuração de linha de base para um sistema de informações [controle NIST SP 800-53 CM-2]. Os modelos do Resource Manager existentes, como os blocos de construção mencionados acima, podem ser personalizados para atender às necessidades de qualquer implantação.

> Instrução de implementação de controle de exemplo CM-2: *como um componente de configuração da linha de base do sistema de informações, os modelos do Azure Resource Manager são mantidos sob o controle de configuração, representando os recursos implantados controlados pelo cliente e a configuração dos componentes do sistema de informações no Azure. Esses modelos capturam recursos implantados, incluindo máquinas virtuais, recursos de armazenamento e de rede (incluindo configurações de recursos de rede que controlam o tráfego de rede de entrada, de saída e no ambiente do Azure do cliente.*

## <a name="next-steps"></a>Próximas etapas

Para fazer consultas relacionadas a Especificações Técnicas do Azure, FedRAMP, DoD ou processos Agency ATO ou obter assistência sobre conformidade ou para fornecer comentários sobre Especificações Técnicas, envie um email a [AzureBlueprint@microsoft.com](mailto:AzureBlueprint@microsoft.com).

[Central de Confiabilidade da Microsoft](https://www.microsoft.com/trustcenter/Compliance/default.aspx)

[Blog do Microsoft Azure Governamental](https://blogs.msdn.microsoft.com/azuregov/)



<!--HONumber=Jan17_HO4-->


