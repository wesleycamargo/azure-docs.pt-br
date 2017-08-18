---
title: "Visão geral de segurança do Azure Service Fabric | Microsoft Docs"
description: "Este artigo fornece uma visão geral da segurança do Azure Service Fabric."
services: security
documentationcenter: na
author: unifycloud
manager: swadhwa
editor: tomsh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: tomsh
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 4cbd2791649c6d2dd005521cedb44c17aa874073
ms.contentlocale: pt-br
ms.lasthandoff: 08/11/2017

---
# <a name="azure-service-fabric-security-overview"></a>Visão geral de segurança do Azure Service Fabric
O [Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis.

Este artigo de Visão Geral da Segurança do Azure Service Fabric tem como foco as seguintes áreas:

-   Proteção do cluster
-   Monitoramento e diagnóstico
-   Proteção usando Certificados
-   RBAC (Controle de Acesso Baseado em Função)
-   Proteção de cluster usando a segurança do Windows
-   Configuração de segurança de aplicativo no Service Fabric
-   Proteção de comunicação para serviços no Azure Service Fabric Security

## <a name="securing-your-cluster"></a>Proteção do cluster
O Azure Service Fabric é um orquestrador de serviços em um cluster de máquinas, os Clusters sempre devem ser protegidos para evitar que usuários não autorizados se conectem ao cluster, especialmente quando ele tiver cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, fazer isso permitirá que usuários anônimos se conectem a ele se ele expuser pontos de extremidade de gerenciamento na Internet pública.

Esta seção fornece uma visão geral dos cenários de segurança para clusters em execução no Azure ou autônomos e as diversas tecnologias usadas para implementar esses cenários. Os cenários de segurança do cluster são:

-   Segurança de nó para nó
-   Segurança de cliente para nó

### <a name="node-to-node-security"></a>Segurança de nó para nó
Protege a comunicação entre as VMs ou os computadores no cluster. Isso faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.

Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://msdn.microsoft.com/library/ff649801.aspx) ou então a [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) para computadores Windows Server.

**Segurança de certificado de nó para nó**

O Service Fabric usa os certificados do servidor X.509 que você especifica como parte das configurações de tipo de nó ao criar um cluster. Uma visão geral rápida do que são esses certificados e [como você pode adquirir ou criá-los é fornecida neste artigo](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates).

A segurança de certificado é configurada durante a criação do cluster por meio do portal do Azure, dos modelos do Azure Resource Manager ou de um modelo JSON autônomo. É possível especificar um certificado primário e um certificado secundário opcional que é usado para substituições de certificado. Os certificados primários e secundários que você especificar devem ser diferentes do que os certificados de cliente administrador e certificados cliente somente leitura especificados para a [Segurança de cliente para nó](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-security).

### <a name="client-to-node-security"></a>Segurança de cliente para nó
A segurança de cliente para nó é configurada usando ClientIdentities. Para estabelecer a confiança entre um cliente e o cluster, você deve configurar o cluster para saber em quais identidades de cliente ele pode confiar. Isso pode ser feito de duas maneiras diferentes:

-   Especificar os usuários do grupo de domínio que podem se conectar ou
-   Especificar os usuários do nó de domínio que podem se conectar.

O Service Fabric oferece suporte a dois tipos de controle de acesso diferentes para clientes conectados a um cluster do Service Fabric:

-   Administrador
-   Usuário

O controle de acesso oferece a capacidade para que o administrador de cluster limite o acesso a determinados tipos de operação de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.

**Segurança de certificado de cliente para nó**

A segurança de certificado de cliente para nó é configurada durante a criação do cluster por meio do portal do Azure, dos Modelos do Resource Manager ou de um modelo JSON autônomo, especificando um certificado de cliente do administrador e/ou um certificado de cliente do usuário. Os certificados de cliente administrador e certificados de cliente de usuário que você especificar devem ser diferentes dos certificados primários e secundários que você especifica para a Segurança de nó para nó.

Clientes que se conectam ao cluster usando o certificado de administrador têm acesso completo aos recursos de gerenciamento. Clientes que se conectam ao cluster usando o certificado de cliente de usuário somente leitura somente acesso de leitura aos recursos de gerenciamento. Em outras palavras, esses certificados são usados para o controle de acesso baseado em função (RBAC).

Para o Azure, leia [Configurar um cluster usando um modelo do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para saber como configurar a segurança de certificado em um cluster.

**Segurança do AAD (Azure Active Directory) de cliente para nó no Azure**

Clusters em execução no Azure também podem proteger o acesso aos pontos de extremidade de gerenciamento usando o AAD (Azure Active Directory). Consulte [Configurar um cluster usando um modelo do Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) para obter informações sobre como criar os artefatos AAD necessários, como preenchê-los durante a criação do cluster e como se conectar-se a esses clusters posteriormente.

O AAD permite às organizações (conhecidas como locatários) gerenciarem o acesso de usuários a aplicativos, os quais são divididos em aplicativos com uma interface do usuário de logon baseada na Web e aplicativos com uma experiência de cliente nativo.

Os clusters do Service Fabric oferecem vários pontos de entrada para a funcionalidade de gerenciamento, incluindo o Service Fabric Explorer e o Visual Studio baseados na Web. Como resultado, você criará dois aplicativos do AAD para controlar o acesso ao cluster, um aplicativo Web e um aplicativo nativo.
Para clusters do Azure, é recomendável usar a segurança do AAD para autenticar clientes e certificados para a segurança de nó para nó.

Para clusters do Windows Server autônomos, é recomendável usar a segurança do Windows com contas gerenciadas de grupo (GMA) se você tiver o Windows Server 2012 R2 e o Active Directory. Caso contrário, ainda use a segurança do Windows com contas do Windows.

## <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Monitoramento e diagnóstico no Azure Service Fabric
[O monitoramento e o diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são essenciais para o desenvolvimento, os testes e a implantação de aplicativos e serviços em qualquer ambiente. As soluções do Service Fabric funcionam melhor quando você planeja e implementa o monitoramento e o diagnóstico que ajudam a garantir que aplicativos e serviços funcionem conforme o esperado em um ambiente de desenvolvimento local ou na produção.

Do ponto de vista da segurança, as principais metas do monitoramento e diagnóstico são:

-   Detectar e diagnosticar problemas de hardware e infraestrutura que podem resultado de um evento relacionado à segurança.
-   Detectar problemas de software e o aplicativo que podem fornecer indicadores de comprometimento (IoC).
-   Entender o consumo de recursos para ajudar uma negação acidental de serviço.

O fluxo de trabalho geral do monitoramento e diagnóstico consiste em três etapas:

-   **Geração de eventos:** isso inclui eventos (logs, rastreamentos, eventos personalizados) nos níveis de infraestrutura (cluster) e serviço/aplicativos. Leia mais sobre [eventos de nível de infraestrutura](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-infra) e [eventos de nível de aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-generation-app) para entender o que é fornecido e como adicionar mais instrumentação.
-   **Agregação de eventos:** os eventos gerados precisam ser coletados e agregados antes que possam ser exibidos. Normalmente, recomendamos usar o [Diagnóstico do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) (mais semelhante à coleta de log com base em agente) ou [EventFlow](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-eventflow) (coleta de log durante o processo).
-   **Análise:** os eventos precisam ser visualizados e estar acessíveis em algum formato, para permitir a análise e a exibição conforme necessário. Existem várias plataformas excelentes no mercado para análise e visualização de dados de monitoramento e diagnóstico. Os dois que recomendamos são [OMS](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-oms) e [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights) devido a sua melhor integração com o Service Fabric.

Você também pode usar o [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) para monitorar muitos dos recursos do Azure onde um cluster de Service Fabric é criado.

Um watchdog é um serviço separado que pode inspecionar a integridade e a carga entre serviços e informar a integridade de qualquer coisa na hierarquia de modelo de integridade. Isso pode ajudar a evitar erros que não seriam detectados com base no modo de exibição de um único serviço. Os watchdogs também são um bom local para hospedar código que executa ações corretivas sem interação do usuário (por exemplo, limpar arquivos de log no armazenamento em determinados intervalos de tempo). Você pode encontrar uma implementação de serviço de watchdog de exemplo [aqui](https://azure.microsoft.com/resources/samples/service-fabric-watchdog-service/).

## <a name="secure-using-certificates"></a>Proteção usando Certificados
Usando certificados, descreve como proteger a comunicação entre os diversos nós do seu cluster do Windows autônomo, bem como autenticar os clientes que estejam se conectando a esse cluster, usando os certificados X.509. Isso garante que somente usuários autorizados possam acessar o cluster e os aplicativos implantados e executar tarefas de gerenciamento. A segurança do certificado deve ser habilitada no cluster quando o cluster é criado.

### <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 e Service Fabric
Os certificados digitais X.509 são usados normalmente para autenticar clientes e servidores, criptografar e assinar mensagens digitalmente.

A tabela a seguir lista os certificados que serão necessárias na configuração do seu cluster:

|Configuração das Informações de Certificado |Descrição|
|-------------------------------|-----------|
|ClusterCertificate|    Esse certificado é necessário para proteger a comunicação entre os nós em um cluster. Você pode usar dois certificados diferentes, um principal e um secundário para atualização.|
|ServerCertificate| Esse certificado é apresentado ao cliente quando ele tenta se conectar a esse cluster. Você pode usar dois certificados de servidor diferentes, um principal e um secundário, para atualização.|
|ClientCertificateThumbprints|  Esse é um conjunto de certificados que você deseja instalar nos clientes autenticados.|
|ClientCertificateCommonNames|  Defina o nome comum do primeiro certificado do cliente para CertificateCommonName. A CertificateIssuerThumbprint é a impressão digital para o emissor deste certificado.|
|ReverseProxyCertificate|   Esse é um certificado opcional que poderá ser especificado se você desejar proteger o [Proxy Reverso](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy).|

Para obter mais informações sobre como proteger os certificados, [clique aqui](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security).

## <a name="role-based-access-control-rbac"></a>RBAC (Controle de Acesso Baseado em Função)
O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro. Dois tipos de controle de acesso diferentes têm suporte para clientes que se conectam a um cluster: funções de Administrador e de Usuário.

Os administradores têm acesso completo aos recursos de gerenciamento (incluindo recursos de leitura/gravação). Os usuários, por padrão, têm apenas acesso de leitura aos recursos de gerenciamento (por exemplo, recursos de consulta) e a capacidade de resolver serviços e aplicativos.

Especifique as funções de cliente do administrador e do usuário durante a criação do cluster, fornecendo identidades separadas (certificados, AAD, etc.) para cada uma. Para saber mais sobre as configurações de controle de acesso padrão e como alterar as configurações padrão, confira [Controle de acesso baseado em função para clientes do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

## <a name="secure-standalone-cluster-using-windows-security"></a>Proteger um cluster autônomo usando a segurança do Windows
Para evitar acesso não autorizado a um cluster do Service Fabric, você deve proteger o cluster. A segurança é especialmente importante quando o cluster executa cargas de trabalho de produção. Descreve como configurar a segurança de nó para nó e de cliente para nó usando a Segurança do Windows no arquivo ClusterConfig.JSON.

**Configurar a segurança do Windows usando gMSA**

A segurança entre nós é configurada definindo [ClustergMSAIdentity](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-windows-security) quando o Service Fabric precisar ser executado em gMSA. Para criar as relações de confiança entre os nós, eles deverão estar cientes uns dos outros.

A segurança de cliente para nó é configurada usando ClientIdentities. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar.

**Configurar a segurança do Windows usando um grupo de máquinas**

A segurança entre nós é definida usando a configuração ClusterIdentity, se você quiser usar um grupo de máquinas em um Domínio do Active Directory. Para saber mais, confira [Criar um grupo de máquinas no Active Directory](https://msdn.microsoft.com/library/aa545347).

A segurança de cliente para nó é configurada usando ClientIdentities. Para estabelecer a confiança entre um cliente e o cluster, você deverá configurar o cluster para saber em quais identidades de cliente ele poderá confiar. Você pode estabelecer confiança de duas maneiras diferentes:

-   Especificar os usuários do grupo de domínio que podem se conectar.
-   Especificar os usuários do nó de domínio que podem se conectar.

## <a name="configure-application-security-in-service-fabric"></a>Configuração de segurança de aplicativo no Service Fabric
### <a name="managing-secrets-in-service-fabric-applications"></a>Gerenciamento de segredos em aplicativos do Service Fabric
Este método ajuda a gerenciar segredos em um aplicativo do Service Fabric. Os segredos podem ser informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados como texto sem formatação.

Esta abordagem usa o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) para gerenciar chaves e segredos. No entanto, o uso de segredos em um aplicativo é independente da plataforma de nuvem para permitir que os aplicativos sejam implantados em um cluster hospedado em qualquer lugar. Há quatro etapas principais nesse fluxo:

-   Obtenha um certificado de codificação de dados.
-   Instale o certificado em seu cluster.
-   Criptografe valores do segredo ao implantar um aplicativo com o certificado e coloque-os no arquivo de configuração Settings.xml de um serviço.
-   Leia os valores criptografados de Settings.xml ao descriptografar com o mesmo certificado de codificação.

>[!Note]
>Saiba mais sobre [Gerenciamento de segredos em aplicativos do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="configure-security-policies-for-your-application"></a>Configurar políticas de segurança para seu aplicativo
Usando o Azure Service Fabric Security, é possível ajudar a proteger aplicativos em execução no cluster em contas de usuário diferentes. O Service Fabric Security também protege os recursos usados pelos aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.
As etapas são as seguintes:

-   Configurar a política para um ponto de entrada de instalação do serviço.
-   Iniciar comandos do PowerShell em um ponto de entrada de instalação.
-   Use o redirecionamento de console para depuração local.
-   Configurar uma política para pacotes de código de serviço.
-   Atribuir uma política de acesso de segurança a pontos de extremidade HTTP e HTTPS.

## <a name="secure-communication-for-services-in-azure-service-fabric-security"></a>Proteção de comunicação para serviços no Azure Service Fabric Security
A segurança é um dos aspectos mais importantes da comunicação. A estrutura de aplicativo dos Reliable Services fornece algumas pilhas e ferramentas de comunicação predefinidas que podem ser usadas para aprimorar a segurança.

-   [Ajudar a proteger um serviço quando você estiver usando a comunicação remota de serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication).
-   [Ajudar a proteger um serviço quando você estiver usando uma pilha de comunicação baseada em WCF](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-secure-communication#help-secure-a-service-when-youre-using-a-wcf-based-communication-stack).

## <a name="next-steps"></a>Próximas etapas
- Para obter informações conceituais sobre a segurança de cluster, consulte [criar um cluster no Azure usando um modelo do Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) e o [portal do Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-portal).
- Para saber mais consulte [Segurança do cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security).

