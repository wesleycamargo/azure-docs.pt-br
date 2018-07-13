---
title: Tutorial de Java da API de Detecção Facial para Android | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Crie um aplicativo Android simples que use a API de Detecção Facial dos Serviços Cognitivos para detectar e enquadrar faces humanas em uma imagem.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: 5164a261d482d0cca3842a973d2109b17999bd25
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364620"
---
# <a name="getting-started-with-face-api-in-java-for-android-tutorial"></a>Tutorial de introdução à API de Detecção Facial em Java para Android

Neste tutorial, você aprenderá a criar e desenvolver um aplicativo simples do Android que invoque a API de Detecção Facial para detectar faces humanas em uma imagem. O aplicativo mostra o resultado enquadrando as faces que ele detecta.

![GettingStartedAndroid](../Images/android_getstarted2.1.PNG)

## <a name="preparation"></a> Preparação

Para usar este tutorial, serão necessários os pré-requisitos a seguir:

- Android Studio e SDK instalados
- Dispositivo Android (opcional para teste).

## <a name="step1"></a>Etapa 1: Assinar a API de Detecção Facial e obter sua chave de assinatura

Antes de usar qualquer API de Detecção Facial, você deverá se inscrever para assinar a API de Detecção Facial no portal dos Serviços Cognitivos da Microsoft. Veja [assinaturas](https://azure.microsoft.com/try/cognitive-services/). Tanto a chave primária quanto a secundária pode ser usada neste tutorial.

## <a name="step2"></a>Etapa 2: Criar a estrutura do aplicativo

Nesta etapa, você criará um projeto de aplicativo do Android para implementar a interface do usuário básica para escolher e exibir uma imagem. Simplesmente siga as instruções abaixo: 

1. Abra o Android Studio.
2. No menu Arquivo, clique em **Novo Projeto...**
3. Dê ao aplicativo o nome de **MyFirstApp** e, em seguida, clique em Avançar. 

    ![GettingStartAndroidNewProject](../Images/AndroidNewProject.png)

4. Escolha a plataforma de destino conforme necessário e, em seguida, clique em Avançar. 

    ![GettingStartAndroidNewProject2](../Images/AndroidNewProject2.png)

5. Selecione **Atividade Básica** e, em seguida, clique em Avançar.
6. Dê à atividade o seguinte nome e clique em Concluir. 

    ![GettingStartAndroidNewProject4](../Images/AndroidNewProject4.png)

7. Abra **activity_main.xml**, você deverá ver o Editor de Layout dessa atividade.
8. Exiba o arquivo de origem de Texto e, em seguida, edite o layout de atividade da seguinte maneira:

    ```xml
    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:tools="http://schemas.android.com/tools" android:layout_width="match_parent"
        android:layout_height="match_parent" android:paddingLeft="@dimen/activity_horizontal_margin"
        android:paddingRight="@dimen/activity_horizontal_margin"
        android:paddingTop="@dimen/activity_vertical_margin"
        android:paddingBottom="@dimen/activity_vertical_margin" tools:context=".MainActivity">
     
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="fill_parent"
            android:id="@+id/imageView1"
            android:layout_above="@+id/button1" />
    
        <Button
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="Browse"
            android:id="@+id/button1"
            android:layout_alignParentBottom="true" />
    </RelativeLayout>
    ```  

9. Abra **MainActivity.java** e insira as seguintes diretivas de importação no início do arquivo:

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
    ```
      
    Em segundo lugar, modifique a classe da seguinte maneira:  
    
    ```java
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
                Intent gallIntent = new Intent(Intent.ACTION_GET_CONTENT);
                gallIntent.setType("image/*");
                startActivityForResult(Intent.createChooser(gallIntent, "Select Picture"), PICK_IMAGE);
            }
        });
         
        detectionProgressDialog = new ProgressDialog(this);
    }
    
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
                } catch (IOException e) {
                e.printStackTrace();
                }
        }
    }
    ```

Agora, seu aplicativo pode procurar uma foto da galeria e exibi-la na janela de modo semelhante à imagem a seguir:

![GettingStartAndroidUI](../Images/android_getstarted1.1.PNG)

## <a name="step3"></a>Etapa 3: Configurar a biblioteca de clientes da API de Detecção Facial

A API de Detecção Facial é uma API de nuvem que você pode invocar usando solicitações HTTPS. Para uma maneira mais conveniente de usar a API de Detecção Facial em aplicativos de plataforma .NET, uma biblioteca de clientes também é fornecida para encapsular solicitações Web. Neste exemplo, usamos a biblioteca de clientes para simplificar nosso trabalho. 

Siga as instruções abaixo para configurar a biblioteca de clientes: 

1. Localize o arquivo **build.gradle** de nível superior do projeto no painel Projeto mostrado no exemplo. Observe que há vários outros arquivos **build.gradle** na sua árvore de projeto e você precisa abrir o arquivo **build.gradle** de nível superior primeiro.
2. Adicione **mavenCentral()** aos repositórios do seu projeto. Você também pode usar jcenter(), que é o repositório padrão do Android Studio, já que jcenter() é um superconjunto de mavenCentral().  

```
    allprojects {
        repositories {
            ...
            mavenCentral()
        }
    }
```

3. Abra o arquivo **build.gradle** em seu projeto 'app'.
4. Adicione uma dependência à nossa biblioteca de clientes armazenada no Repositório Central Maven:

```
    dependencies {  
        ...  
        implementation 'com.microsoft.projectoxford:face:1.4.3'  
    }
```

5. Abra **MainActivity.java** no seu projeto 'app' e insira as seguintes diretivas de importação: 
    
    ```java
    import com.microsoft.projectoxford.face.*;  
    import com.microsoft.projectoxford.face.contract.*;  
    ```
    
   Em seguida, insira o seguinte código na classe:

    ```java
    private FaceServiceClient faceServiceClient = new FaceServiceRestClient("your API endpoint", "<Subscription Key>");
    ```

   Substitua o primeiro parâmetro acima pelo ponto de extremidade de API que foi atribuído à sua chave na etapa 1. Por exemplo: 
   
        https://eastus2.api.cognitive.microsoft.com/face/v1.0
   
   Substitua o segundo parâmetro pela chave de assinatura que você obteve na etapa 1.
   
6. Abra o arquivo **AndroidManifest.xml** em seu projeto 'app'. Insira o seguinte elemento como filho do elemento **manifest**:  

    ```xml
    <uses-permission android:name="android.permission.INTERNET" />  
    ```

7. Agora você está pronto para chamar a API de Detecção Facial usando seu aplicativo. 

## <a name="step4"></a>Etapa 4: Fazer upload de imagens para detectar faces

A maneira mais simples de detectar faces é chamando a API [Face Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) fazendo upload do arquivo de imagem diretamente. Ao usar a biblioteca de clientes, isso pode ser feito com o método assíncrono **DetectAsync** da classe **FaceServiceClient**. Cada face retornada contém um retângulo para indicar sua localização, combinado com uma série de atributos faciais opcionais. Neste exemplo, precisamos apenas recuperar o local da face. Aqui, precisamos inserir um método na classe **MainActivity** para detecção facial: 

```java

    // Detect faces by uploading face images
    // Frame faces after detection
    
    private void detectAndFrame(final Bitmap imageBitmap)
    {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        imageBitmap.compress(Bitmap.CompressFormat.JPEG, 100, outputStream);
        ByteArrayInputStream inputStream = 
            new ByteArrayInputStream(outputStream.toByteArray());
        AsyncTask<InputStream, String, Face[]> detectTask =
            new AsyncTask<InputStream, String, Face[]>() {
                @Override
                protected Face[] doInBackground(InputStream... params) {
                    try {
                        publishProgress("Detecting...");
                        Face[] result = faceServiceClient.detect(
                                params[0], 
                                true,         // returnFaceId
                                false,        // returnFaceLandmarks
                                null           // returnFaceAttributes: a string like "age, gender"
                /* If you want value of FaceAttributes, try adding 4th argument like below.
                            new FaceServiceClient.FaceAttributeType[] {
                    FaceServiceClient.FaceAttributeType.Age,
                    FaceServiceClient.FaceAttributeType.Gender }
                */              
                        );
                        if (result == null)
                        {
                            publishProgress("Detection Finished. Nothing detected");
                            return null;
                        }
                        publishProgress(
                                String.format("Detection Finished. %d face(s) detected",
                                        result.length));
                        return result;
                    } catch (Exception e) {
                        publishProgress("Detection failed");
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
```

## <a name="step5"></a>Etapa 5: Marcar faces na imagem

Nesta última etapa, podemos combinar todas as etapas acima e marcar as faces detectadas com quadros na imagem. Primeiro, abra **MainActivity.java** e insira um método auxiliar para desenhar retângulos: 

```java
    private static Bitmap drawFaceRectanglesOnBitmap(Bitmap originalBitmap, Face[] faces) {
        Bitmap bitmap = originalBitmap.copy(Bitmap.Config.ARGB_8888, true);
        Canvas canvas = new Canvas(bitmap);
        Paint paint = new Paint();
        paint.setAntiAlias(true);
        paint.setStyle(Paint.Style.STROKE);
        paint.setColor(Color.RED);
        int stokeWidth = 2;
        paint.setStrokeWidth(stokeWidth);
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

Agora conclua as partes TODO no método **detectAndFrame** para enquadrar as faces e relatar o status.

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
        if (result == null) return;
        ImageView imageView = (ImageView)findViewById(R.id.imageView1);
        imageView.setImageBitmap(drawFaceRectanglesOnBitmap(imageBitmap, result));
        imageBitmap.recycle();
    }
```
 
Por fim, adicione uma chamada ao método **detectAndFrame** do método **onActivityResult** conforme mostrado abaixo. 

```java
    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (requestCode == PICK_IMAGE && resultCode == RESULT_OK && data != null && data.getData() != null) {
            Uri uri = data.getData();
            try {
                Bitmap bitmap = MediaStore.Images.Media.getBitmap(getContentResolver(), uri);
                ImageView imageView = (ImageView) findViewById(R.id.imageView1);
                imageView.setImageBitmap(bitmap);
     
                // This is the new addition.
                // detectAndFrame(bitmap);
     
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```

Execute esse aplicativo e procure uma imagem contendo uma face. Aguarde alguns segundos para permitir que a API de nuvem responda. Depois disso, você obterá um resultado semelhante à imagem a seguir: 

![GettingStartAndroid](../Images/android_getstarted2.1.PNG)

## <a name="summary"></a> Resumo

Neste tutorial, você aprendeu o processo básico para usar a API de Detecção Facial e criou um aplicativo para exibir marcas faciais em imagens. Para obter mais informações sobre a API de Detecção Facial, consulte as instruções e a [Referência da API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236). 

## <a name="related"></a> Tutoriais relacionados

- [Tutorial de Introdução à API de Detecção Facial em CSharp](FaceAPIinCSharpTutorial.md)
- [Tutorial de introdução à API de Detecção Facial em Python](FaceAPIinPythonTutorial.md)
