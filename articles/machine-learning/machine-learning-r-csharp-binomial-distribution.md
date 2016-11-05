---
title: Pacote de distribuição binomial | Microsoft Docs
description: Pacote de distribuição binomial
services: machine-learning
documentationcenter: ''
author: ireiter
manager: jhubbard
editor: cgronlun

ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2016
ms.author: ireiter

---
# Pacote de distribuição binomial
O Pacote de Distribuição Binomial é um conjunto de serviços Web de exemplo ([Gerador Binomial](https://datamarket.azure.com/dataset/aml_labs/bdg5), [Calculadora de probabilidade](https://datamarket.azure.com/dataset/aml_labs/bdp4), [Calculadora de Quantil](https://datamarket.azure.com/dataset/aml_labs/bdq5)) que ajuda a gerar e lidar com distribuições binomiais. Os serviços permitem gerar uma sequência de distribuição binomial de qualquer tamanho, calcular quantidades a partir de uma determinada probabilidade e calcular probabilidade a partir de um determinado quantil. Cada um dos serviços emite saídas diferentes com base no serviço selecionado (consulte a descrição abaixo). O Pacote de Distribuição Binomial baseia-se nas funções R qbinom, rbinom e pbinom, que são incluídas no pacote de estatísticas de R.

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> Esses serviços Web podem ser consumidos por usuários – potencialmente de forma direta no marketplace, por meio de um aplicativo móvel, um site ou até mesmo em um computador local, por exemplo. Mas a finalidade do serviço Web é também servir como um exemplo de como o Aprendizado de Máquina do Azure pode ser usado para criar serviços Web sobre o código R. Com apenas algumas linhas de código R e cliques de botão dentro do Estúdio de Aprendizado de Máquina do Azure, um experimento pode ser criado com código R e publicado como um serviço Web. O serviço Web pode ser publicado no Azure Marketplace e consumido por dispositivos e usuários em todo o mundo – não é necessária nenhuma configuração de infraestrutura feita pelo autor do serviço Web.
> 
> 

## Consumo do serviço Web
O pacote de Distribuição Binomial inclui os três serviços a seguir.

### Calculadora de quantil de distribuição binomial
Esse serviço aceita quatro argumentos de uma distribuição normal e calcula o quantil associado. Os argumentos de entrada são:

* p - uma única probabilidade agregada de várias tentativas.
* tamanho – o número de tentativas.
* prob – a probabilidade de sucesso em uma tentativa.
* Lado – L para o lado inferior da distribuição, U para o lado superior da distribuição.

A saída do serviço é o quantil calculado associado à probabilidade determinada.

### Calculadora de probabilidade de distribuição binomial
Esse serviço aceita quatro argumentos de uma distribuição binominal e calcula a probabilidade associada. Os argumentos de entrada são:

* q - um único quantil de um evento com distribuição binomial.
* tamanho – o número de tentativas.
* prob – a probabilidade de sucesso em uma tentativa.
* lado – L para o lado inferior da distribuição, U para o lado superior da distribuição ou E, que é igual a um único número de sucessos.

A saída do serviço é a probabilidade calculada associada ao quantil determinado.

### Gerador de distribuição binomial
Este serviço aceita três argumentos de uma distribuição binomial e gera uma sequência aleatória de números distribuídos binomialmente. Os argumentos a seguir devem ser fornecidos a ele na solicitação:

* n - número de observações.
* tamanho – número de tentativas.
* prob – probabilidade de sucesso.

A saída do serviço é uma sequência de tamanho n com uma distribuição binomial com base no tamanho e em argumentos prob.

> Esse serviço, conforme hospedado no Azure Marketplace é um serviço OData; ele pode ser chamado por meio de métodos POST ou GET.
> 
> 

Há várias maneiras de consumir o serviço de forma automática (os aplicativos de exemplo são: [Gerador](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionGenerator.aspx), [Calculadora de probabilidade](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionProbabilityCalculator.aspx), [Calculadora de quantil](http://microsoftazuremachinelearning.azurewebsites.net/BinomialDistributionQuantileCalculator)).

### Iniciando o código C# para consumo de serviço Web:
### Calculadora de quantil de distribuição binomial
    public class Input
    {
            public string p;
            public string size;
            public string prob;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void main()
    {
            var input = new Input() { p = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }

### Calculadora de probabilidade de distribuição binomial
    public class Input
    {
            public string q;
            public string size;
            public string prob;
            public string side;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { q = TextBox1.Text, size = TextBox2.Text, prob = TextBox3.Text, side = TextBox4.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = " PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }


### Gerador de distribuição binomial
    public class Input
    {
            public string n;
            public string size;
            public string p;
    }

    public AuthenticationHeaderValue CreateBasicHeader(string username, string password)
    {
            byte[] byteArray = System.Text.Encoding.UTF8.GetBytes(username + ":" + password);
            return new AuthenticationHeaderValue("Basic", Convert.ToBase64String(byteArray));
    }

    void Main()
    {
            var input = new Input() { n = TextBox1.Text, size = TextBox2.Text, p = TextBox3.Text };
            var json = JsonConvert.SerializeObject(input);
            var acitionUri = "PutAPIURLHere,e.g.https://api.datamarket.azure.com/..../v1/Score";
            var httpClient = new HttpClient();

            httpClient.DefaultRequestHeaders.Authorization = CreateBasicHeader("PutEmailAddressHere", "ChangeToAPIKey");
            httpClient.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

            var response = httpClient.PostAsync(acitionUri, new StringContent(json));
            var result = response.Result.Content;
            var scoreResult = result.ReadAsStringAsync().Result;
    }





## Criação de serviço Web
> Este serviço Web foi criado usando o Aprendizado de Máquina do Azure. Para obter uma avaliação gratuita, bem como vídeos introdutórios sobre a criação de testes e [publicação de serviços Web](machine-learning-publish-a-machine-learning-web-service.md), consulte [azure.com/ml](http://azure.com/ml). Abaixo está uma captura de tela do teste que criou o serviço Web e o exemplo de código para cada um dos módulos dentro do teste.
> 
> 

### Calculadora de quantil de distribuição binomial
![Criar espaço de trabalho][4]

#### Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(p=0.1,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port
#### Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    if (param$p < 0 ) {
    print('Bad input: p must be between 0 and 1')
    param$p = 0
    } else if (param$p > 1) {
    print('Bad input: p must be between 0 and 1')
    param$p = 1
    }

    if (param$prob < 0 ) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 0
    } else if (param$prob > 1) {
    print('Bad input: prob must be between 0 and 1')
    param$prob = 1
    }

    quantile = qbinom(param$p,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    quantile

    if (param$side == 'U'){
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = F)
    band=subset(df,x>quantile)
    } else if (param$side =='L') {
    quantile = qbinom(param$p,size=param$size,prob=param$prob,lower.tail = T)
    band=subset(df,x<=quantile)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(quantile)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");


### Calculadora de probabilidade de distribuição binomial
![Criar espaço de trabalho][5]

#### Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(q=5,size=10,prob=.5,side='L');
    maml.mapOutputPort("data.set"); #send data to output port


#### Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    prob = pbinom(param$q,size=param$size,prob=param$prob)
    prob.eq = dbinom(param$q,size=param$size,prob=param$prob)
    df = data.frame(x=1:param$size, prob=dbinom(1:param$size, param$size, prob=param$prob))
    prob

    if (param$side == 'U'){
    prob = 1 - prob
    band=subset(df,x>param$q)
    } else if (param$side =='E') {
    prob = prob.eq
    band=subset(df,x==param$q)
    } else if (param$side =='L') {
    prob = prob
    band=subset(df,x<=param$q)
    } else {
    print("Invalid side choice")
    }

    output = as.data.frame(prob)

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

### Gerador de distribuição binomial
![Criar espaço de trabalho][6]

#### Módulo 1:
    #data schema with example data (replaced with data from web service)
    data.set=data.frame(n=50,size=10,p=.5);
    maml.mapOutputPort("data.set"); #send data to output port

#### Módulo 2:
    dataset1 <- maml.mapInputPort(1) # class: data.frame
    param = dataset1
    dist = rbinom(param$n,param$size,param$p)

    output = as.data.frame(t(dist))

    # Select data.frame to be sent to the output Dataset port
    maml.mapOutputPort("output");

## Limitações
Estes são exemplos muito simples de Distribuição Binomial. Como se pode ver no exemplo de código acima, pouca captura de erro é implantada.

## Perguntas frequentes
Para obter as perguntas frequentes sobre o consumo do serviço Web ou a publicação no Azure Marketplace, consulte [aqui](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_1.png

[2]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_2.png

[3]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_3.png

[4]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_4.png

[5]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_5.png

[6]: ./media/machine-learning-r-csharp-binomial-distribution/binomial_6.png


<!---HONumber=AcomDC_0914_2016-->