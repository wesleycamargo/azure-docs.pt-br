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
ms.openlocfilehash: 97ac405db3de4fa2c6f1173f813eafd41a5361ad
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209438"
---
# <a name="deploy-models-with-the-azure-machine-learning-service"></a>Implantar modelos com o serviço do Azure Machine Learning

O serviço do Azure Machine Learning fornece várias maneiras de implantar o modelo treinado. Neste documento, saiba como implantar seu modelo como um serviço Web na nuvem do Azure ou em dispositivos do IoT Edge.

Você pode implantar modelos nos destinos de computação a seguir:

- [ACI (Instâncias de Contêiner do Azure)](#aci): implantação rápida. Bom para teste ou desenvolvimento.
- [AKS (Serviço de Kubernetes do Azure)](#aks): bom para cargas de trabalho de produção em larga escala. Fornece o dimensionamento automático e tempo de resposta rápido.
- [Azure IoT Edge](#iotedge): implantar modelos em dispositivos IoT. A inferência acontece no dispositivo.
- [FPGA (matriz de porta programável no campo)](#fpga): latência extremamente baixa para inferência em tempo real.

O restante deste documento aborda cada uma dessas opções em detalhes.

## <a id="aci"></a>Instâncias de Contêiner do Azure

Use Instâncias de Contêiner do Azure para implantar seus modelos como um ponto de extremidade de API REST se uma ou mais das seguintes condições for verdadeira:
- Você precisa implantar rapidamente e validar o modelo. A implantação da ACI termina em menos de cinco minutos.
- Você está buscando implantar seu modelo em um ambiente de desenvolvimento ou de teste. A ACI permite que você implante 20 grupos de contêiner por assinatura. Para obter mais informações, confira o documento [Cotas e disponibilidade de região para Instâncias de Contêiner do Azure](https://docs.microsoft.com/azure/container-instances/container-instances-quotas).

Para obter mais informações, confira o documento [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-to-aci.md).

## <a id="aks"></a>Serviço de Kubernetes do Azure

Para cenários de produção em larga escala, use o AKS (Serviço de Kubernetes do Azure). Você pode usar um cluster AKS existente ou criar outro usando o SDK do Azure Machine Learning, a CLI ou o portal do Azure.

A criação de um cluster AKS é um processo único em seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster, será necessário criar outro cluster na próxima vez que precisar implantar.

O Serviço de Kubernetes do Azure fornece os seguintes recursos:

* Dimensionamento automático
* Registro em log
* Coleta de dados de modelo
* Tempo de resposta rápido para seus serviços Web

O processo de criação de um cluster AKS leva aproximadamente 20 minutos.

Para obter mais informações, confira o documento [Implantar um modelo no Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md).

## <a id="iotedge"></a>Azure IoT Edge

Com dispositivos de IoT, é mais rápido executar pontuação no dispositivo, em vez de enviar dados para nuvem e aguardar. Com o Azure IoT Edge, você pode hospedar seu modelo em dispositivos de borda. Implante seu modelo no IoT Edge se você precisar de uma ou mais das seguintes funcionalidades:
- Lidar com tarefas de prioridade localmente, mesmo sem uma conexão de nuvem
- Trabalhar com os dados gerados que são muito grandes para efetuar pull rapidamente da nuvem
- Habilitar o processamento em tempo real por meio de inteligência em dispositivos locais ou próximos
- Acomodar requisitos relacionados à privacidade de dados 

Para obter mais informações, confira o documento [Implantar no Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning).

Para obter mais informações sobre o serviço do IoT Edge, confira a [documentação do Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/).


## <a id="fpga"></a>FPGA (Matriz de porta programável no campo)

Os Modelos de Aceleração de Hardware na plataforma Project Brainwave permitem alcançar uma latência extremamente baixa para solicitações de inferência em tempo real. O Project Brainwave acelera as DNN (redes neurais profundas) implantadas em matrizes de portas programáveis em campo na nuvem do Azure. As DNNs mais usadas estão disponíveis como recursos para aprendizados de transferência ou personalizável com pesos treinados de seus próprios dados.

Para obter mais informações, confira o documento [Implantar em uma FPGA](how-to-deploy-fpga-web-service.md).

## <a name="next-steps"></a>Próximas etapas

Para obter informações de como implantar um modelo em um destino de computação específico, confira os seguintes documentos:

* [Implantar um modelo nas Instâncias de Contêiner do Azure](how-to-deploy-to-aci.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md)
* [Implantar um modelo no Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-machine-learning)
* [Implantar um modelo na FPGA](how-to-deploy-fpga-web-service.md)
