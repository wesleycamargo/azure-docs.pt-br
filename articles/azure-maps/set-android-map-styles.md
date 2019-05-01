---
title: Funcionalidades de estilo de mapa no Azure Mapas | Microsoft Docs
description: Saiba mais sobre mapas do Azure funcionalidades relacionadas de estilo para o SDK do Android.
author: walsehgal
ms.author: v-musehg
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 3c8c5d4bae16d8e15c8f2c5b1cc8e00eb14e4ce3
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870966"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>Definir o estilo de mapa usando o SDK do Android do Azure mapas

Este artigo mostra duas maneiras de definir estilos de mapa usando o SDK de Android do mapas do Azure. Mapas do Azure tem seis estilos de mapas diferentes para escolher. Para obter mais informações sobre estilos de mapa com suporte, consulte [suporte para estilos de mapa em mapas do Azure](./supported-map-styles.md).


## <a name="prerequisites"></a>Pré-requisitos

Para concluir o processo neste artigo, você precisará instalar [SDK do Android do Azure mapas](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library) para carregar um mapa.


## <a name="set-map-style-in-the-layout"></a>Definir o estilo de mapa no layout

Você pode definir um estilo de mapa no arquivo de layout para a sua classe de atividade. Edite **res > layout > activity_main**, portanto, ele será semelhante à mostrada abaixo:

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

O `mapcontrol_style` acima do atributo define o estilo de mapa como **grayscale_dark**. 

<center>

![style-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>Definir o estilo de mapa na classe de atividade

Estilo de mapa pode ser definido na classe de atividade. Copie o seguinte trecho de código para o **onCreate()** método do seu `MainActivity.java` classe. Isso definirá o estilo de mapa **satellite_road_labels**.

```Java
    mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![style-satellite-road-labels](./media/set-android-map-styles/satellite-road-labels.png)</center>