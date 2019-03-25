---
title: Coletar dados de inteligência de ameaças na visualização do Azure Sentinel | Microsoft Docs
description: Saiba mais sobre como se conectar a dados de inteligência de ameaças para Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: bc8a644f99d9a84e1f2c177a87e2668ae9a57868
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400622"
---
# <a name="collect-data-from-threat-intelligence-providers"></a>Coletar dados de provedores de inteligência de ameaça 

> [!IMPORTANT]
> Sentinela do Azure está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Depois que você transmite seus dados no Azure Sentinel, é possível enriquecê-lo com a feed de inteligência de ameaça que você usa em sua organização. 

Para permitir que você entre verificar seus alertas e regras com inteligência de ameaça true, por exemplo, se você receber um alerta de um endereço IP específico, a integração do provedor de inteligência contra ameaças poderá informá-lo se o endereço IP recentemente foi encontrado como mal-intencionado , Sentinela do azure permite a integração com [provedores de inteligência de ameaças](https://aka.ms/graphsecuritytips). 

É possível transmitir logs de provedores de inteligência de ameaças em Sentinel do Azure com um único clique. Essa conexão permite que você incorpore indicadores que contém vários tipos de observables, como o endereço IP, domínio, URL e regras no Azure Sentinel de alertas de hash do arquivo para pesquisar e criar personalizadas.  
> [!NOTE]
> Você pode inserir indicadores de ameaça personalizado no Azure Sentinel para uso em cenários de procura, painéis e regras de alerta com a integração com o [tiIndicator de segurança do Microsoft Graph](https://aka.ms/graphsecuritytiindicators) entidade ou usando um [Microsoft Gráfico de segurança integrado a plataforma de inteligência de ameaça](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Pré-requisitos  

- Usuário com permissões de administrador de segurança ou de administrador global 

- Aplicativo de inteligência contra ameaças integrado com o Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Conectar-se a inteligência contra ameaças 

1. Se você já estiver usando um provedor de inteligência de ameaça, certifique-se navegar até seu aplicativo de PONTA e conceder permissão para enviar os indicadores para a Microsoft e especificar o serviço como Sentinel do Azure.  

2. No Azure Sentinel, selecione **coleta de dados** e, em seguida, clique no **inteligência contra ameaças** lado a lado.

3. Clique em **Conectar**. 

4. Para usar o esquema relevante no Log Analytics para feeds de inteligência de ameaça, pesquise **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como conectar-se o seu provedor de inteligência contra ameaças para Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os artigos a seguir.

- Para começar a Sentinela do Azure, você precisa de uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- Saiba como [integrar seus dados para Azure Sentinel](quickstart-onboard.md), e [Obtenha visibilidade sobre seus dados e possíveis ameaças](quickstart-get-visibility.md).
