---
title: Práticas recomendadas do operador – Gerenciamento de imagem do contêiner nos Serviços de Kubernetes do Azure (AKS)
description: Conheça as práticas recomendadas de operador do cluster para saber como gerenciar e proteger imagens de contêiner no serviço de Kubernetes do Azure (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 1cc91f55d3895f06176875cb9ae620685dc09a26
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53605544"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Práticas recomendadas para gerenciamento de imagens de contêiner e a segurança no Serviço de Kubernetes do Azure (AKS)

Ao desenvolver e executar aplicativos no serviço de Kubernetes do Azure (AKS), a segurança de seus contêineres e imagens de contêiner é uma consideração importante. Contêineres que incluam as imagens base desatualizadas ou tempo de execução do aplicativo sem patch apresentam um risco de segurança e um possível vetor de ataque. Para minimizar esses riscos, você deve integrar ferramentas que verificam e corrigem problemas em seus contêineres no momento da criação, bem como tempo de execução. Quanto mais cedo no processo de vulnerabilidade ou da imagem de base desatualizada capturada, mais protegido o cluster. Neste artigo *contêineres* significa as duas as imagens de contêiner armazenadas em um registro de contêiner e os contêineres em execução.

Este artigo se concentra em como proteger seus contêineres do AKS. Você aprenderá como:

> [!div class="checklist"]
> * Verificar e corrigir as vulnerabilidades de imagem
> * Use um registro confiável com imagens de contêiner assinados digitalmente
> * Disparar e reimplantar as imagens de contêiner quando uma imagem base é atualizada automaticamente

Você também pode ler as práticas recomendadas para [segurança do cluster][best-practices-cluster-security] e para [gerenciamento de imagem de pod][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>Proteja as imagens e tempo de execução

**Diretrizes de práticas recomendadas** – verifique suas imagens de contêiner de vulnerabilidades e só implante imagens que passaram na validação. Atualize regularmente as imagens base e o tempo de execução do aplicativo, em seguida, implante as cargas de trabalho no cluster AKS.

Uma preocupação com a adoção de cargas de trabalho baseadas em contêiner é verificar a segurança de imagens e o tempo de execução usado para criar seus próprios aplicativos. Como ter certeza de que você não introduziu vulnerabilidades de segurança em suas implantações? O fluxo de trabalho de implantação deve incluir um processo para digitalizar imagens de contêiner usando ferramentas como [Twistlock][twistlock] ou [Aqua][aqua]e, em seguida, permita que apenas imagens verificadas sejam implantadas.

![Verifique e corrija as imagens de contêiner, valide e implante](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Em um exemplo do mundo real, você pode usar um pipeline de implantação contínua (CI/CD) e integração contínua para automatizar as verificações de imagem, verificação e implantações. O Registro de Contêiner do Azure inclui esses recursos de verificação de vulnerabilidades.

## <a name="use-a-trusted-registry"></a>Usar um registro confiável

**Diretrizes de práticas recomendadas** - limite os registros de imagem pods e implantações possíveis de usar. Permita somente registros confiáveis em que você valida e controla as imagens que estão disponíveis.

Para obter segurança adicional, você pode assinar digitalmente também suas imagens de contêiner exatamente como você pode assinar digitalmente o código do aplicativo. Você, em seguida, só permite AKS para implantar imagens assinadas. Esse processo fornece uma camada adicional de segurança em que você limita AKS para só extrair imagens assinadas digitalmente e são confiáveis por você, não apenas imagens que passam em uma verificação de vulnerabilidade. Você também se certifica de que a imagem de contêiner não foi violada e substituída por uma imagem com o mesmo nome.

Registros confiáveis que fornecem imagens de contêiner assinados digitalmente adicionam complexidade ao seu ambiente, mas podem ser necessários para determinada política ou conformidade a normas. O Registro de Contêiner do Azure oferece suporte ao uso de registros confiáveis e imagens assinadas.

Para obter mais informações sobre imagens assinadas digitalmente, consulte [Conteúdo de Confiança no Registro de Contêiner do Azure][acr-content-trust].

## <a name="automatically-build-new-images-on-base-image-update"></a>Crie automaticamente novas imagens na atualização da imagem base

**Diretrizes de práticas recomendadas de** - conforme você usa imagens de base para imagens de aplicativo, use a automação para criar novas imagens quando a imagem base é atualizada. Como essas imagens base geralmente incluem correções de segurança, atualize as imagens de contêiner do aplicativo downtream.

Cada vez que uma imagem base é atualizada, quaisquer imagens de contêiner downstream também devem ser atualizadas. Esse processo de compilação deve ser integrado à validação e pipelines de implantação, como [Pipelines do Azure] [ azure-pipelines] ou Jenkins. Esses pipelines certificam que seus aplicativos continuarão sendo executados nas imagens com base atualizada. Depois que suas imagens de contêiner do aplicativo são validadas, as implantações do AKS, em seguida, podem ser atualizadas para executar as imagens mais recentes e seguras.

Tarefas do Registro de Contêiner do Azure também atualizam automaticamente as imagens de contêiner quando a imagem base é atualizada. Esse recurso permite que você crie um pequeno número de imagens de base e regularmente mantê-las atualizadas com as correções de bugs e de segurança.

Para obter mais informações sobre a imagem base, consulte [Automatizar builds de imagem na atualização da imagem base com as Tarefas do Registro de Contêiner do Azure][acr-base-image-update].

## <a name="next-steps"></a>Próximas etapas

Este artigo se concentrou em como proteger seus contêineres. Para implementar algumas dessas áreas, confira os seguintes artigos:

* [Automatizar builds de imagem na atualização da imagem base com as Tarefas do Registro de Contêiner do Azure][acr-base-image-update]
* [Confiança de conteúdo no Registro de Contêiner do Azure][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
