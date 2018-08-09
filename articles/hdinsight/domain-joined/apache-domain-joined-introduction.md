---
title: Uma introdução à segurança do Hadoop com clusters Azure HDInsight ingressados no domínio
description: Saiba como os clusters Azure HDInsight ingressados no domínio dão suporte aos quatro pilares da segurança empresarial.
services: hdinsight
ms.service: hdinsight
author: omidm1
ms.author: omidm
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/26/2018
ms.openlocfilehash: e697b0ffe7cde65cd47a2f1b1db5b544b6dc1d3c
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2018
ms.locfileid: "39595623"
---
# <a name="an-introduction-to-hadoop-security-with-domain-joined-hdinsight-clusters"></a>Uma introdução à segurança do Hadoop com clusters HDInsight ingressados no domínio

No passado, o Azure HDInsight dava suporte apenas a um único usuário: o administrador local. Isso funcionava bem para equipes de aplicativos ou departamentos menores. Na medida em que as cargas de trabalho baseadas no Hadoop ganharam mais popularidade no setor corporativo, a necessidade de recursos de nível empresarial, como autenticação baseada em Active Directory, suporte multiusuário e controle de acesso baseado em função, tornou-se cada vez mais importante. 

É possível criar um cluster HDInsight ingressado a um domínio do Active Directory. Em seguida, você poderá configurar uma lista de funcionários da empresa que podem autenticar através do Azure Active Directory para fazer logon no cluster HDInsight. Qualquer pessoa fora da empresa não pode fazer logon ou acessar o cluster HDInsight. 

O administrador corporativo pode configurar o RBAC (controle de acesso baseado em função) para segurança do Hive, usando [Apache Ranger](http://hortonworks.com/apache/ranger/). Configurar RBAC restringe o acesso a dados somente para o que é necessário. Finalmente, o administrador pode auditar o acesso aos dados pelos funcionários e quaisquer alterações feitas para acessar as políticas de controle. O administrador pode, então, alcançar um alto grau de governança dos recursos corporativos.

> [!NOTE]
> Os novos recursos descritos neste artigo estão disponíveis em versão prévia apenas nos tipos de cluster a seguir: Hadoop, Spark e Consulta Interativa. O Oozie agora está habilitado nos clusters ingressados no domínio. Para acessar a IU da Web do Oozie, os usuários devem habilitar o [túnel](../hdinsight-linux-ambari-ssh-tunnel.md).

A segurança empresarial contém quatro pilares principais: segurança de perímetro, autenticação, autorização e criptografia.

![Benefícios dos clusters HDInsight ingressados no domínio nos quatro pilares da segurança empresarial](./media/apache-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

## <a name="perimeter-security"></a>Segurança de perímetro
A segurança de perímetro no HDInsight é obtida por meio de redes virtuais e do serviço do Gateway de VPN do Azure. Um administrador corporativo pode criar um cluster HDInsight dentro de uma rede virtual e usar grupos de segurança de rede (regras de firewall) para restringir o acesso à rede virtual. Somente os endereços IP definidos nas regras de firewall de entrada poderão comunicar-se com o cluster HDInsight. Essa configuração fornece segurança de perímetro.

Outra camada de segurança de perímetro é obtida através do serviço de Gateway de VPN. O gateway atua como primeira linha de defesa para qualquer solicitação recebida no cluster HDInsight. Ele aceita a solicitação, valida-a e somente então permitirá que a solicitação passe para os outros nós no cluster. Dessa maneira, o gateway fornece segurança de perímetro para outros nós de nome e dados no cluster.

## <a name="authentication"></a>Autenticação
Um administrador de empresa pode criar um cluster HDInsight ingressado no domínio em uma [rede virtual](https://azure.microsoft.com/services/virtual-network/). Todos os nós do cluster HDInsight são ingressados no domínio que a empresa gerencia. Isso é obtido por meio do uso do [Azure Active Directory Domain Services](../../active-directory-domain-services/active-directory-ds-overview.md). 

Com essa configuração, funcionários da empresa podem fazer logon nos nós de cluster, usando as credenciais de domínio. Eles também podem usar suas credenciais de domínio para se autenticar em outros pontos de extremidade aprovados, como, Ambari Views, ODBC, JDBC, PowerShell e APIs REST para interagir com o cluster. O administrador tem controle total sobre a limitação do número de usuários que interagem com o cluster por meio desses pontos de extremidade.

## <a name="authorization"></a>Autorização
Uma melhor prática que a maioria das empresas segue é garantir que nem todos os funcionários tenham acesso a todos os recursos da empresa. Da mesma forma, o administrador pode definir políticas de controle de acesso baseadas em função para os recursos do cluster. 

Por exemplo, o administrador pode configurar o [Apache Ranger](http://hortonworks.com/apache/ranger/) para definir políticas de controle de acesso para o Hive. Essa funcionalidade garante que os funcionários possam acessar apenas os dados que precisam para ter êxito nos trabalhos. O acesso SSH ao cluster também é restrito apenas ao administrador.

## <a name="auditing"></a>Auditoria
A auditoria de todo acesso aos recursos do cluster e aos dados é necessária para rastrear o acesso não autorizado ou não intencional dos recursos. É tão importante quanto proteger os recursos de cluster HDInsight de usuários não autorizados e proteger os dados. 

O administrador pode exibir e relatar todo o acesso aos recursos e dados de cluster do HDInsight. O administrador também pode exibir e relatar todas as alterações nas políticas de controle de acesso criadas nos pontos de extremidade com suporte pelo Apache Ranger. 

Um cluster HDInsight ingressado no domínio usa a interface do usuário familiar do Apache Ranger para pesquisar logs de auditoria. No back-end, o Ranger usa o [Apache Solr](http://hortonworks.com/apache/solr/) para armazenar e pesquisar os logs.

## <a name="encryption"></a>Criptografia
A proteção de dados é importante para atender aos requisitos de segurança e conformidade da organização. Além de restringir o acesso a dados de funcionários não autorizados, você deve criptografá-lo. 

Ambos os armazenamentos de dados para clusters HDInsight --Armazenamento de Blobs do Azure e Azure Data Lake Storage Gen1--dão suporte à [criptografia de dados](../../storage/common/storage-service-encryption.md) transparente do lado do servidor em repouso. Os clusters seguros do HDInsight funcionarão perfeitamente com esse recurso de criptografia de dados do lado do servidor em repouso.

## <a name="next-steps"></a>Próximas etapas
* [Planejar clusters HDInsight ingressados no domínio](apache-domain-joined-architecture.md)
* [Configurar clusters HDInsight ingressados no domínio](apache-domain-joined-configure.md)
* [Gerenciar clusters HDInsight ingressados no domínio](apache-domain-joined-manage.md)
* [Configurar políticas do Hive para cluster HDInsight ingressado no domínio](apache-domain-joined-run-hive.md)
* [Use o SSH com o HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)

