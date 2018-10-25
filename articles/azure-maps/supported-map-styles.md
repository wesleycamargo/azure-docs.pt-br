---
title: Estilos de mapa compatíveis com o Azure Mapas | Microsoft Docs
description: Estilos de mapa compatíveis com o Azure Mapas
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238813"
---
# <a name="azure-maps-supported-map-styles"></a>Categorias compatíveis com o Azure Mapas
O Azure Mapas dá suporte a quatro estilos de mapa internos diferente. Os estilos, juntamente com as descrições, estão listados abaixo.

## <a name="road"></a>rodoviário
Um **mapa** de estradas é um mapa padrão que exibe estradas, naturais e artificiais recursos juntamente com os rótulos para esses recursos.

![rodoviário](./media/supported-map-styles/road.png)

**APIs aplicáveis:**
* [Imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Peça de mapa](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Controle de mapa JS

## <a name="satellite"></a>satélite 
O estilo **satélite** é uma combinação de imagens de satélite e aéreas.

![satélite](./media/supported-map-styles/satellite.png)

**APIs aplicáveis:**
* [Peça de satélite](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Controle de mapa JS

## <a name="satelliteroadlabels"></a>satellite_road_labels
Este estilo de mapa é um híbrido de estradas e rótulos sobrepostos sobre imagens aéreas e de satélite.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**APIs aplicáveis:**
* Controle de mapa JS

## <a name="grayscaledark"></a>grayscale_dark
**Escala de cinza escuro** é uma versão escura do estilo de mapa rodoviário.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**APIs aplicáveis:**
* Controle de mapa JS 