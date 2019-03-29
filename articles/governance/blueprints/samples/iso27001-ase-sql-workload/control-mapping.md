---
title: Exemplo – ISO 27001 diagrama - mapeamento de controle
description: Mapeamento de controle de exemplo de plano gráfico ISO 27001.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: c062759938652518ac3cafff64973050554ca19d
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58579692"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Mapeamento de controle do exemplo de especificações técnicas do Azure BluePrint ISO 27001

O artigo a seguir fornece detalhes sobre como o exemplo de plano gráfico da carga de trabalho do Azure BluePrint ISO 27001 ASE/SQL é mapeada para os controles do ISO 27001. Para obter mais informações sobre os controles, consulte [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos são para o **ISO 27001:2013** controles. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com um [política do Azure](../../../policy/overview.md) iniciativa. Para revisar a iniciativa concluída, abra **diretiva** no portal do Azure e selecione o **definições** página. Em seguida, localize e selecione o  **[visualização] auditoria ISO 27001:2013 controla e implantar extensões de VM específicas para dar suporte a requisitos de auditoria** iniciativa de política interna.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 segregação de tarefas

Não permite que tendo apenas um proprietário de assinatura do Azure para redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometido. Esta especificação técnica ajuda a manter um número apropriado de proprietários de assinatura do Azure, atribuindo dois [política do Azure](../../../policy/overview.md) definições que o número de proprietários de assinaturas do Azure de auditoria. Gerenciando permissões de proprietário de assinatura pode ajudá-lo a implementar apropriada separação de funções.

- [Visualização]: Audit minimum number of owners for subscription
- [Visualização]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 acesso às redes e serviços de rede

Azure implementa [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) (RBAC) para gerenciar quem tem acesso aos recursos do Azure. Esta especificação técnica ajuda a controlar o acesso aos recursos do Azure por meio da atribuição sete [política do Azure](../../../policy/overview.md) definições. Essas políticas auditar o uso de tipos de recursos e configurações que podem permitir que mais permissivas do acessem aos recursos.
Recursos de compreensão que estão violando essas políticas podem ajuda você tomar ações corretivas para garantir o acesso a recursos do Azure é restrita a usuários autorizados.

- [Visualização]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Visualização]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Senhass
- [Visualização]: Audit Linux VM accounts with no passwords
- [Visualização]: Audit Linux VM allowing remote connections from accounts with no passwords
- Auditar o uso de contas de armazenamento clássicas
- Auditar o uso de máquinas virtuais clássicas
- Auditar VMs que não usam discos gerenciados

## <a name="a922-user-access-provisioning"></a>O provisionamento de acesso de usuário A.9.2.2

Azure implementa [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) (RBAC) para gerenciar quem tem acesso aos recursos do Azure. Esta especificação técnica atribui três [política do Azure](../../../policy/overview.md) usam definições para auditar [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) autenticação para servidores SQL e [do Service Fabric](../../../../service-fabric/service-fabric-overview.md). Usando o Azure Active Directory autenticação que permite o gerenciamento de permissões simplificado e gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft. Esta especificação técnica também atribui uma definição de política do Azure para auditar o uso de regras RBAC personalizadas. Noções básicas sobre onde as regras RBAC personalizadas são implementar pode ajudar a verificar necessidade e a implementação apropriada, como regras RBAC personalizadas estão sujeitas a erros.

- Provisionamento de auditoria de um administrador do Azure Active Directory Domain Services para o SQL Server
- Auditar o uso do Azure Active Directory para autenticação de cliente no Service Fabric
- Auditar o uso de regras personalizadas do RBAC

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 gerenciamento de privilegiado direitos de acesso

Esta especificação técnica o ajuda a restringir e controlar os direitos de acesso privilegiado atribuindo quatro [política do Azure](../../../policy/overview.md) definições para auditar contas externas com o proprietário e/ou contas e permissões de gravação com o proprietário e/ou permissões de gravação que não têm a autenticação multifator habilitada.

- [Visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Visualização]: Audit external accounts with owner permissions on a subscription
- [Visualização]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Gerenciamento de A.9.2.4 secretas das informações de autenticação de usuários

Esta especificação técnica atribui três [política do Azure](../../../policy/overview.md) definições para auditar contas que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma parte das informações de autenticação seja comprometida. Monitorando contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de comprometimento. Esta especificação técnica também atribui duas definições de política do Azure que a VM do Linux de auditoria permissões de arquivo de senha para alertar se eles são definidos incorretamente. Essa configuração permite que você tomar uma ação corretiva para garantir que autenticadores não estão comprometidos.

- [Visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Visualização]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Visualização]: Deploy VM extension to audit Linux VM passwd file permissions
- [Visualização]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Revisão A.9.2.5 dos direitos de acesso do usuário

Azure implementa [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) (RBAC) para ajuda a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso a recursos do Azure e suas permissões. Esta especificação técnica atribui quatro [política do Azure](../../../policy/overview.md) definições para auditar as contas que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas.

- [Visualização]: Audit deprecated accounts on a subscription
- [Visualização]: Audit deprecated accounts with owner permissions on a subscription
- [Visualização]: Audit external accounts with owner permissions on a subscription
- [Visualização]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Remoção de A.9.2.6 ou ajuste de direitos de acesso

Azure implementa [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) (RBAC) para ajuda a gerenciar quem tem acesso aos recursos no Azure. Usando o [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e RBAC, você pode atualizar as funções de usuário para refletir alterações organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), que remove imediatamente os direitos de acesso aos recursos do Azure. Esta especificação técnica atribui dois [política do Azure](../../../policy/overview.md) definições para auditar conta depreciada que deve ser considerada para remoção.

- [Visualização]: Audit deprecated accounts on a subscription
- [Visualização]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>Sistema de gerenciamento de senha A.9.4.3

Esta especificação técnica o ajuda a impor senhas fortes, atribuindo 10 [política do Azure](../../../policy/overview.md) definições de auditar VMs do Windows que não impõem o nível mínimo e outros requisitos de senha. Reconhecimento de VMs em violação da política de força de senha ajuda você a tomar ações corretivas para garantir que as senhas para todas as contas de usuário VM estão em conformidade com a política.

- [Visualização]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Visualização]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Visualização]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Visualização]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Visualização]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Visualização]: Audit Windows VM enforces password complexity requirements
- [Visualização]: Audit Windows VM maximum password age 70 days
- [Visualização]: Audit Windows VM minimum password age 1 day
- [Visualização]: Audit Windows VM passwords must be at least 14 characters
- [Visualização]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Política A.10.1.1 sobre o uso de controles de criptografia

Esta especificação técnica o ajuda a impor sua política sobre o uso de controles cryptograph atribuindo 13 [política do Azure](../../../policy/overview.md) definições que impõem controles cryptograph específico e auditoria usam das configurações de criptografia fracas.
Noções básicas sobre onde os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esta especificação técnica exigem criptografia para as contas de armazenamento de BLOBs e contas de armazenamento do data lake; exigir criptografia transparente de dados em bancos de dados SQL; auditoria faltando criptografia em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação; auditar as conexões não seguras para contas de armazenamento, aplicativos de funções, aplicativo Web, aplicativos de API e o Cache Redis; auditar criptografia de senha de máquina de virtual fraco; e a auditoria de comunicação não criptografada do Service Fabric.

- [Visualização]: Audit HTTPS only access for a Function App
- [Visualização]: Audit HTTPS only access for a Web Application
- [Visualização]: Audit HTTPS only access for an API App
- [Visualização]: Audit missing blob encryption for storage accounts
- [Visualização]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  criptografian
- [Visualização]: Audit Windows VM should not store passwords using reversible encryption
- [Visualização]: Monitor unencrypted SQL database in Azure Security Center
- [Visualização]: Monitor unencrypted VM Disks in Azure Security Center
- Auditar a habilitação de criptografia de variáveis de conta de automação do Azure
- Auditoria de habilitação de somente conexões seguras ao seu Cache Redis
- Auditar transferência segura para contas de armazenamento
- Auditar a configuração da propriedade ClusterProtectionLevel para EncryptAndSign no Service Fabric
- Auditar o status da Transparent Data Encryption

## <a name="a1241-event-logging"></a>Log de eventos A.12.4.1

Esta especificação técnica ajuda a garantir que os eventos de sistema são registrados por meio da atribuição sete [política do Azure](../../../policy/overview.md) definições de configurações de auditoria log em recursos do Azure. Uma política atribuída também audita se as máquinas virtuais não estão enviando logs para um espaço de trabalho de análise de log especificado.

- [Visualização]: Implantação de agente de dependência de auditoria - VM imagem (SO) não listados
- [Visualização]: Implantação do agente de dependência em VMSS - VM imagem (SO) removido da lista de auditoria
- [Visualização]: Implantação do agente do Log Analytics - VM imagem (SO) removido da lista de auditoria
- [Visualização]: Implantação do agente do Log Analytics em VMSS - VM imagem (SO) removido da lista de auditoria
- [Visualização]: Monitor unaudited SQL database in Azure Security Center
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>Gerenciamento de A.12.1 de vulnerabilidades técnicas

Esta especificação técnica o ajuda a gerenciar vulnerabilidades do sistema de informações por meio da atribuição cinco [política do Azure](../../../policy/overview.md) definições que monitoram as atualizações do sistema, vulnerabilidades do sistema operacional, vulnerabilidades de SQL e máquina virtual ausente vulnerabilidades. Essas informações fornecem informações em tempo real sobre o estado de segurança de seus recursos implantados e podem ajudá-lo a priorizar as ações de correção.

- [Visualização]: Monitor missing Endpoint Protection in Azure Security Center
- [Visualização]: Monitor missing system updates in Azure Security Center
- [Visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>Restrições de A.12.6.2 na instalação de software

Controle de aplicativo adaptáveis é a solução da Central de segurança do Azure que ajuda a controlar quais aplicativos pode ser executados em suas VMs localizadas no Azure. Esta especificação técnica atribui uma definição de política do Azure que monitora as alterações feitas ao conjunto de aplicativos permitidos. Restrições de instalação de software podem ajudar a reduzir a probabilidade de introdução de vulnerabilidades de software.

- [Visualização]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>Controles de rede A.13.1.1

Esta especificação técnica o ajuda a gerenciar e controlar redes atribuindo uma [política do Azure](../../../policy/overview.md) definição que monitora os grupos de segurança de rede com regras permissivos. Regras que são muito permissivas podem permitir o acesso à rede não intencionais e devem ser revisadas.

- [Visualização]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procedimentos e diretivas de transferência de informações de A.13.2.1

O projeto ajuda a garantir a transferência de informações com serviços do Azure é segura, atribuindo dois [política do Azure](../../../policy/overview.md) definições para auditar conexões inseguras para contas de armazenamento e Cache Redis.

- Auditoria de habilitação de somente conexões seguras ao seu Cache Redis
- Auditar transferência segura para contas de armazenamento

## <a name="a1413-protecting-application-services-transactions"></a>As transações dos serviços de aplicativos de proteção A.14.1.3

Esta especificação técnica ajuda a proteger os ativos de sistema de informações por meio da atribuição três [política do Azure](../../../policy/overview.md) definições que monitora os pontos de extremidade desprotegidos, aplicativos e contas de armazenamento. Pontos de extremidade e aplicativos que não estão protegidos por um firewall e contas de armazenamento com acesso irrestrito podem permitir o acesso involuntário às informações contidas dentro do sistema de informações.

- [Visualização]: Monitor unprotected network endpoints in Azure Security Center
- [Visualização]: Monitor unprotected web application in Azure Security Center
- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 vulnerabilidades de segurança de informações de emissão de relatórios

Esta especificação técnica ajuda a manter o reconhecimento de vulnerabilidades do sistema por meio da atribuição cinco [política do Azure](../../../policy/overview.md) definições que monitora a vulnerabilidades, o status de patch e malware alertas na Central de segurança do Azure. Central de segurança do Azure fornece recursos de relatórios que permitem que você tenha uma visão em tempo real sobre o estado de segurança de recursos do Azure implantados.

- [Visualização]: Monitor missing Endpoint Protection in Azure Security Center
- [Visualização]: Monitor missing system updates in Azure Security Center
- [Visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Próximas etapas

Agora que você analisou o mapeamento de controle do exemplo de plano gráfico de carga de trabalho ISO 27001 App Service ambiente/SQL Database, visite os seguintes artigos para saber mais sobre a arquitetura e como implantar este exemplo:

> [!div class="nextstepaction"]
> [Diagrama de carga de trabalho do ISO 27001 App Service ambiente/SQL Database - visão geral](./index.md)
> [carga de trabalho de banco de dados do ISO 27001 App Service ambiente/SQL blueprint - as etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).