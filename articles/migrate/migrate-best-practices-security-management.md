---
title: Melhores práticas para proteger e gerenciar cargas de trabalho migradas para o Azure | Microsoft Docs
description: Depois de migrar para o Azure, obtenha as melhores práticas para operar, gerenciar e proteger as cargas de trabalho migradas.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: raynew
ms.openlocfilehash: afcd180146bc349bda9375f10eb56f85f67ccb52
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498730"
---
# <a name="best-practices-for-securing-and-managing-workloads-migrated-to-azure"></a>Melhores práticas para proteger e gerenciar cargas de trabalho migradas para o Azure

À medida que você planeja e projeta a migração, além de pensar sobre a migração em si, é necessário considerar seu modelo de segurança e gerenciamento no Azure após a migração. Este artigo descreve o planejamento e as melhores práticas para proteger a implantação do Azure após a migração e manter a implantação em execução em um nível ideal para tarefas em andamento. 

> [!IMPORTANT]
> As melhores práticas e as opiniões descritas neste artigo baseiam-se na plataforma Azure e nos recursos do serviço disponíveis no momento em que o artigo foi escrito. Os recursos e as funcionalidades mudam ao longo do tempo.

## <a name="secure-migrated-workloads"></a>Proteger cargas de trabalho migradas

Após a migração, a tarefa mais importante é proteger cargas de trabalho migradas de ameaças internas e externas. Estas melhores práticas ajudam você a:

- [Trabalhar com a Central de Segurança do Azure](#best-practice-follow-azure-security-center-recommendations): saiba como trabalhar com monitoramento, avaliações e recomendações fornecidos pela Central de Segurança do Azure
- [Criptografar os dados](#best-practice-encrypt-data): conheça as melhores práticas para criptografar dados no Azure.
- [Configurar o antimalware](#best-practice-protect-vms-with-antimalware): proteja suas VMs contra malware e ataques mal-intencionados.
- [Proteger aplicativos Web](#best-practice-secure-web-apps): proteja informações confidenciais em aplicativos Web migrados.
- [Revisar assinaturas](#best-practice-review-subscriptions-and-resource-permissions): verifique quem pode acessar suas assinaturas e recursos do Azure após a migração.
- [Trabalhar com logs](#best-practice-review-audit-and-security-logs): examine os logs de auditoria e segurança do Azure regularmente.
- [Rever outros recursos de segurança](#best-practice-evaluate-other-security-features): compreenda e avalie os recursos avançados de segurança oferecidos pelo Azure.

## <a name="best-practice-follow-azure-security-center-recommendations"></a>Melhor prática: seguir as recomendações da Central de Segurança do Azure

A Microsoft trabalha duro para fazer com que os administradores de locatários do Azure tenham as informações necessárias para habilitar os recursos de segurança que protegem suas cargas de trabalho contra ataques.  A Central de Segurança do Azure fornece gerenciamento unificado de segurança. Na Central de Segurança, você pode aplicar políticas de segurança em cargas de trabalho, limitar a exposição a riscos e detectar e responder a ataques. A Central de Segurança analisa as configurações e os recursos entre locatários do Azure e faz recomendações de segurança, incluindo:

- **Gerenciamento de política centralizado** – garanta a conformidade com requisitos de segurança da empresa ou de regulamentação, gerenciando políticas de segurança de forma centralizada em todas as suas cargas de trabalho de nuvem híbrida.
- **Avaliação de segurança contínua** – monitore a postura de segurança de computadores, redes, armazenamento e serviços de dados e aplicativos para descobrir problemas potenciais de segurança.
- **Recomendações acionáveis** – corrija a vulnerabilidades de segurança antes que elas sejam exploradas por invasores, usando recomendações de segurança priorizadas e práticas.
- **Alertas e incidentes priorizados** – concentre-se primeiro nas ameaças mais importantes com alertas de segurança e incidentes priorizados.

Além de avaliações e recomendações, a Central de Segurança fornece vários outros recursos de segurança que podem ser habilitados para recursos específicos.

- **Acesso JIT (Just-In-Time)**: reduza a superfície de ataque da rede com o acesso Just-In-Time e controlado às portas de gerenciamento em VMs do Azure.
    - Ter a porta RDP 3389 da VM aberta na Internet expõe VMs a atividades contínuas de atores mal-intencionados. Os endereços IP do Azure são conhecidos e os hackers os investigam continuamente para atacar portas 3389 abertas. 
    - O Just-In-Time usa NSGs (grupos de segurança de rede) e regras de entrada que limitam a quantidade de tempo que uma porta específica fica aberta.
    - Com o Just-In-Time habilitado, a Central de Segurança verifica se um usuário tem permissões de acesso de gravação RBAC (controle de acesso baseado em função) para uma VM. Além disso, ela especifica regras sobre como os usuários podem se conectar às VMs. Se as permissões estiverem corretas, uma solicitação de acesso será aprovada e a Central de Segurança configurará NSGs, a fim de permitir tráfego de entrada para as portas selecionadas pelo período de tempo especificado. Os NSGs retornam ao estado anterior quando o tempo expira.
- **Controles de aplicativos adaptáveis**: mantenha software e malware fora das VMs, controlando quais aplicativos são executados nesses computadores por meio de uma lista de permissões de aplicativo dinâmica.
    - Os controles de aplicativo adaptáveis permitem que você coloque aplicativos na lista de permissões e impeça que usuários não autorizados ou administradores instalem aplicativos de software de habilitação ou não aprovados em suas VMs.
    - Você pode bloquear ou criar alertas para tentativas de execução de aplicativos mal-intencionados, evitar aplicativos mal-intencionados ou indesejados e garantir a conformidade com a política de segurança de aplicativo de sua organização.
- **Monitoramento de integridade do arquivo**: garanta a integridade dos arquivos em execução nas VMs.
    - Você não precisa instalar software para causar problemas em uma VM.  A alteração de um arquivo do sistema também pode causar degradação de desempenho ou falha da VM.  O Monitoramento de Integridade de Arquivo verifica alterações nos arquivos de sistema e nas configurações de registro e notifica você quando algo é atualizado.
    - A Central de Segurança recomenda os arquivos que você deve monitorar.


**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/security-center/security-center-intro) sobre a Central de Segurança do Azure.
- [Saiba mais](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) sobre acesso à VM Just-In-Time.
- [Saiba mais sobre](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application) como aplicar controles de aplicativo adaptáveis.
- [Introdução](https://docs.microsoft.com/azure/security-center/security-center-file-integrity-monitoring) ao Monitoramento de Integridade de Arquivo.


## <a name="best-practice-encrypt-data"></a>Melhor prática: Criptografar dados 

A criptografia é uma parte importante das práticas de segurança do Azure. Habilitar a criptografia em todos os níveis ajuda a evitar que partes não autorizadas obtenham acesso a dados confidenciais, incluindo dados em trânsito e em repouso. 

### <a name="encryption-for-iaas"></a>Criptografia para IaaS

- **VMs**: para VMs, você pode usar o Azure Disk Encryption para criptografar seus discos de VM de IaaS do Linux e do Windows.
    - A criptografia de disco aproveita o BitLocker para Windows e o DM-Crypt para Linux a fim de fornecer criptografia do volume para o SO e os discos de dados.
    - Você pode usar uma chave de criptografia criada pelo Azure ou fornecer suas próprias chaves de criptografia guardadas no Azure Key Vault. 
    - Com a Criptografia de Disco, os dados da VM IaaS são protegidos em repouso (no disco) e durante a inicialização da VM. 
    - A Central de Segurança do Azure alertará você se tiver VMs não criptografadas.
- **Armazenamento**: proteja dados armazenados no armazenamento do Azure.
    - Dados armazenados nas contas de armazenamento do Azure podem ser criptografados usando chaves AES geradas pela Microsoft que tenham a certificação FIPS 140-2, ou então, você pode usar suas próprias chaves.
    - A Criptografia do Serviço de Armazenamento está habilitada para todas as contas de armazenamento novas e existentes e não pode ser desabilitada.


### <a name="encryption-for-paas"></a>Criptografia para PaaS

Ao contrário do IaaS, na qual você gerencia suas próprias VMs e infraestrutura, em um modelo de PaaS a plataforma e a infraestrutura são gerenciadas pelo provedor, permitindo que você se concentre na lógica de aplicativo e nas funcionalidades principais. Com tantos tipos de serviços de PaaS diferentes, cada serviço será avaliado individualmente para fins de segurança. Por exemplo, vamos ver como podemos habilitar a criptografia para o Banco de Dados SQL do Azure.

- **Always Encrypted**: use o assistente Always Encrypted no SQL Server Management Studio para proteger dados em repouso.
    - Crie a chave Always Encrypted para criptografar dados de colunas individuais.
    - As chaves Always Encrypted podem ser armazenadas em metadados do banco de dados, ou armazenadas em repositórios de chaves confiáveis, como o Azure Key Vault.
    - As alterações no aplicativo provavelmente precisarão usar esse recurso.
- **TDE (Transparent Data Encryption)**: protege o Banco de Dados SQL do Azure com criptografia e descriptografia em tempo real do banco de dados, dos backups associados e dos arquivos de log de transações em repouso.
    - A TDE permite que as atividades de criptografia ocorram na camada do aplicativo sem alterações.
    - A TDE pode usar chaves de criptografia fornecidas pela Microsoft, ou você pode fornecer suas próprias chaves usando o suporte do Bring Your Own Key.


**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) Azure Disk Encryption para VMs IaaS.
- [Habilitar](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-windows) criptografia para VMs IaaS do Windows.
- [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) sobre a Criptografia do Serviço de Armazenamento do Azure para dados em repouso.
- [Leia](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) uma visão geral do Always Encrypted.
- [Leia sobre](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?view=sql-server-2017) TDE para o Banco de Dados SQL do Azure.
- [Saiba mais](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql) sobre TDE com Bring Your Own Key.

## <a name="best-practice-protect-vms-with-antimalware"></a>Melhor prática: proteger VMs com antimalware

As VMs mais antigas migradas do Azure podem não ter o nível apropriado de antimalware instalado.  O Azure fornece uma solução de ponto de extremidade gratuita que ajuda a proteger VMs contra vírus, spyware e outros tipos de malware.
- O Microsoft Antimalware para Azure gera alertas quando software mal-intencionado ou indesejado tenta se instalar na máquina.
- É uma solução de agente único que é executada em segundo plano sem intervenção humana.
- Na Central de Segurança do Azure, você pode identificar facilmente as VMs que não têm a proteção de ponto de extremidade em execução e instalar o Microsoft Antimalware conforme a necessidade.

![Antimalware para VMs](./media/migrate-best-practices-security-management/antimalware.png)
*Antimalware para VMs*

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/security/azure-security-antimalware) sobre o Microsoft Antimalware.

## <a name="best-practice-secure-web-apps"></a>Melhor prática: proteger aplicativos Web

Os aplicativos Web migrados enfrentam alguns problemas:

- A maioria dos aplicativos Web herdados tendem a ter informações confidenciais nos arquivos de configuração.  Os arquivos que contêm essas informações podem apresentar problemas de segurança quando o backup dos aplicativos é feito ou quando o código do aplicativo é inserido no controle do código-fonte ou recuperado dele.
- Além disso, ao migrar aplicativos Web que residem em uma VM, você provavelmente estará movendo essa máquina de uma rede local e de um ambiente protegido por firewall para um ambiente conectado à Internet. Você precisa configurar uma solução que faça o mesmo trabalho dos recursos de proteção locais.


O Azure fornece algumas soluções:

- **Azure Key Vault**: os desenvolvedores de aplicativos Web atualmente estão tomando medidas para ter certeza de que informações confidenciais não vazarão desses arquivos. Um método para proteger as informações é extraí-las dos arquivos e colocá-las em um Azure Key Vault.
    - Você pode usar o Key Vault para centralizar o armazenamento de segredos do aplicativo e controlar sua distribuição. Ele evita a necessidade de armazenar informações de segurança nos arquivos de aplicativo.
    - Os aplicativos podem obter informações de acesso de segurança no cofre usando URIs, sem a necessidade de código personalizado.
    - O Azure Key Vault permite que você bloqueie o acesso por meio de controles de segurança do Azure e implemente “chaves de sobreposição” sem interrupção. A Microsoft não vê ou extrai seus dados.
- **Ambiente do Serviço de Aplicativo**: se um aplicativo migrado precisar de proteção extra, pense em adicionar um Ambiente do Serviço de Aplicativo e um Firewall do Aplicativo Web para proteger os recursos do aplicativo.
    - O Ambiente do Serviço de Aplicativo do Azure fornece um ambiente totalmente isolado e dedicado para a execução de aplicativos do Serviço de Aplicativo, por exemplo, aplicativos Web Windows e Linux, contêineres do Docker, aplicativos móveis e funções.
    - É útil para aplicativos com escala muito alta, que exigem isolamento e acesso de rede seguros ou com alta utilização de memória
- **Firewall do Aplicativo Web**: um recurso do Gateway de Aplicativo que fornece proteção centralizada para aplicativos Web.
    - Ele protege aplicativos Web sem a necessidade de modificações no código de back-end.
    - Ele protege vários aplicativos Web ao mesmo tempo por trás de um gateway de aplicativo.
    - O firewall do aplicativo Web pode ser monitorado com o Azure Monitor e está integrado à Central de Segurança do Azure.



![Proteger aplicativos Web](./media/migrate-best-practices-security-management/web-apps.png)
*Azure Key Vault*

**Saiba mais:**

- [Obtenha uma visão geral](https://docs.microsoft.com/azure/key-vault/key-vault-overview) do Azure Key Vault.
- [Saiba mais sobre o](https://docs.microsoft.com/azure/application-gateway/waf-overview) firewall do aplicativo Web.
- [Obtenha uma introdução](https://docs.microsoft.com/azure/app-service/environment/intro) aos Ambientes do Serviço de Aplicativo.
- [Saiba como](https://docs.microsoft.com/azure/key-vault/tutorial-web-application-keyvault) configurar um aplicativo Web para ler segredos do Key Vault.
- [Saiba mais sobre o](https://docs.microsoft.com/azure/application-gateway/waf-overview) Firewall do Aplicativo Web

## <a name="best-practice-review-subscriptions-and-resource-permissions"></a>Melhor prática: rever assinaturas e permissões de recurso

À medida que você migra suas cargas de trabalho e as executa no Azure, as equipes com acesso à carga de trabalho vão mudando. Sua equipe de segurança deve examinar o acesso aos grupos de recursos e de locatário do Azure regularmente. O Azure fornece uma quantidade de ofertas de gerenciamento de identidade e segurança de controle de acesso, incluindo RBAC (controle de acesso baseado em função), a fim de autorizar permissões para acessar recursos do Azure.

- O RBAC atribui permissões de acesso para entidades de segurança. As entidades de segurança representam usuários, grupos (um conjunto de usuários), entidades de serviço (identidade usada por aplicativos e serviços) e identidades gerenciadas (uma identidade do Azure Active Directory gerenciada automaticamente pelo Azure).
- O RBAC pode atribuir funções a entidades de segurança, como proprietário, colaborador e leitor, e definições de função (uma coleção de permissões) que definem as operações que podem ser executadas pelas funções.
- O RBAC também pode definir escopos que definem o limite de uma função. O escopo pode ser definido em vários níveis, incluindo um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um recurso
- Os administradores com acesso do Azure só devem poder acessar os recursos que você deseja permitir.  Se as funções predefinidas no Azure não forem suficientemente granulares, você poderá criar funções personalizadas para separar e limitar as permissões de acesso.

![Controle de acesso](./media/migrate-best-practices-security-management/subscription.png)
*Controle de acesso – IAM*

**Saiba mais:**

- [Sobre](https://docs.microsoft.com/azure/role-based-access-control/overview) RBAC.
- [Aprenda](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) a gerenciar o acesso usando o RBAC e o portal do Azure.
- [Saiba mais sobre](https://docs.microsoft.com/azure/role-based-access-control/custom-roles) funções personalizadas.

## <a name="best-practice-review-audit-and-security-logs"></a>Melhor prática: rever logs de auditoria e de segurança

O Azure AD (Active Directory) fornece logs de atividade que aparecem no Azure Monitor. Os logs capturam as operações executadas na locação do Azure, onde elas ocorreram e quem as executou. 

- Os logs de auditoria mostram o histórico de tarefas no locatário. Os logs de atividade de entrada mostram quem realizou as tarefas. 
- O acesso aos relatórios de segurança depende da sua licença do Azure AD. Nas licenças Gratuita e Básica, você obtém uma lista de usuários e entradas de risco. Nas edições Premium 1 e Premium 2, você obtém as informações por trás do evento.
- É possível rotear os logs de atividade para vários pontos de extremidade para retenção de longo prazo e insights sobre os dados.
- Torne uma prática comum rever os logs ou integrar sua ferramenta SIEM (gerenciamento de eventos e informações de segurança) para examinar as anomalias automaticamente.  Se você não estiver usando Premium 1 ou 2, precisará fazer grande parte da análise por conta própria ou usar o sistema SIEM.  A análise inclui a procura por entradas e eventos de risco e outros padrões de ataque de usuários.


![Usuários e grupos](./media/migrate-best-practices-security-management/azure-ad.png)
*Usuários e grupos do Azure AD*

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor) sobre logs de atividades do Azure AD no Azure Monitor.
- [Saiba como](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs) auditar relatórios de atividade no portal do Azure AD.

## <a name="best-practice-evaluate-other-security-features"></a>Melhor prática: avaliar outros recursos de segurança

O Azure fornece vários outros recursos de segurança que fornecem opções avançadas de segurança. Algumas dessas melhores práticas exigem licenças complementares e opções premium.

- **Implementar AUs (unidades administrativas) do Azure AD**: a delegação de tarefas administrativas para a equipe de suporte pode ser difícil com apenas o controle de acesso do Azure básico.  Dar acesso à equipe de suporte para administrar todos os grupos no Azure AD pode não ser a abordagem ideal para a segurança da organização.  O uso de AUs permite separar recursos do Azure em contêineres de maneira semelhante às UOs (unidades organizacionais ) locais.  Para usar a AU, o administrador da AU deve ter uma licença do Azure AD premium. [Saiba mais](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).
- **Usar a MFA (autenticação multifator)**: se você tiver uma licença do Azure AD premium, poderá habilitar e impor MFA em suas contas de administrador. O phishing é a maneira mais comum de comprometer as credenciais de conta.  Quando um ator mal-intencionado obtém credenciais de conta de administrador, é impossível impedi-los de realizar ações com graves consequências, como excluir todos os grupos de recursos. Você pode configurar MFA de várias maneiras, inclusive com email, aplicativo autenticador e mensagens de texto do telefone. Como administrador, você pode selecionar a opção menos intrusiva. O MFA integra-se à análise de ameaças e às políticas de acesso condicional para exigir aleatoriamente uma resposta ao desafio de MFA. Saiba mais sobre [diretrizes de segurança](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) e [como configurar](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication-security-best-practices) MFA.
- **Implementar acesso condicional**: na maioria das organizações de pequeno e médio porte, os administradores do Azure e a equipe de suporte provavelmente estão localizados em uma mesma área geográfica. Nesse caso, a maioria dos logons virão das mesmas áreas. Se os endereços IP desses locais são razoavelmente estáticos, faz sentido que você não consiga ver logons de administrador fora dessas áreas. Mesmo em uma situação em que um ator mal-intencionado remoto tenha comprometido credenciais de administrador, você poderá implementar recursos de segurança, como acesso condicional, combinados com MFA para impedir o logon de locais remotos ou locais falsificados de endereços IP aleatórios. [Saiba mais](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) sobre o acesso condicional e [reveja as melhores práticas](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices) para acesso condicional no Azure AD.
- **Rever permissões de aplicativo empresarial**: ao longo do tempo, os administradores clicam em links da Microsoft e de terceiros sem saber seu impacto na organização. Os links podem apresentar telas de consentimento que atribuem permissões aos aplicativos do Azure e podem permitir acesso de leitura de dados do Azure AD, ou mesmo acesso completo para gerenciar sua assinatura inteira do Azure. Você deve examinar os aplicativos aos quais seus administradores e usuários deram permissão para acessar recursos do Azure regularmente. Faça com que esses aplicativos tenham somente as permissões necessárias. Além disso, a cada trimestre ou semestre, você pode enviar um email para os usuários com um link para as páginas de aplicativo, informando a quais aplicativos eles concederam acesso aos dados organizacionais. [Saiba mais](https://docs.microsoft.com/azure/active-directory/manage-apps/application-types) sobre os tipos de aplicativo e [como controlar](https://docs.microsoft.com/azure/active-directory/manage-apps/remove-user-or-group-access-portal) as atribuições de aplicativo no Azure AD.



## <a name="managed-migrated-workloads"></a>Cargas de trabalho migradas gerenciadas

Nesta seção, recomendaremos algumas melhores práticas para gerenciamento do Azure, incluindo:

- [Gerenciar recursos](#best-practice-name-resource-groups): as melhores práticas para recursos e grupos de recursos do Azure, incluindo nomenclatura inteligente, bloqueio de exclusão acidental, gerenciamento de permissões de recurso e marcação eficaz de recursos.
- [Usar blueprints](#best-practice-implement-blueprints): obtenha uma visão geral sobre como usar blueprints para compilar e gerenciar seus ambientes de implantação.
- [Rever as arquiteturas](#best-practice-review-azure-reference-architectures): examine as arquiteturas do Azure para aprender com a criação de suas implantações pós-migração.
- [Configurar grupos de gerenciamento](#best-practice-manage-resources-with-management-groups): se você tiver várias assinaturas, poderá reuni-las em grupos de gerenciamento e aplicar configurações de governança a esses grupos.
- [Configurar políticas de acesso](#best-practice-deploy-azure-policy): aplique políticas de conformidade aos recursos do Azure.
- [Implementar uma estratégia de BCDR](#best-practice-implement-a-bcdr-strategy): crie uma estratégia de BCDR (continuidade dos negócios e recuperação de desastres) para manter seus dados seguros, o ambiente flexível e os recursos em execução em caso de paralisações.
- [Gerenciar VMs](#best-practice-use-managed-disks-and-availability-sets): agrupe VMs em grupos de disponibilidade para alta disponibilidade e resiliência. Use discos gerenciados para facilitar o gerenciamento de armazenamentos e discos de VM.
- [Monitorar o uso de recursos](#best-practice-monitor-resource-usage-and-performance): habilite o log de diagnóstico para recursos do Azure, crie alertas e guias estratégicos para uma solução de problemas proativa e use o painel do Azure para uma exibição unificada da integridade e do status da implantação.
- [Gerenciar atualizações e suporte](#best-practice-manage-updates): entenda seu plano de suporte do Azure e como implementá-lo, veja as melhores práticas para manter as VMs atualizadas e crie processos para o gerenciamento de alterações.


## <a name="best-practice-name-resource-groups"></a>Melhor prática: nomear grupos de recursos

Ter grupos de recursos com nomes significativos que os administradores e os membros da equipe de suporte possam reconhecer e navegar melhorará drasticamente a produtividade e a eficiência.
- Recomendamos seguir as convenções de nomenclatura do Azure.
- Se você estiver sincronizando seu AD DS local com o Azure AD usando o AD Connect, considere a possibilidade de corresponder os nomes dos grupos de segurança locais com os nomes dos grupos de recursos no Azure.

![Nomenclatura](./media/migrate-best-practices-security-management/naming.png)
*Nomenclatura do grupo de recursos*


**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) convenções de nomenclatura

## <a name="best-practice-implement-delete-locks-for-resource-groups"></a>Melhor prática: implementar bloqueios de exclusão em grupos de recursos

A pior coisa que poderia acontecer é você perder um grupo de recursos porque o excluiu acidentalmente. Por isso, recomendamos que você implemente bloqueios de exclusão para que isso não aconteça.


![Bloqueios de exclusão](./media/migrate-best-practices-security-management/locks.png)
*Bloqueios de exclusão*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) como bloquear recursos para evitar alterações inesperadas.


## <a name="best-practice-understand-resource-access-permissions"></a>Melhor prática: entender permissões de acesso de recurso 

Um proprietário de assinatura tem acesso a todos os recursos e grupos de recursos em sua assinatura.
- Tenha cuidado ao adicionar pessoas a essa atribuição valiosa. Entender as implicações desses tipos de permissão é importante para manter seu ambiente seguro e estável.
- Coloque recursos nos grupos de recursos apropriados:
    - Faça a correspondência entre recursos com ciclos de vida semelhantes. O ideal é que não seja necessário mover um recurso quando você precisar excluir um grupo de recursos inteiro.
    - Recursos que dão suporte a uma função ou a uma carga de trabalho devem ser colocados juntos para simplificar o gerenciamento.

**Saiba mais:**

- [Saiba mais sobre](https://azure.microsoft.com/blog/organizing-subscriptions-and-resource-groups-within-the-enterprise/) como organizar grupos de recursos e assinaturas.

## <a name="best-practice-tag-resources-effectively"></a>Melhor prática: marcar recursos com eficiência

Muitas vezes, usar apenas um nome de grupo de recursos relacionado aos recursos não fornecerá metadados suficientes para a implementação eficaz de mecanismos como cobrança interna ou gerenciamento dentro de uma assinatura.
- Como prática recomendada, você deve usar marcas do Azure para adicionar metadados úteis que podem ser consultados e informados. 
- As marcas fornecem uma maneira de organizar recursos logicamente com as propriedades que você definir.  As marcas podem ser aplicadas a grupos de recursos ou recursos diretamente.
- As marcas podem ser aplicadas a um grupo de recursos ou a recursos individuais. As marcas do grupo de recursos não são herdadas pelos recursos no grupo.
- Você pode automatizar a marcação usando o Powershell, a Automação do Azure ou marcando grupos e recursos individualmente. – Abordagem de marcação ou serviço de autoatendimento.  Se você tiver uma solicitação e um sistema de gerenciamento de alterações em vigor, poderá facilmente utilizar as informações na solicitação para preencher as marcas de recursos específicas da empresa.


![Marcação](./media/migrate-best-practices-security-management/tagging.png)
*Marcação*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) marcação e limitações de marca.
- [Reveja](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#powershell) exemplos do PowerShell e da CLI para configurar a marcação e aplicar marcas de um grupo de recursos a seus recursos.
- [Leia](https://www.azurefieldnotes.com/2016/07/18/azure-resource-tagging-best-practices/) as melhores práticas de marcação do Azure.


## <a name="best-practice-implement-blueprints"></a>Melhor prática: implementar blueprints

Assim como um blueprint permite que engenheiros e arquitetos desenhem os parâmetros de um projeto, o Azure Blueprints permite que arquitetos de nuvem e grupos centrais de TI definam um conjunto repetitivo de recursos do Azure que implementa e adere aos padrões e requisitos de uma organização. Usando blueprints do Azure, as equipes de desenvolvimento do Azure podem compilar e criar rapidamente novos ambientes que atendem aos requisitos de conformidade organizacional e que têm um conjunto de componentes internos, como rede, para acelerar o desenvolvimento e a entrega.

- Use blueprints para coordenar a implantação de grupos de recursos, modelos do Azure Resource Manager e atribuições de função e política.
- Os blueprints do Azure são armazenados em um Azure Cosmos DB distribuído globalmente. Objetos de blueprint são replicados para várias regiões do Azure. A replicação fornece baixa latência, alta disponibilidade e acesso consistente ao blueprint, independentemente da região na qual o projeto implanta os recursos.

**Saiba mais:**

- [Leia](https://docs.microsoft.com/azure/governance/blueprints/overview) sobre blueprints.
- [Reveja](https://azure.microsoft.com/blog/customizing-azure-blueprints-to-accelerate-ai-in-healthcare/) um exemplo de blueprint usado para acelerar a Inteligência Artificial nos serviços de saúde.

## <a name="best-practice-review-azure-reference-architectures"></a>Melhor prática: Rever arquiteturas de referência do Azure

A criação de cargas de trabalho seguras, escalonáveis e gerenciáveis no Azure pode ser complicada.  Com alterações contínuas, pode ser difícil saber o status de diferentes recursos para ter um ambiente ideal. Ter uma referência para consulta pode ser útil ao projetar e migrar suas cargas de trabalho.  O Azure e os parceiros do Azure criaram várias arquiteturas de referência de exemplo para vários tipos de ambientes. Esses exemplos são projetados para fornecer ideias com as quais você pode aprender e desenvolver. 

As arquiteturas de referência são organizadas por cenário. Elas contêm práticas recomendáveis e conselhos sobre gerenciamento, disponibilidade, escalabilidade e segurança.
O Ambiente do Serviço de Aplicativo do Azure fornece um ambiente totalmente isolado e dedicado para a execução de aplicativos do Serviço de Aplicativo, incluindo aplicativos Web Windows e Linux, contêineres do Docker, aplicativos móveis e funções. O Serviço de Aplicativo adiciona o poder do Azure ao seu aplicativo, com segurança, balanceamento de carga, dimensionamento automático e gerenciamento automatizado. Você também pode aproveitar seus recursos de DevOps, como implantação contínua do Azure DevOps e do GitHub, gerenciamento de pacote, ambientes de preparo, domínio personalizado e certificados SSL. O Serviço de Aplicativo é útil para aplicativos que precisam de isolamento e acesso seguro à rede e para os que usam grande quantidade de memória e outros recursos que precisam ser dimensionados.
**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/reference-architectures/) arquiteturas de referência do Azure.
- [Reveja](https://docs.microsoft.com/azure/architecture/example-scenario/) os cenários de exemplo do Azure.

## <a name="best-practice-manage-resources-with-management-groups"></a>Melhor prática: organizar recursos com Grupos de Gerenciamento

Se sua organização tiver várias assinaturas, você precisará gerenciar o acesso, as políticas e a conformidade delas. Os grupos de gerenciamento do Azure fornecem um nível de escopo acima das assinaturas.

- Você organiza assinaturas em contêineres chamados grupos de gerenciamento e aplica as condições de governança a eles.
- Todas as assinaturas em um grupo de gerenciamento herdam automaticamente as condições do grupo de gerenciamento.
- Os grupos de gerenciamento fornecem gerenciamento de nível empresarial em larga escala, independentemente do tipo de assinaturas que você tenha.
- Por exemplo, você pode aplicar uma política de grupo de gerenciamento que limita as regiões em que as VMs podem ser criadas. Essa política é, então, aplicada a todos os grupos de gerenciamento, assinaturas e recursos nesse grupo de gerenciamento.
- É possível criar uma estrutura flexível de grupos de gerenciamento e assinaturas, com o objetivo de organizar seus recursos em uma hierarquia para políticas unificadas e gerenciamento de acesso.

O diagrama a seguir mostra um exemplo de criação de uma hierarquia para governança usando grupos de gerenciamento.

![Grupos de gerenciamento](./media/migrate-best-practices-security-management/management-groups.png)
*Grupos de gerenciamento*

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/governance/management-groups/index) sobre como organizar recursos em grupos de gerenciamento.

## <a name="best-practice-deploy-azure-policy"></a>Melhor prática: implantar o Azure Policy

O Azure Policy é um serviço no Azure que você pode usar para criar, atribuir e gerenciar políticas.

- As políticas impõem diferentes regras e efeitos sobre os recursos para que esses recursos permaneçam em conformidade com seus padrões corporativos e contratos de nível de serviço.
- O Azure Policy avalia os recursos, verificando aqueles que não são compatíveis com suas políticas.
- Por exemplo, você poderia criar uma política que permite que apenas um tamanho de SKU específico para VMs em seu ambiente. O Azure Policy avaliará essa configuração durante a criação e a atualização de recursos e ao verificar os recursos existentes. 
- O Azure fornece algumas políticas internas para você atribuir, mas você também pode criar suas próprias políticas.


![Azure Policy](./media/migrate-best-practices-security-management/policy.png)
*Azure Policy*

**Saiba mais:**
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/governance/policy/overview) do Azure Policy.
- [Saiba mais sobre](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)como criar e gerenciar políticas para impor conformidade.


## <a name="best-practice-implement-a-bcdr-strategy"></a>Melhor prática: implementar uma estratégia de BCDR

O planejamento de BCDR (continuidade dos negócios e recuperação de desastres) é um exercício crítico que deve ser concluído durante o planejamento da migração para o Azure. Em termos jurídicos, seu contrato inclui uma cláusula de força maior que exclui obrigações não cumpridas por conta de casos de força maior, como furacões e terremotos. No entanto, você também tem a obrigação de garantir que os serviços continuarão a ser executados e serão recuperados sempre que necessário durante um desastre. Sua capacidade de fazer isso pode significar o sucesso ou o fim de sua empresa.

De maneira geral, sua estratégia de BCDR deve considerar:
- **Backup de dados**: como manter seus dados seguros para que você possa recuperá-los facilmente em caso de falhas.
- **Recuperação de desastre**: como manter seus aplicativos resilientes e disponíveis em caso de paralisações. 

### <a name="azure-resiliency-features"></a>Recursos de resiliência do Azure
A plataforma Azure fornece uma série de recursos de resiliência.

- **Emparelhamento de região**: o Azure emparelha regiões para fornecer proteção regional dentro dos limites da residência dos dados. O Azure garante o isolamento físico entre pares de regiões, prioriza a recuperação de uma região do par em caso de interrupção ampla, implanta atualizações de sistema separadamente em cada região e permite que recursos como o armazenamento com redundância geográfica do Azure seja replicado entre pares regionais.
- **Zonas de disponibilidade**: As zonas de disponibilidade protegem contra falhas de um data center do Azure inteiro estabelecendo zonas físicas separadas em uma região do Azure. Cada zona tem uma fonte de energia, uma infraestrutura de rede e um mecanismo de resfriamento diferentes.
- **Conjuntos de disponibilidade**: os conjuntos de disponibilidade protegem contra falhas em um data center. Agrupe VMs em conjuntos de disponibilidade para mantê-los altamente disponíveis. Em cada conjunto de disponibilidade, o Azure implementa vários domínios de falha que agrupam hardwares subjacentes com uma fonte de alimentação e um comutador de rede em comum, assim como domínios de atualização que agrupam hardware subjacentes que possam passar por manutenção ou ser reinicializados ao mesmo tempo. Por exemplo, quando uma carga de trabalho é distribuída entre VMs do Azure, você pode colocar duas ou mais VMs para cada camada de aplicativo em um conjunto. Por exemplo, você pode colocar as VMs de front-end em um conjunto e as VMs de camada de dados em outro. Como somente um domínio de atualização é reinicializado por vez em um conjunto, e o Azure faz com que as VMs em um conjunto sejam distribuídas entre domínios de falha, você garante que nem todas as VMs em um conjunto falhem ao mesmo tempo.

### <a name="set-up-bcdr"></a>Configurar a BCDR

Ao migrar para o Azure, é importante entender que, embora a plataforma Azure forneça esses recursos de resiliência embutidos, você precisará projetar sua implantação do Azure para tirar proveito dos recursos e serviços do Azure que fornecem alta disponibilidade, recuperação de desastre e backup.

- Sua solução de BCDR dependerá dos objetivos da empresa e será influenciada por sua estratégia de implantação do Azure. As implantações de IaaS (Infraestrutura como Serviço) e PaaS (Plataforma como Serviço) apresentam desafios diferentes de BCDR.
- Uma vez em vigor, suas soluções de BCDR devem ser testadas regularmente para verificar que a estratégia permanece viável.


## <a name="best-practice-back-up-your-data"></a>Melhor prática: Fazer backup dos dados

Na maioria dos casos, uma carga de trabalho local é desativada após a migração e sua estratégia local para fazer backup dos dados precisa ser estendida ou substituída. Se você migrar todo o data center para o Azure, precisará projetar e implementar uma solução de backup completa usando as tecnologias do Azure ou soluções integradas de terceiros. 


### <a name="back-up-an-iaas-deployment"></a>Fazer backup de uma implantação de IaaS

Para cargas de trabalho em execução em VMs IaaS do Azure, considere estas soluções de backup:

- **Backup do Azure**: fornece backups consistentes com o aplicativo para VMs Linux e Windows do Azure.
- **Instantâneos de armazenamento**: tire instantâneos do armazenamento de blobs.

#### <a name="azure-backup"></a>Serviço de Backup do Azure

O Backup do Azure cria pontos de recuperação de dados que são armazenados no armazenamento do Azure. O Backup do Azure pode fazer backup de discos de VM do Azure e arquivos do Azure (versão prévia). Os Arquivos do Azure oferecem compartilhamentos de arquivos na nuvem, que podem ser acessados por meio de SMB.
   
Você pode usar o Backup do Azure para fazer backup de VMs de duas maneiras.

- **Backup direto das configurações de VM**: você pode fazer backup de VMs com o Backup do Azure diretamente nas opções de VM no portal do Azure. Você pode fazer backup da VM uma vez por dia e restaurar o disco da VM conforme necessário. O Backup do Azure usa instantâneos de dados com reconhecimento de aplicativo (VSS), mas nenhum agente é instalado na VM.
- **Backup direto em um cofre dos Serviços de Recuperação**: você pode fazer backup de VMs IaaS implantando um cofre dos Serviços de Recuperação do Backup do Azure. Isso fornece um local único para controlar e gerenciar backups e fornece opções de backup e restauração granulares. O Backup ocorre até três vezes por dia, no nível de arquivo/pasta. Ele não tem reconhecimento de aplicativo e não tem suporte para Linux. Você precisa instalar o agente MARS (Serviços de Recuperação do Microsoft Azure) em cada VM que terá o backup feito.
- **Servidor de Backup do Azure: proteja a VM para o Servidor de Backup do Azure**: O Servidor de Backup do Azure é fornecido gratuitamente com o Backup do Azure. O backup da VM é feito no armazenamento local do Servidor de Backup do Azure. Depois disso, você faz o backup do Servidor de Backup do Azure no Azure em um cofre. O backup tem reconhecimento de aplicativo com granularidade completa sobre a frequência e a retenção de backup. Você pode fazer backup no nível do aplicativo. Por exemplo, fazendo backup do SQL Server ou do SharePoint.

Para segurança, o Backup do Azure criptografa os dados em trânsito usando AES 256 e os envia por HTTPS para o Azure. Os dados de backup em repouso no Azure são criptografados com [SSE (Criptografia do Serviço de Armazenamento)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) e os dados para armazenamento e transmissão.


![Backup do Azure](./media/migrate-best-practices-security-management/iaas-backup.png)
*Backup do Azure*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup) diferentes tipos de backups.
- [Planejar uma infraestrutura de backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-introduction) para VMs do Azure.

#### <a name="storage-snapshots"></a>Instantâneos de armazenamento

As VMs do Azure são armazenadas como blobs de páginas no Armazenamento do Azure. 

- Os instantâneos capturam o estado do blob em um momento específico.
- Como método alternativo de backup para discos de VM do Azure, você pode capturar um instantâneo de blobs de armazenamento e copiá-lo para outra conta de armazenamento. 
- Você pode copiar um blob inteiro ou usar uma cópia de instantâneo incremental para copiar somente alterações delta e reduzir o espaço de armazenamento.
- Como precaução extra, você pode habilitar a exclusão reversível para contas de armazenamento de blobs. Com esse recurso habilitado, um blob excluído é marcado para exclusão, mas não é limpo imediatamente. Durante o período intermediário, o blob pode ser restaurado.

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) o Armazenamento de Blobs do Azure.
- [Saiba](https://docs.microsoft.com/azure/storage/blobs/storage-blob-snapshots) como criar um instantâneo de blob.
- [Reveja um cenário de exemplo](https://azure.microsoft.com/blog/microsoft-azure-block-blob-storage-backup) para o backup de armazenamento de blobs.
- [Leia sobre](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) exclusão reversível.
- [Recuperação de desastre e failover forçado (versão prévia) no Armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

#### <a name="third-party-backup"></a>Backup de terceiros

Além disso, você pode usar soluções de terceiros para fazer backup de VMs do Azure e contêineres de armazenamento para o armazenamento local ou para outros provedores de nuvem. [Saiba mais](https://azuremarketplace.microsoft.com/marketplace/apps?search=backup&page=1) sobre soluções de backup no Azure Marketplace. 


### <a name="back-up-a-paas-deployment"></a>Fazer backup de uma implantação de PaaS


Ao contrário do IaaS, na qual você gerencia suas próprias VMs e infraestrutura, em um modelo de PaaS a plataforma e a infraestrutura são gerenciadas pelo provedor, permitindo que você se concentre na lógica de aplicativo e nas funcionalidades principais. Com tantos tipos de serviços de PaaS diferentes, cada serviço será avaliado individualmente em relação à finalidade do backup. Vamos examinar dois serviços PaaS do Azure comuns: o Banco de Dados SQL do Azure e o Azure Functions.

#### <a name="back-up-azure-sql-database"></a>Fazer backup do Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure é um mecanismo de Banco de Dados de PaaS totalmente gerenciado. Ele fornece uma série de recursos de continuidade dos negócios, inclusive a automatização do backup.

- O Banco de Dados SQL executa automaticamente backups de banco de dados completos semanais e backups diferenciais a cada 12 horas. Os backups de log de transações são feitos a cada cinco a dez minutos para proteger o banco de dados contra perda de dados.
- Os backups são transparentes e não incorrem em custos adicionais.
- Os backups são armazenados no RA-GRS para redundância geográfica e replicados para a região geográfica emparelhada.
- A retenção do backup depende do modelo de compra. As camadas de serviço baseadas em DTU têm desde sete dias para a camada básica a 35 dias para outras camadas.
- Você pode restaurar um banco de dados para um ponto no tempo dentro do período de retenção. Você também pode restaurar um banco de dados, restaurar para uma região geográfica diferente ou restaurar de um backup de longo prazo se o banco de dados tem uma política de LTR (retenção de longo prazo).


![Backup do SQL do Azure](./media/migrate-best-practices-security-management/sql-backup.png)
*Backup do SQL do Azure*

**Saiba mais:**
- [Backups automáticos](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) para o Banco de Dados SQL.
- [Recuperar um banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups) usando backups automáticos.

 
#### <a name="back-up-azure-functions"></a>Fazer backup do Azure Functions

Uma vez que o Azure Functions funciona mais ou menos como código, você deve fazer backup dele usando os mesmos métodos usados para proteger código, como o controle do código-fonte no GitHub ou serviços do Azure DevOps

**Saiba mais:**

- [Proteção de dados](/azure/devops/organizations/security/data-protection) para operações do Azure DevOps.

## <a name="best-practice-set-up-disaster-recovery"></a>Melhor prática: Configurar recuperação de desastre 

Além de proteger dados, o planejamento de BCDR deve considerar como manter os aplicativos e cargas de trabalho disponíveis em caso de desastre. 

### <a name="set-up-disaster-recovery-for-iaas-apps"></a>Configurar a recuperação de desastre para aplicativos de IaaS

Para cargas de trabalho em execução em VMs IaaS do Azure e no armazenamento do Azure, considere estas soluções:

- **Azure Site Recovery**: coordena a replicação de VMs do Azure em uma região primária para uma região secundária. Quando ocorrem paralisações, faça failover da região primária para a secundária para que os usuários possam continuar a acessar os aplicativos. Quando as coisas estão de volta ao normal, faça failback para a região primária.
- **Armazenamento do Azure**: o Azure fornece resiliência interna e alta disponibilidade para diferentes tipos de armazenamento:

#### <a name="azure-site-recovery"></a>Azure Site Recovery 

O Azure Site Recovery é o principal serviço do Azure para fazer com que as VMs do Azure possam ser colocadas online e os aplicativos VM possam ser disponibilizados quando ocorrem paralisações. O Site Recovery replica as VMs de uma região primária para uma região secundária do Azure. Quando ocorre um desastre, faça failover das VMs da região primária e continue a acessá-las normalmente na região secundária. Quando as operações voltarem ao normal, você poderá fazer failback das VMs para a região primária.


![Azure Site Recovery](./media/migrate-best-practices-security-management/site-recovery.png)
*Site Recovery*

**Saiba mais:**
- [Reveja](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-disaster-recovery-guidance) os cenários de recuperação de desastre para VMs do Azure.
- [Saiba como](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-replicate-after-migration) configurar a recuperação de desastre para uma VM do Azure após a migração.

#### <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento do Azure é replicado para resiliência interna e alta disponibilidade.

-   **GRS (Armazenamento com redundância geográfica)**: protege contra uma interrupção em toda a região, com pelo menos 99,99999999999999% (16 noves) de durabilidade dos objetos em determinado ano.
    - Os dados de armazenamento são replicados para a região secundária com a qual sua região primária está emparelhada.
    - Se a região primária ficar inativa e a Microsoft iniciar um failover para a região secundária, você terá acesso de leitura aos seus dados.
- **RA-GRS (Armazenamento com redundância geográfica com acesso de leitura)**: protege contra uma interrupção em toda a região.
    - Os dados de armazenamento são replicados para a região secundária.
    - Você tem acesso de leitura aos dados replicados na região secundária garantido, independentemente de a Microsoft iniciar um failover ou não. Quando dois ou mais data centers na mesma região possam ter um problema, mas seus dados ainda estão disponíveis em uma região geográfica separada.
-   **ZRS (Armazenamento com redundância de zona)**:  protege contra falhas do data center.
    - O ZRS replica dados de forma síncrona entre três clusters de armazenamento em uma única região. Os clusters estão fisicamente separados e cada um fica localizado em sua própria zona de disponibilidade.
    - Se ocorrer um desastre, seu armazenamento ainda estará disponível. O ZRS é o mínimo necessário para cargas de trabalho de missão crítica.



**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/storage/common/storage-redundancy) replicação do Armazenamento do Azure.


### <a name="set-up-disaster-recovery-for-paas-workloads"></a>Configurar a recuperação de desastre para cargas de trabalho de PaaS

Vamos considerar opções de recuperação de desastre para nossos exemplos de carga de trabalho de PaaS.

#### <a name="disaster-recovery-of-azure-sql-server"></a>Recuperação de desastre do Azure SQL Server

Há várias opções diferentes, cada uma com impacto no custo, no tempo de recuperação e na perda de dados.

Você pode usar grupos de failover e replicação geográfica ativa para fornecer resiliência contra falhas catastróficas e interrupções regionais

- **Replicação geográfica ativa**: implante a replicação geográfica ativa para recuperação de desastre rápida em caso de uma interrupção do data center ou uma conexão que não possa ser feita com um banco de dados primário.
    - A replicação geográfica cria continuamente réplicas legíveis do banco de dados em até quatro secundários nas mesmas regiões ou em regiões diferentes.
    - Em uma interrupção, faça failover para uma das regiões secundárias e coloque o banco de dados online novamente.
- **Grupos de failover automático**: os grupos de failover automático estendem a replicação geográfica ativa com failover transparente de vários bancos de dados.
    - Um grupo de failover automático fornece uma abstração eficaz da replicação geográfica ativa com failover automático e replicação de banco de dados no nível do grupo.
    - Você cria um grupo de failover que contém um servidor primário hospedando um ou mais bancos de dados primários, um servidor secundário hospedando réplicas somente leitura dos bancos de dados primários, ouvintes que apontam para cada servidor, e uma política de failover automático.
    - Os pontos de extremidade do ouvinte especificado eliminam a necessidade de alterar a cadeia de conexão de SQL após o failover.
- **Replicação geográfica**: 
    -   a replicação geográfica permite que você recupere o banco de dados para uma região diferente. O backup automatizado de todos os bancos de dados do Azure será replicado para uma região secundária em segundo plano. Ele sempre vai restaurar o banco de dados da cópia dos arquivos de backup armazenados na região secundária.
-   Os **bancos de dados com redundância de zona** fornecem suporte interno para as zonas de disponibilidade do Azure.
    - Os bancos de dados com redundância de zona melhoram a alta disponibilidade do Azure SQL Server em caso de falha do data center.
    - Com redundância de zona, você pode colocar as réplicas de banco de dados redundantes em diferentes zonas de disponibilidade em uma região. 



![Replicação geográfica](./media/migrate-best-practices-security-management/geo-replication.png)
*Replicação geográfica*

**Saiba mais:**
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability) alta disponibilidade para o Azure SQL Server.
- [Leia](https://azure.microsoft.com/blog/azure-sql-databases-disaster-recovery-101/) Noções básicas de Banco de Dados SQL do Azure para recuperação de desastre.
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) dos grupos de failover e da replicação geográfica ativa.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery) como projetar levando em conta a recuperação de desastre.
- [Obtenha as melhores práticas](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) para grupos de failover.
- [Obtenha as melhores práticas](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-security-config) de segurança após uma restauração geográfica ou um failover.
- [Saiba mais sobre](https://docs.microsoft.com/azure/sql-database/sql-database-high-availability#zone-redundant-configuration) redundância de zona
- [Saiba como](https://docs.microsoft.com/azure/sql-database/sql-database-disaster-recovery-drills) executar uma simulação de recuperação de desastre para o banco de dados SQL.

### <a name="disaster-recovery-for-azure-functions"></a>Recuperação de desastre para o Azure Functions

Se a infraestrutura de computação no Azure falhar, um aplicativo de funções do Azure poderá se tornar indisponível.

- Para minimizar a possibilidade desse tempo de inatividade, use dois aplicativos de funções implantados em regiões diferentes.
- O Gerenciador de Tráfego do Azure pode ser configurado para detectar problemas no aplicativo de funções principal e redirecionar automaticamente o tráfego para o aplicativo de funções na região secundária
- O Gerenciador de Tráfego com armazenamento com redundância geográfica permite que você tenha a mesma função em várias regiões em caso de falha regional


![Gerenciador de Tráfego](./media/migrate-best-practices-security-management/traffic-manager.png)
*Gerenciador de Tráfego*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) recuperação de desastre para aplicativos do Azure.
- [Saiba mais](https://docs.microsoft.com/azure/azure-functions/durable/durable-functions-disaster-recovery-geo-distribution) sobre recuperação de desastre e distribuição geográfica para funções do Azure duráveis.


## <a name="best-practice-use-managed-disks-and-availability-sets"></a>Melhor prática: usar discos gerenciados e conjuntos de disponibilidade

O Azure usa conjuntos de disponibilidade para agrupar as VMs logicamente e isolar as VMs em um conjunto de outros recursos. As VMs no conjunto de disponibilidade são distribuídas entre vários domínios de falha com subsistemas separados, para proteção contra falhas locais, e também são distribuídas entre vários domínios de atualização, de modo que nem todas as VMs em um conjunto reiniciam ao mesmo tempo.

O Azure Managed Disks simplifica o gerenciamento de discos para VMs IaaS do Azure por meio do gerenciamento de contas de armazenamento associadas aos discos de VM.

- Recomendamos que você use discos gerenciados sempre que possível. Você só precisa especificar o tipo de armazenamento que deseja usar e o tamanho do disco necessário, em seguida, o Azure cria e gerencia o disco para você em segundo plano.
- Você pode converter discos existentes em gerenciados.
- Você deve criar VMs em conjuntos de disponibilidade para ter alta resiliência e disponibilidade. Quando ocorrem paralisações, planejadas ou não, os conjuntos de disponibilidade fazem com que pelo menos uma das VMs no conjunto permaneça disponível.


![Discos gerenciados](./media/migrate-best-practices-security-management/managed-disks.png)
*Discos gerenciados*

**Saiba mais:**
- [Obtenha uma visão geral](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) sobre discos gerenciados.
- [Saiba mais sobre](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks) como converter discos gerenciados.
- [Saiba como](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) gerenciar a disponibilidade de VMs Windows no Azure.


## <a name="best-practice-monitor-resource-usage-and-performance"></a>Melhor prática: monitorar o desempenho e o uso de recursos 

Você pode ter movido suas cargas de trabalho para o Azure por causa de seus grandes recursos de dimensionamento. No entanto, mover a carga de trabalho não significa que o Azure implementará o dimensionamento automaticamente sem sua indicação. Por exemplo:

- Se sua organização de marketing envia um novo anúncio de TV que gera 300% a mais de tráfego, isso pode causar problemas de disponibilidade do site. Sua carga de trabalho recém-migrada poderá atingir limites atribuídos e falhar.
- Outro exemplo pode ser um ataque de DDoS (negação de serviço distribuído) na carga de trabalho migrada. Nesse caso, talvez seja melhor não dimensionar e, sim, impedir que a origem dos ataques alcance os recursos.

Esses dois casos tem resoluções diferentes, mas para ambos você precisa de uma visão geral do que está acontecendo com o monitoramento de desempenho e uso.

- O Azure Monitor pode ajudar a revelar essas métricas e a fornecer uma resposta com alertas, dimensionamento automático, hubs de eventos, aplicativos lógicos e muito mais.
- Além do monitoramento do Azure, você pode integrar seu aplicativo de SIEM de terceiros para monitorar os logs do Azure em relação a eventos de auditoria e de desempenho.


![Azure Monitor](./media/migrate-best-practices-security-management/monitor.png)
*Azure Monitor*

**Saiba mais:**

- [Saiba mais](https://docs.microsoft.com/azure/azure-monitor/overview) sobre o Azure Monitor.
- [Obtenha as melhores práticas](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) para monitoramento e diagnóstico.
- [Saiba mais sobre](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) dimensionamento automático.
- [Saiba como](https://docs.microsoft.com/azure/security-center/security-center-export-data-to-siem) rotear dados do Azure para uma ferramenta SIEM.

## <a name="best-practice-enable-diagnostic-logging"></a>Melhor prática: Habilitar registro em log de diagnóstico

Os recursos do Azure geram um número razoável de métricas de registro em log e dados telemétricos.

- Por padrão, a maioria dos tipos de recurso não tem o log de diagnóstico habilitado.
- Ao habilitar o log de diagnóstico em seus recursos, você poderá consultar os dados de registro em log e criar alertas e guias estratégicos com base neles.
- Quando você habilita o log de diagnóstico, cada recurso ganha um conjunto específico de categorias. Selecione uma ou mais categorias de registro em log e um local para os dados de log. Os logs podem ser enviados para uma conta de armazenamento, hub de eventos ou logs do Azure Monitor. 


![Registro em log de diagnóstico](./media/migrate-best-practices-security-management/diagnostics.png)
*Registro em log de diagnóstico*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) como coletar e consumir dados de log.
- [Saiba o que tem suporte](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema) para registro em log de diagnóstico.


## <a name="best-practice-set-up-alerts-and-playbooks"></a>Melhor prática: configurar alertas e guias estratégicos

Com o log de diagnóstico habilitado para os recursos do Azure, você pode começar a usar dados de registro em log para criar alertas personalizados.

- Os alertas trabalham de forma proativa, mandando notificações quando encontram condições em seus dados de monitoramento. Com isso, você pode lidar com problemas antes que os usuários do sistema os percebam. Você pode gerar alertas sobre itens como valores de métrica, consultas de pesquisa de log, eventos de log de atividades, integridade de plataforma e disponibilidade do site.
- Quando os alertas são disparados, você pode executar um Guia Estratégico de Aplicativo Lógico. Um guia estratégico ajuda você a automatizar e a orquestrar uma resposta a um alerta específico. Os guias estratégicos se baseiam nos Aplicativos Lógicos do Azure. Você pode usar modelos de Aplicativo Lógico para criar guias estratégicos ou criar seus próprios.
- Como um exemplo simples, você pode criar um alerta que dispara quando uma verificação de porta é feita em relação a um grupo de segurança de rede.  Você pode configurar um guia estratégico que é executado e bloqueia o endereço IP de origem da verificação.
- Outro exemplo pode ser um aplicativo com perda de memória.  Quando o uso da memória chega a um determinado ponto, um guia estratégico pode reciclar o processo.


![Alertas](./media/migrate-best-practices-security-management/alerts.png)
*Alertas*

**Saiba mais:**
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts) sobre os alertas.
- [Saiba mais sobre](https://docs.microsoft.com/azure/security-center/security-center-playbooks) guias estratégicos de segurança que respondem a alertas da Central de Segurança.

## <a name="best-practice-use-the-azure-dashboard"></a>Melhor prática: usar o painel do Azure

O portal do Azure é um console unificado e baseado na Web que permite compilar, gerenciar e monitorar tudo, desde aplicativos Web simples até aplicativos complexos na nuvem. Ele inclui um painel personalizável e opções de acessibilidade.
- Você pode criar vários painéis e compartilhá-los com outras pessoas que tenham acesso às suas assinaturas do Azure.
- Com esse modelo compartilhado, sua equipe consegue ver o ambiente do Azure, o que permite que eles sejam proativos ao gerenciar sistemas na nuvem.


![Painel do Azure](./media/migrate-best-practices-security-management/dashboard.png)
*Painel do Azure*

**Saiba mais:**

- [Saiba como](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) criar um painel.
- [Saiba mais sobre](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards-structure) estrutura de painéis.


## <a name="best-practice-understand-support-plans"></a>Melhor prática: entender os planos de suporte

Em algum momento, você precisará colaborar com sua equipe de suporte ou a equipe de suporte da Microsoft. É essencial ter um conjunto de políticas e procedimentos de suporte para cenários como recuperação de desastre. Além disso, sua equipe de suporte e os administradores devem receber treinamento sobre a implementação dessas políticas.

- Na remota hipótese de um problema no serviço do Azure afetar sua carga de trabalho, os administradores devem saber como enviar um tíquete de suporte para a Microsoft da forma mais apropriada e eficiente.
- Familiarize-se com os vários planos de suporte oferecidos para o Azure. Eles vão desde tempos de resposta dedicados para instâncias de desenvolvedor a suporte Premier com um tempo de resposta de menos de 15 minutos.


![Planos de suporte](./media/migrate-best-practices-security-management/support.png)
*Planos de suporte*

**Saiba mais:**
- [Obtenha uma visão geral](https://azure.microsoft.com/support/options/) dos planos de suporte do Azure.
- [Saiba mais](https://azure.microsoft.com/support/legal/sla/) sobre os SLA (contratos de nível de serviço).

## <a name="best-practice-manage-updates"></a>Melhor prática: Gerenciar atualizações

Manter as VMs do Azure atualizadas com o sistema operacional e as atualizações de software mais recentes é uma tarefa árdua. A capacidade de revelar todas as VMs para descobrir de quais atualizações elas precisam e efetuar push automaticamente dessas atualizações é extremamente valiosa.

- Você pode usar o Gerenciamento de Atualizações na Automação do Azure a fim de gerenciar atualizações do sistema operacional para computadores executando Windows e Linux implantados no Azure, localmente e em outros provedores de nuvem. 
- Use o Gerenciamento de Atualizações para avaliar rapidamente o status das atualizações disponíveis em todos os computadores agentes e gerenciar a instalação da atualização.
- Você pode habilitar o Gerenciamento de Atualizações para VMs diretamente de uma conta da Automação do Azure. Você também pode atualizar uma única VM na página de VMs no portal do Azure.
- Além disso, as VMs do Azure podem ser registradas no System Center Configuration Manager. Você pode, em seguida, migrar a carga de trabalho do Configuration Manager para o Azure e fazer atualizações de software e relatórios em uma única interface da Web.


![Atualizações de VM](./media/migrate-best-practices-security-management/updates.png)
*Atualizações*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/automation/automation-update-management) gerenciamento de atualizações no Azure.
- [Saiba como](https://docs.microsoft.com/azure/automation/oms-solution-updatemgmt-sccmintegration) integrar o Configuration Manager com o gerenciamento de atualizações.
- [Perguntas frequentes](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure) sobre o Configuration Manager no Azure.


## <a name="implement-a-change-management-process"></a>Implementar um processo de gerenciamento de alterações

Assim como em qualquer sistema de produção, fazer alterações de qualquer tipo pode afetar seu ambiente. Um processo de gerenciamento de alterações que exige o envio de solicitações para a realização das alterações em sistemas de produção é uma adição valiosa ao ambiente migrado.

- Você pode criar estruturas de melhores práticas para gerenciamento de alterações a fim de aumentar a conscientização da equipe de suporte e dos administradores.
- Você pode usar a Automação do Azure para ajudar no gerenciamento de configuração e no controle de alterações dos fluxos de trabalho migrados.
- Ao impor o processo de gerenciamento de alterações, você poderá usar logs de auditoria para vincular os logs de alterações do Azure a solicitações de alteração supostamente (ou não) existentes. Assim, se você vir uma alteração feita sem uma solicitação de alteração correspondente, poderá investigar o que deu errado no processo.

O Azure tem uma solução de Controle de Alterações na Automação do Azure:

- A solução rastreia as alterações no software e nos arquivos Windows e Linux, nas chaves de registro do Windows, nos serviços Windows e nos daemons do Linux.
- Alterações nos servidores monitorados são enviadas para o serviço do Azure Monitor na nuvem para processamento.
- A lógica é aplicada aos dados recebidos e o serviço de nuvem registra os dados.
- No painel Controle de Alterações, você pode ver facilmente as alterações feitas à sua infraestrutura de servidor.


![Gerenciamento de alterações](./media/migrate-best-practices-security-management/change.png)
*Gerenciamento de alterações*

**Saiba mais:**

- [Saiba mais sobre](https://docs.microsoft.com/azure/automation/automation-change-tracking) Controle de Alterações.
- [Saiba mais sobre](https://docs.microsoft.com/azure/automation/automation-intro) recursos da Automação do Azure.




## <a name="next-steps"></a>Próximas etapas 

Examine outras melhores práticas:


- [Melhores práticas](migrate-best-practices-networking.md) de rede após a migração.
- [Melhores práticas](migrate-best-practices-costs.md) de gerenciamento de custos após a migração.








