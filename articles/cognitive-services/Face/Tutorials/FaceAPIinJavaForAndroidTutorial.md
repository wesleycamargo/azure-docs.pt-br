---
title: 'Tutorial: detectar e enquadrar rostos em uma imagem – API de Detecção Facial, Java para Android'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo Android simples que usa a API de Detecção Facial para detectar e enquadrar rostos em uma imagem.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 07/12/2018
ms.author: nolachar
ms.openlocfilehash: 50185588b77f01bced9462d5fd1ad67bb5de6e08
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129719"
---
# <a name="tutorial-create-an-android-app-to-detect-and-frame-faces-in-an-image"></a>Tutorial: Criar um aplicativo Android para detectar e enquadrar rostos em uma imagem

Neste tutorial, você cria um aplicativo Android simples que usa a biblioteca de classes Java do serviço de Detecção Facial para detectar e enquadrar rostos em uma imagem. O aplicativo mostra uma imagem selecionada com cada rosto detectado enquadrado dentro de um retângulo. O código de exemplo completo está disponível no GitHub em [Detectar e enquadrar rostos em uma imagem no Android](https://github.com/Azure-Samples/cognitive-services-face-android-sample).

![Captura de tela do Android de uma foto com rostos enquadrados dentro de um retângulo vermelho](../Images/android_getstarted2.1.PNG)

Este tutorial mostra como:

> [!div class="checklist"]
> - Criar um aplicativo do Android
> - Instalar a biblioteca de clientes do serviço de Detecção Facial
> - Use a biblioteca de clientes para detectar rostos em uma imagem
> - Desenhar um quadro em torno de cada rosto detectado

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa de uma chave de assinatura para executar o exemplo. É possível obter chaves de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- [Android Studio](https://developer.android.com/studio/) com no mínimo SDK 22 (necessário para a biblioteca de clientes de Detecção Facial).
- A biblioteca de clientes de Detecção Facial [com.microsoft.projectoxford:face:1.4.3](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.microsoft.projectoxford%22) do Maven. Não é necessário baixar o pacote. As instruções de instalação são fornecidas abaixo.

## <a name="create-the-project"></a>Criar o projeto

Crie seu projeto de aplicativo Android seguindo estas etapas:

1. Abra o Android Studio. Este tutorial usa o Android Studio 3.1.
1. Selecione **Iniciar um novo projeto do Android Studio**.
1. Na tela **Criar projeto do Android**, modifique os campos padrão, se necessário, e clique em **Avançar**.
1. Na tela **Dispositivos Android de destino**, use o seletor de lista suspensa para escolher **API 22** ou superior, em seguida, clique em **Avançar**.
1. Selecione **Atividade vazia**, em seguida, clique em **Avançar**.
1. Desmarque a opção **Compatibilidade com versões anteriores**, em seguida, clique em **Concluir**.

## <a name="create-the-ui-for-selecting-and-displaying-the-image"></a>Criar a interface do usuário para selecionar e exibir a imagem

Abra *activity_main.xml*, você deverá ver o Editor de layout. Selecione a guia **Texto** e substitua o conteúdo pelo código a seguir.

```xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <ImageView
        android:layout_width="match_parent"
        android:layout_height="fill_parent"
        android:id="@+id/imageView1"
        android:layout_above="@+id/button1"
        android:contentDescription="Image with faces to analyze"/>

    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Browse for face image"
        android:id="@+id/button1"
        android:layout_alignParentBottom="true"/>
</RelativeLayout>
```

Abra *MainActivity.java*, em seguida, substitua tudo, exceto a primeira instrução `package` pelo código a seguir.

O código define um manipulador de eventos para `Button` que inicia uma nova atividade para permitir que o usuário selecione uma imagem. Depois de selecionada, a imagem é exibida no `ImageView`.

```java
import java.io.*;
import android.app.*;
import android.content.*;
import android.net.*;
import android.os.*;
import android.view.*;
import android.graphics.*;
import android.widget.*;
import android.provider.*;

public class MainActivity extends Activity {
    private final int PICK_IMAGE = 1;
    private ProgressDialog detectionProgressDialog;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            Button button1 = (Button)findViewById(R.id.button1);
            button1.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View v) {
                Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
                intent.setType("image/*");
                startActivityForResult(Intent.createChooser(
                        intent, "Select Picture"), PICK_IMAGE);
            }
        });

        detectionProgressDialog = new ProgressDialog(this);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                        getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);

                // Uncomment
                //detectAndFrame(bitmap);
                } catch (IOException e) {
                    e.printStackTrace();
                }
        }
    }
}
```

Agora, seu aplicativo pode procurar uma foto e exibi-la na janela de modo semelhante à imagem a seguir.

![Captura de tela do Android de uma foto com rostos](../Images/android_getstarted1.1.PNG)

## <a name="configure-the-face-client-library"></a>Configurar a biblioteca de clientes de Detecção Facial

A API de Detecção Facial é uma API de nuvem que você pode chamar usando solicitações HTTPS. Este tutorial usa a biblioteca de clientes de Detecção Facial, que encapsula essas solicitações da Web, para simplificar seu trabalho.

No painel **Projetos**, use o seletor de lista suspensa para selecionar **Android**. Expanda **Scripts Gradle**, em seguida, abra *build.gradle (Module:app)*.

Adicione uma dependência para a biblioteca de clientes de Detecção Facial `com.microsoft.projectoxford:face:1.4.3`, conforme mostrado na captura de tela abaixo, em seguida, clique em **Sincronizar agora**.

![Captura de tela do Android Studio Build do arquivo build.gradle](../Images/face-tut-java-gradle.png)

Abra **MainActivity.java** e acrescente as seguintes diretivas de importação:

```java
import com.microsoft.projectoxford.face.*;
import com.microsoft.projectoxford.face.contract.*;
```

## <a name="add-the-face-client-library-code"></a>Adicionar o código da biblioteca de clientes de Detecção Facial

Insira o seguinte código na classe `MainActivity`, acima do método `onCreate`:

```java
private final String apiEndpoint = "<API endpoint>";
private final String subscriptionKey = "<Subscription Key>";

private final FaceServiceClient faceServiceClient =
        new FaceServiceRestClient(apiEndpoint, subscriptionKey);
```

Substitua `<API endpoint>` pelo ponto de extremidade da API que foi atribuída à sua chave. As chaves de assinatura de avaliação gratuita são geradas na região **westcentralus**. Portanto, se você estiver usando uma chave de assinatura de avaliação gratuita, a instrução seria:

```java
apiEndpoint = "https://westcentralus.api.cognitive.microsoft.com/face/v1.0";
```

Substitua `<Subscription Key>` por sua chave de assinatura. Por exemplo: 

```java
subscriptionKey = "0123456789abcdef0123456789ABCDEF"
```

No painel **Projeto**, expanda **app**, em seguida, **manifests** e abra *AndroidManifest.xml*.

Insira o seguinte elemento como um filho direto do elemento `manifest`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Compile o projeto para verificar se há erros. Agora você está pronto para chamar o serviço de Detecção Facial.

## <a name="upload-an-image-to-detect-faces"></a>Carregar uma imagem para detectar rostos

A maneira mais simples de detectar rostos é chamar o método `FaceServiceClient.detect`. Este método encapsula o método da API de [Detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e retorna uma matriz de `Face`'s.

Cada `Face` retornada contém um retângulo para indicar sua localização, combinado com uma série de atributos faciais opcionais. Neste exemplo, somente a localização dos rostos é necessária.

Se ocorrer um erro, um `AlertDialog` exibe o motivo.

Insira os seguintes métodos na classe `MainActivity`.

```java
// Detect faces by uploading a face image.
// Frame faces after detection.
private void detectAndFrame(final Bitmap imageBitmap) {
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
    ByteArrayInputStream inputStream =
            new ByteArrayInputStream(outputStream.toByteArray());

    AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                String exceptionMessage = "";

                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0],
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null          // returnFaceAttributes:
                                /* new FaceServiceClient.FaceAttributeType[] {
                                    FaceServiceClient.FaceAttributeType.Age,
                                    FaceServiceClient.FaceAttributeType.Gender }
                                */
                        );
                        if (result == null){
                            publishProgress(
                                    "Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(String.format(
                                "Detection Finished. %d face(s) detected",
                                result.length));
                        return result;
                    } catch (Exception e) {
                        exceptionMessage = String.format(
                                "Detection failed: %s", e.getMessage());
                        return null;
                    }
                }

                @Override
                protected void onPreExecute() {
                    //TODO: show progress dialog
                }
                @Override
                protected void onProgressUpdate(String... progress) {
                    //TODO: update progress
                }
                @Override
                protected void onPostExecute(Face[] result) {
                    //TODO: update face frames
                }
            };

    detectTask.execute(inputStream);
}

private void showError(String message) {
    new AlertDialog.Builder(this)
    .setTitle("Error")
    .setMessage(message)
    .setPositiveButton("OK", new DialogInterface.OnClickListener() {
            public void onClick(DialogInterface dialog, int id) {
        }})
    .create().show();
}
```

## <a name="frame-faces-in-the-image"></a>Enquadrar rostos na imagem

Insira o método auxiliar a seguir na classe `MainActivity`. Esse método desenha um retângulo ao redor de cada rosto detectado.

```java
private static Bitmap drawFaceRectanglesOnBitmap(
        Bitmap originalBitmap, Face[] faces) {
    Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
    Canvas canvas = new Canvas(bitmap);
    Paint paint = new Paint();
    paint.setAntiAlias(true);
    paint.setStyle(Paint.Style.STROKE);
    paint.setColor(Color.RED);
    paint.setStrokeWidth(10);
    if (faces != null) {
        for (Face face : faces) {
            FaceRectangle faceRectangle = face.faceRectangle;
            canvas.drawRect(
                    faceRectangle.left,
                    faceRectangle.top,
                    faceRectangle.left + faceRectangle.width,
                    faceRectangle.top + faceRectangle.height,
                    paint);
        }
    }
    return bitmap;
}
```

Conclua os métodos `AsyncTask`, indicados pelos comentários `TODO`, no método `detectAndFrame`. Em caso de sucesso, a imagem selecionada é exibida com rostos enquadrados no `ImageView`.

```java
@Override
protected void onPreExecute() {
    detectionProgressDialog.show();
}
@Override
protected void onProgressUpdate(String... progress) {
    detectionProgressDialog.setMessage(progress[0]);
}
@Override
protected void onPostExecute(Face[] result) {
    detectionProgressDialog.dismiss();
    if(!exceptionMessage.equals("")){
        showError(exceptionMessage);
    }
    if (result == null) return;
    ImageView imageView = findViewById(R.id.imageView1);
    imageView.setImageBitmap(
            drawFaceRectanglesOnBitmap(imageBitmap, result));
    imageBitmap.recycle();
}
```

Por fim, no método `onActivityResult`, remova a marca de comentário da chamada para o método `detectAndFrame`, conforme mostrado abaixo.

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PICK_IMAGE && resultCode == RESULT_OK &&
                data != null && data.getData() != null) {
        Uri uri = data.getData();
        try {
            Bitmap bitmap = MediaStore.Images.Media.getBitmap(
                    getContentResolver(), uri);
            ImageView imageView = findViewById(R.id.imageView1);
            imageView.setImageBitmap(bitmap);

            // Uncomment
            detectAndFrame(bitmap);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## <a name="run-the-app"></a>Execute o aplicativo

Execute esse aplicativo e procure uma imagem contendo um rosto. Aguarde alguns segundos para permitir que o serviço de Detecção Facial responda. Depois disso, você obterá um resultado semelhante à imagem a seguir:

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a>Resumo

Neste tutorial, você aprendeu o processo básico para usar o serviço de Detecção Facial e criou um aplicativo para exibir rostos enquadrados em uma imagem.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como detectar e usar pontos de referência para rostos.

> [!div class="nextstepaction"]
> [Como detectar rostos em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)

Explore as APIs de Detecção Facial usadas para detectar rostos e seus atributos, como posição, sexo, idade, posição da cabeça, pelos faciais e óculos.

> [!div class="nextstepaction"]
> [Referência da API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).