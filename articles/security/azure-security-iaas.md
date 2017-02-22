---
title: "Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure | Microsoft Docs"
description: " A migração das cargas de trabalho para o Azure IaaS traz oportunidades para reavaliar nosso designss "
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
ms.date: 01/09/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 3451bdde3be60df0f1e1ed977f23db65d39f535f
ms.openlocfilehash: 8416de09a4a3c2cdf9c73b9f79e8df9e292514ca




---

# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Práticas recomendadas de segurança para as cargas de trabalho IaaS no Azure

Quando você começou a pensar sobre como mover as cargas de trabalho para o Azure IaaS, provavelmente chegou à conclusão de que algumas considerações são familiares. Talvez você já tenha experiência em proteger os ambientes virtuais. A mudança para o Azure IaaS permite aplicar seu conhecimento em proteger os ambientes virtuais e também traz um novo conjunto de opções para ajudá-lo a proteger seus ativos.

Antes de continuarmos, começamos dizendo que não devemos esperar trazer os recursos locais individualmente para o Azure. Os novos desafios e as novas opções trazem uma oportunidade para reavaliar os designs, ferramentas e processos existentes.




![Áreas de Responsabilidade](./media/azure-security-iaas/sec-cloudstack-new.png)

>[!NOTE]
> Sua responsabilidade pela segurança é baseada no tipo de serviço de nuvem. O gráfico acima resume o equilíbrio da responsabilidade para a Microsoft e você.

## <a name="best-practices"></a>Práticas recomendadas
Abordaremos algumas das opções disponíveis no Azure que podem ajudá-lo a atender aos requisitos de segurança de sua organização. Ao fazer isso, devemos lembrar dos diferentes tipos de cargas de trabalho e como seus requisitos de segurança podem variar. Nenhuma dessas práticas recomendadas pode, por si só, proteger seus sistemas. Como tudo em segurança, você precisa escolher as opções apropriadas e ver como as soluções podem complementar-se, preenchendo as lacunas deixadas pelas outras.

### <a name="use-privileged-access-workstations-paw"></a>Usar Estações de Trabalho com Acesso Privilegiado (PAW)

As organizações geralmente são vítimas de ataques cibernéticos por causa dos administradores que executam ações enquanto usam contas com direitos elevados. Geralmente, isso não é feito maliciosamente, mas porque a configuração existente e os processos permitem fazê-lo. A maioria desses usuários entende o risco de um ponto de vista conceitual, mas ainda opta por etapas que eles concordariam ser arriscadas.

Fazer coisas como verificar o email e navegar na Internet parecem bem inocentes, mas podem expor as contas elevadas ao comprometimento por pessoas mal-intencionadas que podem usar as atividades de navegação, especialmente os emails criados ou outras técnicas, para ter acesso à sua empresa. O uso de estações de trabalho de gerenciamento seguro para realizar todas as tarefas de administração do Azure é altamente recomendado como uma maneira de reduzir a exposição a um comprometimento acidental.

As Estações de Trabalho com Acesso Privilegiado (PAWs) fornecem um sistema operacional dedicado para as tarefas confidenciais protegidas contra ataques da Internet e vetores de ameaça. Separar essas tarefas confidenciais e contas das estações de trabalho de uso diário e dispositivos fornece uma ótima proteção contra ataques de phishing, aplicativo e vulnerabilidades do SO, vários ataques de representação e ataques de roubo de credenciais, como o registro de teclas pressionadas, Passagem de Hash e Passagem de Tíquete.

A abordagem PAW é uma extensão da prática recomendada bem estabelecida para usar contas de usuário e administrador separadas para a equipe administrativa. Esta prática usa uma conta administrativa individualmente atribuída que é separada da conta de usuário padrão. A PAW se baseia nessa prática de separação de conta fornecendo uma estação de trabalho confiável para essas contas confidenciais.

Para obter mais informações sobre as Estações de Trabalho com Acesso Privilegiado e diretrizes para a implementação da PAW, siga este link:

- [Estações de Trabalho com Acesso Privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)

### <a name="use-multifactor-authentication"></a>Usar a autenticação multifator

No passado, o perímetro da rede era usado para controlar o acesso aos dados corporativos. Em um mundo que prioriza a nuvem e os dispositivos móveis, a identidade é o plano de controle: você pode usá-la para controlar o acesso aos serviços de IaaS a partir de qualquer dispositivo e usá-la para ter visibilidade e percepção de onde e como os dados estão sendo usados. Proteger a identidade digital dos usuários do Azure é a base para proteger suas assinaturas contra o roubo de identidade e outros crimes cibernéticos.

Uma das etapas mais vantajosas que podem ser tomadas para proteger uma conta é habilitar a autenticação com dois fatores. A autenticação com dois fatores é uma maneira de autenticar usando mais do que apenas a senha. O segundo fator é algo além da senha.  Isso ajuda a minimizar o risco do acesso por uma pessoa que consegue obter a senha de outra pessoa.

O Azure Multi-Factor Authentication ajuda a proteger o acesso a dados e aplicativos enquanto atende à demanda dos usuários para um processo de logon simples. Ele fornece uma autenticação eficiente com várias opções de verificação fáceis, como chamada telefônica, mensagem de texto ou notificação por aplicativos móveis, permitindo que os usuários escolham seu método de preferência.

A maneira mais fácil de usar o [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) é o aplicativo móvel do Microsoft Authenticator, que pode ser usado em dispositivos móveis que executam o Windows, IOS e Android. Com a versão mais recente do Windows 10 e seu Active Directory local integrado ao AD do Azure, o [Microsoft Hello para Negócios](../active-directory/active-directory-azureadjoin-passport-deployment.md) pode ser usado para fazer um logon único e perfeito nos recursos do Azure. Neste caso, o dispositivo do Windows 10 será usado como o segundo fator para a autenticação.


No caso do Azure, a maneira mais simples de habilitar a autenticação com dois fatores é usar a Autenticação Multifator do Azure (MFA). O [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) tem um aplicativo que pode ser usado em seus dispositivos móveis, pode funcionar por meio de chamadas telefônicas, mensagens de texto ou um código gerado no aplicativo e pode integrar-se no diretório local.

Para as contas que gerenciam sua assinatura do Azure, você deve usar o MFA e para as contas que podem fazer logon nas Máquinas Virtuais, você deve usar o MFA quando possível. Usar o MFA para essas contas oferece um nível de segurança muito maior do que apenas usar uma senha. Usar outras formas de autenticação com dois fatores funcionaria muito bem, mas pode ser mais complicado de ser implantado, caso não ainda estejam em produção.

A captura de tela abaixo mostra algumas opções disponíveis para a autenticação do Azure MFA.

![Opções do MFA](./media/azure-security-iaas/mfa-options.png)

### <a name="limit-and-constrain-administrative-access"></a>Limitar e Restringir o Acesso Administrativo

Proteger as contas que podem gerenciar sua assinatura do Azure é extremamente importante. O comprometimento de qualquer uma dessas contas efetivamente nega o valor de todas as outras etapas que você pode tomar para garantir a confidencialidade e a integridade dos dados. Como ilustrado recentemente por [Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), que vazou informações confidenciais, os ataques internos representam uma enorme ameaça à segurança geral de qualquer organização.

Os indivíduos que têm direitos administrativos foram avaliados seguindo um critério semelhante ao seguinte:

- Eles estão realizando tarefas que exigem privilégios administrativos?
- Com que frequência as tarefas são realizadas?
- Um motivo específico para as tarefas não poderem ser realizadas por outro administrador em seu nome.
- Documente todas as outras abordagens alternativas conhecidas para conceder o privilégio e por que cada uma não é aceitável.

O uso de uma administração apropriada impede a existência desnecessária de contas com direitos elevados nos períodos de tempo quando esses direitos não são necessários. As contas têm direitos elevados por um período de tempo limitado, permitindo que os administradores realizem seus trabalhos, então, têm esses direitos removidos no final de um turno ou quando uma tarefa é concluída.

O [PIM](../active-directory/active-directory-privileged-identity-management-configure.md) permite que você gerencie, monitore e controle o acesso em sua organização. Ele ajuda você a ficar ciente das ações realizadas pelas pessoas em sua organização e traz a administração apropriada para o Azure AD, introduzindo o conceito de um administrador qualificado. Estas são as pessoas que têm contas com o potencial de receberem os direitos de administrador. Esses tipos de usuários podem passar por um processo de ativação e receber direitos de administrador por um tempo limitado.


### <a name="use-devtest-labs"></a>Usar DevTest Labs

Usar o Azure para laboratórios e ambientes de desenvolvimento permite que as organizações tenham agilidade no desenvolvimento e teste, acabando com os atrasos introduzidos pela aquisição do hardware. Infelizmente, o risco da falta de familiaridade com o Azure ou um desejo de ajudar a agilizar sua adoção pode levar o administrador a ser excessivamente permissivo com a atribuição de direitos. Isso pode expor, sem querer, a organização a ataques internos. Alguns usuários poderiam receber muito mais acesso do que deveriam.

No Azure, agora incluímos um serviço denominado [DevTest Labs](../devtest-lab/devtest-lab-overview.md). O DevTest Labs usa o [controle de acesso com base em funções do Azure (RBAC)](../active-directory/role-based-access-control-what-is.md)(RBAC). O RBAC permite separar as tarefas dentro de sua equipe em funções que concedem somente o nível de acesso necessário para os usuários fazerem seus trabalhos. Ele vem com funções predefinidas (proprietário, usuário do laboratório e colaborador). Essas funções ainda podem ser usadas para atribuir direitos a parceiros externos e simplificar muito a colaboração.

Como o DevTest Labs usa o RBAC, é possível criar outras [funções personalizadas](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). O DevTest Labs não apenas simplifica o gerenciamento das permissões, como também é projetado para simplificar o processo de obtenção de ambientes provisionados e lidar com outros desafios comuns das equipes que trabalham em ambientes de desenvolvimento e teste. Ele requer certa preparação, mas a longo prazo facilitará as coisas para sua equipe.

Alguns dos principais recursos do Azure DevTest Labs incluem:

- Controle administrativo sobre as opções disponíveis para os usuários. Coisas como tamanhos da VM permitidos, número máximo de VMs e quando as VMs são iniciadas e finalizadas podem ser gerenciadas centralmente pelo administrador
- Automação da criação do ambiente de laboratório
- Controle de custos
- Distribuição simplificada das VMs para o trabalho colaborativo temporário
- Autoatendimento permitindo que os usuários provisionem seus laboratórios usando modelos
- Gerenciando e limitando o consumo

![Criando um DevTestLab](./media/azure-security-iaas/devtestlabs.png)

Não há custos adicionais associados ao uso do DevTest Labs. A criação de laboratórios, configuração da política, modelos e artefatos é gratuita. Você paga apenas pelos recursos do Azure usados em seus laboratórios, como as máquinas virtuais, contas de armazenamento e redes virtuais.



### <a name="control-and-limit-endpoint-access"></a>Controlar e Limitar o Acesso do Ponto de Extremidade

Hospedar laboratórios ou sistemas de produção no Azure significa que os sistemas precisam ser acessíveis pela Internet. Por padrão, uma nova máquina virtual do Windows tem a porta RDP acessível pela Internet e uma máquina virtual Linux tem a porta SSH aberta. Isso significa que executar etapas para 'limitar os pontos de extremidade expostos' é necessário para minimizar o risco do acesso não autorizado.

Existem tecnologias no Azure que podem ajudá-lo a limitar o acesso a esses pontos de extremidade administrativos. No Azure, você pode usar os Grupos de Segurança da Rede ([NSGs](../virtual-network/virtual-networks-nsg.md)). Quando você usa o Gerenciador de Recursos para a implantação, os NSGs são usados para limitar o acesso de todas as redes para apenas os pontos de extremidade de gerenciamento (RDP ou SSH).  Quando você pensar em NSGs, considere as ACLs do roteador. Você pode usá-las para controlar totalmente a comunicação de rede entre os vários segmentos das redes do Azure. Isso é semelhante a criar redes em DMZs ou outras redes isoladas. Elas não inspecionam o tráfego, mas ajudarão na segmentação da rede.


No Azure, você pode configurar uma [VPN site a site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) a partir de sua rede local, efetivamente estendendo sua rede local para a nuvem.  Isso forneceria outra oportunidade de usar os NSGs, pois você também poderia modificar os NSGs para não permitir o acesso de qualquer lugar além da rede local, então, exigir que a administração seja feita pela primeira conexão com a rede do Azure por meio da VPN.

A opção VPN site a site pode ser muito atraente nos casos em que você hospeda os sistemas de produção que são muito integrados com seus recursos locais no Azure.

Como alternativa, a opção [ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md) pode ser usada nas situações em que você deseja gerenciar os sistemas que não precisam acessar os recursos locais. Esses sistemas podem ser isolados em sua própria Rede Virtual do Azure e os administradores podem usar a VPN para o ambiente hospedado pelo Azure a partir de sua estação de trabalho administrativa.

>[!NOTE]
A opção VPN permitiria que você reconfigurasse as ACLs nos NSGs para não permitir o acesso aos pontos de extremidade de gerenciamento a partir da Internet.

Outra opção que vale a pena considerar é uma implantação do [Gateway da Área de Trabalho Remota](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md). Você pode usar essa implantação do gateway da área de trabalho remota para conectar com segurança via HTTPS os servidores da área da trabalho remota ao aplicar controles mais granulares nessas conexões.

Alguns dos recursos que você acessaria incluem:

- Opções do administrador para limitar as conexões às solicitações de sistemas específicos.
- Autenticação de cartão inteligente ou MFA do Azure
- Controle sobre quais sistemas alguém pode conectar por meio do gateway.
- Controle sobre o redirecionamento do dispositivo e do disco.

### <a name="use-a-key-management-solution"></a>Uso de uma solução de Gerenciamento de Chaves

O gerenciamento de chaves seguro é fundamental para proteger os dados na nuvem. Com o [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md), você pode armazenar com segurança as chaves de criptografia e pequenos segredos, como senhas nos módulos de segurança do hardware (HSMs). Para garantia extra, você pode importar ou gerar chaves em HSMs.

Se você optar por fazer isso, a Microsoft processará suas chaves em HSMs FIPS 140-2 Nível 2 validados (hardware e firmware). Monitore e faça a auditoria do uso da chave com o log do Azure — redirecione os logs para a aplicação do Azure ou seu SIEM para ter uma análise adicional e detecção de ameaças.

Qualquer pessoa com uma assinatura do Azure pode criar e usar os cofres de chaves. Embora o Cofre de Chaves beneficie os desenvolvedores e administradores de segurança, ele pode ser implementado e gerenciado pelo administrador de uma organização responsável por gerenciar os serviços do Azure.


### <a name="encrypt-virtual-disks-and-disk-storage"></a>Criptografar Discos Virtuais e o Armazenamento de Discos

O [Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) endereça a ameaça do roubo de dados ou a exposição contra o acesso não autorizado feito movendo um disco. O disco pode ser anexado a outro sistema como uma forma de ignorar os outros controles de segurança. A criptografia de disco usa o [BitLocker](https://technet.microsoft.com/library/hh831713 ) no Windows e o DM-Crypt no Linux para criptografar o sistema operacional e as unidades de dados. O Azure Disk Encryption se integra ao cofre de chaves para controlar e gerenciar as chaves de criptografia e está disponível para as VMs padrão e as VMs com armazenamento premium.

Para obter mais informações, examine a cobertura do artigo [Azure Disk Encryption nas VMs do Windows e Linux IaaS](azure-security-disk-encryption.md).

A [Criptografia do Serviço de Armazenamento do Azure](../storage/storage-service-encryption.md) protege seus dados em repouso. Ela é habilitada no nível da conta de armazenamento e criptografa os dados como gravados em nossos data centers e eles são descriptografados automaticamente quando você os acessa. Ele oferece suporte para os seguintes cenários:

- Criptografia de blobs de bloco, blobs de acréscimo e blobs de páginas.
- Criptografia de VHDs arquivados e modelos colocados no Azure a partir do local.
- Criptografia de sistema operacional subjacente e discos de dados para VMs de IaaS criadas com seus VHDs.

Antes de prosseguir com a criptografia de Armazenamento do Azure, você deve estar ciente de duas limitações importantes:

- não está disponível nas contas de armazenamento clássicas.
- só criptografa os dados gravados após a criptografia estar habilitada.

### <a name="use-a-centralized-security-management-system"></a>Usar um Sistema de Gerenciamento de Segurança Centralizado

Seus servidores precisam ser monitorados para a aplicação de patch, configuração, eventos e atividades que podem ser considerados preocupações com a segurança. Para lidar com essas preocupações, você pode usar a [Central de Segurança](https://azure.microsoft.com/services/security-center/) e a [Conformidade e Segurança do Operations Management Suite](https://azure.microsoft.com/services/security-center/). Essas duas opções vão além da configuração do sistema operacional e também oferecem o monitoramento da configuração da infraestrutura subjacente, como a configuração da rede e o uso do dispositivo virtual.

### <a name="operating-system-management-best-practices"></a>Práticas recomendadas de gerenciamento do sistema operacional

Em uma implantação IaaS, você ainda é responsável pelo gerenciamento dos sistemas que implanta, assim como por qualquer outro servidor ou estação de trabalho em seu ambiente. Isso significa que os patches, proteção, atribuições de direitos e qualquer outra atividade relacionadas à manutenção do sistema ainda é sua responsabilidade.  Para os sistemas que são muito integrados com seus recursos locais, você pode querer usar as mesmas ferramentas e procedimentos que está usando no local para coisas como antivírus, antimalware, aplicação de patches e backup.

**Proteção** Todas as máquinas virtuais no Azure IaaS devem ser protegidas para que apenas exponham os pontos de extremidade dos serviços necessários para os aplicativos instalados. Para as máquinas virtuais do Windows, siga as recomendações que são publicadas pela Microsoft como linhas de base para a solução do Security Compliance Manager.

[Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) -recentemente lançamos a versão 4.0. É uma ferramenta gratuita que permite configurar e gerenciar rapidamente suas áreas de trabalho, data center tradicional, nuvens pública e privada usando a Política de Grupo e o System Center Configuration Manager.

O SCM está pronto para implantar as políticas e os pacotes de configuração do DCM que são testados. Essas linhas de base são fundamentadas nas recomendações e práticas recomendadas do setor no [guia de Segurança da Microsoft](https://technet.microsoft.com/en-us/library/cc184906.aspx), permitindo que você gerencie os desvios da configuração, enderece os requisitos de conformidade e reduza as ameaças de segurança.

Você pode aproveitar o SCM para importar a configuração atual dos computadores usando dois métodos diferentes: primeiro, você pode importar as políticas de grupo baseadas no Active Directory; em segundo lugar, pode importar a configuração do computador de referência "principal dourado" usando a [ferramenta LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) para fazer backup da política de grupo local que, então, você pode importar para o SCM.

Compare seus padrões de práticas recomendadas do setor, personalize-os e crie novas políticas e pacotes de configuração do DCM.
Linhas de base foram publicadas para todos os sistemas operacionais suportados, incluindo o Windows 10 Atualização de Aniversário e o Windows Server 2016.


**Instalar e gerenciar o antimalware**

Para os ambientes hospedados separadamente do seu ambiente de produção, há uma extensão de antimalware que pode ser usada para proteger as máquinas virtuais e os serviços de nuvem, e integra-se com a [Central de Segurança do Azure](../security-center/security-center-intro.md).


O [Microsoft Antimalware](azure-security-antimalware.md) inclui recursos como a proteção em tempo real, verificação agendada, remediação de malware, atualizações de assinatura, atualizações do mecanismo, exemplos de relatório, coleta de eventos de exclusão e [suporte PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Antimalware do Azure](./media/azure-security-iaas/azantimalware.png)

**Instale as últimas atualizações de segurança** Algumas das primeiras cargas de trabalho que vemos os clientes moverem para o Azure são laboratórios e sistemas externos. Se você estiver hospedando máquinas virtuais no Azure que hospedam aplicativos ou serviços que precisam ser disponibilizadas para a Internet, precisará observar os patches. Lembre-se que isso vai além da aplicação de patches no sistema operacional. As vulnerabilidades sem patch dos aplicativos de terceiros também podem levar a problemas que teriam sido facilmente evitados se um bom gerenciamento de patches existisse.

Para obter mais informações sobre o gerenciamento de patches no Azure IaaS, examine as [Práticas recomendadas para atualizações de software no Microsoft Azure IaaS](azure-security-best-practices-software-updates-iaas.md).

**Implantar e testar uma solução de backup**

Assim como as atualizações de segurança, o backup precisa ser tratado da mesma maneira como você lida com qualquer outra operação. Isso é verdadeiro para os sistemas que fazem parte de seu ambiente de produção que se estende para a nuvem. Os Sistemas de Desenvolvimento e Teste devem seguir as estratégias de backup capazes de fornecer recursos semelhantes de restauração com os quais os usuários já estão acostumados com base em sua experiência com os ambientes locais.

As cargas de trabalho de produção movidas para o Azure devem integrar-se com as soluções de backup existentes quando possível ou você pode usar o [Backup do Azure](../backup/backup-azure-arm-vms.md) para ajudar a endereçar seus requisitos de backup.


### <a name="monitor"></a>Monitoramento

A [Central de Segurança](../security-center/security-center-intro.md) fornece uma avaliação contínua do estado de segurança de seus recursos do Azure para identificar as possíveis vulnerabilidades de segurança. Uma lista de recomendações orienta você no processo de configuração dos controles necessários.

Os exemplos incluem:

- Provisionamento de antimalware para ajudar a identificar e remover software mal-intencionado
- Como configurar grupos de segurança de rede e regras para controlar o tráfego para máquinas virtuais
- Provisionamento de firewalls do aplicativo Web para ajudar a proteger contra ataques direcionados aos seus aplicativos Web
- Como implantar atualizações de sistema ausentes
- Endereçamento de configurações do sistema operacional que não coincidem com as linhas de base recomendadas

Na imagem abaixo, você pode ver algumas das opções disponíveis para habilitar na Central de Segurança.

![Políticas da central de segurança do Azure](./media/azure-security-iaas/security-center-policies.png)

 O [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) é a solução de gerenciamento de TI baseada em nuvem da Microsoft que ajuda a gerenciar e proteger sua infraestrutura de nuvem e local. Como o OMS é implementado como um serviço baseado em nuvem, ele pode ser implantado rapidamente e com um investimento mínimo em recursos de infraestrutura.

Os novos recursos são entregues automaticamente, evitando os custos contínuos com manutenção e atualização. Ele também se integra com o System Center Operations Manager.  O OMS tem componentes diferentes para ajudá-lo a gerenciar melhor as cargas de trabalho do Azure, incluindo um módulo de [Segurança e Conformidade](../operations-management-suite/oms-security-getting-started.md).

Os recursos de segurança e conformidade no OMS permitem que você exiba informações sobre os recursos organizadas em quatro categorias principais:

- Domínios de Segurança: nessa área, você poderá explorar ainda mais os registros de segurança ao longo do tempo, acessar a avaliação do malware, atualizar as informações de avaliação, segurança de rede, identidade e acesso, computadores com eventos de segurança e ter acesso rapidamente ao painel da Central de Segurança do Azure.
- Problemas Importantes: essa opção permitirá que você identifique rapidamente o número de problemas ativos e sua severidade.
- Detecções (Visualização): permite que você identifique os padrões de ataque visualizando os alertas de segurança à medida que eles ocorrem em relação aos recursos.
- Inteligência contra Ameaças: permite que você identifique os padrões de ataque visualizando o número total de servidores com tráfego IP de saída mal-intencionado, tipo de ameaça mal-intencionada e um mapa que mostra a origem desses IPs.
- Consultas comuns de segurança: essa opção fornece uma lista das consultas de segurança mais comuns que você pode usar para monitorar seu ambiente. Quando você clica em uma dessas consultas, ela abre a folha Pesquisa com os resultados da consulta.

A captura de tela abaixo mostra um exemplo do tipo de informações que podem ser exibidas pelo OMS.

![Linhas de base de segurança do OMS](./media/azure-security-iaas/oms-security-baseline.png)



## <a name="next-steps"></a>Próximas etapas


* [Blog da Equipe de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx)
* [Padrões e práticas recomendadas de segurança do Azure](security-best-practices-and-patterns.md)



<!--HONumber=Jan17_HO2-->


