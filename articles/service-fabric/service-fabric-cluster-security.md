<properties
   pageTitle="Proteger um cluster do Service Fabric | Microsoft Azure"
   description="Descreve os cenários de segurança para um cluster do Service Fabric e as diferentes tecnologias usadas para implementar esses cenários."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="chackdan"/>

# Cenários de segurança do cluster do Service Fabric

Um cluster do Service Fabric é um recurso que pertence a você. Para impedir o acesso não autorizado ao recurso, você deverá protegê-lo, especialmente quando ele tiver cargas de trabalho de produção em execução. Este artigo fornece uma visão geral dos cenários de segurança para clusters em execução no Azure e o Windows server e as diversas tecnologias usadas para implementar esses cenários. Os cenários de segurança do cluster são:

- Segurança de nó para nó
- Segurança de cliente para nó
- RBAC (Controle de Acesso Baseado em Função)

## Segurança de nó para nó
Protege a comunicação entre as VMs e os computadores no cluster. Isso faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.

![Diagrama de comunicação de nó para nó][Node-to-Node]

Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então o a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx). A segurança de certificado é configurada durante a criação do cluster (seja por meio do Portal do Azure ou por meio de modelos do ARM), especificando um certificado primário e um certificado secundário opcional. Os certificados primários e secundários que você especificar devem ser diferentes do que os certificados de cliente administrador e certificados cliente somente leitura especificados para a [Segurança de cliente para nó](#client-to-node-security). Consulte [Proteger um cluster do Service Fabric no Azure usando certificados](service-fabric-secure-azure-cluster-with-certs.md) ou [Configurar um cluster usando um modelo de ARM](service-fabric-cluster-creation-via-arm.md) para saber como configurar a segurança de certificado em um cluster em execução no Azure.

## Segurança de cliente para nó
Autentica clientes e protege a comunicação entre um cliente e nós individuais no cluster. Esse tipo de segurança autentica e protege as comunicações do cliente, o que garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados no cluster. Os clientes são identificados exclusivamente por meio de suas credenciais de segurança do Windows ou pelas credenciais de segurança do certificado deles.

![Diagrama de comunicação de cliente para nó][Client-to-Node]

Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então o a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx). A segurança de certificado do cliente para nó é configurada durante a criação do cluster (seja por meio do Portal do Azure ou por meio de modelos do ARM), especificando um certificado de cliente administrador e/ou um certificado de cliente somente leitura. Os certificados de cliente administrador e certificados de cliente somente leitura que você especificar devem ser diferentes dos certificados primários e secundários que você especificar para a [Segurança de nó para nó](#node-to-node-security). Clientes conectando-se ao cluster usando o certificado de administrador ou o certificado primário têm acesso completo aos recursos de gerenciamento. Clientes conectando-se ao cluster usando o certificado de cliente somente leitura somente acesso de leitura aos recursos de gerenciamento. Consulte [Proteger um cluster do Service Fabric no Azure usando certificados](service-fabric-secure-azure-cluster-with-certs.md) ou [Configurar um cluster usando um modelo de ARM](service-fabric-cluster-creation-via-arm.md) para saber como configurar a segurança de certificado em um cluster em execução no Azure.

O Service Fabric usa os certificados do servidor X.509 que você especifica como parte das configurações de tipo de nó ao criar um cluster. Confira no final deste artigo uma visão geral rápida do que são esses certificados e como você pode adquirir ou criá-los.

Clusters em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o AAD (Azure Active Directory). Consulte [Criar um cluster do Service Fabric usando o Azure Active Directory para autenticação de cliente](service-fabric-cluster-security-client-auth-with-aad.md) para obter informações sobre como criar os artefatos AAD necessários, como preenchê-los durante a criação do cluster e como se conectar a esses grupos posteriormente.

## RBAC (Controle de Acesso Baseado em Função)
O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Clientes que se conectam a um cluster dão suporte a dois tipos de controle de acesso diferentes: administrador e usuário. Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos. As funções do cliente de administrador e usuário são especificadas no momento da criação do cluster, com o fornecimento de certificados separados para cada um. Para obter mais informações sobre as configurações de controle de acesso padrão e de como alterar as configurações padrão, consulte [Controle de acesso com base em funções para clientes](service-fabric-cluster-security-roles.md).


## Certificados X.509 e Service Fabric
Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores, criptografar e assinar mensagens digitalmente. Para obter mais detalhes sobre esses certificados, vá para [Trabalhando com certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algumas coisas importantes a considerar:

- Os certificados usados em clusters que executam cargas de trabalho de produção devem ser criados por meio de um serviço de certificado do Windows Server configurado corretamente ou obtidos por meio de uma [AC (Autoridade de Certificação)](https://en.wikipedia.org/wiki/Certificate_authority) aprovada.
- Nunca use nenhum certificado temporário ou de teste em produção criado com ferramentas como MakeCert.exe.
- Você pode usar um certificado autoassinado, mas deve fazer isso somente para clusters de teste e não em produção.

### Certificados X.509 do servidor

Os certificados de servidor têm a tarefa principal de autenticar o servidor (nó) para clientes ou autenticar um servidor (nó) para um servidor (nó). Uma das verificações iniciais quando um cliente ou um nó autentica um nó é verificar o valor do nome comum no campo Entidade. Esse nome comum ou um dos nomes alternativos do assunto dos certificados deve estar presente na lista de nomes comuns permitidos.

O artigo a seguir descreve como gerar certificados com SAN (nomes alternativos da entidade): [Como adicionar um nome alternativo da entidade a um certificado LDAP seguro](http://support.microsoft.com/kb/931351).

O campo Entidade pode conter vários valores, cada um prefixado com iniciais para indicar o tipo de valor. Geralmente, a inicialização é "CN" para o nome comum, por exemplo, "CN = www.contoso.com". Também é possível deixar o campo Assunto em branco. Se o campo opcional Nome alternativo da entidade estiver preenchido, ele deverá conter o nome comum do certificado e uma entrada por nome alternativo da entidade. Eles são inseridos como valores de Nome DNS.

O valor do campo Finalidades pretendidas do certificado deve incluir um valor apropriado, como "Autenticação do servidor" ou "Autenticação do cliente".

### Certificados X.509 do cliente

Normalmente, os certificados de cliente não são emitidos por uma autoridade de certificação de terceiros. Em vez disso, o repositório Pessoal do local atual do usuário geralmente contém os certificados colocados lá por uma autoridade raiz, com uma finalidade de "Autenticação do cliente". O cliente pode usar esse certificado quando a autenticação mútua é necessária.

>[AZURE.NOTE] Todas as operações de gerenciamento no cluster do Service Fabric exigem certificados de servidor. Certificados de cliente não podem ser usados para gerenciamento.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## Próximas etapas
Depois que seu cluster estiver configurado, saiba mais sobre atualizações de cluster:

- [Processo de atualização de Cluster do Service Fabric e expectativas](service-fabric-cluster-upgrade.md)

Saiba mais sobre a segurança de aplicativo:

- [Segurança de aplicativo e RunAs](service-fabric-application-runas-security.md)

- [Proteger comunicações de serviço](service-fabric-reliable-services-secure-communication.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0608_2016-->