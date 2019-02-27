---
title: Como usar o controle de mapa do Android no Azure Mapas | Microsoft Docs
description: Use o controle de mapa do Android no Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 57cc585d621c71872a4b7658c74f581c8998b245
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56341072"
---
# <a name="how-to-use-azure-maps-android-sdk"></a>Como usar o SDK do Android do Azure Mapas

O SDK do Android do Azure Mapas é uma biblioteca de mapas vetoriais para Android. Este artigo irá orientá-lo sobre o processo de instalação do SDK do Android do Azure Mapas, incluindo o carregamento de um mapa e a adição de um marcador.

## <a name="prerequisites-to-get-started"></a>Pré-requisitos para introdução

### <a name="create-an-azure-maps-account"></a>Criar uma conta dos Mapas do Azure 

Para seguir as etapas deste guia, primeiro é necessário consultar [gerenciar conta e chaves](how-to-manage-account-keys.md) para criar e gerenciar a assinatura da conta com tipo de preço S1.

### <a name="download-android-studio"></a>Baixar o Android Studio

É possível baixar o [Android Studio](https://developer.android.com/studio/) gratuitamente pelo Google. Para instalar o SDK do Android do Azure Mapas, primeiro é necessário baixar o Android Studio e criar um projeto com uma atividade vazia.

## <a name="create-a-project-in-android-studio"></a>Criar um projeto no Android Studio

Será necessário criar um novo projeto com uma atividade vazia. Siga as etapas abaixo para criar um novo projeto do Android Studio:

1. Em *Escolher seu projeto*, selecione "Telefone e Tablet" como fator forma em que o aplicativo será executado.
2. Clique em *Atividade Vazia* no fator forma e clique em **Avançar**.
3. Em *Configurar seu projeto*, selecione `API 21: Android 5.0.0 (Lollipop)` como o SDK mínimo. Essa é a versão inferior com suporte pelo SDK do Android do Azure Mapas.
4. Aceite o padrão `Activity Name` e `Layout Name` e clique em **Concluir**

Consulte a [documentação do Android Studio](https://developer.android.com/studio/intro/) para obter mais informações sobre a instalação do Android Studio e como Criar um novo projeto.

![criar um novo projeto](./media/how-to-use-android-map-control-library/form-factor-android.png)

## <a name="set-up-a-virtual-device"></a>Configurar um dispositivo virtual

O Android Studio permite configurar um dispositivo Android virtual no computador. Isso poderá ajudá-lo a testar o aplicativo enquanto você o desenvolve. Para configurar um dispositivo virtual, clique no ícone do Gerenciador de AVD no canto superior direito da tela do projeto. Em seguida, clique no botão **Criar Dispositivo Virtual**. Também é possível acessar o gerenciador através de **Ferramentas > Android > Gerenciador de AVD** na barra de ferramentas. Na categoria **Telefones**, selecione **Nexus 5X** e clique em **Avançar**.

Saiba mais sobre como configurar um AVD, consultando a [documentação do Android Studio](https://developer.android.com/studio/run/managing-avds).

![Android Emulator](./media/how-to-use-android-map-control-library/android-emulator.png)

## <a name="install-azure-maps-android-sdk"></a>Instalar o SDK do Android do Azure Mapas

Antes de avançar para a criação do aplicativo, siga as etapas abaixo para instalar o SDK do Android do Azure Mapas. 

1. Adicione o seguinte ao bloco de repositórios **all projects** no arquivo **build.gradle**.

    ```
    maven {
            url "https://atlas.microsoft.com/sdk/android"
    }
    ```

2. Atualize o **app/build.gradle** e adicione a ele o seguinte:

    1. Adicione o seguinte ao bloco do Android:

        ```
        compileOptions {
            sourceCompatibility JavaVersion.VERSION_1_8
            targetCompatibility JavaVersion.VERSION_1_8
        }
        ```
    2. Atualize o bloco de dependências e adicione a ele o seguinte:

        ```
        implementation "com.microsoft.azure.maps:mapcontrol:0.1"
        ```

3. Configure as permissões, adicionando o seguinte ao **AndroidManifest.xml**

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <manifest>
        ...
        <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
        ...
    </manifest>
    ```

4. Edite **res > layout > activity_main.xml**, que será semelhante ao XML abaixo:
    
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
            app:mapcontrol_cameraTargetLat="47.64"
            app:mapcontrol_cameraTargetLng="-122.33"
            app:mapcontrol_cameraZoom="12"
            />

    </FrameLayout>
    ```

5. Edite **MainActivity.java** para criar uma classe de atividade de exibição de mapa. Após a edição, a classe deverá ser semelhante à seguinte:

    ```java
    package com.example.myapplication;

    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import com.microsoft.azure.maps.mapcontrol.AzureMaps;
    import com.microsoft.azure.maps.mapcontrol.MapControl;
    import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
    import com.microsoft.azure.maps.mapcontrol.options.MapStyle;
    import com.microsoft.azure.maps.mapcontrol.source.DataSource;

    public class MainActivity extends AppCompatActivity {
        
        static {
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

        MapControl mapControl;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            mapControl = findViewById(R.id.mapcontrol);

            mapControl.onCreate(savedInstanceState);

        }

        @Override
        public void onResume() {
            super.onResume();
            mapControl.onResume();
        }

        @Override
        public void onPause() {
            super.onPause();
            mapControl.onPause();
        }

        @Override
        public void onStop() {
            super.onStop();
            mapControl.onStop();
        }

        @Override
        public void onLowMemory() {
            super.onLowMemory();
            mapControl.onLowMemory();
        }

        @Override
        protected void onDestroy() {
            super.onDestroy();
            mapControl.onDestroy();
        }

        @Override
        protected void onSaveInstanceState(Bundle outState) {
            super.onSaveInstanceState(outState);
            mapControl.onSaveInstanceState(outState);
        }

    }

    ```

## <a name="import-classes"></a>Importar classes

Após concluir as etapas acima, você provavelmente receberá avisos do Android Studio sobre algum texto no código. Para resolver esses avisos, importe as classes que estão sendo referenciadas em `MainActivity.java`.

É possível importar essas classes automaticamente, pressionando `Alt`+`Enter`(`Option`+`Return` no Mac). 

Clique no botão **Executar 'App'** (ou `Control`+`R` em um Mac) para criar o aplicativo.

![Clique em Executar](./media/how-to-use-android-map-control-library/run-app.png)

Levará alguns segundos para o Android Studio criar o aplicativo. Após concluir a criação do aplicativo, você poderá testá-lo no dispositivo Android emulado. Você verá um mapa semelhante ao apresentado abaixo.

![Mapa do Android](./media/how-to-use-android-map-control-library/android-map.png)

## <a name="add-a-marker-to-the-map"></a>Adicionar um marcador ao mapa

Para adicionar um marcador ao mapa, adicione a função `mapView.getMapAsync()` para `MainActivity.java`. O `MainActivity.java` final deverá ser semelhante ao seguinte:

```java
package com.example.myapplication;

import android.app.Activity;
import android.os.Bundle;
import com.mapbox.geojson.Feature;
import com.mapbox.geojson.Point;
import com.microsoft.azure.maps.mapcontrol.AzureMaps;
import com.microsoft.azure.maps.mapcontrol.MapControl;
import com.microsoft.azure.maps.mapcontrol.layer.SymbolLayer;
import com.microsoft.azure.maps.mapcontrol.source.DataSource;
import static com.microsoft.azure.maps.mapcontrol.options.SymbolLayerOptions.iconImage;
public class MainActivity extends AppCompatActivity {
    
    static{
            AzureMaps.setSubscriptionKey("{subscription-key}");
        }

    MapControl mapControl;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mapControl = findViewById(R.id.mapcontrol);

        mapControl.onCreate(savedInstanceState);

        mapControl.getMapAsync(map -> {
            DataSource dataSource = new DataSource();
            dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64)));

            SymbolLayer symbolLayer = new SymbolLayer(dataSource);
            symbolLayer.setOptions(iconImage("my-icon"));

            map.images.add("my-icon", R.drawable.mapcontrol_marker_red);
            map.sources.add(dataSource);
            map.layers.add(symbolLayer);
        });
    }

    @Override
    public void onStart() {
        super.onStart();
        mapControl.onStart();
    }

    @Override
    public void onResume() {
        super.onResume();
        mapControl.onResume();
    }

    @Override
    public void onPause() {
        super.onPause();
        mapControl.onPause();
    }

    @Override
    public void onStop() {
        super.onStop();
        mapControl.onStop();
    }

    @Override
    public void onLowMemory() {
        super.onLowMemory();
        mapControl.onLowMemory();
    }

    @Override
    protected void onDestroy() {
        super.onDestroy();
        mapControl.onDestroy();
    }

    @Override
    protected void onSaveInstanceState(Bundle outState) {
        super.onSaveInstanceState(outState);
        mapControl.onSaveInstanceState(outState);
    }
}
```

Execute novamente o aplicativo e você deve ver um marcador no mapa como mostrado abaixo.

![Marcador de mapa do Android](./media/how-to-use-android-map-control-library/android-map-pin.png)