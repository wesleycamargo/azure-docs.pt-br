---
title: Onde implantar modelos com o serviço do Azure Machine Learning | Microsoft Docs
description: Conheça as diferentes maneiras de implantar seus modelos em produção usando o serviço do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 08/29/2018
ms.openlocfilehash: 606e8aed42bce69d5b3210b4e97f8cbfeaaf104c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961001"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

O serviço do Azure Machine Learning fornece várias maneiras de implantar o modelo treinado. Neste documento, saiba como implantar seu modelo como um serviço Web na nuvem do Azure ou em dispositivos do IoT Edge.

Você pode implantar modelos nos destinos de computação a seguir:

- ACI (Instâncias de Contêiner do Azure)
- AKS (Serviço do Kubernetes do Azure)
- Azure IoT Edge
- FPGA (Matriz de portas programáveis em campo)

O restante deste documento aborda cada uma dessas opções em detalhes.

## <a name="azure-container-instances"></a>Instâncias de Contêiner do Azure

Use Instâncias de Contêiner do Azure para implantar seus modelos como um ponto de extremidade de API REST se uma ou mais das seguintes condições for verdadeira:
- Você está buscando uma implantação rápida para pontuar e validar o modelo. A implantação da ACI geralmente é concluída em menos de cinco minutos.
- Você está buscando implantar seu modelo em um ambiente de desenvolvimento ou de teste. A ACI permite que você implante 20 grupos de contêiner por assinatura. Para obter mais informações, confira o documento [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Para obter mais informações, confira o documento [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-to-aci.md).

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Para cenários de produção de grande escala, você pode implantar seu modelo no AKS (Serviço de Kubernetes do Azure). Você pode usar um cluster AKS existente ou criar outro usando o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

A criação do cluster AKS é um processo avulso do workspace. Depois de criado, esse cluster poderá ser reutilizado para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, será necessário criar outro cluster na próxima vez que precisar implantar.

A implantação no AKS fornece dimensionamento automático, registro em log, coleta de dados de modelo e tempos de resposta rápidos para seu serviços Web. 

O processo de criação de um cluster AKS leva aproximadamente 20 minutos.

Para obter mais informações, confira o documento [Implantar um modelo no Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md).

## <a name="azure-iot-edge"></a>Azure IoT Edge

Com dispositivos de IoT, é mais rápido executar pontuação no dispositivo, em vez de enviar dados para nuvem e aguardar que um modelo hospedado na nuvem retorne os dados. Com o Azure IoT Edge, você pode hospedar seu modelo em dispositivos de borda. Implante seu modelo no IoT Edge se você precisar de uma ou mais das seguintes funcionalidades:
- Lidar com tarefas de prioridade localmente, mesmo sem uma conexão de nuvem
- Trabalhar com os dados gerados que são muito grandes para efetuar pull rapidamente da nuvem
- Habilitar o processamento em tempo real por meio de inteligência em dispositivos locais ou próximos
- Acomodar requisitos relacionados à privacidade de dados 

Para obter mais informações, confira o documento [Implantar no Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

Para obter mais informações sobre o serviço do IoT Edge, confira a [documentação do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a name="field-programmable-gate-arrays-fpga"></a>FPGA (Matrizes de portas programáveis em campo)

Os Modelos de Aceleração de Hardware na plataforma Project Brainwave permitem alcançar uma latência extremamente baixa para solicitações de inferência em tempo real. O Project Brainwave acelera as DNN (redes neurais profundas) implantadas em matrizes de portas programáveis em campo na nuvem do Azure. As DNNs mais usadas estão disponíveis como recursos para aprendizados de transferência ou personalizável com pesos treinados de seus próprios dados.

Para obter mais informações, confira o documento [Implantar em uma FPGA](how-to-deploy-fpga-web-service.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como implantar um modelo em um destino de computação específico, confira os seguintes documentos:

* [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-to-aci.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md)
* [Implantar um modelo no Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Implantar um modelo na FPGA](how-to-deploy-fpga-web-service.md)
