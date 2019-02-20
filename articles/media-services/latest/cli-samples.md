---
title: Exemplos da CLI do Azure – Serviços de Mídia do Azure | Microsoft Docs
description: Exemplos da CLI do Azure para os Serviços de Mídia do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 02/11/2019
ms.author: juliako
ms.openlocfilehash: 919dbd057f2fa1d762775ed799505796a420864a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109241"
---
# <a name="azure-cli-examples-for-azure-media-services"></a>Exemplos da CLI do Azure para os Serviços de Mídia do Azure

A tabela a seguir inclui links para exemplos da CLI do Azure para os Serviços de Mídia do Azure.

## <a name="examples"></a>Exemplos

|  |  |
|---|---|
|**Escala**||
| [Dimensionar Unidades Reservadas de Mídia](media-reserved-units-cli-how-to.md)|Para os trabalhos de análise de áudio e análise de vídeo acionados pelo Serviços de Mídia do Microsoft Azure v3 ou pelo Video Indexer, é altamente recomendável provisionar sua conta com 10 MRUs do S3. <br/>O script mostra como usar a CLI para dimensionar as Unidades Reservadas de Mídia (MRUs).|
|**Conta**||
| [Criar uma conta dos Serviços de Mídia](./scripts/cli-create-account.md) | Cria uma conta dos Serviços de Mídia do Azure. Além disso, cria uma entidade de serviço que pode ser usada para acessar APIs e gerenciar a conta de forma programática. |
| [Redefinir credenciais de conta](./scripts/cli-reset-account-credentials.md)|Redefine as credenciais de conta e obtém as configurações de app.config de volta.|
|**Ativos**||
| [Criar ativos](./scripts/cli-create-asset.md)|Cria um ativo de Serviços de Mídia para carregar o conteúdo.|
| [Carregar um arquivo](./scripts/cli-upload-file-asset.md)|Carrega um arquivo local para um contêiner de armazenamento.|
| **Transformações** e **Trabalhos**||
| [Criar transformações](./scripts/cli-create-transform.md)|Mostra como criar transformações. Transformações descreve um fluxo de trabalho simples de tarefas para processar seus arquivos de áudio ou vídeos (também conhecidos como “receita”).<br/> Você sempre deve verificar se uma Transformação com o nome desejado e "receita" já existe. Em caso afirmativo, reutilize-a. |
| [Criar trabalhos](./scripts/cli-create-jobs.md)|Enviar um trabalho para uma Transformação de codificação simples usando a URL HTTPs.|
| [Criar EventGrid](./scripts/cli-create-event-grid.md)|Cria uma assinatura de Grade de Eventos de nível de conta para Alterações de Estado do Trabalho.|
| **Entregar**||
| [Publicar um ativo](./scripts/cli-publish-asset.md)| Cria um localizador de Streaming e obtém as URLs de Streaming novamente. |
| [Filter](filters-dynamic-manifest-cli-howto.md)| Configura um filtro para um ativo de Vídeo sob Demanda e mostra como usar a CLI para criar [Filtros de Conta](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) e [Filtros de Ativo](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="see-also"></a>Consulte também

[CLI do Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
