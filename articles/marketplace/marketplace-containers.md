---
title: Guia de Publicação da Oferta de Contêineres para o Azure Marketplace
description: Este artigo descreve os requisitos para publicar Contêineres no Marketplace
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: ellacroi
manager: nunoc
ms.service: marketplace
ms.topic: article
ms.date: 07/09/2018
ms.author: ellacroi
ms.openlocfilehash: 41a09be36262ff09c383b8ccb64a94230a11d3f1
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64937924"
---
# <a name="containers-offer-publishing-guide"></a>Guia de Publicação da Oferta de Contêineres

Ofertas de contêiner ajudam você a publicar sua imagem de contêiner no Azure Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Essas são ofertas de transação que são implantadas e cobradas pelo Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".

Use o tipo de oferta de Contêiner quando a solução é uma imagem de contêiner do Docker provisionada como um serviço de contêiner do Azure baseado em Kubernetes.

>[!NOTE]
>Por exemplo, um serviço de contêiner do Azure baseado em Kubernetes, como o Serviço de Kubernetes do Azure ou as Instâncias de Contêiner do Azure, a escolha de clientes do Azure para um tempo de execução de contêiner baseado em Kubernetes.  

A Microsoft atualmente dá suporte a modelos de licenciamento BYOL (traga sua própria licença) e gratuito.

## <a name="containers-offer"></a>Oferta de Contêineres

| Requisito | Detalhes |  
|:--- |:--- |  
| Cobrança e medição | Suporte a qualquer modelo de cobrança gratuito ou BYOL. |  
| Imagem criada a partir do Dockerfile | As imagens de contêiner devem ser baseadas na especificação da imagem do Docker e devem ser criadas a partir de um Dockerfile.<ul> <li>Para saber mais sobre como criar imagens do docker, visite a seção Uso localizada em [docs.docker.com/engine/reference/builder/#usage](https://docs.docker.com/engine/reference/builder/#usage).</li> </ul> |  
| Hospedagem no ACR | As imagens de contêiner devem ser hospedadas em um repositório ACR (Registro de Contêiner do Azure).<ul> <li>Para obter mais informações sobre como trabalhar com o ACR, visite o Início Rápido: criar um registro de contêiner usando a página do portal do Azure localizada em [docs.microsoft.com/azure/container-registry/container-registry-get-started-portal](https://docs.microsoft.com/azure/container-registry/container-registry-get-started-portal).</li> </ul> |  
| Marcação de imagem | As imagens de contêiner devem conter pelo menos 1 marcação (máximo de marcações: 16).<ul> <li>Para saber mais sobre como marcar uma imagem, visite a página de marca do docker localizada em [docs.docker.com/engine/reference/commandline/tag](https://docs.docker.com/engine/reference/commandline/tag).</li> </ul> |  

## <a name="next-steps"></a>Próximas etapas

Caso ainda não tenha feito isso, 

- [Registre-se](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em uma existente,

- [Entre no Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir a oferta.
- Confira [Contêineres](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) para obter mais informações.
