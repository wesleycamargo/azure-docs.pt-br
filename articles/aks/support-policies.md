---
title: Políticas de suporte para o serviço de Kubernetes do Azure (AKS)
description: Saiba mais sobre as políticas de suporte, responsabilidade compartilhada e recursos que estão em versão prévia (ou alfa ou beta) do serviço de Kubernetes do Azure (AKS).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 9b779021eca11638e8ee52ec11d082e5b0e89cd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506675"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Políticas de suporte do serviço Kubernetes do Azure

Este artigo fornece detalhes sobre as políticas de suporte técnico e limitações para o serviço de Kubernetes do Azure (AKS). O artigo também fornece detalhes sobre gerenciamento de nós de trabalho, componentes de plano do controle gerenciado, componentes de software livre de terceiros e gerenciamento de segurança ou de patch.

## <a name="service-updates-and-releases"></a>Versões e atualizações de serviço

* Para obter informações de versão, consulte [notas de versão do AKS](https://github.com/Azure/AKS/releases).
* Para obter informações sobre os recursos na versão prévia, consulte [AKS visualizar recursos e projetos relacionados](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Gerenciado recursos no AKS

Infraestrutura de base como componentes de nuvem um serviço (IaaS), como computação ou componentes de rede, conceder aos usuários acesso a controles de nível baixo e opções de personalização. Por outro lado, o AKS fornece uma implantação pronta para uso do Kubernetes que dá aos clientes o conjunto comum de configurações e recursos que precisam. Os clientes do AKS limitaram a personalização, implantação e outras opções. Esses clientes não precisam se preocupar sobre ou gerenciar clusters do Kubernetes diretamente.

Com o AKS, o cliente recebe um totalmente gerenciado *plano de controle*. O plano de controle contém todos os componentes e serviços que o cliente precisa para operar e oferecer clusters Kubernetes para os usuários finais. Todos os componentes de Kubernetes são mantidos e operados pela Microsoft.

A Microsoft gerencia e monitora os seguintes componentes por meio do painel de controle:

* Servidores Kubelet ou a API do Kubernetes
* Etcd ou um repositório de chave-valor compatível, fornecendo a qualidade de serviço (QoS), escalabilidade e tempo de execução
* Serviços DNS (por exemplo, kube-dns ou CoreDNS)
* Proxy de Kubernetes ou o sistema de rede

AKS não é uma solução de cluster totalmente gerenciado. Alguns componentes, como nós de trabalho têm *responsabilidade compartilhada*, em que os usuários devem ajudar a manter o cluster do AKS. Entrada do usuário é necessária, por exemplo, para aplicar um patch de segurança de sistema operacional (SO) de nó de trabalho.

Os serviços estão *gerenciados* no sentido de que a Microsoft e a equipe AKS implanta, opera e é responsáveis pela disponibilidade do serviço e a funcionalidade. Os clientes não é possível alterar esses componentes gerenciados. Microsoft limita a personalização para garantir uma experiência de usuário consistente e escalável. Para uma solução totalmente personalizável, consulte [AKS mecanismo](https://github.com/Azure/aks-engine).

> [!NOTE]
> Nós de trabalho do AKS aparecem no portal do Azure como recursos de IaaS do Azure regulares. Mas essas máquinas virtuais são implantadas em um grupo de recursos do Azure personalizado (prefixados com MC\\*). É possível alterar nós de trabalho do AKS. Por exemplo, você pode usar o SSH (Secure Shell) para alterar a maneira como você alternar máquinas de virtuais normais de nós de trabalho do AKS (você não pode, no entanto, alterar a imagem base do sistema operacional, e as alterações não podem persistir por meio de uma atualização ou reinicialização), e você pode anexar outros recursos do Azure no AKS nós de trabalho. Mas quando você faz alterações *fora do gerenciamento de banda e personalização,* o cluster do AKS pode ficar sem suporte. Evite alterar os nós de trabalho, a menos que o Microsoft Support direciona você para fazer alterações.

## <a name="shared-responsibility"></a>Responsabilidade compartilhada

Quando um cluster é criado, o cliente define os nós de trabalho de Kubernetes AKS cria. Cargas de trabalho do cliente são executadas em nós. Os clientes possuem e podem exibir ou modificar os nós de trabalho.

Como nós de cluster de cliente executar código privado e armazenam dados confidenciais, o Microsoft Support pode acessá-los de apenas uma maneira limitada. Microsoft Support não pode entrar, executar comandos em ou exibir os logs para esses nós sem permissão expressa de cliente ou obter assistência.

Como nós de trabalho são confidenciais, a Microsoft leva muito cuidadoso a fim de limitar seu gerenciamento em segundo plano. Em muitos casos, sua carga de trabalho continue em execução, mesmo se o Kubernetes mestre nós, etcd e outras falhas de componentes gerenciados pela Microsoft. Nós de trabalho modificado com negligência podem causar perda de dados e cargas de trabalho e podem renderizar o cluster sem suporte.

## <a name="aks-support-coverage"></a>Cobertura de suporte do AKS

A Microsoft fornece suporte técnico para o seguinte:

* Conectividade com todos os componentes do Kubernetes que o serviço de Kubernetes fornece e dá suporte, como o servidor de API.
* Gerenciamento, tempo de atividade, QoS e operações do Kubernetes controlam os serviços de plano (nós mestres de Kubernetes, API server, etcd e kube-dns, por exemplo).
* Etcd. O suporte inclui backups automatizados, transparentes de todos os dados etcd a cada 30 minutos para restauração de estado de planejamento e o cluster de desastre. Esses backups não estão diretamente disponíveis aos clientes ou usuários. Eles garantem a consistência e confiabilidade de dados.
* Quaisquer pontos de integração no driver do provedor de nuvem do Azure para Kubernetes. Eles incluem integrações a outros serviços do Azure, como balanceadores de carga, volumes persistentes ou rede (Kubernetes e CNI do Azure).
* Dúvidas ou problemas sobre a personalização do controle do plano de componentes, como o servidor de API do Kubernetes, etcd e kube-dns.
* Problemas sobre questões de rede, como o CNI do Azure, kubenet, ou outro acesso à rede e a funcionalidade. Pode incluir a problemas de DNS resolução, perda de pacotes, roteamento e assim por diante. A Microsoft oferece suporte a vários cenários de rede:
  * Kubenet (basic) e o advanced networking (CNI do Azure) dentro do cluster e componentes associados
  * Conectividade com outros aplicativos e serviços do Azure
  * Controladores de entrada e configurações do balanceador de carga ou de entrada
  * Desempenho de rede e latência

Microsoft não fornece suporte técnico para o seguinte:

* Perguntas sobre como usar o Kubernetes. Por exemplo, o Microsoft Support não fornece conselhos sobre como criar controladores de entrada personalizada, use as cargas de trabalho do aplicativo ou aplicar pacotes de software de terceiros ou de software livre ou ferramentas.
  > [!NOTE]
  > Microsoft Support podem aconselhar sobre a funcionalidade do cluster AKS, personalização e ajuste (por exemplo, problemas de operações do Kubernetes e procedimentos).
* Projetos de código-fonte aberto de terceiros que não são fornecidos como parte do que o Kubernetes controlam o plano ou implantados com clusters AKS. Esses projetos podem incluir Istio, Helm, Envoy ou outras pessoas.
  > [!NOTE]
  > Microsoft pode fornecer suporte de melhor esforço para projetos de código-fonte aberto de terceiros, como o Helm e Kured. Em que a ferramenta de software livre de terceiros se integra com o provedor de nuvem do Kubernetes Azure ou outros bugs específicos do AKS, a Microsoft dá suporte a exemplos e aplicativos da documentação da Microsoft.
* Software de código-fonte fechado de terceiros. Esse software pode incluir ferramentas de verificação de segurança e dispositivos de rede ou software.
* Problemas de saídas de compilação multinuvem ou vários fornecedores. Por exemplo, Microsoft não oferece suporte a problemas relacionados à execução de uma solução de fornecedor de nuvem multipublic federado.
* Rede personalizações diferente das listadas as [documentação do AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft oferece suporte a problemas e bugs relacionados aos grupos de segurança de rede (NSGs). Por exemplo, Microsoft Support pode responder a perguntas sobre uma falha NSG para atualizar ou um comportamento inesperado de Balanceador de carga ou NSG.

## <a name="aks-support-coverage-for-worker-nodes"></a>Cobertura de suporte de AKS para nós de trabalho

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilidades da Microsoft para nós de trabalho do AKS

A Microsoft e os clientes compartilham a responsabilidade para nós de trabalho do Kubernetes em que:

* A imagem base do sistema operacional exigida adições (por exemplo, monitoramento e agentes de rede).
* Os nós de trabalho recebem os patches do sistema operacional automaticamente.
* Componentes que são executados em nós de trabalho são corrigidos automaticamente de plano de controle de problemas com o Kubernetes. Os componentes incluem o seguinte:
  * Kube-proxy
  * componentes de mestre de túneis de rede que fornecem os caminhos de comunicação para o Kubernetes
  * kubelet
  * Daemon do docker ou Moby

> [!NOTE]
> Em um nó de trabalho, se um componente do plano de controle não está funcionando, a equipe AKS talvez seja necessário reinicializar o nó de trabalho inteira. Devido a seu acesso restrito aos dados e cargas de trabalho ativo do cliente, a equipe AKS reinicializa um nó de trabalho somente se o cliente escala o problema. Sempre que possível, a equipe AKS trabalha para impedir que uma reinicialização necessária para afetar o aplicativo.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilidades do cliente para nós de trabalho do AKS

Microsoft não reinicializar automaticamente nós de trabalho para aplicar patches de nível de sistema operacional. Embora os patches do sistema operacional são entregues a nós de trabalho, o *cliente* é responsável por reinicializar os nós de trabalho para aplicar as alterações. Bibliotecas compartilhadas, daemons, como a unidade de estado sólido híbrido (SSHD) e outros componentes no nível do sistema ou do sistema operacional são corrigidas automaticamente.

Os clientes são responsáveis por executar as atualizações do Kubernetes. Eles podem executar as atualizações por meio do painel de controle do Azure ou a CLI do Azure. Isso se aplica a atualizações que contêm a segurança ou melhorias de funcionalidade no Kubernetes.

> [!NOTE]
> Porque o AKS é um *serviço gerenciado*, seus objetivos finais incluem a remoção de responsabilidade de patches, atualizações e coleta para tornar o gerenciamento de serviço mais completo e participação ativa do log. À medida que aumenta a capacidade de gerenciamento de ponta a ponta do serviço, as versões futuras podem omitir algumas funções (por exemplo, a reinicialização do nó e a aplicação de patch automática).

### <a name="security-issues-and-patching"></a>Problemas de segurança e aplicação de patch

Se uma falha de segurança for encontrada em um ou mais componentes do AKS, a equipe AKS corrigirá todos os clusters afetados para atenuar o problema. Como alternativa, a equipe oferece aos usuários orientações sobre a atualização.

Para nós de trabalho que uma afeta uma falha de segurança, se um patch de tempo de inatividade zero está disponível, a equipe AKS aplicará esse patch e notificar os usuários da alteração.

Quando um patch de segurança exige reinicializações de nó de trabalho, a Microsoft notificará os clientes desse requisito. O cliente é responsável pela reinicialização ou a atualização para obter o patch de cluster. Se os usuários não aplicam os patches de acordo com as diretrizes de AKS, o seu cluster continuará a estar vulnerável ao problema de segurança.

### <a name="node-maintenance-and-access"></a>Acesso e a manutenção de nó

Nós de trabalho são uma responsabilidade compartilhada e são de propriedade pelos clientes. Por isso, os clientes têm a capacidade de entrar em seus nós de trabalho e fazer alterações potencialmente prejudiciais, como atualizações de kernel e instalar ou remover os pacotes.

Se os clientes fazer alterações destrutivas ou fazer com que o controle de componentes de plano ficar offline ou não funcionar, AKS detectará essa falha e automaticamente restaurar o nó de trabalho para o estado de funcionamento anterior.

Embora os clientes podem entrar e alterar nós de trabalho, isso não é recomendada porque as alterações podem fazer um cluster sem suporte.

## <a name="network-ports-access-and-nsgs"></a>Portas de rede, acesso e NSGs

Como um serviço gerenciado, o AKS tem requisitos específicos de rede e de conectividade. Esses requisitos são menos flexíveis que requisitos para os componentes de IaaS normais. No AKS, operações como personalizar regras NSG, bloqueando uma porta específica (por exemplo, usando as regras de firewall que bloqueiam a porta de saída 443) e lista de URLs pode tornar seu cluster sem suporte.

> [!NOTE]
> Atualmente, AKS não permite que você bloqueie completamente a saída do seu cluster (por exemplo, domínio explícita ou lista de permissões de porta). A lista de URLs e portas está sujeitas a alterações sem aviso. Você pode obter a lista atualizada com a criação de um tíquete de suporte do Azure. A lista é somente para clientes que estão dispostos a aceitar que sua disponibilidade de cluster pode ser afetada *a qualquer momento.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Recursos sem suporte alfa e beta de Kubernetes

AKS oferece suporte a apenas estáveis recursos dentro do projeto Kubernetes upstream. A menos que documentado em contrário, o AKS não oferece suporte a recursos de alfa e beta que estão disponíveis no projeto Kubernetes upstream.

Nos dois cenários, recursos alfa ou beta podem ser distribuídos antes que elas estão geralmente disponíveis:

* Os clientes foram atendidos com o produto do AKS, suporte ou equipes de engenharia e pediram para experimentar esses novos recursos.
* Esses recursos foram [habilitado por um sinalizador de recursos](https://github.com/Azure/AKS/blob/master/previews.md). Os clientes devem aceitar explicitamente para usar esses recursos.

## <a name="preview-features-or-feature-flags"></a>Recursos de visualização ou sinalizadores de recursos

Para recursos e funcionalidades que exigem teste estendidos e comentários do usuário, a Microsoft lança novos recursos de visualização ou recursos por trás de um sinalizador de recursos. Considere esses recursos como recursos de pré-lançamento ou beta.

Recursos de visualização ou de sinalizador de recursos não são destinados a produção. As mudanças contínuas em APIs e comportamento, correções de bug e outras alterações podem resultar em tempo de inatividade e clusters instáveis.

Recursos em visualização pública são outono no suporte de 'melhor esforço' como esses recursos estão em versão prévia e não destinados a produção e são compatíveis com as equipes de suporte técnico do AKS horário comercial. Para obter mais informações, consulte:

* [Perguntas frequentes sobre o suporte do Azure.](https://azure.microsoft.com/en-us/support/faq/)

> [!NOTE]
> Recursos de visualização entrarão em vigor no Azure *assinatura* nível. Não instale a versão prévia dos recursos em uma assinatura de produção. Em uma assinatura de produção, recursos de visualização podem alterar o comportamento da API padrão e afetar as operações normais.

## <a name="upstream-bugs-and-issues"></a>Problemas e erros de upstream

Erros devido a velocidade de desenvolvimento do projeto Kubernetes upstream, invariavelmente surgem. Alguns desses bugs não podem ser corrigido ou contornado dentro do sistema do AKS. Em vez disso, correções de bug exigem maiores patches aos projetos upstream (como Kubernetes, sistemas operacionais de nó ou de trabalho e kernels). Para componentes que a Microsoft possui (por exemplo, o provedor de nuvem do Azure), o AKS e a equipe do Azure é confirmadas para corrigir problemas de upstream na comunidade.

Quando um problema de suporte técnico é causados por raiz por um ou mais bugs de upstream, as equipes de engenharia e suporte ao AKS serão:

* Identifique e vincular os bugs upstream com quaisquer detalhes de suporte para ajudar a explicar por que esse problema afeta o seu cluster ou a carga de trabalho. Os clientes recebem links para os repositórios necessários para que possam assistir os problemas e ver quando uma nova versão será fornecer correções.
* Fornece possíveis soluções alternativas ou atenuações. Se o problema poderá ser reduzido, uma [problema conhecido](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) serão arquivados no repositório do AKS. Explica o arquivamento de problema conhecido:
  * O problema, incluindo links para bugs de upstream.
  * A solução alternativa e detalhes sobre uma atualização ou de outra persistência da solução.
  * Aproximado cronogramas para a inclusão desse problema, com base na cadência de lançamento de upstream.
