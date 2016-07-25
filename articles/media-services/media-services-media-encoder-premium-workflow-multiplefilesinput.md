<properties 
	pageTitle="Usando vários arquivos de entrada e propriedades do componente com o Codificador Premium | Microsoft Azure" 
	description="Este tópico explica como usar setRuntimeProperties para usar vários arquivos de entrada e transmitir dados personalizados para o processador de mídia do Fluxo de Trabalho Premium do Codificador de Mídia." 
	services="media-services" 
	documentationCenter="" 
	authors="xpouyat" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2016"  
	ms.author="xpouyat;anilmur;juliako"/>

#Usando vários arquivos de entrada e propriedades do componente com o Codificador Premium

##Visão geral 

Há situações em que talvez seja necessário personalizar as propriedades do componente, especifique o conteúdo XML da Lista de Clipes ou envie vários arquivos de entrada ao enviar uma tarefa com o processador de mídia **Fluxo de Trabalho Premium do Codificador de Mídia**. Alguns exemplos incluem:

- sobrepor texto em vídeo e definir o valor de texto (por exemplo, a data atual) em tempo de execução de cada vídeo de entrada
- personalizar o XML da Lista de Clipes (para especificar um ou vários arquivos de origem, com ou sem corte, etc.)
- sobrepor uma imagem de logotipo no vídeo de entrada durante a codificação

Para permitir que o **Fluxo de Trabalho Premium do Codificador de Mídia** reconheça que você está alterando algumas propriedades no fluxo de trabalho ao criar a tarefa ou enviar vários arquivos de entrada, é necessário usar uma cadeia de caracteres de configuração que contém **setRuntimeProperties** e/ou **transcodeSource**. Este tópico explica como usá-los.


##Sintaxe da cadeia de caracteres de configuração

A cadeia de caracteres de configuração a ser definida na tarefa de codificação usa um documento XML com esta aparência:
  
    <?xml version="1.0" encoding="utf-8"?>
    <transcodeRequest>
      <transcodeSource>
      </transcodeSource>
      <setRuntimeProperties>
        <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      </setRuntimeProperties>
    </transcodeRequest>


O código em C# para ler a configuração XML de um arquivo e transmiti-la para a tarefa em um trabalho é:

    XDocument configurationXml = XDocument.Load(xmlFileName);
    IJob job = _context.Jobs.CreateWithSingleTask(
                                                  "Media Encoder Premium Workflow",
                                                  configurationXml.ToString(),
                                                  myAsset,
                                                  "Output asset",
                                                  AssetCreationOptions.None);


##Personalizando as propriedades do componente  

###Propriedade com um valor simples
Em alguns casos, é útil personalizar uma propriedade do componente junto com o arquivo de fluxo de trabalho que será executado pelo Fluxo de Trabalho Premium do Codificador de Mídia. Suponha que você criou um fluxo de trabalho que sobrepõe texto em vídeos, e o texto (por exemplo, a data atual) deve ser definido em tempo de execução. É possível fazer isso enviando o texto como o novo valor da propriedade de texto do componente de sobreposição por meio da tarefa de codificação. Você pode usar esse mecanismo para alterar outras propriedades de um componente no fluxo de trabalho (como alterar a posição ou a cor da sobreposição, alterar a taxa de bits do codificador AVC, etc.). **setRuntimeProperties** é usado para substituir uma propriedade nos componentes do fluxo de trabalho.

Exemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Optional Overlay/Overlay/filename" value="MyLogo.png"/>
          <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
      </setRuntimeProperties>
    </transcodeRequest>


###Propriedade com um valor XML

Para definir uma propriedade que espera um valor XML, encapsule-a usando <![CDATA[ and ]]>

Exemplo:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

>[AZURE.NOTE]Lembre-se de não colocar um retorno de carro logo após <![CDATA[


###Valor de propertyPath

Nos exemplos anteriores, propertyPath era “/Entrada de Arquivo de Mídia/nomearquivo”, “/inactiveTimeout” ou “clipListXml”. Em geral, esse é o nome do componente que, por sua vez, é o nome da propriedade. O caminho poderá ter mais ou menos níveis, como “/primarySourceFile” (pois a propriedade está na raiz do fluxo de trabalho) ou “/Processamento de Vídeo/Sobreposição Gráfica/Opacidade” (pois a Sobreposição está em um grupo).

Para verificar o caminho e o nome da propriedade, use o botão de ação imediatamente ao lado de cada propriedade. Clique neste botão de ação e selecione “Editar”. Isso mostrará o nome da propriedade atual e, imediatamente acima dele, o namespace.

![Ação/Editar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Propriedade](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

##Vários arquivos de entrada

Cada tarefa enviada para o **Fluxo de Trabalho Premium do Codificador de Mídia** exige dois Ativos:

- o primeiro é um “Ativo de Fluxo de Trabalho” que contém um arquivo de fluxo de trabalho. Você pode criar arquivos de fluxo de trabalho usando o [Designer de Fluxo de Trabalho](media-services-workflow-designer.md);
- o segundo é um “Ativo de Mídia” que contém o(s) arquivo(s) de mídia que você deseja codificar.

Ao enviar vários arquivos de mídia para o **Fluxo de Trabalho Premium do Codificador de Mídia**, as seguintes restrições se aplicarão:

- Todos os arquivos de mídia devem estar no mesmo “Ativo de Mídia”. Não há suporte para o uso de vários Ativos de Mídia.
- Você deve definir o arquivo primário neste Ativo de Mídia (de preferência, esse é o arquivo de vídeo principal que o codificador deverá processar).
- É necessário transmitir os dados de configuração que incluem o elemento **setRuntimeProperties** e/ou **transcodeSource** para o processador.
  - **setRuntimeProperties** é usado para substituir o nome do arquivo ou outra propriedade nos componentes do fluxo de trabalho
  - **transcodeSource** é usado para especificar o conteúdo XML da Lista de Clipes

Conexões no fluxo de trabalho
  - Se você usar um ou vários componentes de Entrada do Arquivo de Mídia e pretende usar **setRuntimeProperties** para especificar o nome do arquivo, não conecte o PIN do componente do arquivo primário a eles. Verifique se não há nenhuma conexão entre o objeto do arquivo primário e a(s) Entrada(s) do Arquivo de Mídia.
  - Se você preferir usar o XML da Lista de Clipes e um componente da Fonte de Mídia, poderá conectar os dois juntos.

![Nenhuma conexão do Arquivo de Fonte Primária à Entrada do Arquivo de Mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Nenhuma conexão do arquivo primário ao(s) componente(s) de Entrada do Arquivo de Mídia, se você usar setRuntimeProperties para definir o nome do arquivo*


![Conexão do XML da Lista de Clipes à Fonte da Lista de Clipes](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*É possível conectar o XML da Lista de Clipes à Fonte de Mídia e usar transcodeSource*


###Personalização do XML da Lista de Clipes
É possível especificar o XML da Lista de Clipes no fluxo de trabalho, em tempo de execução, usando **sourceTranscode** no XML da cadeia de caracteres de configuração. Isso exige que o marcador do XML da Lista de Clipes esteja conectado ao componente de Fonte de Mídia no fluxo de trabalho.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <transcodeSource>
          <clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>video-part1.mp4</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
          </clipList>
        </transcodeSource>
        <setRuntimeProperties>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Se você deseja especificar /primarySourceFile para usar essa propriedade, para nomear os arquivos de saída usando “Expressões”, é recomendável transmitir o XML da Lista de Clipes como uma propriedade, *após* a propriedade /primarySourceFile, para evitar que a Lista de Clipes seja substituída pela configuração de /primarySourceFile.

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
                <mediaFile>
                  <file>c:\temp\start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

Com outro corte preciso de quadro:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="/primarySourceFile" value="start.mxf" />
          <property propertyPath="/inactiveTimeout" value="65" />
          <property propertyPath="clipListXml" value="xxx">
          <extendedValue><![CDATA[<clipList>
            <clip>
              <videoSource>
                <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </videoSource>
              <audioSource>
               <trim>
                  <inPoint fps="25">00:00:05:24</inPoint>
                  <outPoint fps="25">00:00:10:24</outPoint>
                </trim>
                <mediaFile>
                  <file>start.mxf</file>
                </mediaFile>
              </audioSource>
            </clip>
            <primaryClipIndex>0</primaryClipIndex>
            </clipList>]]>
          </extendedValue>
          </property>
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


##Exemplo

Considere um exemplo no qual você deseja sobrepor uma imagem de logotipo no vídeo de entrada durante a codificação. Neste exemplo, o vídeo de entrada é nomeado “MyInputVideo.mp4” e o logotipo é nomeado “MyLogo.png”. As seguintes etapas devem ser realizadas:

- Criar um Ativo de Fluxo de Trabalho com o arquivo de fluxo de trabalho (exemplo abaixo)
- Criar um Ativo de Mídia, que contém dois arquivos: MyInputVideo.mp4 como o arquivo primário e MyLogo.png.
- Enviar uma Tarefa para o processador de mídia do Fluxo de Trabalho Premium do Codificador de Mídia com os ativos de entrada indicados acima e especificar a seguinte cadeia de caracteres de configuração

Configuração:

    <?xml version="1.0" encoding="utf-8"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
          <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="MyLogo.png" />
        </setRuntimeProperties>
      </transcodeRequest>


No exemplo acima, o nome do arquivo de vídeo é enviado ao componente de Entrada do Arquivo de Mídia e à propriedade primarySourceFile, e o nome do arquivo de logotipo é enviado a outra Entrada do Arquivo de Mídia conectada ao componente de sobreposição gráfica.

>[AZURE.NOTE]O nome do arquivo de vídeo é enviado à propriedade primarySourceFile. A razão disso é para que essa propriedade seja usada no fluxo de trabalho, a fim de criar o nome do arquivo de saída correto usando Expressões, por exemplo.


###Criação de fluxo de trabalho passo a passo que sobrepõe um logotipo na parte superior do vídeo     

Estas são as etapas para criar um fluxo de trabalho que usa como entrada dois arquivos: um vídeo e uma imagem. Isto fará a sobreposição da imagem na parte superior do vídeo.

Abra o **Designer de Fluxo de Trabalho** e selecione **Arquivo**-> **Novo Espaço de Trabalho** -> **Transcodificar Plano Gráfico**

O novo fluxo de trabalho mostra três elementos:

- Arquivo de Origem Principal
- XML da Lista de Clipes
- Arquivo/Ativo de Saída

![Novo fluxo de trabalho de codificação](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Novo fluxo de trabalho de codificação*


Para aceitar o arquivo de mídia de entrada, comece adicionando um componente de Entrada do Arquivo de Mídia. Para adicionar um componente ao fluxo de trabalho, procure-o na caixa de pesquisa do Repositório e arraste a entrada desejada até o painel do designer.

Em seguida, adicione o arquivo de vídeo que será usado para criar o fluxo de trabalho. Para fazer isso, clique no painel da tela de fundo do Designer de Fluxo de Trabalho e procure a propriedade do Arquivo de Origem Primário no painel de propriedades do lado direito. Clique no ícone de pasta e selecione o arquivo de vídeo apropriado.

![Fonte de Arquivo Primário](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Fonte de Arquivo Primário*


Em seguida, especifique o arquivo de vídeo no componente de Entrada do Arquivo de Mídia.

![Fonte de Entrada do Arquivo de Mídia](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Fonte de Entrada do Arquivo de Mídia*


Assim que isso for feito, o componente de Entrada do Arquivo de Mídia inspecionará o arquivo e preencherá seus pinos de saída para refletir o arquivo inspecionado por ele.

A próxima etapa é adicionar um “Atualizador de Tipo de Dados de Vídeo” para especificar o espaço de cores de Rec.709. Adicione um “Conversor de Formato de Vídeo” definido como tipo de Layout de Dados/Layout = Planar Configurável. Isso converterá a transmissão de vídeo em um formato que pode ser usado como uma fonte do componente de sobreposição.

![Atualizador de Tipo de Dados e Conversor de Formato de vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Atualizador de tipo de dados e conversor de formato de vídeo*

![Tipo de layout = Planar Configurável](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Tipo de layout = Planar Configurável*

Em seguida, adicione um componente de Sobreposição de Vídeo e conecte o marcador de vídeo (descompactado) ao marcador de vídeo (descompactado) da entrada do arquivo de mídia.

Adicione outra Entrada do Arquivo de Mídia (para carregar o arquivo de logotipo), clique neste componente e renomeie-o “Logotipo de Entrada do Arquivo de Mídia”, selecione uma imagem (arquivo .png, por exemplo) na propriedade do arquivo. Conecte o marcador da imagem Descompactada ao marcador da imagem Descompactada da sobreposição.

![Fonte de arquivo de imagem e componente de sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Fonte de arquivo de imagem e componente de sobreposição*


Se você deseja modificar a posição do logotipo no vídeo (por exemplo, talvez você queira posicioná-lo em 10% do canto superior esquerdo do vídeo), desmarque “Entrada Manual”. É possível desmarcar essa opção, pois você está usando uma Entrada do Arquivo de Mídia para fornecer o arquivo de logotipo para o componente de sobreposição.

![Posição da sobreposição](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Posição da sobreposição*


Para codificar a transmissão de vídeo para H.264, adicione os componentes do Codificador de Vídeo AVC e do codificador AAC à superfície do designer. Conecte os marcadores. Configure o codificador AAC e selecione: Conversão de Formato de Áudio/Predefinição: 2.0 (L, R)

![Codificadores de Áudio e Vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Codificadores de Áudio e Vídeo*


Agora, adicione os componentes **Multiplexador ISO Mpeg-4** e **Saída de Arquivo** e conecte os marcadores, conforme mostrado.

![Multiplexador MP4 e saída de arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*Multiplexador MP4 e saída de arquivo*


É necessário definir o nome do arquivo de saída. Clique no componente “Saída de Arquivo” e edite a expressão do arquivo:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Nome de saída do arquivo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Nome de saída do arquivo*


Você pode executar o fluxo de trabalho localmente para verificar se ele é executado corretamente.

Se ele estiver correto, você poderá executá-lo nos Serviços de Mídia do Azure.

Primeiro, prepare um ativo nos Serviços de Mídia do Azure com dois arquivos: o arquivo de vídeo e o logotipo. Faça isso usando o .NET ou a API REST. Você também pode fazer isso com o Portal do Azure ou com o [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) (Gerenciador dos Serviços de Mídia do Azure).

Este tutorial mostra como gerenciar ativos com o AMSE. Há duas maneiras de adicionar arquivos a um ativo.

1. Crie uma pasta local, copie os dois arquivos nela e arraste e solte a pasta na guia Ativo
1. Carregue o arquivo de vídeo como um ativo, exiba as informações do ativo, vá para a guia Arquivos e carregue um arquivo adicional (logotipo).

>[AZURE.NOTE]Lembre-se de definir um arquivo primário no ativo (o arquivo de vídeo principal).

![Arquivos de ativo no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Arquivos de ativo no AMSE*


Selecione o ativo e escolha codificá-lo com o Codificador Premium. Carregue o fluxo de trabalho e selecione-o.

Clique no botão para transmitir dados para o processador e adicione o seguinte XML para definir as propriedades de tempo de execução:

![Codificador Premium no AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Codificador Premium no AMSE*


Em seguida, cole os dados de XML a seguir. Você precisa especificar o nome do arquivo de vídeo para a Entrada do Arquivo de Mídia e para primarySourceFile. Especifique o nome do arquivo para o logotipo também.

    <?xml version="1.0" encoding="utf-16"?>
      <transcodeRequest>
        <setRuntimeProperties>
          <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
          <property propertyPath="Media File Input Logo/filename" value="logo.png" />
        </setRuntimeProperties>
      </transcodeRequest>

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*


Se você usar o SDK do .NET para criar e executar a tarefa, esses dados de XML deverão ser transmitidos como a cadeia de caracteres de configuração.

    public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
  
Após a conclusão do trabalho, o arquivo MP4 no ativo de saída exibirá a sobreposição!

![Sobreposição no vídeo](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Sobreposição no vídeo*


Baixe o fluxo de trabalho de exemplo [aqui](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).


##Consulte também 

[Apresentando a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)

[Como usar a codificação Premium nos Serviços de Mídia do Azure](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)

[Codificando conteúdo sob demanda com os Serviços de Mídia do Azure](media-services-encode-asset.md#media_encoder_premium_workflow)

[Codecs e formatos de fluxo de trabalho do Media Encoder Premium](media-services-premium-workflow-encoder-formats.md)

[Exemplos de arquivos de fluxo de trabalho](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)

[Ferramenta do Explorador dos Serviços de Mídia do Azure](http://aka.ms/amse)

##Roteiros de aprendizagem dos Serviços de Mídia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0713_2016-->