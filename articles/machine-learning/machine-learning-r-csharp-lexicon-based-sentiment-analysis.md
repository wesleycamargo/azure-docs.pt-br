---
title: Análise de sentimento baseada em léxico | Microsoft Docs
description: Análise de sentimento baseada em léxico
services: machine-learning
documentationcenter: ''
author: pengxia
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2016
ms.author: pengxia

---
# Análise de sentimento baseada em léxico
Como medir as opiniões e atitudes dos usuários sobre marcas ou tópicos em redes sociais online, como postagens no Facebook, tweets, análises etc.? A análise de sentimento fornece um método para analisar essas questões.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Em geral, há dois métodos para análise de sentimento. Um é usando um algoritmo de aprendizado supervisionado e o outro pode ser tratado como aprendizado sem supervisão. Um algoritmo de aprendizado supervisionado geralmente cria um modelo de classificação em um grande corpus anotado. A sua precisão se baseia principalmente na qualidade da anotação e, geralmente, o processo de treinamento levará um longo tempo. Além disso, quando aplicamos o algoritmo a outro domínio, o resultado geralmente não é válido. Comparado ao aprendizado supervisionado, o aprendizado baseado em léxico sem supervisão usa um dicionário de sentimento, que não requer o armazenamento de um grande corpus de dados e treinamento, tornando todo o processo muito mais rápido.

Nosso [serviço](https://datamarket.azure.com/dataset/aml_labs/lexicon_based_sentiment_analysis) baseia-se no dicionário de subjetividade MPQA (http://mpqa.cs.pitt.edu/lexicons/subj_lexicon/), que é um dos dicionários de subjetividade mais comumente usados. Há 5097 palavras negativas e 2533 palavras positivas em MPQA. E todas essas palavras são anotadas como tendo uma polaridade forte ou fraca. Todo corpus inteiro está sob a licença pública geral GNU. O serviço Web pode ser aplicado a qualquer frase curta, como tweets e postagens no Facebook.

> Este serviço Web poderia ser consumido por usuários – potencialmente por meio de um aplicativo móvel, de um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – sem qualquer infraestrutura configurada pelo autor do serviço Web.
> 
> 

## Consumo do serviço Web
Os dados de entrada podem ser qualquer texto, mas o serviço Web funciona melhor com frases curtas. A saída é um valor numérico entre -1 e 1. Qualquer valor abaixo de 0 indica que ao sentimento do texto é negativo; ele é positivo se estiver acima de 0. O valor absoluto do resultado indica a intensidade do sentimento associado.

> Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.
> 
> 

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo estão [aqui](http://microsoftazuremachinelearning.azurewebsites.net/)).

### Iniciando o código C# para consumo de serviço Web:
    public class ScoreResult
    {
            [DataMember]
            public double result
            {
                get;
                set;
            }
    }

    void main()
    {
            using (var wb = new WebClient())
            {
                var acitionUri = new Uri("PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score");
                DataServiceContext ctx = new DataServiceContext(acitionUri);
                var cred = new NetworkCredential("PutEmailAddressHere", "ChangeToAPIKey");
                var cache = new CredentialCache();

                cache.Add(acitionUri, "Basic", cred);
                ctx.Credentials = cache;
                var query = ctx.Execute<ScoreResult>(acitionUri, "POST", true, new BodyOperationParameter("Text", TextBox1.Text));
                ScoreResult scoreResult = query.ElementAt(0);
                double result = scoreResult.result;
            }
    }



A entrada é "Hoje é um bom dia". A saída será "1", que indica um sentimento positivo associado à frase de entrada.

## Criação de serviço Web
> Este serviço Web foi criado usando o Aprendizado de Máquina do Azure. Para obter uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de testes e [publicação de serviços Web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.
> 
> 

De dentro do Aprendizado de Máquina do Azure, um novo teste em branco foi criado. A figura a seguir mostra o fluxo de teste da análise de sentimento baseada em léxico. O arquivo “sent\_dict.csv” é o léxico de subjetividade MPQA, e é definido como uma entrada de [Executar script R][execute-r-script]. Outra entrada é uma análise de amostra do conjunto de dados de análise Amazon para teste, em que realizamos a seleção, a modificação do nome de coluna e as operações de divisão. Usamos um pacote de hash para armazenar o léxico de subjetividade na memória e acelerar o processo de cálculo de pontuação. O texto inteiro será indexado pelo pacote "tm" e comparado com a palavra no dicionário sentimento. Por fim, uma pontuação será calculada adicionando o peso de cada palavra subjetiva no texto.

### Fluxo de teste:
![fluxo de teste][2]

#### Módulo 1:
    # Map 1-based optional input ports to variables
    sent_dict_data<- maml.mapInputPort(1) # class: data.frame
    dataset2 <- maml.mapInputPort(2) # class: data.frame

# Instalar pacote de hash install.packages("src/hash\_2.2.6.zip", lib = ".", repos = NULL, verbose = TRUE) success <- library("hash", lib.loc = ".", logical.return = TRUE, verbose = TRUE) library(tm) library(stringr)
    #create sentiment dictionary
    negation_word <- c("not","nor", "no")
    result <- c()
    sent_dict <- hash()
    sent_dict <- hash(sent_dict_data$word, sent_dict_data$polarity)

    #  Compute sentiment score for each document
    for (m in 1:nrow(dataset2)){
    polarity_ratio <- 0
    polarity_total <- 0
    not <- 0
    sentence <- tolower(dataset2[m,1])
    if (nchar(sentence) > 0){
        token_array <- scan_tokenizer(sentence)
        for (j in 1:length(token_array)){
            word = str_replace_all(token_array[j], "[^[:alnum:]]", "")
            for (k in 1:length(negation_word)){
              if (word == negation_word[k]){
                not <- (not+1) %% 2

              }
            }
            if (word != ""){
                if (!is.null(sent_dict[[word]])){
                  polarity_ratio <- polarity_ratio + (-2*not+1)*sent_dict[[word]]
                  polarity_total <- polarity_total + abs(sent_dict[[word]])
                }
            }

        }
    }
    if (polarity_total > 0){
        result <- c(result, polarity_ratio/polarity_total)
    }else{
        result<- c(result,0)
    }
    }

    # Sample operation
    data.set <- data.frame(result)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("data.set")



## Limitações
Da perspectiva de um algoritmo, a análise de sentimento baseada em léxico é uma ferramenta de análise geral de sentimento, que pode não funcionar melhor que o método de classificação para campos específicos. O problema de negação não é bem processado. Nós codificamos várias palavras de negação no nosso programa, mas uma maneira melhor é usar um dicionário de negação e criar algumas regras. O serviço Web tem um melhor desempenho em frases curtas e simples, como tweets e postagens do Facebook, do que em frases longas e complexas, como a análise Amazon.

## Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_1.png
[2]: ./media/machine-learning-r-csharp-lexicon-based-sentiment-analysis/sentiment_analysis_2.png


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/



<!---HONumber=AcomDC_0921_2016-->