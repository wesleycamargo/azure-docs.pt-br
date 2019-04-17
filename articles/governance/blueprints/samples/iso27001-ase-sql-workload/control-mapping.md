---
title: Amostra – Blueprint da carga de trabalho do ASE/SQL ISO 27001 – mapeamento de controle
description: Mapeamento de controle da amostra de blueprint de carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 para o Azure Policy e o RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7a760bfe70fa2a83c43a0b41b77ba9bf45e809ca
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59258600"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapeamento de controle da amostra de blueprint de carga de trabalho do ASE/SQL ISO 27001

O artigo a seguir fornece detalhes sobre como a amostra de blueprint da carga de trabalho do ASE/SQL ISO 27001 do Azure Blueprints é mapeada para os controles ISO 27001. Para obter mais informações sobre os controles, confira [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos referem-se aos controles **ISO 27001:2013**. Use a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa do [Azure Policy](../../../policy/overview.md). Para examinar a iniciativa completa, abra **Política** no portal do Azure e selecione a página **Definições**. Em seguida, localize e selecione os controles de **[Versão prévia] ISO 27001:2013 de Auditoria e implante Extensões de VM específicas para dar suporte aos requisitos de auditoria** da iniciativa de política interna.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Diferenciação de direitos

Ter apenas um proprietário de assinatura do Azure não permite a redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Esse blueprint ajuda você a manter um número apropriado de proprietários de assinatura do Azure por meio da atribuição de duas definições do [Azure Policy](../../../policy/overview.md) que audita o número de proprietários de assinaturas do Azure. O gerenciamento de permissões de proprietário de assinatura pode ajudá-lo a implementar uma diferenciação de direitos apropriada.

- [Visualização]: Audit minimum number of owners for subscription
- [Visualização]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Acesso a redes e serviços de rede

O Azure implementa o [RBAC](../../../../role-based-access-control/overview.md) (controle de acesso baseado em função) para gerenciar quem tem acesso aos recursos no Azure. Esse blueprint ajuda você a controlar o acesso aos recursos do Azure por meio da atribuição de sete definições do [Azure Policy](../../../policy/overview.md). Essas políticas auditam o uso de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos.
Entender os recursos que estão violando essas políticas pode ajudar você a tomar ações corretivas para garantir que o acesso aos recursos do Azure seja restrito a usuários autorizados.

- [Visualização]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Visualização]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  senhass
- [Visualização]: Audit Linux VM accounts with no passwords
- [Visualização]: Audit Linux VM allowing remote connections from accounts with no passwords
- Auditar o uso de contas de armazenamento clássicas
- Auditar o uso de máquinas virtuais clássicas
- Auditar VMs que não usam discos gerenciados

## <a name="a922-user-access-provisioning"></a>A.9.2.2 Provisionamento de acesso do usuário

O Azure implementa o [RBAC](../../../../role-based-access-control/overview.md) (controle de acesso baseado em função) para gerenciar quem tem acesso aos recursos no Azure. Esse blueprint atribui três definições do [Azure Policy](../../../policy/overview.md) para auditar o uso da autenticação do [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) para SQL Servers e o [Service Fabric](../../../../service-fabric/service-fabric-overview.md). O uso da autenticação do Azure Active Directory permite o gerenciamento simplificado de permissões e o gerenciamento centralizado de identidades dos usuários de banco de dados e de outros serviços da Microsoft. Esse blueprint também atribui uma definição do Azure Policy para auditar o uso de regras RBAC personalizadas. Entender o local em que as regras RBAC personalizadas são implementadas pode ajudar você a verificar a necessidade e a implementação apropriada, pois as regras RBAC personalizadas estão sujeitas a erros.

- Provisionamento de auditoria de um administrador do Azure Active Directory Domain Services para o SQL Server
- Auditar o uso do Azure Active Directory para autenticação de cliente no Service Fabric
- Auditar o uso de regras personalizadas do RBAC

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gerenciamento de direitos de acesso privilegiado

Esse blueprint ajuda você a restringir e controlar os direitos de acesso privilegiado por meio da atribuição de quatro definições do [Azure Policy](../../../policy/overview.md) para auditar contas externas com proprietário e/ou permissões de gravação e contas com proprietário e/ou permissões de gravação que não têm a autenticação multifator habilitada.

- [Visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Visualização]: Audit external accounts with owner permissions on a subscription
- [Visualização]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gerenciamento de informações de autenticação secreta de usuários

Esse blueprint atribui três definições do [Azure Policy](../../../policy/overview.md) para auditar contas que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a proteger contas, mesmo que uma única informação de autenticação seja comprometida. Monitorando as contas sem a autenticação multifator habilitada, você pode identificar as contas que têm mais probabilidade de serem comprometidas. Esse blueprint também atribui duas definições do Azure Policy que auditam as permissões de arquivo de senha da VM do Linux para alertar se elas forem definidas incorretamente. Essa configuração permite que você tome uma ação corretiva para garantir que os autenticadores não sejam comprometidos.

- [Visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Visualização]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Visualização]: Deploy VM extension to audit Linux VM passwd file permissions
- [Visualização]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Análise dos direitos de acesso do usuário

O Azure implementa o [RBAC](../../../../role-based-access-control/overview.md) (controle de acesso baseado em função) para ajudar você a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode examinar quem tem acesso aos recursos do Azure e suas permissões. Esse blueprint atribui quatro definições do [Azure Policy](../../../policy/overview.md) para auditar as contas que devem ser priorizadas para análise, incluindo contas preteridas e contas externas com permissões elevadas.

- [Visualização]: Audit deprecated accounts on a subscription
- [Visualização]: Audit deprecated accounts with owner permissions on a subscription
- [Visualização]: Audit external accounts with owner permissions on a subscription
- [Visualização]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Remoção ou ajuste de direitos de acesso

O Azure implementa o [RBAC](../../../../role-based-access-control/overview.md) (controle de acesso baseado em função) para ajudar você a gerenciar quem tem acesso aos recursos no Azure. Usando o [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e o RBAC, você pode atualizar as funções de usuário para que elas reflitam as mudanças organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Esse blueprint atribui duas definições do [Azure Policy](../../../policy/overview.md) para auditar contas preteridas que devem ser consideradas para remoção.

- [Visualização]: Audit deprecated accounts on a subscription
- [Visualização]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>A.9.4.3 Sistema de gerenciamento de senhas

Esse blueprint ajuda você a impor senhas fortes por meio da atribuição de 10 definições do [Azure Policy](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. O reconhecimento de VMs que estejam violando a política de força da senha ajuda você a tomar ações corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

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

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Política sobre o uso de controles de criptografia

Esse blueprint ajuda você a impor sua política sobre o uso de controles de criptografia por meio da atribuição de 13 definições do [Azure Policy](../../../policy/overview.md) que impõem controles de criptografia específicos e auditam o uso de configurações de criptografia fraca.
Entender em que local os recursos do Azure podem ter configurações de criptografia não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos sejam configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse blueprint exigem criptografia para contas de armazenamento de blobs e contas do Data Lake Storage; exigem a Transparent Data Encryption em bancos de dados SQL; auditam a criptografia ausente em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis da conta de automação; auditam conexões não seguras para contas de armazenamento, Aplicativos de funções, Aplicativo Web, Aplicativos de API e o Cache Redis; auditam a criptografia fraca de senhas de máquina virtual; e auditam a comunicação não criptografada do Service Fabric.

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

## <a name="a1241-event-logging"></a>A.12.4.1 Log de eventos

Esse blueprint ajuda você a garantir que os eventos do sistema sejam registrados em log por meio da atribuição de sete definições do [Azure Policy](../../../policy/overview.md) que auditam as configurações do log de auditoria nos recursos do Azure. Uma política atribuída também audita se as máquinas virtuais não estão enviando logs para um workspace especificado do Log Analytics.

- [Versão Prévia]: Auditar a implantação do Dependency Agent – imagem de VM (sistema operacional) não listada
- [Versão Prévia]: Auditar a implantação do Dependency Agent no VMSS – imagem de VM (sistema operacional) não listada
- [Versão Prévia]: Auditar a implantação do Agente do Log Analytics – imagem de VM (sistema operacional) não listada
- [Versão Prévia]: Auditar a implantação do Agente do Log Analytics no VMSS – imagem de VM (sistema operacional) não listada
- [Visualização]: Monitor unaudited SQL database in Azure Security Center
- Configuração de diagnóstico de auditoria
- Auditar configurações de Auditoria de nível do servidor SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>A.12.1 Gerenciamento de vulnerabilidades técnicas

Esse blueprint ajuda você a gerenciar as vulnerabilidades do sistema de informações por meio da atribuição de cinco definições do [Azure Policy](../../../policy/overview.md) que monitoram atualizações ausentes do sistema, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades da máquina virtual. Esses insights fornecem informações em tempo real sobre o estado de segurança dos recursos implantados e podem ajudá-lo a priorizar as ações de correção.

- [Visualização]: Monitor missing Endpoint Protection in Azure Security Center
- [Visualização]: Monitor missing system updates in Azure Security Center
- [Visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrições de instalação de software

Os controles de aplicativos adaptáveis são a solução da Central de Segurança do Azure que ajuda você a controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Esse blueprint atribui uma definição do Azure Policy que monitora as alterações feitas no conjunto de aplicativos permitidos. As restrições de instalação de software podem ajudar você a reduzir a probabilidade de introdução de vulnerabilidades de software.

- [Visualização]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Controles de rede

Esse blueprint ajuda você a gerenciar e controlar redes por meio da atribuição de uma definição do [Azure Policy](../../../policy/overview.md) que monitora os grupos de segurança de rede com regras permissivas. Regras que são muito permissivas podem permitir o acesso não intencional à rede e devem ser examinadas.

- [Visualização]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Políticas e procedimentos de transferência de informações

O blueprint ajuda você a garantir que a transferência de informações com os serviços do Azure seja segura por meio da atribuição de duas definições do [Azure Policy](../../../policy/overview.md) para auditar conexões não seguras com contas de armazenamento e o Cache Redis.

- Auditoria de habilitação de somente conexões seguras ao seu Cache Redis
- Auditar transferência segura para contas de armazenamento

## <a name="a1413-protecting-application-services-transactions"></a>A.14.1.3 Como proteger transações de serviços de aplicativo

Esse blueprint ajuda você a proteger os ativos do sistema de informações por meio da atribuição de três definições do [Azure Policy](../../../policy/overview.md) que monitoram pontos de extremidade, contas de armazenamento e aplicativos desprotegidos. Os pontos de extremidade e os aplicativos que não estão protegidos por um firewall e as contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- [Visualização]: Monitor unprotected network endpoints in Azure Security Center
- [Visualização]: Monitor unprotected web application in Azure Security Center
- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="a1613-reporting-information-security-weaknesses"></a>A.16.1.3 Como relatar vulnerabilidades de segurança de informações

Esse blueprint ajuda você a manter o reconhecimento de vulnerabilidades do sistema por meio da atribuição de cinco definições do [Azure Policy](../../../policy/overview.md) que monitoram vulnerabilidades, o status de patch e alertas de malware na Central de Segurança do Azure. A Central de Segurança do Azure fornece funcionalidades de relatórios que permitem ter insights em tempo real sobre o estado de segurança de recursos implantados do Azure.

- [Visualização]: Monitor missing Endpoint Protection in Azure Security Center
- [Visualização]: Monitor missing system updates in Azure Security Center
- [Visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Próximas etapas

Agora que você examinou o mapeamento de controle da amostra de blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001, leia os seguintes artigos para saber mais sobre a arquitetura e como implantar essa amostra:

> [!div class="nextstepaction"]
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – Visão geral](./index.md)
> [Blueprint da carga de trabalho do Ambiente do Serviço de Aplicativo/Banco de Dados SQL ISO 27001 – etapas de implantação](./deploy.md)

Outros artigos sobre blueprints e como usá-los:

- Saiba mais sobre o [ciclo de vida do blueprint](../../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../../how-to/update-existing-assignments.md).