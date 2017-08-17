---
title: "Melhores práticas de segurança do Azure Service Fabric | Microsoft Docs"
description: "Este artigo fornece um conjunto de melhores práticas de segurança do Azure Service Fabric."
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
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3132e42aac72c0132c7526ac56d80bc5eec269e7
ms.contentlocale: pt-br
ms.lasthandoff: 08/09/2017

---
# <a name="azure-service-fabric-security-best-practices"></a>Melhores práticas de segurança do Azure Service Fabric
Implantar um aplicativo no Azure é rápido, fácil e econômico. Antes de implantar o aplicativo na nuvem em produção, é útil ter uma melhor prática para ajudar na avaliação de seu aplicativo mediante uma lista de melhores práticas recomendadas.

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric resolve os desafios significativos de desenvolvimento e gerenciamento de aplicativos em nuvem. Desenvolvedores e administradores podem evitar problemas complexos de infraestrutura e se concentrarem na implementação de cargas de trabalho essenciais e exigentes que são escalonáveis, confiáveis e gerenciáveis. 

Para cada prática recomendada, vamos explicar:

-   O que é a prática recomendada
-   Por que é ideal habilitar essa prática recomendada
-   O que poderá acontecer se você não habilitar a prática recomendada
-   Como você pode aprender a habilitar a prática recomendada

No momento, temos as seguintes melhores práticas de segurança do Azure Service Fabric:

-   Use o modelo do ARM (Azure Resource Manager) e o módulo Azure PowerShell do Service Fabric para criar um cluster seguro
-   Usar certificados X.509
-   Configurar políticas de segurança
-   Configuração de segurança de Reliable Actors
-   Configurar o SSL para o Azure Service Fabric
-   Segurança/isolamento de Rede com o Azure Service Fabric
-   Configurar um cofre de chaves de segurança
-   Atribuir usuários a funções


## <a name="best-practices-for-securing-your-cluster"></a>Melhores para proteger o cluster

**Visão global**

Usar sempre um cluster seguro
-   Segurança de cluster: usar certificados
-   Acesso de cliente (Admin e Somente leitura): usar o AAD

Usar implantações automatizadas
-   Usar scripts para gerar, implantar e sobrepor segredos
-   Manter os segredos no KV e usar o AD para todos os outros acessos de cliente
-   Nenhum humano deve ter acesso a eles sem autenticação.

Além disso, considere o seguinte:
-   Criar DMZs usando NSGs (grupos de segurança de rede)
-   Usar Servidores de atalho para RDP em VMs do cluster ou para gerenciar o cluster

Os clusters sempre devem ser protegidos para evitar que usuários não autorizados se conectem ao cluster, especialmente quando ele tiver cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, fazer isso permitirá que usuários anônimos se conectem a ele se ele expuser pontos de extremidade de gerenciamento na Internet pública.

Tecnologias usadas para implementar esses cenários. Os [cenários de segurança do cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) são:

-   Segurança nó a nó: ela protege a comunicação entre VMs e computadores no cluster. Isso faz com que somente os computadores autorizados a ingressar no cluster possam hospedar aplicativos e serviços no cluster.
Os clusters em execução no Azure ou clusters autônomos em execução no Windows podem usar a [Segurança de Certificado](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security) ou então a [Segurança do Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-windows-cluster-windows-security) para computadores Windows Server.
-   Segurança cliente a nó: ela protege a comunicação entre um cliente do Service Fabric e nós individuais do cluster.
-   RBAC (controle de acesso baseado em função): especifique as funções de cliente do administrador e do usuário durante a criação do cluster, fornecendo identidades separadas (certificados, AAD, etc.) para cada uma.
-   Recomendações de segurança: para clusters do Azure, é recomendável usar a segurança do AAD para autenticar clientes e certificados para a segurança de nó para nó.

Para configurar o cluster do Windows autônomo, confira [Definir as configurações de cluster do windows autônomo](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Use o modelo do Azure Resource Manager e o módulo Azure PowerShell do Service Fabric para criar um cluster seguro.
Um guia passo a passo orienta você pela configuração de um cluster do Azure Service Fabric seguro no Azure usando o Azure Resource Manager está disponível [aqui](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm).

Usar o modelo do Azure Resource Manager para personalizar o cluster
-   Armazenamento gerenciado por configuração para VHDs de VM

Usar o modelo do Azure Resource Manager para fazer alterações no seu grupo de recursos
-   Gerenciamento de configuração fácil
-   Auditoria

Tratar a configuração do cluster como código
-   Seja criterioso ao verificar as configurações escolhidas para implantar
-   Evite usar comandos implícitos para ajustar seus recursos diretamente

Muitos aspectos do [ciclo de vida de um aplicativo do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-lifecycle) podem ser automatizados. O [Módulo Azure PowerShell do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications#upload-the-application-package) automatiza tarefas comuns de implantação, atualização, remoção e teste de aplicativos do Service Fabric. APIs gerenciadas e HTTP para gerenciamento de aplicativos também estão disponíveis.

## <a name="use-x509-certificates"></a>Usar certificados X.509
Os clusters sempre devem ser protegidos usando certificados X.509 ou a segurança do Windows. A segurança só é configurada no momento de criação do cluster e não é possível habilitar a segurança após a criação dele.

Se estiver especificando um [certificado de cluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-windows-cluster-x509-security), defina o valor de ClusterCredentialType como X509. Se estiver especificando um certificado do servidor para conexões externas, defina ServerCredentialType como X509.

-   Os certificados usados em clusters que executam cargas de trabalho de produção devem ser criados por meio de um serviço de certificado do Windows Server configurado corretamente ou obtidos por meio de uma AC (Autoridade de Certificação) aprovada.
-   Nunca use nenhum certificado temporário ou de teste em produção criado com ferramentas como MakeCert.exe.
-   Você pode usar um certificado autoassinado, mas deve fazer isso somente para clusters de teste e não em produção.

Se o cluster não é seguro. Qualquer pessoa pode conectar-se anonimamente e realizar operações de gerenciamento, portanto, os clusters de produção sempre devem ser protegidos usando os certificados x.509 ou a segurança do Windows.

Para saber mais sobre como habilitar os certificados no cluster do Service Fabric, confira [Adicionar ou remover certificados para um cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure).

## <a name="configure-security-policies"></a>Configurar políticas de segurança
O Service Fabric também protege os recursos usados pelos aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.

-   Use um grupo de domínio ou usuário do Active Directory: você pode executar o serviço com as credenciais de uma conta de usuário ou de grupo do Active Directory. Esse é o Active Directory local em seu domínio e não é com o Azure Active Directory (Azure AD). Ao usar um usuário de domínio ou grupo, você pode acessar outros recursos do domínio (por exemplo, compartilhamentos de arquivos) que receberam permissões.

-   Atribuir política de acesso de segurança para pontos de extremidade HTTP e HTTPS: se você aplicar uma política RunAs a um serviço e o manifesto do serviço declarar recursos de ponto de extremidade com o protocolo HTTP, será necessário especificar uma SecurityAccessPolicy para assegurar que as portas alocadas a esses pontos de extremidade sejam corretamente listadas no controle de acesso para a conta de usuário RunAs na qual o serviço é executado. Caso contrário, o http.sys não terá acesso ao serviço e você receberá uma falha com chamadas do cliente.
Para saber mais sobre como habilitar políticas de segurança do Service Fabric, confira [Configurar políticas de segurança para seu aplicativo](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-runas-security).

## <a name="reliable-actors-security-configuration"></a>Configuração de segurança de Reliable Actors
Os Reliable Actors do Service Fabric são uma implementação do padrão de design de ator. Assim como ocorre com qualquer padrão de design de software, a escolha de determinado padrão leva em conta se um problema relacionado ao design de software é adequado ao padrão ou não.

Como diretriz geral, considere o padrão de ator para modelar seu problema ou cenário se:
-   Seu problema de espaço envolve um grande número (milhares ou milhões) de pequenas unidades de estado e lógica que, além de serem independentes, são isoladas.
-   Você deseja trabalhar com objetos single-threaded que não exigem interação significativa de componentes externos, incluindo a consulta de estado em um conjunto de atores.
-   Suas instâncias de ator não bloquearão chamadores com atrasos imprevisíveis emitindo operações de E/S.

No Service Fabric, os atores são implementados na estrutura dos Reliable Actors: uma estrutura do aplicativo baseada no padrão de ator criada nos [Reliable Services do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-introduction). Cada serviço escrito do Reliable Actor é, de fato, um Reliable Service com estado particionado.
Cada ator é definido como uma instância de um tipo de ator, da mesma forma que um objeto do .NET é uma instância de um tipo do .NET. Por exemplo, pode haver um tipo de ator que implementa a funcionalidade de uma calculadora e pode haver muitos atores desse tipo que são distribuídos em vários nós em um cluster. Cada ator desse é exclusivamente identificado por uma ID de ator.

As [configurações de segurança do replicador](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-actors-kvsactorstateprovider-configuration) servem para proteger o canal de comunicação que é usado durante a replicação. Isso significa que os serviços não podem ver o tráfego de replicação uns dos outros, garantindo que os dados que têm alta disponibilidade também estejam seguros. Por padrão, uma seção de configuração de segurança vazia evita a segurança de replicação.
Configurações do replicador configuram o replicador que será responsável por tornar o Provedor de Estado do Ator altamente confiável.

## <a name="configure-ssl-for-azure-service-fabric"></a>Configurar o SSL para o Azure Service Fabric

Autenticação de servidor: [autentica](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) os pontos de extremidade de gerenciamento de cluster para um cliente de gerenciamento, para que o gerenciamento do cliente saiba que está se comunicando com o cluster real. Esse certificado também fornece um [SSL](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm) para a API de gerenciamento de HTTPS e para o Service Fabric Explorer sobre HTTPS.
Você deve obter um nome de domínio personalizado para seu cluster. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para seu cluster.

Para configurar SSL de um aplicativo, você deve primeiro obter um certificado SSL assinado por uma Autoridade de Certificação (CA), um terceiro confiável que emita certificados com essa finalidade. Se ainda não tiver um, você precisará obtê-lo junto a uma empresa que venda certificados SSL.

O certificado deve atender aos seguintes requisitos para certificados SSL no Azure:
-   O certificado deve conter uma chave privada.
-   O certificado deve ser criado para troca de chaves, exportável para um arquivo Troca de Informações Pessoais (.pfx).
-   O nome de assunto do certificado deve corresponder ao domínio usado para acessar o serviço de nuvem. Você não pode obter um certificado SSL de uma autoridade de certificação (CA) para o domínio cloudapp.net. Você deve adquirir um nome de domínio personalizado para usar quando acessar o serviço. Quando você solicitar um certificado de uma autoridade de certificação, o nome de assunto do certificado deve corresponder ao nome de domínio personalizado usado para acessar o aplicativo. Por exemplo, se o nome de domínio personalizado for contoso.com, você pode solicitar um certificado da autoridade de certificação para **.contoso.com** ou **www.contoso.com**
-   O certificado deve usar, no mínimo, uma criptografia de 2048 bits.

HTTP não é seguro e está sujeito a ataques de espionagem, pois os dados que estão sendo transferidos do navegador da Web para o servidor Web ou entre outros pontos de extremidade são transmitidos em texto sem formatação. Isso significa que os invasores podem interceptar e exibir dados confidenciais, como detalhes de cartão de crédito e logons de conta. Quando os dados são enviados ou postados em um navegador usando HTTPS, o protocolo SSL faz com que essas informações sejam criptografadas e fiquem protegidas contra interceptação.

Para saber mais, confira [Configurar SSL para o aplicativo do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-configure-ssl-certificate).

## <a name="network-isolationsecurity-with-azure-service-fabric"></a>Segurança/isolamento de Rede com o Azure Service Fabric
Use o [modelo do ARM (Azure Resource Manager)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) como um exemplo para configurar um cluster seguro de três tipos de nó segura e controlar o tráfego de rede de entrada e saída usando Grupos de Segurança de Rede.

O modelo tem um Grupo de Segurança de Rede para cada VMSS (conjunto de dimensionamento de máquinas virtuais) a fim de controlar o tráfego dentro e fora do VMSS. Por padrão, as regras são configuradas para permitir todo o tráfego necessário aos serviços do sistema e às portas de aplicativo especificadas no modelo. Examine essas regras e faça alterações de acordo com suas necessidades, incluindo adicionar novas regras para seus aplicativos.

Para saber mais, confira [Azure Service Fabric – cenários comuns de rede](https://docs.microsoft.com/azure/service-fabric/service-fabric-patterns-networking).

## <a name="set-up-a-key-vault-for-security"></a>Configurar um cofre de chaves de segurança
Os certificados são usados no Service Fabric para fornecer autenticação e criptografia para proteger vários aspectos de um cluster e de seus aplicativos.

O Service Fabric usa certificados x.509 para proteger um cluster e fornecer recursos de segurança do aplicativo. O Key Vault é usado para [gerenciar certificados](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-update-certs-azure) de clusters do Service Fabric no Azure. Quando um cluster é implantado no Azure, o provedor de recursos do Azure responsável pela criação de clusters do Service Fabric recebe certificados do Key Vault e os instala nas VMs do cluster.

O diagrama a seguir ilustra o relacionamento entre o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), um cluster do Service Fabric e o provedor de recursos do Azure que usa certificados armazenados em um cofre de chaves quando ele cria um cluster.

**Criar um grupo de recursos** A primeira etapa é criar um grupo de recursos especificamente para o cofre de chaves. Recomendamos que você coloque o cofre de chaves em seu próprio grupo de recursos. Essa ação permite que você remova os grupos de recursos de computação e armazenamento, incluindo o grupo de recursos que contém o cluster do Service Fabric sem perder suas chaves e seus segredos. O grupo de recursos que contém o cofre de chaves tem que estar na mesma região que o cluster que está sendo usado.

**Criar um cofre de chaves no novo grupo de recursos** O cofre de chaves tem que estar habilitado para implantação para permitir que o provedor de recursos de computação obtenha certificados e os instale em instâncias de máquina virtual.
Para saber mais sobre como configurar o Azure Key Vault, confira [Introdução ao Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).

## <a name="assign-users-roles"></a>Atribuir funções de usuários
Depois de criar os aplicativos para representar seu cluster, atribua os usuários às funções com suporte no Service Fabric: somente leitura e administrador. Você pode atribuir as funções usando o portal clássico do Azure.

>[!Note]
> Para saber mais sobre as funções no Service Fabric, veja [Controle de acesso baseado em função para clientes do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security-roles).

O Azure Service Fabric dá suporte a dois tipos de controle de acesso diferentes para clientes conectados a um [cluster do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm): administrador e usuário. O controle de acesso permite que o administrador de cluster limite o acesso a determinadas operações de cluster para diferentes grupos de usuários, tornando o cluster mais seguro.

## <a name="next-steps"></a>Próximas etapas
- Configurando o [ambiente de desenvolvimento](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started).
- Saiba mais sobre as [opções de suporte do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).


