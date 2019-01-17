---
title: Comparação baseada em recursos dos níveis de gerenciamento de API do Azure | Microsoft Docs
description: Este artigo compara os níveis de gerenciamento de API com base nos recursos que eles oferecem.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: apimpm
ms.openlocfilehash: eae36aa6e60e3da03c59952a1d9e035e6a773d2d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156690"
---
# <a name="feature-based-comparison-of-the-azure-api-management-tiers"></a>Comparação baseada em recursos dos níveis de gerenciamento de API do Azure

Cada [tipo de preço](https://aka.ms/apimpricing) de Gerenciamento de API oferece um conjunto distinto de recursos e por unidade de [capacidade](api-management-capacity.md). A tabela a seguir resume os principais recursos disponíveis em cada um dos níveis. Alguns recursos podem funcionar de maneira diferente ou ter recursos diferentes dependendo da camada. Em tais casos, as diferenças são mencionadas nos artigos de documentação que descrevem esses recursos individuais.

| Recurso                                                                                      | Consumo<sup>VISUALIZAÇÃO</sup> | Desenvolvedores      | Básico          | Standard       | Premium        |
| -------------------------------------------------------------------------------------------- | ----------------------------- | -------------- | -------------- | -------------- | -------------- |
| Integração do Azure AD<sup>1</sup>                                                             | Não                             | Sim            | Não              | sim            | SIM            |
| Suporte de rede virtual (VNet)                                                               | Não                             | Sim            | Não             | Não              | SIM            |
| Implantação em várias regiões                                                                      | Não                             | Não             | Não             | Não              | SIM            |
| Vários nomes de domínio personalizado                                                                 | Não                             | Não             | Não             | Não              | SIM            |
| Portal do desenvolvedor<sup>2</sup>                                                                 | Não                             | sim            | sim            | sim            | SIM            |
| Cache embutido                                                                               | Não                             | sim            | sim            | sim            | SIM            |
| Análise integrada                                                                           | Não                             | sim            | sim            | sim            | SIM            |
| [Configurações de SSL](api-management-howto-manage-protocols-ciphers.md)                             | Não                             | sim            | sim            | sim            | SIM            |
| [Cache externo](https://aka.ms/apimbyoc)                                                    | SIM                           | Nº<sup>3</sup> | Nº<sup>3</sup> | Nº<sup>3</sup> | Nº<sup>3</sup> |
| [Autenticação de certificado de cliente](api-management-howto-mutual-certificates-for-clients.md) | Nº<sup>4</sup>                | SIM            | sim            | sim            | SIM            |
| [Backup e restauração](api-management-howto-disaster-recovery-backup-restore.md)               | Não                             | sim            | sim            | sim            | SIM            |
| [Gerenciamento via Git](api-management-configuration-repository-git.md)                        | Não                             | sim            | sim            | sim            | SIM            |
| API de Gerenciamento                                                                        | Não                             | sim            | sim            | sim            | SIM            |
| Métricas e logs do Azure Monitor                                                               | Nº<sup>5</sup>                | SIM            | sim            | sim            | SIM            |

<sup>1</sup> Permite o uso do Azure AD (e do Azure AD B2C) como um provedor de identidade para o login do usuário no portal do desenvolvedor.<br/>
<sup>2</sup> Incluindo funcionalidade relacionada, por ex. usuários, grupos, problemas, aplicativos e modelos de email e notificações.<br/>
<sup>3</sup> O suporte de cache externo para esta camada está disponível em breve.<br/>
<sup>4</sup> A autenticação do certificado de cliente será adicionada à camada de consumo antes de sua disponibilidade geral.<br/>
<sup>5</sup> O suporte completo do Azure Monitor será adicionado à camada de Consumo.
