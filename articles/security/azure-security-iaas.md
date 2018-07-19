---
title: Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure | Microsoft Docs
description: " A migração das cargas de trabalho para o Azure IaaS traz oportunidades para reavaliar nosso designs "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: barclayn
ms.openlocfilehash: 37620e70377e3f1fbeeeb73aaa294c5f54cf5b3d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723768"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure

Na maioria dos cenários de IaaS (Infraestrutura como Serviço), as [máquinas virtuais (VMs) do Azure](https://docs.microsoft.com/azure/virtual-machines/) são a carga de trabalho principal para as organizações que usam a computação em nuvem. Esse fato é especialmente evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx), nos quais as organizações desejam migrar cargas de trabalho para a nuvem de forma lenta. Nesses cenários, siga o [considerações gerais de segurança para IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplicar práticas recomendadas de segurança para todas as suas VMs.

Sua responsabilidade pela segurança é baseada no tipo de serviço de nuvem. A tabela a seguir resume a proporção de responsabilidade assumida pela Microsoft e por você:

![Áreas de Responsabilidade](./media/azure-security-iaas/sec-cloudstack-new.png)

Os requisitos de segurança variam, dependendo de vários fatores, incluindo diferentes tipos de cargas de trabalho. Nenhuma dessas práticas recomendadas pode, por si só, proteger seus sistemas. Como tudo em segurança, você precisa escolher as opções apropriadas e ver como as soluções podem complementar-se, preenchendo as lacunas.

Este artigo aborda várias melhores práticas de segurança de VM, cada uma obtida com as experiências de clientes e as próprias experiências diretas da Microsoft com VMs.

As recomendações baseiam-se um consenso de opinião, e trabalhar com recursos da plataforma Windows Azure atuais e conjuntos de recursos. Como as opiniões e as tecnologias podem mudar com o tempo, este artigo será atualizado para refletir essas alterações.

## <a name="use-privileged-access-workstations"></a>Usar Estações de Trabalho com Acesso Privilegiado

As organizações geralmente são vítimas de ataques cibernéticos por causa dos administradores que executam ações enquanto usam contas com direitos elevados. Embora isso possa não ser o resultado de atividades mal-intencionadas, isso ocorre porque a configuração e os processos existentes permitem. A maioria desses usuários entende o risco dessas ações na teoria, mas ainda optam por executá-las.

Ações como verificação de email e navegação na Internet parecem seguras. Porém, elas podem expor contas com privilégios elevados a serem comprometidas por atores mal-intencionados. Atividades de navegação, emails especialmente criados ou outras técnicas podem ser usados para obter acesso à sua empresa. É altamente recomendável o uso de SAWs (estações de trabalho de gerenciamento seguro) para realizar todas as tarefas de administração do Azure. As SAWs são uma maneira de reduzir a exposição a comprometimentos acidentais.

As Estações de Trabalho com Acesso Privilegiado (PAWs) fornecem um sistema operacional dedicado para as tarefas confidenciais protegido contra ataques da Internet e vetores de ameaça. A separação dessas contas e tarefas confidenciais dos dispositivos e das estações de trabalho de uso diário fornece uma proteção robusta. Essa separação limita o impacto de ataques de phishing, vulnerabilidades de SO e aplicativos, vários ataques de representação e ataques de roubo de credenciais. (registro de pressionamento de teclas, Pass-the-Hash e Pass-the-Ticket)

A abordagem PAW é uma extensão da prática recomendada e bem estabelecida de usar uma conta administrativa individualmente atribuída. A conta administrativa é separada de uma conta de usuário padrão. Uma PAW fornece uma estação de trabalho confiável para essas contas confidenciais.

Para obter mais informações e orientações de implementação, confira [Estações de trabalho de acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations).

## <a name="use-multi-factor-authentication"></a>Usar Autenticação Multifator

No passado, o perímetro da rede era usado para controlar o acesso aos dados corporativos. Em um mundo de nuvem e dispositivos móveis, a identidade é o plano de controle: você pode usá-la para controlar o acesso aos serviços IaaS de qualquer dispositivo. Você também usá-la para obter visibilidade e percepção de onde e como os dados estão sendo usados. Proteger a identidade digital dos usuários do Azure é a base para proteger suas assinaturas contra o roubo de identidade e outros crimes cibernéticos.

Uma das etapas mais vantajosas que podem ser tomadas para proteger uma conta é habilitar a autenticação de dois fatores. A autenticação de dois fatores é uma maneira de autenticar usando algo além de uma senha. Isso ajuda a minimizar o risco do acesso por uma pessoa que consegue obter a senha de outra pessoa.

A [Autenticação Multifator do Azure](../active-directory/authentication/multi-factor-authentication.md) ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece autenticação forte por meio de uma variedade de opções de fácil verificação: chamada telefônica, mensagem de texto ou notificação de aplicativo móvel. Os usuários escolhem o método preferido.

A maneira mais fácil de usar a Autenticação Multifator do Azure é o aplicativo móvel do Microsoft Authenticator, que pode ser usado em dispositivos móveis que executam o Windows, IOS e Android. Com a versão mais recente do Windows 10 e a integração do Active Directory local com o Azure AD (Azure Active Directory), o [Windows Hello para Empresas](../active-directory/active-directory-azureadjoin-passport-deployment.md) pode ser usado para fazer um logon único e perfeito nos recursos do Azure. Neste caso, o dispositivo do Windows 10 é usado como o segundo fator para a autenticação.

No caso de contas que gerenciam sua assinatura do Azure e de contas que podem entrar em máquinas virtuais, o uso da autenticação multifator fornece um nível muito maior de segurança do que o uso de apenas uma senha. Outras formas de autenticação de dois fatores podem funcionar muito bem, mas implantá-las pode ser complicado se ainda não estiverem em produção.

A captura de tela a seguir mostra algumas das opções disponíveis para a Autenticação Multifator do Azure:

![Opções da Autenticação Multifator](./media/azure-security-iaas/mfa-options.png)

## <a name="limit-and-constrain-administrative-access"></a>Limitar e restringir o acesso administrativo

É importante proteger as contas que podem gerenciar sua assinatura do Azure. O comprometimento de alguma dessas contas nega o valor de todas as outras etapas que você pode tomar para garantir a confidencialidade e a integridade dos dados. Como ilustrado recentemente por [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), ataques internos representam uma enorme ameaça à segurança geral de qualquer organização.

Avalie os direitos administrativos que devem ser concedidos a cada pessoa usando critérios semelhantes a estes:

- Eles estão realizando tarefas que exigem privilégios administrativos?
- Com que frequência as tarefas são realizadas?
- Existe um motivo específico para as tarefas não poderem ser realizadas por outro administrador em seu nome?

Documente todas as outras abordagens alternativas conhecidas para conceder o privilégio e por que cada uma não é aceitável.

A administração Just-In-Time impede a existência desnecessária de contas com direitos elevados nos períodos em que esses direitos não são necessários. Contas que têm direitos elevados por tempo limitado para que os administradores possam fazer seus trabalhos. Em seguida, esses direitos são removidos no final de um turno ou quando uma tarefa é concluída.

Você pode usar o [Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) para gerenciar, monitorar e controlar o acesso em sua organização. Ele ajuda você a permanecer ciente das ações que as pessoas realizam em sua organização. Ele também oferece administração just-in-time para o AD do Azure, introduzindo o conceito de administradores qualificados. Estas são as pessoas que têm contas com o potencial de receberem os direitos de administrador. Esses tipos de usuários podem passar por um processo de ativação e receber direitos de administrador por um tempo limitado.

## <a name="use-devtest-labs"></a>Usar DevTest Labs

O Azure para laboratórios e ambientes de desenvolvimento elimina os atrasos introduzidos pela aquisição de hardware. Isso permite que as organizações tenham agilidade no teste e no desenvolvimento. Por outro lado, a falta de familiaridade com o Azure ou um desejo de ajudar a agilizar sua adoção pode levar o administrador a ser excessivamente permissivo com a atribuição de direitos. Esse risco pode expor a organização a ataques internos sem querer. Alguns usuários podem receber muito mais acesso do que deveriam.

O serviço [Azure DevTest Labs](../devtest-lab/devtest-lab-overview.md) utiliza o [RBAC](../role-based-access-control/overview.md) (Controle de Acesso Baseado em Função do Azure). Com o RBAC, você pode separar as tarefas dentro de sua equipe em funções que concedem somente o nível de acesso necessário para os usuários fazerem seus trabalhos. O RBAC vem com funções predefinidas (proprietário, usuário do laboratório e colaborador). Você pode até usar essas funções para atribuir direitos a parceiros externos e simplificar muito a colaboração.

Como o DevTest Labs usa o RBAC, é possível criar outras [funções personalizadas](../lab-services/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). O DevTest Labs não apenas simplifica o gerenciamento de permissões, mas também o processo de provisionar os ambientes. Ele também ajuda a lidar com outros desafios típicos de equipes que trabalham em ambientes de desenvolvimento e teste. Ele requer certa preparação, mas a longo prazo facilitará as coisas para sua equipe.

Os principais recursos do Azure DevTest Labs incluem:

- Controle administrativo sobre as opções disponíveis para os usuários. O administrador pode gerenciar centralmente aspectos como tamanhos da VM permitidos, número máximo de VMs e quando as VMs são iniciadas e finalizadas.
- Automação da criação do ambiente de laboratório.
- Controle de custos.
- Distribuição simplificada das VMs para o trabalho colaborativo temporário.
- Autoatendimento que permite aos usuários provisionar seus laboratórios usando modelos.
- Gerenciando e limitando o consumo.

![Laboratórios de Desenvolvimento/Teste](./media/azure-security-iaas/devtestlabs.png)

Não há custos adicionais associados ao uso do DevTest Labs. A criação de laboratórios, políticas, modelos e artefatos é gratuita. Você paga apenas pelos recursos do Azure usados em seus laboratórios, como máquinas virtuais, contas de armazenamento e redes virtuais.

## <a name="control-and-limit-endpoint-access"></a>Controlar e limitar o acesso do ponto de extremidade

Hospedar laboratórios ou sistemas de produção no Azure significa que os sistemas precisam ser acessíveis pela Internet. Por padrão, uma nova máquina virtual do Windows tem a porta RDP acessível pela Internet e uma máquina virtual Linux tem a porta SSH aberta. Medidas para 'limitar os pontos de extremidade expostos' são necessárias a fim de minimizar o risco do acesso não autorizado.

As tecnologias no Azure podem ajudá-lo a limitar o acesso a esses pontos de extremidade administrativos. No Azure, você pode usar os [NSGs](../virtual-network/security-overview.md) (grupos de segurança de rede). Quando você usa o Azure Resource Manager para a implantação, os NSGs limitam o acesso de todas as redes para apenas os pontos de extremidade de gerenciamento (RDP ou SSH). Quando você pensar em NSGs, considere as ACLs do roteador. Você pode usá-las para controlar totalmente a comunicação de rede entre os vários segmentos das redes do Azure. Isso é semelhante a criar redes em redes de perímetro ou outras redes isoladas. Elas não inspecionam o tráfego, mas ajudam na segmentação da rede.

Uma maneira mais dinâmica de limitar o acesso às máquinas virtuais é usar a [administração Just-In-Time](../security-center/security-center-just-in-time.md) da Central de Segurança do Azure. A Central de segurança pode bloquear as VMs do Azure e fornece acesso quando necessário. O processo funciona com a permissão do acesso a um usuário que o solicitar depois de verificar se ele tem as permissões necessárias, com base no [RBAC](../role-based-access-control/role-assignments-portal.md) (Controle de Acesso Baseado em Função). Em seguida, a Central de Segurança do Azure criará os NSGs (Grupos de Segurança de Rede) necessários para permitir o tráfego de entrada.

### <a name="site-to-site-vpnvpn-gatewayvpn-gateway-howto-site-to-site-resource-manager-portalmd"></a>[VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Um VPN site a site estende sua rede local para a nuvem. Isso lhe dá outra oportunidade de usar NSGs, pois você também pode modificar os NSGs para não permitir o acesso de outro lugar que não seja a rede local. Em seguida, você pode exigir que a administração seja feita primeiro pela conexão com a rede do Azure por meio de VPN.

A opção VPN site a site pode ser muito atraente nos casos em que você hospeda os sistemas de produção que são muito integrados com seus recursos locais no Azure.

### <a name="point-to-sitevpn-gatewayvpn-gateway-howto-point-to-site-rm-psmd"></a>[Ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

Em situações em que você deseja gerenciar sistemas que não precisam de acesso aos recursos locais. Esses sistemas podem ser isolados em sua própria rede virtual do Azure. Os administradores podem usar VPN para o ambiente hospedado pelo Azure de suas estações de trabalho administrativas.

>[!NOTE]
>Você pode usar a opção VPN para reconfigurar as ACLs nos NSGs a fim de impedir o acesso aos pontos de extremidade de gerenciamento pela Internet.

### <a name="remote-desktop-gatewayactive-directoryauthenticationhowto-mfaserver-nps-rdgmd"></a>[Gateway de Área de Trabalho Remota](../active-directory/authentication/howto-mfaserver-nps-rdg.md)

Use o Gateway de Área de Trabalho Remota para se conectar com segurança a servidores de Área de Trabalho Remota por HTTPS, ao mesmo tempo em que aplica controles mais detalhados a essas conexões.

Os recursos que você acessaria incluem:

- Opções do administrador para limitar as conexões às solicitações de sistemas específicos.
- Autenticação com cartão inteligente ou autenticação multifator do Azure.
- Controle sobre quais sistemas alguém pode conectar por meio do gateway.
- Controle sobre o redirecionamento do dispositivo e do disco.

### <a name="vm-availability"></a>Disponibilidade da VM

Se uma VM executar aplicativos críticos que precisam ter alta disponibilidade, será altamente recomendável usar várias VMs. Para uma melhor disponibilidade, crie, no mínimo, duas VMs no [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md).

O [Azure Load Balancer](../load-balancer/load-balancer-overview.md) também exige que as VMs com balanceamento de carga pertençam ao mesmo conjunto de disponibilidade. Se essas VMs devem ser acessadas da Internet, você deve configurar um [balanceador de carga para Internet](../load-balancer/load-balancer-internet-overview.md).

## <a name="use-a-key-management-solution"></a>Uso de uma solução de gerenciamento de chaves

O gerenciamento de chaves seguro é fundamental para proteger os dados na nuvem. Com o [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md), você pode armazenar com segurança as chaves de criptografia e pequenos segredos, como senhas nos módulos de segurança do hardware (HSMs). Para garantia extra, você pode importar ou gerar chaves em HSMs.

A Microsoft processará suas chaves em HSMs FIPS 140-2 Nível 2 validados (hardware e firmware). Execute o monitoramento e a auditoria do uso das chaves com o registro em log do Azure: redirecione logs no Azure ou em seu sistema SIEM (gerenciamento de eventos e de informações de segurança) para obter mais análises e detecções de ameaças.

Qualquer pessoa com uma assinatura do Azure pode criar e usar os cofres de chaves. Embora o Key Vault beneficie os desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado pelo administrador responsável por gerenciar os serviços do Azure em uma organização.

## <a name="encrypt-virtual-disks-and-disk-storage"></a>Criptografar discos virtuais e o armazenamento de discos

O [Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) endereça a ameaça do roubo de dados ou a exposição contra o acesso não autorizado feito movendo um disco. O disco pode ser anexado a outro sistema como uma forma de ignorar os outros controles de segurança. A criptografia de disco usa o [BitLocker](https://technet.microsoft.com/library/hh831713) no Windows e o DM-Crypt no Linux para criptografar o sistema operacional e as unidades de dados. O Azure Disk Encryption se integra com o Key Vault para controlar e gerenciar as chaves de criptografia. Ele está disponível para VMs padrão e VMs com armazenamento premium.

Para saber mais, confira [Azure Disk Encryption em VMs IaaS com Windows e Linux](azure-security-disk-encryption.md).

A [Criptografia do Serviço de Armazenamento do Azure](../storage/common/storage-service-encryption.md) ajuda a proteger os dados em repouso. Ela é habilitada no nível da conta de armazenamento. Ela criptografa os dados conforme eles são gravados em nossos data centers e eles são descriptografados automaticamente quando você a acessa. Ele oferece suporte para os seguintes cenários:

- Criptografia de blobs de bloco, blobs de acréscimo e blobs de páginas
- Criptografia de VHDs arquivados e modelos colocados no Azure localmente
- Criptografia de sistema operacional subjacente e discos de dados para VMs de IaaS criadas com seus VHDs

Antes de prosseguir com a Criptografia de Armazenamento do Azure, você deve estar ciente de duas limitações:

- não está disponível nas contas de armazenamento clássicas.
- só criptografa os dados gravados após a criptografia estar habilitada.

## <a name="use-a-centralized-security-management-system"></a>Usar um sistema de gerenciamento de segurança centralizado

Os servidores precisam ser monitorados quanto à aplicação de patch, à configuração, aos eventos e às atividades que podem ser considerados problemas de segurança. Para lidar com essas preocupações, você pode usar a [Central de Segurança](https://azure.microsoft.com/services/security-center/) e a [Conformidade e Segurança do Operations Management Suite](https://azure.microsoft.com/services/security-center/). Essas duas opções vão além da configuração no sistema operacional. Eles também fornecem monitoramento da configuração da infraestrutura subjacente, como configuração de rede e o uso da solução de virtualização.

## <a name="manage-operating-systems"></a>Gerenciar sistemas operacionais

Em uma implantação IaaS, você ainda é responsável pelo gerenciamento dos sistemas que implanta, assim como por qualquer outro servidor ou estação de trabalho em seu ambiente. Patches, proteção, atribuições de direitos e qualquer outra atividade relacionadas à manutenção do sistema ainda são sua responsabilidade. Para os sistemas que são muito integrados com seus recursos locais, você pode querer usar as mesmas ferramentas e procedimentos que está usando no local para coisas como antivírus, antimalware, aplicação de patches e backup.

### <a name="harden-systems"></a>Proteger sistemas

Todas as máquinas virtuais no Azure IaaS devem ser protegidas para que apenas exponham os pontos de extremidade dos serviços necessários para os aplicativos instalados. Para as máquinas virtuais do Windows, siga as recomendações que a Microsoft publica como linhas de base para a solução [Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx).

O Security Compliance Manager é uma ferramenta gratuita. Você pode usá-lo para configurar e gerenciar seus desktops, data center tradicional e nuvem pública e privada rapidamente usando a política de grupo e o System Center Configuration Manager.

O Security Compliance Manager fornece políticas prontas para implantar e pacotes de configuração de DCM testados. Essas linhas de base são baseadas em recomendações das [Diretrizes de segurança da Microsoft](https://technet.microsoft.com/library/cc184906.aspx) e práticas recomendadas do setor. Elas ajudam a gerenciar os descompassos de configuração, atender aos requisitos de conformidade e reduzir as ameaças de segurança.

Você pode usar o Security Compliance Manager para importar a configuração atual dos computadores usando dois métodos diferentes. Primeiro, você pode importar as políticas de grupo baseadas no Active Directory. Você também pode importar a configuração de um computador de referência "mestre dourado" usando a [ferramenta LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) para fazer backup da política de grupo local. Você pode importar a política de grupo local para o Security Compliance Manager.

Compare seus padrões de práticas recomendadas do setor, personalize-os e crie novas políticas e pacotes de configuração de Gerenciamento de Configurações Desejadas. Linhas de base foram publicadas para todos os sistemas operacionais suportados, incluindo o Windows 10 Atualização de Aniversário e o Windows Server 2016.


### <a name="install-and-manage-antimalware"></a>Instalar e gerenciar o antimalware

Para ambientes hospedados separadamente do seu ambiente de produção, você pode usar uma extensão de antimalware para ajudar a proteger máquinas virtuais e serviços de nuvem. Ela se integra à [Central de segurança do Azure](../security-center/security-center-intro.md).

O [Microsoft Antimalware](azure-security-antimalware.md) inclui recursos como a proteção em tempo real, verificação agendada, remediação de malware, atualizações de assinatura, atualizações do mecanismo, exemplos de relatório, coleta de eventos de exclusão e [suporte PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Antimalware do Azure](./media/azure-security-iaas/azantimalware.png)

### <a name="install-the-latest-security-updates"></a>Instalar as últimas atualizações de segurança 

Algumas das primeiras cargas de trabalho que os clientes mudam para o Azure são laboratórios e sistemas voltados para fora. Se as máquinas virtuais hospedadas hospedam aplicativos ou serviços que precisam ser acessados pela Internet, preste atenção nos patches. Patch além do sistema operacional. As vulnerabilidades sem patch dos aplicativos de terceiros também podem levar a problemas que podem ser evitados com um bom gerenciamento de patches.

### <a name="deploy-and-test-a-backup-solution"></a>Implantar e testar uma solução de backup

Assim como as atualizações de segurança, um backup precisa ser tratado da mesma maneira como você lida com qualquer outra operação. Isso é verdadeiro para os sistemas que fazem parte de seu ambiente de produção que se estende para a nuvem. Os sistemas de desenvolvimento e teste devem seguir as estratégias de backup que ofereçam recursos de restauração semelhantes aos que os usuários já utilizam com base em sua experiência com os ambientes locais.

As cargas de trabalho de produção movidas para o Azure devem se integrar a soluções de backup existentes quando possível. Você também pode usar o [Backup do Azure](../backup/backup-azure-arm-vms.md) para ajudar a solucionar suas necessidades de backup.

## <a name="monitor"></a>Monitoramento

### <a name="security-centersecurity-centersecurity-center-intromd"></a>[Central de Segurança](../security-center/security-center-intro.md)

A Central de segurança fornece uma avaliação contínua do estado de segurança de seus recursos do Azure para identificar possíveis vulnerabilidades de segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários.

Os exemplos incluem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado.
- Como configurar grupos de segurança de rede e as regras para controlar o tráfego para máquinas virtuais.
- O provisionamento de firewalls do aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web.
- Implantando atualizações de sistema ausentes.
- Abordando configurações do sistema operacional que não coincidem com as linhas de base recomendadas.

A imagem a seguir mostra algumas das opções que você pode habilitar na Central de Segurança.

![Políticas da Central de Segurança do Azure](./media/azure-security-iaas/security-center-policies.png)

### <a name="operations-management-suiteoperations-management-suiteoperations-management-suite-overviewmd"></a>[Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) 

O Operations Management Suite é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda você a gerenciar e proteger sua infraestrutura local e de nuvem. Como o Operations Management Suite é implementado como um serviço baseado em nuvem, ele pode ser implantado rapidamente e com um investimento mínimo em recursos de infraestrutura.

Os novos recursos são entregues automaticamente, evitando os custos contínuos com manutenção e atualização. O Operations Management Suite também se integra com o System Center Operations Manager. Ele tem componentes diferentes para ajudá-lo a gerenciar melhor as cargas de trabalho do Azure, incluindo um módulo de [Segurança e Conformidade](../operations-management-suite/oms-security-getting-started.md).

Você pode usar os recursos de segurança e conformidade no Operations Management Suite para exibir informações sobre seus recursos. As informações estão organizadas em quatro categorias principais:

- **Domínios de segurança**: explorar mais registros de segurança ao longo do tempo. Acessar a avaliação de malware, atualizar a avaliação, informações de segurança de rede, informações de identidade e acesso e computadores com eventos de segurança. Aproveite o acesso rápido ao painel da Central de Segurança do Azure.
- **Problemas Importantes**: identifique rapidamente o número de problemas ativos e sua severidade.
- **Detecções** (visualização): identifique os padrões de ataque visualizando os alertas de segurança à medida que eles ocorrem em relação aos recursos.
- **Inteligência contra ameaças**: identifique os padrões de ataque visualizando o número total de servidores com tráfego IP de saída mal-intencionado, tipo de ameaça mal-intencionada e um mapa que mostra a origem desses IPs.
- **Consultas comuns de segurança**: veja uma lista das consultas de segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você clica em uma dessas consultas, ela abre a folha **Pesquisa** e mostra os resultados da consulta.

A captura de tela a seguir mostra um exemplo das informações que podem ser exibidas pelo Operations Management Suite.

![Linhas de base de segurança do Operations Management Suite](./media/azure-security-iaas/oms-security-baseline.png)

### <a name="monitor-vm-performance"></a>Monitorar o desempenho de VM

Abuso de recursos pode ser um problema quando os processos VM consomem mais recursos do que deveriam. Problemas de desempenho com uma máquina virtual podem levar a interrupção do serviço, o que viola o princípio de segurança de disponibilidade. Por esse motivo, é fundamental monitorar o acesso à VM não apenas de forma reativa, enquanto um problema está ocorrendo, mas também de forma proativa, em relação ao desempenho de linha de base, conforme medido durante a operação normal.

Analisando [arquivos de log de diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), você pode monitorar os recursos VM e identificar problemas potenciais que podem comprometer o desempenho e disponibilidade. A Extensão de Diagnóstico do Azure fornece funcionalidades de monitoramento e diagnóstico em VMs baseadas no Windows. É possível habilitar essas funcionalidades ao incluir a extensão como parte do [modelo do Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Use também o [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) para obter visibilidade da integridade dos recursos.

As organizações que não monitoram o desempenho da máquina virtual não conseguem determinar se são determinadas alterações nos padrões de desempenho normais ou anormais. Se a VM está consumindo mais recursos do que o normal, tal uma anomalia pode indicar um ataque potencial de um recurso externo ou um processo comprometido em execução na máquina virtual.

## <a name="next-steps"></a>Próximas etapas

* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md)
