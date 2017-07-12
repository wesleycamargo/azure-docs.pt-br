---
title: Proteger um cluster do Service Fabric | Microsoft Docs
description: "Descreve os cenários de segurança para um cluster do Service Fabric e as diferentes tecnologias usadas para implementar esses cenários."
services: service-fabric
documentationcenter: .net
author: ChackDan
manager: timlt
editor: 
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2017
ms.author: chackdan
ms.translationtype: Human Translation
ms.sourcegitcommit: 628df1df8f5de99a5c18d0df5b7ee41e2fb747df
ms.openlocfilehash: c3ff370b105a1f9bdacd1bdb4b32d6209e150be2
ms.contentlocale: pt-br
ms.lasthandoff: 12/08/2016

---
<a id="service-fabric-cluster-security-scenarios" class="xliff"></a>

# Cenários de segurança do cluster do Service Fabric
Um cluster do Service Fabric é um recurso que pertence a você. Os clusters sempre devem ser protegidos para evitar que usuários não autorizados se conectem ao cluster, especialmente quando ele tiver cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, fazer isso permitirá que usuários anônimos se conectem a ele se ele expuser pontos de extremidade de gerenciamento na Internet pública. 

Este artigo fornece uma visão geral dos cenários de segurança para clusters em execução no Azure ou autônomos e as diversas tecnologias usadas para implementar esses cenários. Os cenários de segurança do cluster são:

* Segurança de nó para nó
* Segurança de cliente para nó
* RBAC (Controle de Acesso Baseado em Função)

<a id="node-to-node-security" class="xliff"></a>

## Segurança de nó para nó
Protege a comunicação entre as VMs ou os computadores no cluster. Isso faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.

![Diagrama de comunicação de nó para nó][Node-to-Node]

Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

<a id="node-to-node-certificate-security" class="xliff"></a>

### Segurança de certificado de nó para nó
O Service Fabric usa os certificados do servidor X.509 que você especifica como parte das configurações de tipo de nó ao criar um cluster. Confira no final deste artigo uma visão geral rápida do que são esses certificados e como você pode adquirir ou criá-los.

A segurança de certificado é configurada durante a criação do cluster por meio do portal do Azure, dos modelos do Azure Resource Manager ou de um modelo JSON autônomo. É possível especificar um certificado primário e um certificado secundário opcional que é usado para substituições de certificado. Os certificados primários e secundários que você especificar devem ser diferentes do que os certificados de cliente administrador e certificados cliente somente leitura especificados para a [Segurança de cliente para nó](#client-to-node-security).

Para o Azure, leia [Configurar um cluster usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para saber como configurar a segurança de certificado em um cluster.

Para Windows Server autônomo, leia [Proteger um cluster autônomo no Windows usando certificados X.509 ](service-fabric-windows-cluster-x509-security.md)

<a id="node-to-node-windows-security" class="xliff"></a>

### Segurança do Windows de nó para nó
Para Windows Server autônomo, leia [Proteger um cluster autônomo no Windows usando a segurança do Windows](service-fabric-windows-cluster-windows-security.md)

<a id="client-to-node-security" class="xliff"></a>

## Segurança de cliente para nó
Autentica clientes e protege a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança autentica e protege as comunicações do cliente, o que garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou pelas credenciais de segurança do certificado deles.

![Diagrama de comunicação de cliente para nó][Client-to-Node]

Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

<a id="client-to-node-certificate-security" class="xliff"></a>

### Segurança de certificado de cliente para nó
 A segurança de certificado de cliente para nó é configurada durante a criação do cluster por meio do portal do Azure, dos modelos do Resource Manager ou de um modelo JSON autônomo, especificando um certificado de cliente do administrador e/ou um certificado de cliente do usuário.  Os certificados de cliente administrador e certificados de cliente de usuário que você especificar devem ser diferentes dos certificados primários e secundários que você especifica para a [Segurança de nó para nó](#node-to-node-security).

Clientes que se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento.  Clientes que se conectam ao cluster usando o certificado de cliente de usuário somente leitura somente acesso de leitura aos recursos de gerenciamento. Em outras palavras, esses certificados são usados para o RBAC (controle de acesso baseado em função) descrito adiante neste artigo.

Para o Azure, leia [Configurar um cluster usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para saber como configurar a segurança de certificado em um cluster.

Para Windows Server autônomo, leia [Proteger um cluster autônomo no Windows usando certificados X.509 ](service-fabric-windows-cluster-x509-security.md)

<a id="client-to-node-azure-active-directory-aad-security-on-azure" class="xliff"></a>

### Segurança do AAD (Azure Active Directory) de cliente para nó no Azure
Clusters em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o AAD (Azure Active Directory). Consulte [Configurar um cluster usando um modelo do Azure Resource Manager](service-fabric-cluster-creation-via-arm.md) para obter informações sobre como criar os artefatos AAD necessários, como preenchê-los durante a criação do cluster e como se conectar-se a esses clusters posteriormente.

<a id="security-recommendations" class="xliff"></a>

## Recomendações de Segurança
Para clusters do Azure, é recomendável usar a segurança do AAD para autenticar clientes e certificados para a segurança de nó para nó.

Para clusters do Windows Server autônomos, será recomendável usar a segurança do Windows com GMA (contas gerenciadas de grupo) se você tiver o Windows Server 2012 R2 e o Active Directory. Caso contrário, ainda use a segurança do Windows com contas do Windows.

<a id="role-based-access-control-rbac" class="xliff"></a>

## RBAC (Controle de Acesso Baseado em Função)
O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Dois tipos de controle de acesso diferentes têm suporte para clientes que se conectam a um cluster: funções de Administrador e de Usuário.

Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.

Especifique as funções de cliente do administrador e do usuário durante a criação do cluster, fornecendo identidades separadas (certificados, AAD, etc.) para cada uma. Para saber mais sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, confira [Controle de acesso baseado em função para clientes do Service Fabric](service-fabric-cluster-security-roles.md).

<a id="x509-certificates-and-service-fabric" class="xliff"></a>

## Certificados X.509 e Service Fabric
Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores, criptografar e assinar mensagens digitalmente. Para obter mais detalhes sobre esses certificados, vá para [Trabalhando com certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algumas coisas importantes a considerar:

* Os certificados usados em clusters que executam cargas de trabalho de produção devem ser criados por meio de um serviço de certificado do Windows Server configurado corretamente ou obtidos por meio de uma [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority)aprovada.
* Nunca use nenhum certificado temporário ou de teste em produção criado com ferramentas como MakeCert.exe.
* Você pode usar um certificado autoassinado, mas deve fazer isso somente para clusters de teste e não em produção.

<a id="server-x509-certificates" class="xliff"></a>

### Certificados X.509 do servidor
Os certificados de servidor têm a tarefa principal de autenticar o servidor (nó) para clientes ou autenticar um servidor (nó) para um servidor (nó). Uma das verificações iniciais quando um cliente ou um nó autentica um nó é verificar o valor do nome comum no campo Entidade. Esse nome comum ou um dos nomes alternativos do assunto dos certificados deve estar presente na lista de nomes comuns permitidos.

O artigo a seguir descreve como gerar certificados com SAN (nomes alternativos da entidade): [Como adicionar um nome alternativo da entidade a um certificado LDAP seguro](http://support.microsoft.com/kb/931351).

O campo Entidade pode conter vários valores, cada um prefixado com iniciais para indicar o tipo de valor. Geralmente, a inicialização é "CN" para o nome comum, por exemplo, "CN = www.contoso.com". Também é possível deixar o campo Assunto em branco. Se o campo opcional Nome alternativo da entidade estiver preenchido, ele deverá conter o nome comum do certificado e uma entrada por nome alternativo da entidade. Eles são inseridos como valores de Nome DNS.

O valor do campo Finalidades pretendidas do certificado deve incluir um valor apropriado, como "Autenticação do servidor" ou "Autenticação do cliente".

<a id="client-x509-certificates" class="xliff"></a>

### Certificados X.509 do cliente
Normalmente, os certificados de cliente não são emitidos por uma autoridade de certificação de terceiros. Em vez disso, o repositório Pessoal do local atual do usuário geralmente contém os certificados colocados lá por uma autoridade raiz, com uma finalidade de "Autenticação do cliente". O cliente pode usar esse certificado quando a autenticação mútua é necessária.

> [!NOTE]
> Todas as operações de gerenciamento no cluster do Service Fabric exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.
> 
> 

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


<a id="next-steps" class="xliff"></a>

## Próximas etapas
Este artigo fornece informações conceituais sobre a segurança de cluster. Em seguida,


1.  [crie um cluster no Azure usando um modelo do Resource Manager](service-fabric-cluster-creation-via-arm.md) 
2.  [Portal do Azure](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

