---
title: Diretrizes para dados pessoais armazenados no Azure Log Analytics| Microsoft Docs
description: Este artigo descreve como gerenciar dados pessoais armazenados no Azure Log Analytics e os métodos para identificá-los e removê-los.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3692c83a4991fc67ec176687bd076ab14e4c640d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129363"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Diretrizes para dados pessoais armazenados no Log Analytics

O Log Analytics é um armazenamento de dados no qual é possível localizar dados pessoais. Este artigo abordará onde, no Log Analytics, esses dados normalmente estão localizado, bem como os recursos disponíveis para tratar os dados.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Estratégia para tratamento de dados pessoais

Enquanto couber a você e à sua empresa determinar qual estratégia será adotada para o tratamento de dados privados (se for o caso), algumas abordagens possíveis são apresentadas a seguir. As abordagens estão listadas em ordem de preferência, do ponto de vista técnico, da mais para a menos preferencial:

* Sempre que possível, pare de coletar, ocultar, anonimizar ou ajustar os dados que estão sendo coletados para excluí-lo de ser considerado "privado". Isso é _sem dúvida_ a abordagem preferencial, evitando a necessidade de criar uma estratégia de tratamento de dados muito cara e impactante.
* Quando não for possível, tente normalizar os dados para reduzir o impacto na plataforma e no desempenho dos dados. Por exemplo, em vez de registrar uma ID de usuário explícita, crie um dado de pesquisa que correlacionará o nome de usuário e os respectivos detalhes a uma ID interna que poderá ser registrada em outro lugar. Dessa forma, se um dos usuários solicitar que você exclua as informações pessoais, é possível que somente excluir a linha na tabela de consulta correspondente ao usuário seja suficiente. 
* Por fim, se os dados privados precisarem ser coletados, compile um processo em torno do caminho de API de limpeza e do caminho de API de consulta existente para atender a quaisquer obrigações necessárias para exportar e excluir dados privados associados a um usuário. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Onde pesquisa dados privados no Log Analytics?

O Log Analytics é um armazenamento flexível que, enquanto prescreve um esquema aos dados, permite substituir todos os campos por valores personalizados. Além disso, qualquer esquema personalizado pode ser processado. Assim sendo, é impossível dizer exatamente onde os dados privados estarão localizados no espaço de trabalho específico. No entanto, os locais a seguir são bons pontos iniciais no inventário:

* *Endereços IP*: O Log Analytics coleta uma variedade de informações de IP em muitas tabelas diferentes. Por exemplo, a consulta a seguir mostra todas as tabelas nas quais os endereços IPv4 foram coletados nas últimas 24 horas:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *IDs de usuário*: as IDs de usuário são localizadas em uma grande variedade de soluções e tabelas. É possível pesquisar um nome de usuário específico em todo o conjunto de dados usando o comando de pesquisa:
    ```
    search "[username goes here]"
    ```
Lembre-se de procurar não apenas nomes de usuários legíveis, mas também GUIDs que podem ser rastreados diretamente para um usuário em particular!
* *IDs de dispositivo*: como as IDs de usuário, as IDs de dispositivo algumas vezes são consideradas "privadas". Use a mesma abordagem listada acima para IDs de usuário para identificar tabelas nas quais isso possa ser uma preocupação. 
* *Dados personalizados*: o Log Analytics permite a coleção em uma variedade de métodos: logs personalizados e campos personalizados, a [API do coletor de dados HTTP](log-analytics-data-collector-api.md) e dados personalizados coletados como parte dos logs de eventos do sistema. Todos são suscetíveis a conter dados privados e devem ser examinados para verificar se esses dados existem.
* *Dados capturados pela solução*: como o mecanismo da solução é aberto, é recomendável revisar todas as tabelas geradas pelas soluções para garantir a conformidade.

## <a name="how-to-export-and-delete-private-data"></a>Como exportar e excluir dados privados

Conforme mencionado na seção [Estratégia para tratamento de dados pessoais](#strategy-for-personal-data-handling) anterior, é __altamente__ recomendável, se possível, reestruturar a política de coleta de dados para desabilitar a coleta de dados privados, ofuscando ou anonimizando-os, ou modificando-os de alguma outra forma, para evitar que sejam considerados "privados". O tratamento dos dados resultará, principalmente, em custos para você e sua equipe para definir e automatizar uma estratégia, compilar uma interface para seus clientes interagirem com os dados, e custos de manutenção contínuos. Além disso, é computacionalmente oneroso para o Log Analytics, e um grande volume de consultas simultâneas ou chamadas de API de limpeza têm o potencial de impactar negativamente todas as outras interações com a funcionalidade do Log Analytics. Dito isso, há de fato alguns cenários válidos em que dados privados devem ser coletados. Para esses casos, os dados devem ser tratados conforme descrito nesta seção.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Exibir e exportar

Para solicitações de visualização e exportação de dados, poderá ser usada uma [API de consulta](https://dev.loganalytics.io/). A lógica para converter a forma dos dados em uma forma apropriada para entregar aos usuários será de sua responsabilidade. [Azure Functions](https://azure.microsoft.com/services/functions/) é um ótimo local para hospedar essa lógica.

### <a name="delete"></a>Excluir

> [!WARNING]
> As exclusões no Log Analytics são destrutivas e irreversíveis! Tenha extremo cuidado na execução.

Disponibilizamos como parte de uma privacidade que trata um caminho de API de *limpeza*. Esse caminho deve ser utilizado com moderação devido ao risco associado, o potencial impacto no desempenho e o potencial para distorcer agregações, medidas e outros aspectos dos dados do Log Analytics. Consulte a seção [Estratégia para tratamento de dados pessoais](#strategy-for-personal-data-handling) para obter abordagens alternativas para tratar dados privados.

A limpeza é uma operação altamente privilegiada que nenhum aplicativo ou usuário no Azure (incluindo até mesmo o proprietário do recurso) terá permissões para executar sem que seja concedida explicitamente uma função no Azure Resource Manager. Essa função é _Limpador de Dados_ e deve ser cuidadosamente delegada devido ao potencial de perda de dados. 

Depois que a função do Azure Resource Manager for atribuída, dois novos caminhos de API estarão disponíveis: 

* [Limpeza POST] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - obtém um objeto especificando parâmetros de dados para excluir e retorna um GUID de referência 
* Status de limpeza GET – a chamada de limpeza POST retornará um cabeçalho 'x-ms-status-location' que incluirá uma URL que você poderá chamar para determinar o status da API de limpeza. Por exemplo: 

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Embora esperemos que a grande maioria das operações de limpeza seja concluída muito mais rapidamente do que o SLA, devido ao grande impacto na plataforma de dados usada pelo Log Analytics, o SLA formal para a conclusão das operações de limpeza é definido em 30 dias. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como os dados são coletados, processados e protegidos, consulte [Segurança de dados do Log Analytics](log-analytics-data-security.md).