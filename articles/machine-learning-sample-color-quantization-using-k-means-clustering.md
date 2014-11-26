<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Machine Learning Sample: Color quantization using K-Means clustering | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye" />

# Amostra de Aprendizado de Máquina do Azure: Quantização de cor usando clustering K-Means

*Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção <b>TESTES</b> sob a guia <b>AMOSTRAS</b>. O nome do teste é:*

    Sample Experiment - Color Based Image Compression using K-Means Clustering - Development

## Descrição do problema

[Quantização de cor][Quantização de cor] é o processo de reduzir o número de cores diferentes em uma imagem, compactando-a. Normalmente, o objetivo é preservar a aparência da cor da imagem o máximo possível, enquanto reduz o número de cores, seja por limitações de memória ou compactação.

## Dados

Nesse teste de amostra, supondo uma imagem em RGB de 24 bits com 256 x 256 x 256 cores possíveis. E, com certeza, podemos compilar histogramas padrão com base nesses valores de intensidade. Mas, outra abordagem é quantizar explicitamente a imagem e *reduzir* o número de cores para, digamos, 16 ou 64. Isso cria um espaço substancialmente menor e (idealmente) com menos ruído e variação. Para isso, passamos os dados de pixel (cada pixel como uma linha de conjunto de dados) para nosso Modelo de clustering K-Means.

## Modelo

O modelo é criado como mostrado na imagem abaixo:

![Modelo][Modelo]

Executamos o clustering K-Means com K=10 a 500 em 5 ramificações diferentes. Primeiro, calculamos os clusters e depois agregamos o clustering à média de suas cores em pixels (usando um R Script).
Finalmente, associamos cada pixel à cor de cluster agregada e enviamos a nova imagem no formato CSV. Enquanto isso, calculamos a Diferença média quadrática das novas cores em pixel com a imagem original e as exibimos em uma plotagem R (usando Executar script R).

## Resultados

Testamos o resultado em um número diferente de clusters (cores) como mostrado no modelo do teste abaixo. Na medida em que essa diferença fica visível mais se cria clustering de imagens em melhor qualidade com menos compactação:

<table>
<tr>
<th>
Original

</th>
<td>
![Original][Original]

</td>
</tr>
<tr>
<th>
K=10

</th>
<td>
![K=10][K=10]

</td>
</tr>
<tr>
<th>
K=20

</th>
<td>
![K=20][K=20]

</td>
</tr>
<tr>
<th>
K=50

</th>
<td>
![K=50][K=50]

</td>
</tr>
<tr>
<th>
K=100

</th>
<td>
![K=100][K=100]

</td>
</tr>
<tr>
<th>
K=500

</th>
<td>
![K=500][K=500]

</td>
</tr>
</table>
Também medimos a precisão usando a Diferença média quadrática raiz das cores da imagem original, que pode ser vista na segunda porta de saída do Módulo “Executar script R":

![Saída do módulo Executar script R][Saída do módulo Executar script R]

Na medida em que ela fica visível, mais clusters de cor e mais cores correspondem às imagens originais (melhor qualidade).

## Código para converter imagens para CSV e reverter

Para alimentar as imagens no Estúdio AM, gravamos um código conversor simples que pode converter arquivos de imagem em um formato csv que o Estúdio AM pode usar, e também um que os converte de volta em imagem. Fique à vontade para usar o código a seguir. No futuro, planejamos adicionar um módulo para leitura de imagens também.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Drawing;
    using System.Drawing.Imaging;
    using System.IO;
     
    namespace Text2Image
    {
        class Program
        {
            static void img2csv(string img_path)
            {
                FileInfo img_info = new FileInfo(img_path);
                string destination_file_directory = img_info.DirectoryName + "\\";
                string destination_file_name = img_info.Name.Remove(img_info.Name.LastIndexOf('.'), 4);
                string destination_file_path = destination_file_directory + destination_file_name + ".csv";
     
                // Read the image
                Bitmap img = new Bitmap(img_path);
     
                // Create the CSV File and write the header values
                System.IO.StreamWriter file = new System.IO.StreamWriter(destination_file_path);
                file.WriteLine("X,Y,R,G,B");
     
                // Write the Pixel values
                for (int x = 0; x < img.Width; x++)
                    for (int y = 0; y < img.Height; y++)
                    {
                        string line = x + "," + y + "," + img.GetPixel(x, y).R + "," + img.GetPixel(x, y).G + "," + img.GetPixel(x, y).B ;
                        file.WriteLine(line);
                    }
     
                file.Close();
            }
     
            static void csv2img(string csv_path)
            {
                FileInfo csv_info = new FileInfo(csv_path);
                string destination_file_directory = csv_info.DirectoryName + "\\";
                string destination_file_name = csv_info.Name.Remove(csv_info.Name.LastIndexOf('.'), 4);
                string destination_file_path = destination_file_directory + destination_file_name + ".png";
                
                // Read all the lines in the CSV file
                string[] lines = System.IO.File.ReadAllLines(csv_path);
     
                // set a new bitmap image with the provided width and height in the header
                string[] wh = lines.Last().Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
                int img_width = Convert.ToInt32(wh[0])+1;
                int img_height = Convert.ToInt32(wh[1])+1;
     
                Bitmap bmp_img = new Bitmap(img_width, img_height);
     
                for (int i = 1; i < lines.Length ;i++ )
                {
                    string[] values = lines[i].Split(new Char[] { ' ', ',', '.', ':', '\t', '{', '}' });
                    if (values.Length < 3)
                        continue;
     
                    int x = Convert.ToInt16(values[0]);
                    int y = Convert.ToInt32(values[1]);
                    int r = Convert.ToInt32(values[2]);
                    int g = Convert.ToInt32(values[3]);
                    int b = Convert.ToInt32(values[4]);
     
                    bmp_img.SetPixel(x, y, Color.FromArgb(r, g, b));
                }
     
                bmp_img.Save(destination_file_path);
            }
     
            static void Main(string[] args)
            {
                string source_path = args[1];
                FileInfo source_info = new FileInfo(source_path);
     
                if (source_info.Extension == ".csv")
                    csv2img(source_path);
                else
                    img2csv(source_path);
            }
        }
    }

  [Quantização de cor]: http://en.wikipedia.org/wiki/Color_quantization "Quantização de cor"
  [Modelo]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
  [Original]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
  [K=10]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
  [K=20]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
  [K=50]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
  [K=100]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
  [K=500]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
  [Saída do módulo Executar script R]: ./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png
