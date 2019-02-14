---
title: Arquitetura do Azure HDInsight com o pacote de segurança da empresa
description: Saiba como planejar a segurança do HDInsight com o pacote de segurança Enterprise.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: acae8076350c26e7a7157fd2063f64220b167771
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55486054"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>Usar o pacote de segurança Enterprise no HDInsight

O cluster HDInsight do Azure padrão é um cluster de usuário único. É adequado para a maioria das empresas que têm equipes de aplicativos menores compilando grandes cargas de trabalho de dados. Cada usuário pode criar um cluster dedicado sob demanda e destruí-lo quando não for mais necessário. 

Muitas empresas adotaram um modelo no qual os clusters são gerenciados por equipes de TI e várias equipes de aplicativos compartilham clusters. Essas empresas maiores precisam de acesso multiusuário para cada cluster no Azure HDInsight.

O HDInsight conta com um provedor de identidade popular -- o Active Directory -- de maneira gerenciada. Ao integrar o HDInsight com [Azure AD DS (Azure Active Directory Domain Services)](../../active-directory-domain-services/active-directory-ds-overview.md), será possível acessar os clusters usando as credenciais de domínio. 

As VMs (máquinas virtuais) no HDInsight são ingressadas no domínio para o domínio fornecido. Portanto, todos os serviços em execução no HDInsight (Apache Ambari, servidor Apache Hive, Apache Ranger, servidor thrift do Apache Spark e outros) funcionam perfeitamente para o usuário autenticado. Os administradores podem, então, criar políticas de autorização fortes usando o Apache Ranger para fornecer controle de acesso baseado em função para recursos no cluster.

## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight ao Active Directory

O código-fonte aberto do Apache Hadoop depende do Kerberos para autenticação e segurança. Portanto, os nós de cluster do HDInsight com o Enterprise Security Package (ESP) são unidos a um domínio gerenciado pelo Azure AD DS. A segurança do Kerberos é configurada para os componentes do Hadoop no cluster. 

As seguintes coisas são criadas automaticamente:
- uma entidade de serviço para cada componente do Hadoop 
- uma entidade de segurança do computador para cada computador que tenha ingressado no domínio
- uma UO (unidade organizacional) para cada cluster para armazenar essas entidades de serviço e de máquina 

Para resumir, você precisa configurar um ambiente com:

- Um domínio do Active Directory (gerenciado pelo Azure AD DS).
- LDAP Seguro (LDAPS) habilitado no Azure AD DS.
- Conectividade de rede adequada da rede virtual do HDInsight para a rede virtual do Azure AD DS, se você escolher redes virtuais separadas para elas. Uma VM dentro da rede virtual do HDInsight deve ter uma linha de visão para o Azure AD DS por meio de emparelhamento de rede virtual. Se o HDInsight e o Azure AD DS forem implantados na mesma rede virtual, a conectividade será fornecida automaticamente e nenhuma outra ação será necessária.

## <a name="set-up-different-domain-controllers"></a>Configurar controladores de domínio diferentes
O HDInsight atualmente dá suporte somente ao Azure AD DS como o controlador de domínio principal que o cluster usa para comunicação de Kerberos. Mas outras configurações complexas do Active Directory são possíveis, desde que essa configuração leve à habilitação do Azure AD DS para acesso ao HDInsight.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
O [Azure AD DS](../../active-directory-domain-services/active-directory-ds-overview.md) fornece um domínio gerenciado totalmente compatível com o Windows Server Active Directory. A Microsoft cuida do gerenciamento, aplicação de patches e monitoramento do domínio em uma configuração HA (altamente disponível). Você pode implantar o cluster sem se preocupar em manter os controladores de domínio. 

Usuários, grupos e senhas são sincronizados do Azure AD (Azure Active Directory). A sincronização unidirecional da instância do Azure AD para Azure AD DS permite que os usuários entrem no cluster usando as mesmas credenciais corporativas. 

Para obter mais informações, consulte [Configurar os clusters do HDInsight com o ESP usando o Azure AD DS](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>Active Directory ou Active Directory local em VMs de IaaS

Se você tiver uma instância do Active Directory local ou configurações mais complexas do Active Directory para o domínio, poderá sincronizá-las com o Azure AD usando o Azure AD Connect. É possível habilitar o Azure AD DS nesse locatário do Active Directory. 

Como o Kerberos depende de hashes de senha, é necessário [habilitar a sincronização de hash de senha no Azure AD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). 

Se estiver usando a federação com o Serviços de Federação do Active Directory (AD FS), será necessário habilitar a sincronização de hash de senha (uma instalação recomendada, confira [isto](https://youtu.be/qQruArbu2Ew)) que também ajuda na recuperação de desastre caso a infraestrutura do ADFS falhe e a proteção de credenciais fique vazada. Para obter mais informações, consulte [Habilitar sincronização de hash de senha com a sincronização do Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

O uso do Active Directory local ou do Active Directory somente em VMs IaaS, sem o Azure AD e o Azure AD DS, não é uma configuração com suporte para clusters HDInsight com ESP.

Se a federação for usada e os hashes de senha forem sincronizados corretamente, mas você estiver recebendo falhas na autenticação, verifique se a autenticação de senha da nuvem do princípio de serviço do PowerShell está habilitada; caso contrário, será necessário definir uma [política de HRD (Descoberta de Realm inicial)](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) para o locatário do AAD. Para verificar e definir a política de HRD:

 1. Instale o módulo do PowerShell do Azure AD

 ```
  Install-Module AzureAD
 ```

 2. ```Connect-AzureAD``` usando credenciais de um administrador global (administrador de locatários)

 3. Verifique se a entidade de serviço do “Microsoft Azure PowerShell” já foi criada

 ```
  $powershellSPN = Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
 ```

 4. Se ela não existir (por ex., se ($powershellSPN -eq $null)), então crie a entidade de serviço

 ```
  $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
 ```

 5. Crie e anexe a política a essa entidade de serviço: 

 ```
 $policy = New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuth -Type HomeRealmDiscoveryPolicy

 Add-AzureADServicePrincipalPolicy -Id $powershellSPN.ObjectId -refObjectID $policy.ID
 ```

## <a name="next-steps"></a>Próximas etapas

* [Configurar clusters do HDInsight com ESP](apache-domain-joined-configure-using-azure-adds.md)
* [Configurar políticas do Apache Hive para clusters HDInsight com ESP](apache-domain-joined-run-hive.md)
* [Gerenciar clusters do HDInsight com ESP](apache-domain-joined-manage.md) 
