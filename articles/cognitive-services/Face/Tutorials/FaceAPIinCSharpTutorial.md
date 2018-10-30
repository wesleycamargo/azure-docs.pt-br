---
title: 'Tutorial: Detectar e enquadrar rostos em uma imagem usando o SDK do .NET com C#'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você cria um aplicativo Windows que usa a API de Detecção Facial para detectar e enquadrar rostos em uma imagem.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: pafarley
ms.openlocfilehash: df5e46270275ca08a8499607fbad4c1161382fab
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956487"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image-using-the-net-sdk"></a>Tutorial: Criar um aplicativo WPF para detectar e enquadrar faces em uma imagem usando o SDK do .NET

Neste tutorial, você cria um aplicativo do Windows Presentation Framework (WPF) que usa o serviço de Detecção Facial por meio de sua biblioteca de clientes .NET. O aplicativo detecta rostos em uma imagem, desenha um quadro em volta de cada rosto e exibe uma descrição do rosto na barra de status. O código de exemplo completo está disponível no GitHub em [Detectar e enquadrar rostos em uma imagem no Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample).

![Captura de tela mostrando os rostos detectadas enquadrados dentro de retângulos](../Images/getting-started-cs-detected.png)

Este tutorial mostra como:

> [!div class="checklist"]
> - Criar um aplicativo WPF
> - Instalar a biblioteca de clientes do serviço de Detecção Facial
> - Use a biblioteca de clientes para detectar rostos em uma imagem
> - Desenhar um quadro em torno de cada rosto detectado
> - Exibir uma descrição do rosto na barra de status

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa de uma chave de assinatura para executar o exemplo. É possível obter chaves de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Qualquer edição do [Visual Studio 2015 ou 2017](https://www.visualstudio.com/downloads/). Para o Visual Studio 2017, a carga de trabalho de desenvolvimento do aplicativo de área de trabalho do .NET é necessária. Este tutorial usa o Visual Studio 2017 Community Edition.
- O pacote NuGet da biblioteca de clientes [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Não é necessário baixar o pacote. As instruções de instalação são fornecidas abaixo.

## <a name="create-the-visual-studio-solution"></a>Criar a solução do Visual Studio

Siga estas etapas para criar um projeto de aplicativo do WPF do Windows.

1. Abra o Visual Studio e, no menu **Arquivo**, clique em **Novo** e, em seguida, clique em **Projeto**.
   - No Visual Studio 2017, expanda **Instalado**, em seguida, **Outras linguagens**. Selecione **Visual C#**, em seguida, **Aplicativo WPF (.NET Framework)**.
   - No Visual Studio 2015, expanda **Instalado**, em seguida, **Modelos**. Selecione **Visual C#**, em seguida, **Aplicativo WPF**.
1. Nomeie o aplicativo **FaceTutorial** e, em seguida, clique em **OK**.

## <a name="install-the-face-service-client-library"></a>Instalar a biblioteca de clientes do serviço de Detecção Facial

Siga estas instruções para instalar a biblioteca de clientes.

1. No menu **Ferramentas**, selecione **Gerenciador de Pacotes do NuGet** e, em seguida, **Console do Gerenciador de Pacotes**.
1. No **Console do Gerenciador de Pacotes**, cole o comando a seguir e, em seguida, pressione **Enter**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.2.0-preview`

## <a name="add-the-initial-code"></a>Adicionar o código inicial

### <a name="mainwindowxaml"></a>MainWindow.xaml

Abra *MainWindow.xaml* (Dica: alterne entre os painéis usando o **ícone de seta para cima/para baixo**) e substitua o conteúdo pelo código a seguir. Esse código xaml é usado para criar a janela de interface do usuário. Anote os manipuladores de eventos, `FacePhoto_MouseMove` e `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Expanda *MainWindow.xaml*, abra *MainWindow.xaml.cs* e substitua o conteúdo pelo código a seguir. Ignore os sublinhados ondulados vermelhos; eles desaparecerão após a primeira compilação.

As duas primeiras linhas importam os namespaces da biblioteca de clientes. Em seguida, o `FaceClient` é criado, passando a chave de assinatura, enquanto a região do Azure é definida no construtor `MainWindow`. Os dois métodos, `BrowseButton_Click` e `FacePhoto_MouseMove`, correspondem aos manipuladores de eventos declarados em *MainWindow.xaml*.

`BrowseButton_Click` cria um `OpenFileDialog`, que permite que o usuário selecione uma imagem jpg. A imagem é lida e exibida na janela principal. O código restante para `BrowseButton_Click` e o código para `FacePhoto_MouseMove` são inseridos nas etapas posteriores.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(faceEndpoint, UriKind.Absolute))
            {
                faceClient.Endpoint = faceEndpoint;
            }
            else
            {
                MessageBox.Show(faceEndpoint,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Insira a chave de assinatura e verifique ou altere a região

- Localize a seguinte linha em *MainWindow.xaml.cs* e substitua `<Subscription Key>` pela sua chave de assinatura de API de Detecção Facial:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Localize a seguinte linha em *MainWindow.xaml.cs* e substitua ou verifique a região do Azure associada à sua chave de assinatura:

    ```csharp
    private const string Endpoint =
        "https://westcentralus.api.cognitive.microsoft.com";
    ```

    Certifique-se de que o local é o mesmo onde você obteve suas chaves de assinatura. Se você obteve as chaves de assinatura da região **westus**, por exemplo, substitua `Westcentralus` por `Westus`.

    Se você recebeu as chaves de assinatura usando a avaliação gratuita, a região das chaves é **westcentralus**, portanto, nenhuma alteração é necessária.

### <a name="test-the-app"></a>Testar o aplicativo

Pressione **Iniciar** no menu para testar seu aplicativo. Quando a janela abrir, clique em **Procurar** no canto inferior esquerdo. Uma caixa de diálogo chamada **Abrir arquivo** aparece onde você pode procurar e selecione uma foto, que é exibida na janela.

![Captura de tela mostrando a imagem não modificada dos rostos](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Carregar uma imagem para detectar rostos

A maneira mais simples de detectar rostos é chamando o método `FaceClient.Face.DetectWithStreamAsync`, que encapsula o método da API de [Detecção](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para carregar a imagem local.

Insira o seguinte método na classe `MainWindow`, abaixo do método `FacePhoto_MouseMove`.

Uma lista de atributos faciais para análise é criada e o arquivo de imagem enviado é lido em um `Stream`. Ambos são passados para a chamada de `DetectWithStreamAsync`.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Desenhar retângulos em torno de cada rosto

Adicione o código para desenhar um retângulo ao redor de cada rosto detectado na imagem.

Em *MainWindow.xaml.cs*, adicione o modificador `async` ao método `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Insira o código a seguir no final do método `BrowseButton_Click`, após a linha `FacePhoto.Source = bitmapSource`.

A lista de rostos detectados é preenchida pela chamada para `UploadAndDetectFaces`. Em seguida, um retângulo é desenhado ao redor de cada rosto e a imagem alterada é exibida na janela principal.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Descrever os rostos na imagem

Acrescente Insira o seguinte método na classe `MainWindow`, abaixo do método `UploadAndDetectFaces`.

O método converte os atributos faciais em uma cadeia de caracteres que descreve o rosto. Essa cadeia de caracteres é exibida quando o ponteiro do mouse passa sobre o retângulo de enquadramento facial.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Exibir a descrição facial

Substitua o método `FacePhoto_MouseMove` pelo seguinte código.

Esse manipulador de eventos exibe a cadeia de caracteres de descrição facial quando o ponteiro do mouse passa sobre o retângulo de enquadramento facial.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Execute o aplicativo

Execute o aplicativo e procure uma imagem contendo um rosto. Aguarde alguns segundos para permitir que o serviço de Detecção Facial responda. Depois disso, você verá um retângulo vermelho nos rostos da imagem. Ao mover o mouse sobre um retângulo de enquadramento facial, a descrição desse rosto aparecerá na barra de status.

![Captura de tela mostrando os rostos detectadas enquadrados dentro de retângulos](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Resumo

Neste tutorial, você aprendeu o processo básico para usar a biblioteca de clientes do serviço de Detecção Facial e criou um aplicativo para exibir e enquadrar rostos em uma imagem.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como detectar e usar pontos de referência para rostos.

> [!div class="nextstepaction"]
> [Como detectar rostos em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
