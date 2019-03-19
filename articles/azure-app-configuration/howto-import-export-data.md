---
title: Importar ou exportar dados com configuração de aplicativo do Azure | Microsoft Docs
description: Saiba como importar ou exportar dados para ou de configuração de aplicativo do Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 851a8705b3cfa9c88e369af7b961a653dee8fd7a
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959843"
---
# <a name="import-or-export-configuration-data"></a>Importar ou exportar dados de configuração

Configuração de aplicativo dá suporte a dados do Azure operações importação e exportação. Elas permitem que você trabalhe com dados de configuração em massa, bem como trocar dados entre sua configuração de aplicativo, armazenar e projeto de código. Por exemplo, você pode configurar repositório de configuração de um aplicativo para teste e outro para produção e copiar configurações de aplicativo entre eles por meio de um arquivo para que você não precise inserir os dados duas vezes.

Este artigo fornece um guia para importar e exportar dados com a configuração de aplicativo.

## <a name="import-data"></a>Importar dados

Importação traz a configuração de armazenamento de dados na configuração de aplicativo de uma fonte existente, em vez de inserir manualmente-los. Você pode usar a função de importação para migrar dados para um repositório de configurações do aplicativo ou agregar dados de várias fontes. Configuração de aplicativo suporte à importação de um arquivo JSON, YAML ou propriedades.

Você pode importar dados usando o [portal do Azure](https://aka.ms/azconfig/portal) ou [CLI do Azure](./scripts/cli-import.md). No portal do Azure, siga estas etapas:

1. Navegue até seu repositório de configurações do aplicativo e clique em **importação/exportação**.

2. No **importação** guia, escolha **serviço de fonte** e **arquivo de configuração**.

3. Escolher **do idioma** e **tipo de arquivo**.

4. Clique no **pasta** ícone e navegue até o arquivo para importar.

    ![Arquivo de importação](./media/import-file.png)

5. Escolha uma **separador** e, opcionalmente, insira um **prefixo** a ser usado para nomes de chave importados.

6. Opcionalmente, selecione uma **rótulo**.

7. Clique em **aplicar** para concluir a importação.

    ![Arquivo de importação concluída](./media/import-file-complete.png)

## <a name="export-data"></a>Exportar dados

Exportação grava os dados de configuração armazenados na configuração de aplicativo para outro destino. Você pode usar a função de exportação, por exemplo, para salvar dados em um repositório de configuração de aplicativo para um arquivo que será inserido com o código do aplicativo durante a implantação.

Você pode exportar dados usando o [portal do Azure](https://aka.ms/azconfig/portal) ou [CLI do Azure](./scripts/cli-export.md). No portal do Azure, siga estas etapas:

1. Navegue até seu repositório de configurações do aplicativo e clique em **importação/exportação**.

2. No **exportar** guia, escolha **serviço de destino** e **arquivo de configuração**.

3. Opcionalmente, insira um **prefixar** e escolha um **rótulo** e um point-in-time para chaves a serem exportados.

4. Escolha uma **tipo de arquivo** e **separador**.

5. Clique em **aplicar** para concluir a exportação.

    ![Arquivo de exportação concluída](./media/export-file-complete.png)

## <a name="next-steps"></a>Próximas etapas

* [Início Rápido: Criar um aplicativo Web ASP.NET](./quickstart-aspnet-core-app.md)  
