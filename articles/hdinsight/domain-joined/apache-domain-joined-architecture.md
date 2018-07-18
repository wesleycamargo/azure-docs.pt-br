---
title: Arquitetura do Azure HDInsight ingressado no domínio | Microsoft Docs
description: Aprenda a planejar o HDInsight associado ao domínio.
services: hdinsight
documentationcenter: ''
author: omidm1
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: omidm
ms.openlocfilehash: f4380f5d6ec379d5807f697294623a672bd270ae
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34715234"
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planejar clusters Hadoop do Azure associados ao domínio no HDInsight

O cluster HDInsight padrão é um cluster de usuário único. Ele é adequado para a maioria das empresas que têm equipes de aplicativos menores que criam grandes cargas de trabalho de dados. Cada usuário pode ter um cluster diferente dedicado para ele mesmo sob demanda e destruí-lo quando não for mais necessário. No entanto, muitas empresas iniciado migrando para um modelo no qual clusters sejam gerenciados por equipes de TI e aplicativos de várias equipes clusters de compartilhamento. Assim, acesso multiusuário para o cluster é necessária no Azure HDInsight para essas empresas maiores.

HDInsight depende do provedor de identidade mais popular – AD (Active Directory) de forma gerenciada. Integrando o HDInsight com [do Azure Active Directory Domain Services (AAD DS)](../../active-directory-domain-services/active-directory-ds-overview.md), você pode acessar os clusters usando suas credenciais de domínio. As VMs no HDInsight são unidos por domínio para seu domínio fornecido, então todos os serviços em execução no HDInsight (Ambari, Hive Spark thrift do servidor, Ranger, servidor e outros) diretamente para o usuário autenticado. Os administradores podem, em seguida, criar políticas de autorização segura usando o Apache Ranger para fornecer controle de acesso baseado em função para recursos no cluster.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight ao Active Directory

Hadoop do código-fonte aberto depende de Kerberos para fornecer autenticação e segurança. Portanto, nós de cluster do HDInsight serão ingressado no domínio a um domínio gerenciado pelo DS AAD. A segurança do Kerberos é configurada para os componentes do Hadoop no cluster. Para cada um dos componentes do Hadoop, uma entidade de serviço é criada automaticamente. Uma entidade de computador correspondente também é criada para cada computador ingressado no domínio. Para armazenar essas entidades de serviço e máquinas, é necessário fornecer uma unidade organizacional (UO) no controlador de domínio (AAD DS), em que essas entidades são colocadas. 

Para resumir, você precisa configurar um ambiente com:

- Um domínio do Active Directory (gerenciado pelo DS AAD)
- Proteger LDAP (LDAPS) habilitada no AAD DS.
- Conectividade do HDInsight de rede apropriado VNET para VNET DS AAD, caso você escolha separar VNETs para eles. Uma VM dentro do VNET HDI deve ter uma linha de visão para AAD DS usando o emparelhamento de rede virtual. Se tanto o HDI quanto o AAD-DS forem implantados na mesma VNET, a conectividade será fornecida automaticamente e nenhuma outra ação será necessária.
- Uma unidade organizacional (UO) [criado no AAD DS](../../active-directory-domain-services/active-directory-ds-admin-guide-create-ou.md)
- Uma conta de serviço que tenha permissões para:
    - Criar entidades de serviço na OU.
    - Adicionar computadores ao domínio e criar objetos de computador na UO.computador

A captura de tela a seguir mostra uma UO criada no contoso.com. Algumas das entidades de serviço e entidades de computador são mostradas na captura de tela também.

![OU dos clusters HDInsight associados ao domínio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="different-domain-controllers-setup"></a>Instalação de controladores de domínio diferentes:
HDInsight atualmente suporta apenas DS AAD como controlador de domínio principal que o cluster se comunicará com Kerberise o cluster. No entanto, outras configurações complexas do AD também são possíveis, desde que ele leva para habilitar acesso HDI DS AAD.

- **[Azure Active Directory Domain Services (AAD DS)](../../active-directory-domain-services/active-directory-ds-overview.md)**: este serviço oferece um domínio gerenciado, que é totalmente compatível com o Windows Server Active Directory. A Microsoft cuida do gerenciamento, aplicação de patches e monitoramento do domínio em uma configuração de alta disponibilidade (HA). Você pode implantar o cluster sem se preocupar em manter os controladores de domínio. Usuários, grupos e senhas são sincronizadas do seu Directory(AAD) ativa do Azure [sincronização unidirecional do AAD para AAD DS], permitindo que os usuários para entrar no cluster usando as mesmas credenciais corporativas. Para obter mais informações, consulte [como configurar o domínio HDInsight clusters usando o AAD DS](./apache-domain-joined-configure-using-azure-adds.md).
- **Local anúncio ou nas VMs de IaaS**: se você tiver um anúncio locais ou outras mais complexos configurações para seu domínio, você pode sincronizar as identidades para o AAD usando AD Connect e, em seguida, ativar locatário AAD-DS em que o AD. Como o Kerberos depende de hashes de senha, você precisará [habilita a sincronização de hash de senha no AAD DS](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md). Se você estiver usando a federação com federação AD ADFS (serviços), opcionalmente, você pode configurar Sincronização de hash de senha como um backup no caso de falha de sua infraestrutura do AD FS. Para obter mais informações, consulte [habilita a sincronização de hash de senha com a sincronização do AAD Connect](../../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md). Usar local anúncio ou nas VMs de IaaS sozinho, sem AAD e DS AAD não é uma configuração com suporte para ingressar em domínio do HDI cluster.

## <a name="next-steps"></a>Próximas etapas
* [Configurar domínio clusters de HDInsight](apache-domain-joined-configure-using-azure-adds.md).
* [Configurar políticas de Hive para clusters de HDInsight domínio](apache-domain-joined-run-hive.md).
* [Gerenciar clusters de HDInsight domínio](apache-domain-joined-manage.md). 
