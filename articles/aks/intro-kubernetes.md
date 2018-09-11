---
title: Introdução ao Serviço de Kubernetes do Azure
description: O Serviço de Kubernetes do Azure simplifica a implantação e o gerenciamento de aplicativos baseados em contêiner no Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 294ccd70e0339ed5ad68022f002e4864fc52b452
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43286713"
---
# <a name="azure-kubernetes-service-aks"></a>AKS (Serviço do Kubernetes do Azure)

O AKS (Serviço de Kubernetes do Azure) simplifica a implantação de um cluster do Kubernetes gerenciado no Azure. O AKS reduz a complexidade e a sobrecarga operacional de gerenciar o Kubernetes passando grande parte dessa responsabilidade para o Azure. Como um serviço Kubernetes hospedado, o Azure lida com as tarefas críticas para você, como o monitoramento da integridade e a manutenção. Além disso, o serviço é gratuito; você paga apenas pelos nós de agentes dentro dos clusters, não pelos mestres.

Este documento fornece uma visão geral sobre os recursos do AKS (Serviço de Kubernetes do Azure).

## <a name="flexible-deployment-options"></a>Opções de implantação flexíveis

O Serviço de Kubernetes do Azure oferece opções de implantação de portal, linha de comando e gerado por modelos (modelos do Resource Manager e do Terraform). Ao implantar um cluster do AKS, o mestre do Kubernetes e todos os nós serão implantados e configurados para você. Recursos adicionais, como rede avançada, integração do Azure Active Directory e monitoramento, também podem ser configurados durante o processo de implantação.

Para obter mais informações, confira o [Início rápido do portal do AKS][aks-portal] ou o [Início rápido da CLI do AKS][aks-cli].

## <a name="identity-and-security-management"></a>Gerenciamento de segurança e identidade

Os clusters do AKS dão suporte a [RBAC (controle de acesso baseado em função)][kubernetes-rbac]. Um cluster do AKS também pode ser configurado para se integrar ao Azure Active Directory. Nessa configuração, o acesso Kubernetes pode ser configurado com base na associação de grupo e na identidade do Azure Active Directory.

Para obter mais informações, confira [Integrar o Azure Active Directory ao AKS][aks-aad].

## <a name="integrated-logging-and-monitoring"></a>Monitoramento e registro em log integrados

A integridade do contêiner oferece visibilidade de desempenho com a coleta de memória e métricas de processador de contêineres, nós e controladores. Os logs do contêiner também são coletados. Esses dados são armazenados em seu espaço de trabalho do Log Analytics e ficam disponíveis por meio do Portal do Azure, da CLI do Azure ou de um ponto de extremidade REST.

Para obter mais informações, confira [Monitorar a integridade de contêiner do Serviço de Kubernetes do Azure][container-health].

## <a name="cluster-node-scaling"></a>Dimensionamento de nó de cluster

À medida que aumenta a demanda por recursos, os nós de um cluster do AKS podem ser expandidos para fazer a correspondência. Se a demanda por recursos cair, os nós podem ser removidos com o dimensionamento no cluster. As operações de dimensionamento do AKS podem ser concluídas usando o Portal do Azure ou a CLI do Azure.

Para obter mais informações, confira [Dimensionar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-scale].

## <a name="cluster-node-upgrades"></a>Atualizações de nó de cluster

O Serviço de Kubernetes do Azure oferece várias versões de Kubernetes. Conforme novas versões ficam disponíveis no AKS, o cluster pode ser atualizado pelo Portal do Azure ou pela CLI do Azure. Durante o processo de atualização, os nós são cuidadosamente isolados e esvaziados para minimizar as interrupções nos aplicativos em execução.

Para obter mais informações, confira [Atualizar um cluster do AKS (Serviço de Kubernetes do Azure)][aks-upgrade].

## <a name="http-application-routing"></a>Roteamento de aplicativo HTTP

A solução de roteamento de aplicativo HTTP facilita o acesso a aplicativos implantados ao cluster AKS. Quando habilitada, a solução de roteamento do aplicativo HTTP configura um controlador de entrada em seu cluster do AKS. Quando os aplicativos são implantados, os nomes DNS publicamente acessíveis são configurados automaticamente.

Para obter mais informações, confira [Roteamento de aplicativo HTTP][aks-http-routing].

## <a name="gpu-enabled-nodes"></a>Nós habilitados para GPU

AKS oferece suporte à criação de pools de nó habilitados para GPU. Atualmente, o Azure fornece uma ou várias VMs habilitasdas para GPU. As VMs habilitadas para GPU são projetadas para cargas de trabalho de visualização e com muita computação e muitos gráficos.

Para obter mais informações, confira [Usando GPUs no AKS][aks-gpu].

## <a name="development-tooling-integration"></a>Integração de ferramentas de desenvolvimento

O Kubernetes tem um amplo ecossistema de ferramentas de desenvolvimento e gerenciamento, como o Helm, o Rascunho e a extensão do Kubernetes para o Visual Studio Code. Essas ferramentas funcionam perfeitamente com o Serviço de Kubernetes do Azure.

Além disso, o Azure Dev Spaces fornece uma experiência de desenvolvimento Kubernetes rápida e iterativa para equipes. Com a configuração mínima, você pode executar e depurar contêineres diretamente no AKS (Serviço de Kubernetes do Azure).

Para obter mais informações, confira [Azure Dev Spaces][azure-dev-spaces].

O projeto de DevOps do Azure fornece uma solução simples para trazer o código existente e o repositório Git para o Azure. O projeto de DevOps cria automaticamente recursos do Azure, como o AKS, um pipeline de versão no VSTS que inclui uma definição de versão de CI, configura uma definição de versão de CD e cria um recurso do Azure Application Insights para monitoramento.

Para obter mais informações, confira [Projeto de DevOps do Azure][azure-devops].

## <a name="virtual-network-integration"></a>Integração de rede virtual

Um cluster do AKS pode ser implantado em uma rede virtual existente. Nessa configuração, cada pod do cluster é atribuído a um endereço IP na rede virtual e pode se comunicar diretamente com outros pods no cluster e outros nós na VNet. Um pod também pode se conectar a outros serviços em uma VNet pareada e com redes locais, em conexões VPN site a site (S2S) e do ExpressRoute.

Para obter mais informações, confira a [Visão geral da rede do AKS][aks-networking].

## <a name="private-container-registry"></a>Registro de contêiner privado

Integre ao ACR (Registro de Contêiner do Azure) para ter armazenamento particular de suas imagens do Docker.

Para obter mais informações, confira [ACR (Registro de Contêiner do Azure)][acr-docs].

## <a name="storage-volume-support"></a>Suporte a volumes de armazenamento

O AKS (Serviço de Kubernetes do Azure) oferece suporte a volumes de armazenamento de montagem para dados persistentes. Os clusters do AKS são criados com suporte para Arquivos do Azure e Discos do Azure.

Para obter mais informações, confira [Arquivos do Azure][azure-files] e [Discos do Azure][azure-disk].

## <a name="docker-image-support"></a>Suporte a imagens do Docker

O AKS (Serviço de Kubernetes do Azure) oferece suporte ao formato de imagem do Docker.

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O AKS (Serviço de Kubernetes do Azure) foi certificado pela CNCF como compatível com o Kubernetes.

## <a name="regulatory-compliance"></a>Conformidade normativa

O AKS (Serviço de Kubernetes do Azure) está em conformidade com SOC, ISO e PCI DSS.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como implantar e gerenciar o AKS com os tutoriais de início rápido do AKS.

> [!div class="nextstepaction"]
> [Início rápido do AKS][aks-cli]

<!-- LINKS - external -->
[acs-engine]: https://github.com/Azure/acs-engine
[draft]: https://github.com/Azure/draft
[helm]: https://helm.sh/
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[kubernetes-rbac]: https://kubernetes.io/docs/reference/access-authn-authz/rbac/

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./aad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./networking-overview.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./scale-cluster.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/en-us/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/en-us/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md

