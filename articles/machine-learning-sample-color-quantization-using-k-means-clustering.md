<properties title="Azure Machine Learning Sample: Color quantization using K-Means clustering" pageTitle="Amostra de Aprendizado de Máquina: Quantização de cor usando clustering de K-Means | Azure" description="A sample Azure Machine Learning experiment that evaluates using different K-Means clustering values for quantizing a color image." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Amostra de Aprendizado de Máquina do Azure: Quantização de cor usando clustering K-Means

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjunto de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de experimento]: #sample-experiment
[Amostra de conjunto de dados]: #sample-dataset


##Descrição do problema

[Quantização de cor](http://en.wikipedia.org/wiki/Color_quantization "Color quantization") é o processo de reduzir o número de cores distintas em uma imagem, assim compactando-a. Normalmente, o objetivo é preservar a aparência da cor da imagem o máximo possível, enquanto reduz o número de cores, seja por limitações de memória ou compactação. 

##Dados

Nesse teste de amostra, supondo uma imagem em RGB de 24 bits com 256 x 256 x 256 cores possíveis. E, com certeza, podemos compilar histogramas padrão com base nesses valores de intensidade. Mas, outra abordagem é quantizar explicitamente a imagem e *reduzir* o número de cores para, digamos, 16 ou 64. Isso cria um espaço substancialmente menor e (idealmente) com menos ruído e variação. Para isso, passamos os dados de pixel (cada pixel como uma linha de conjunto de dados) para nosso Modelo de clustering K-Means. 

##Modelo

O modelo é criado como mostrado na imagem abaixo:

![Model][image1]

Executamos o clustering K-Means com K=10 a 500 em 5 ramificações diferentes. Primeiro, calculamos os clusters e então agregamos o clustering para a média das suas cores de pixels (usando um Script R). 
Por fim, associados cada pixel com a cor de cluster agregada e enviamos a nova imagem no formato CSV. Enquanto isso, calculamos a Diferença média quadrática das novas cores em pixel com a imagem original e as exibimos em uma plotagem R (usando Executar script R). 

##Resultados

Testamos o resultado em um número diferente de clusters (cores) como mostrado no modelo do teste abaixo. Como você pode ver abaixo, mais clustering cria imagens de qualidade superiores com menos compactação:

||
------------ | ---------
**Original** | ![Original][image2a]
**K=10**     | ![K=10][image2b]
**K=20**     | ![K=20][image2c]
**K=50**     | ![K=50][image2d]
**K=100**    | ![K=100][image2e]
**K=500**    | ![K=500][image2f]


Também medimos a precisão usando a Diferença média quadrática raiz das cores da imagem original, que pode ser vista na segunda porta de saída do Módulo "Executar script R":

![Output of Execute R Script module][image3]

Na medida em que ela fica visível, mais clusters de cor e mais cores correspondem às imagens originais (melhor qualidade). 

##Código para converter imagens para CSV e reverter

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


## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de teste - Compactação de imagem baseada em cor usando Clustering de K-Means - Desenvolvimento**


## Conjunto de dados de amostra

O seguinte conjunto de dados de exemplo usado por essa experiência está disponível no Estúdio AM na paleta de módulo em **Conjuntos de dados salvos**.

###Imagem RGB de Bil Gates
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]



[image1]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image1.png
[image2a]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2a.jpg
[image2b]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2b.png
[image2c]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2c.png
[image2d]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2d.png
[image2e]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2e.png
[image2f]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image2f.png
[image3]:./media/machine-learning-sample-color-quantization-using-k-means-clustering/image3.png

