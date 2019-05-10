---
title: Introdução ao Red Hat OpenShift no Azure | Microsoft Docs
description: Aprenda os recursos e benefícios do Red Hat OpenShift no Azure na Microsoft para implantar e gerenciar aplicativos baseados em contêiner.
services: container-service
author: tylermsft
ms.author: twhitney
ms.service: container-service
manager: jeconnoc
ms.topic: overview
ms.date: 05/06/2019
ms.custom: mvc
ms.openlocfilehash: 6121c0f654a61a147e84f0697f3ddb06b7c5db92
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079944"
---
# <a name="azure-red-hat-openshift"></a>Red Hat OpenShift no Azure

O serviço *Red Hat OpenShift no Azure* da Microsoft permite que você implante clusters [OpenShift](https://www.openshift.com/) totalmente gerenciados.

O Red Hat OpenShift no Azure amplia o [Kubernetes](https://kubernetes.io/). A execução de contêineres em produção com o Kubernetes requer ferramentas e recursos adicionais, como um registro de imagens, gerenciamento de armazenamento, soluções de rede e ferramentas de registro e monitoramento, e todos devem ser testados e ter controle de versão. Criar aplicativos baseados em contêiner requer mais trabalho de integração com o middleware, estruturas, bancos de dados e ferramentas de CI/CD. O Red Hat OpenShift no Azure combina tudo isso em uma única plataforma, facilitando as operações das equipes de TI enquanto fornece o aplicativo que elas necessitam para trabalhar.

O Red Hat OpenShift no Azure é desenvolvido, operado e compatível com Red Hat e Microsoft para fornecer uma experiência de suporte integrado em conjunto. Não existem máquinas virtuais para operar e nenhuma aplicação de patch é necessária. Os nós mestre, de infraestrutura e de aplicativo são corrigidos, atualizados e monitorados em seu nome pela Red Hat e Microsoft. Os clusters do Red Hat OpenShift no Azure são implantados em sua assinatura do Azure e são incluídos na sua fatura do Azure.

Você pode escolher suas próprias soluções de registro, rede, armazenamento e CI/CD, ou usar as soluções internas para gerenciamento automatizado de código fonte, criações de contêiner e aplicativo, implantações, dimensionamento, gerenciamento de integridade e muito mais. O Red Hat OpenShift no Azure fornece uma experiência de logon integrada por meio do Azure Active Directory.

Para começar, conclua o tutorial [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md).

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitoramento

Para gerenciamento e segurança aprimorados, o Red Hat OpenShift no Azure permite integrar com o AAD (Azure Active Directory) e usar RBAC (controle de acesso baseado em função) do Kubernetes. Você também pode monitorar a integridade do cluster e dos recursos.

## <a name="cluster-and-node"></a>Cluster e nó

Os nós do Red Hat OpenShift no Azure são executados em máquinas virtuais do Azure. Você pode conectar o armazenamento a nós e pods, atualizar componentes do cluster e usar GPUs.

## <a name="virtual-networks-and-ingress"></a>Entrada e redes virtuais

Você pode implantar um cluster do Red Hat OpenShift no Azure em uma rede virtual existente. Nessa configuração, cada pod no cluster é atribuído um endereço IP na rede virtual e pode se comunicar diretamente com outros pods no cluster e outros nós na rede virtual. Os pods também podem se conectar a outros serviços em uma rede virtual emparelhada e a redes locais com conexões [ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou VPN site a site (S2S).

Para obter mais informações, confira [Criar um cluster Microsoft Red Hat OpenShift no Azure](tutorial-create-cluster.md).

## <a name="kubernetes-certification"></a>Certificação Kubernetes

O serviço do Red Hat OpenShift no Azure foi certificado na CNCF como compatível com o Kubernetes.

## <a name="next-steps"></a>Próximas etapas

Conheça os pré-requisitos do Red Hat OpenShift no Azure:

> [!div class="nextstepaction"]
> [Configurar seu ambiente de desenvolvimento](howto-setup-environment.md)