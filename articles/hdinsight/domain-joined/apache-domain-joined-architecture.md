---
title: "Arquitetura do Azure HDInsight ingressado no domínio | Microsoft Docs"
description: "Aprenda a planejar o HDInsight associado ao domínio."
services: hdinsight
documentationcenter: 
author: bhanupr
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7dc6847d-10d4-4b5c-9c83-cc513cf91965
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2017
ms.author: bprakash
ms.openlocfilehash: 5285199d22528ed6b9fa3b7dbc85e382e7b28569
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="plan-azure-domain-joined-hadoop-clusters-in-hdinsight"></a>Planejar clusters Hadoop do Azure associados ao domínio no HDInsight

O cluster HDInsight padrão é um cluster de usuário único. Ele é adequado para a maioria das empresas que têm equipes de aplicativos menores que criam grandes cargas de trabalho de dados. À medida que o Hadoop ganhou popularidade, muitas empresas começaram a mudar para um modelo em que clusters são gerenciados por equipes de TI, e várias equipes de aplicativo compartilham clusters. Assim, as funcionalidades que envolvem clusters multiusuários estão entre as funcionalidades mais solicitadas no Azure HDInsight.

Em vez de criar sua própria autorização e autenticação multiusuário, o HDInsight recorre ao provedor de identidade mais popular – o AD (Active Directory). A funcionalidade de segurança eficiente no AD pode ser usada para gerenciar a autenticação multiusuário no HDInsight. Integrando HDInsight com o AD, você pode se comunicar com os clusters, usando suas credenciais do AD. Os domínios das VMs no HDInsight são ingressados no AD e é assim que o HDInsight mapeia o usuário do AD para um usuário do Hadoop local, para que todos os serviços em execução no HDInsight (Ambari, servidor Hive, Ranger, servidor Spark Thrift e outros) funcionem sem problemas para o usuário autenticado. Os administradores poderão, em seguida, criar políticas de autorização seguras usando o Apache Ranger para fornecer controle de acesso baseado em função para os recursos no HDInsight.


## <a name="integrate-hdinsight-with-active-directory"></a>Integrar o HDInsight ao Active Directory

Quando você integra o HDInsight ao Active Directory, os nós do cluster HDInsight são ingressados em um domínio do AD. A segurança do Kerberos é configurada para os componentes do Hadoop no cluster. Para cada um dos componentes do Hadoop, uma entidade de serviço é criada no Active Directory. Uma entidade de computador correspondente também é criada para cada computador ingressado no domínio. Essas entidades de serviço e entidades de computador podem sobrecarregar o Active Directory. Como resultado, é necessário fornecer uma UO (unidade organizacional) no Active Directory, onde essas entidades são colocadas. 

Para resumir, você precisa configurar um ambiente com:

- Um controlador de domínio do Active Directory com o LDAPS configurado.
- Conectividade da rede virtual do HDInsight com o controlador de domínio do Active Directory.
- Uma unidade organizacional criada no Active Directory.
- Uma conta de serviço que tenha permissões para:

    - Criar entidades de serviço na OU.
    - Ingressar computadores ao domínio e criar entidades de computador na UO.

A captura de tela a seguir mostra uma UO criada no contoso.com. Algumas das entidades de serviço e entidades de computador são mostradas na captura de tela também.

![OU dos clusters HDInsight associados ao domínio](./media/apache-domain-joined-architecture/hdinsight-domain-joined-ou.png).

### <a name="two-ways-of-bringing-your-own-active-directory-domain-controllers"></a>Duas maneiras de implementar seus próprios controladores de domínio do Active Directory

Há duas maneiras de implantar controladores de domínio do Active Directory para criar clusters HDInsight ingressados no domínio. 

- **Azure Active Directory Domain Services**: esse serviço fornece um domínio do Active Directory gerenciado totalmente compatível com o Active Directory do Windows Server. A Microsoft cuida do gerenciamento, da aplicação de patch e do monitoramento do domínio do AD. Você pode implantar o cluster sem se preocupar em manter os controladores de domínio. Usuários, grupos e senhas são sincronizados do Azure Active Directory, permitindo que os usuários entrem no cluster usando as respectivas credenciais corporativas. Consulte mais informações em [Configurar clusters HDInsight ingressados no domínio usando o Azure Active Directory Domain Services](./apache-domain-joined-configure-using-azure-adds.md).

- **Active Directory em VMs de IaaS do Azure:** nessa opção, você implanta e gerencia seu próprio domínio do Active Directory do Windows Server em VMs de IaaS do Azure. Consulte mais informações em [Configurar ambiente de área restrita ingressado no domínio](./apache-domain-joined-configure.md).

## <a name="next-steps"></a>Próximas etapas
* Para configurar um cluster HDInsight ingressado em um domínio, confira [Configurar clusters HDInsight ingressados em domínio](apache-domain-joined-configure.md).
* Para gerenciar clusters HDInsight ingressados em um domínio, confira [Gerenciar clusters HDInsight ingressados em domínio](apache-domain-joined-manage.md).
* Para configurar políticas do Hive e executar consultas do Hive, confira [Configurar políticas do Hive para clusters HDInsight ingressados no domínio](apache-domain-joined-run-hive.md).
* Para executar consultas Hive usando SSH em clusters HDInsight adicionados ao domínio, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
