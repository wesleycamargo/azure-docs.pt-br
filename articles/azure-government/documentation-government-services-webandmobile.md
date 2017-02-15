---
title: Azure Governamental Web, Mobile e API | Microsoft Docs
description: "Este guia fornece uma comparação dos recursos e orientações sobre como desenvolver aplicativos para o Azure Government"
services: azure-government
cloud: gov
documentationcenter: 
author: brendalee
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/5/2016
ms.author: brendal
translationtype: Human Translation
ms.sourcegitcommit: 6b77e338e1c7f0f79ea3c25b0b073296f7de0dcf
ms.openlocfilehash: f0ba3e50c4281a0e8c365be075ae45ab72f6e82d

---
# <a name="azure-government-web--mobile"></a>Azure Governamental Web + Mobile
## <a name="app-services"></a>Serviços de Aplicativos
### <a name="variations"></a>Variações
Os Serviços de Aplicativos do Azure estão disponíveis no Azure Governamental.

O endereço de aplicativos do Serviço de Aplicativo do Azure criados no Azure Governamental é diferente daqueles criados na nuvem pública:

| Tipo de serviço | Público do Azure | Azure Government |
| --- | --- | --- |
| Serviço de Aplicativo |*.azurewebsites.net |*.azurewebsites.us|

Alguns recursos do Serviço de Aplicativo disponíveis na nuvem pública ainda não estão disponíveis no Azure Governamental:

- Ambientes de Serviço de Aplicativo
- Implantação de aplicativo
    - Entrega contínua (visualização)
- Configurações
    - Integração vNet e conexões híbridas
    - Verificação de segurança
- Ferramentas de desenvolvimento
    - Teste de desempenho
    - Gerenciador de Recursos
    - Depuração de PHP
- Monitoramento
    - Application Insights
    - Métricas por instância
    - Tráfego HTTP ao vivo
    - Eventos de aplicativo
    - Logs FREB
- Suporte e solução de problemas
    - Supervisor do Serviço de Aplicativo
    - Histórico de falha
    - Diagnóstico como Serviço
    - Atenuar


### <a name="considerations"></a>Considerações
As informações a seguir identificam o limite do Azure Governamental para o Serviço de Aplicativo:

| Dados regulamentados/controlados permitidos | Dados regulamentados/controlados não permitidos |
| --- | --- |
| Dados inseridos, armazenados e processados com o Serviço de Aplicativo do Azure podem conter dados de exportação controlados. Binários em execução no Serviço de Aplicativo do Azure. Autenticadores estáticos, como senhas e PINs de cartão inteligente para acesso aos componentes da plataforma Azure. Chaves privadas de certificados usados para gerenciar os componentes da plataforma Azure. Cadeias de conexão SQL. Outros informações/segredos de segurança, como certificados, chaves de criptografia, chaves mestras e chaves de armazenamento armazenadas nos serviços do Azure. |Metadados não podem conter dados de exportação controlados. Esses metadados incluem todos os dados de configuração inseridos durante a criação e manutenção de seu Serviço de Aplicativo do Azure. Não insira dados Regulamentados/controlados nos seguintes campos: Grupos de recursos, Nomes de recursos, Marcas de recurso|

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o [Blog do Microsoft Azure Governamental](https://blogs.msdn.microsoft.com/azuregov/).




<!--HONumber=Dec16_HO2-->


