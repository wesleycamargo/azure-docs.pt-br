---
title: Entidades de dados | Microsoft Docs
description: Uma visão geral de entidades de dados.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446571"
---
# <a name="data-entities"></a>Entidades de dados

Este artigo define e fornece uma visão geral das entidades de dados. Além disso, inclui informações sobre os recursos das entidades de dados, os cenários em que dão suporte, as categorias que são usadas e os métodos de criá-las.

## <a name="overview"></a>Visão geral

Uma entidade de dados é uma abstração da implementação física de tabelas de banco de dados. Por exemplo, em tabelas normalizadas, muitos dos dados de cada cliente podem ser armazenados em uma tabela de clientes e, em seguida, o restante pode ser distribuído em um pequeno conjunto de tabelas relacionadas. Nesse caso, a entidade de dados para o conceito de cliente aparece como uma exibição desnormalizada, na qual cada linha contém todos os dados da tabela de clientes e as tabelas relacionadas. Uma entidade de dados encapsula um conceito de negócio em um formato que facilita o desenvolvimento e a integração. A natureza abstrata de uma entidade de dados pode simplificar o desenvolvimento e a personalização de aplicativos. Posteriormente, a abstração também isola o código de aplicativo da inevitável rotatividade das tabelas físicas entre as versões.

Resumidamente: a entidade de dados fornece abstração e encapsulamento conceituais (exibição desnormalizada) dos esquemas de tabelas subjacentes para representar conceitos e funcionalidades de dados-chave.

## <a name="capabilities"></a>Funcionalidades

Uma entidade de dados tem os recursos a seguir:

- Substitui conceitos divergentes e fragmentados de AXD, entidades DIXF (Estrutura de Importação/Exportação de Dado) e consultas agregadas com um único conceito.
- Fornece uma única pilha para capturar a lógica de negócios e habilitar cenários como importação/exportação, integração e programação.
- Torna-se o mecanismo primário para exportar e importar pacotes de dados para o ALM (Gerenciamento do Ciclo de Vida do Aplicativo) e demonstrar cenários de dados.
- Pode ser exposta como serviços OData e, em seguida, usada em cenários de integração síncrona estilo tabular e integrações do Microsoft Office.

Consulte [Entidades de Dados](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) para obter mais informações.
