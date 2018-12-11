---
title: Armazenar e usar segredos do aplicativo do Azure Service Fabric Mesh | Microsoft Docs
description: Armazenar e usar segredos de Malha do Service Fabric.
services: service-fabric-mesh
keywords: segredos
author: v-steg
ms.author: v-steg
ms.date: 10/25/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 63ac34e184d537eba2b915d4d108c7c1d8368aba
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52891837"
---
# <a name="service-fabric-mesh-application-secrets"></a>Segredos do aplicativo de Malha do Fabric Mesh
A Malha do Service Fabric dá suporte a Segredos como recursos do Azure. Um segredo de Malha do Service Fabric pode ser qualquer informação de texto confidenciais como cadeias de caracteres de conexão de armazenamento, senhas ou outros valores que devem ser armazenados e transmitidos de forma segura.

![Visão geral dos Segredos de Malha][sf-mesh-secrets-overview]

## <a name="mesh-secrets-resources"></a>Recursos dos segredos de Malha
Um segredo de aplicativo de Malha consiste em:
* Um recurso **Segredos**, que é um contêiner que armazena os segredos de texto. Os segredos contidos no recurso **Segredos** são armazenados e transmitidos de forma segura.
* Um ou mais recursos **Segredos/Valores** que são armazenados no contêiner do recurso **Segredos**. Cada recurso **Segredos/Valores** é diferenciado por um número de versão.

## <a name="inline-or-stored-in-azure-key-vault"></a>Embutido ou armazenado no Azure Key Vault
- Os aplicativos de malha e os recursos de serviço incluem Managed Service Identity (MSI) com Azure Active Directory (AAD) para acessar os segredos no cofre de chaves do Azure.
- Os segredos e certificados podem ser autossobrepostos pelas políticas.

## <a name="next-steps"></a>Próximas etapas 
Para saber mais sobre os Segredos de Malha do Service Fabric, confira:
- [Gerenciar Segredos do Aplicativo do Service Fabric Mesh](service-fabric-mesh-howto-manage-secrets.md)
- [Introdução ao Modelo de Recurso do Service Fabric](service-fabric-mesh-service-fabric-resources.md)

<!-- pics -->
[sf-mesh-secrets-overview]: ./media/service-fabric-mesh-secrets-overview/MeshAppSecretsOverview.png
