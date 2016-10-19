<properties
	pageTitle="Guia de início rápido: API de Recomendações de Aprendizado de Máquina | Microsoft Azure"
	description="Recomendações do aprendizado de máquina do Azure - guia de início rápido"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/22/2016"
	ms.author="luisca"/>

# Guia de início rápido da API de Recomendações dos Serviços Cognitivos

Este documento descreve como integrar seu serviço ou aplicativo para usar a [API de Recomendações](http://go.microsoft.com/fwlink/?LinkId=759710). Mais detalhes sobre a API de Recomendações e outros Serviços Cognitivos estão disponíveis [aqui](http://go.microsoft.com/fwlink/?LinkId=759709). Durante a leitura deste guia, você também pode considerar a [Referência da API de Recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) útil.


<a name="Overview"></a>
## Visão geral

Este documento é um guia passo a passo. Nosso objetivo é delinear as etapas necessárias para treinar um modelo e indicar os recursos que lhe permitirão consumir o modelo em seu ambiente de produção.

Esse exercício levará cerca de 30 minutos.

Para usar [API de Recomendações](http://go.microsoft.com/fwlink/?LinkId=759710), você precisa seguir estas etapas:

1. Criar um modelo – um modelo é um contêiner de seus dados de uso, dados do catálogo e o modelo de recomendação.
1. Importar dados do catálogo - um catálogo contém informações de metadados sobre os itens.
1. Importar dados de uso: os dados de uso podem ser carregados de uma entre duas maneiras (ou ambas):
  -  Carregando um arquivo que contém os dados de uso.
  -  Enviar eventos de aquisição de dados. Normalmente, você carrega um arquivo de uso para poder criar um modelo de recomendação inicial (inicialização) e usá-lo até que o sistema reúna dados suficientes usando o formato de aquisição de dados.
1. Criar um modelo de recomendação – Esta é uma operação assíncrona, em que o sistema de recomendação emprega todos os dados de uso e cria um modelo de recomendação. Essa operação pode levar vários minutos ou várias horas, dependendo do tamanho dos dados e dos parâmetros de configuração da compilação. Ao disparar a compilação, você receberá uma ID de compilação. Use-o para verificar quando o processo de compilação foi concluído antes de começar a consumir recomendações.
1. Consumir recomendações - Obtenha recomendações para um item específico ou uma lista de itens.

<a name="GetStarted"></a>
### Vamos começar!

Você começará com a criação de um modelo de Recomendações. Em seguida, vamos orientá-lo sobre como usar os resultados gerados pelo modelo para fornecer recomendações em um site de comércio eletrônico.

<a name="Ex1Task1"></a>
#### Tarefa 1 - Inscrição na API de Recomendações ####

Nesta tarefa, você vai se inscrever no serviço da API de Recomendações e criará um modelo de recomendações.

1. Vá atá o **Portal do Azure** no endereço [http://portal.azure.com/](http://portal.azure.com/) e entre com sua conta do Azure.

1.  Clique em **+ Novo**.

1. Selecione a opção **Inteligência**.

1. Selecione o produto **APIs dos Serviços Cognitivos**. Este produto permitirá que você inicie uma assinatura de qualquer uma das APIs dos serviços cognitivos (Detecção Facial, Análise de Texto, Pesquisa Visual Computacional etc.). Hoje, nos concentraremos na API de Recomendações.

1. Na página de aterrissagem da API dos Serviços Cognitivos, informe o **Nome da conta** da sua assinatura das Recomendações. (Por exemplo, "MinhasRecomendações"). O nome não pode conter espaços.

1. Em **Tipo de API**, selecione **Recomendações**.

1. Em **Tipo de preço**, você pode selecionar um plano. Você pode escolher a camada **gratuita** para 10.000 transações/mês. Trata-se de um plano gratuito e, sendo assim, de uma boa maneira de começar a experimentar o sistema. Quando passar para a produção, recomendamos que você leve em consideração seu volume de solicitações e altere o tipo de plano adequadamente. (Observação: você pode ter apenas uma assinatura de camada gratuita por vez)

1. Selecione um **Grupo de Recursos** ou crie um novo se ainda não tiver um.

1. Você pode alterar outros elementos na caixa de diálogo Criar. Devemos destacar que, atualmente, o provedor de recursos tem suporte apenas de data centers nos Estados Unidos.

1. Quando terminar de selecionar sua opções, clique em **Criar**.

1. Aguarde alguns minutos para que o recurso seja implantado. Após a implantação, você pode ir para a seção **Chaves** na folha **Configurações**, na qual serão fornecidas uma chave primária e secundária para usar a API. Copie a chave primária, pois você precisará dela durante a criação de seu primeiro modelo.

<a name="Ex1Task2"></a>
#### Tarefa 2 - Trouxe seus dados? ####

A API de Recomendações aprenderá com seu catálogo e suas transações para fornecer boas recomendações de produto. Isso significa que você precisa alimentá-la com dados sólidos sobre seus produtos (que chamamos de arquivo de **catálogo**) e um conjunto de transações grande o suficiente para que ela encontre padrões interessantes de consumo (que chamamos de **uso**).

1. Normalmente, você consultaria seu banco de dados de transações para obter essas informações. Caso não as tenha à mão, nós fornecemos alguns dados de exemplo para você baseados nos dados de transação da Microsoft Store.

 Você pode baixá-los [aqui](http://aka.ms/RecoSampleData). Copie e descompacte MsStoreData.Zip em uma pasta no computador local.

 > **Observação:** o código de exemplo que você vai baixar e executar na Tarefa 3 já contém dados de exemplo incorporados nele, de modo que esta tarefa é opcional. Dito isso, esta Tarefa permitirá que você baixe conjuntos de dados mais realistas e compreenda melhor as entradas da API de Recomendações.

1.	Agora, vamos dar uma olhada no arquivo de catálogo. Navegue até o local no qual você copiou os dados. Abra o arquivo de catálogo no **bloco de notas**.

 Você observará que o arquivo de catálogo é bem simples. Ele tem o seguinte formato `<itemid>,<item name>,<product category>`

 >  AAA-04294,OfficeLangPack 2013 32/64 E34 Online DwnLd,Office <br> AAA-04303,OfficeLangPack 2013 32/64 ET Online DwnLd,Office <br> C9F-00168,KRUSELL Kiruna Flip Cover for Nokia Lumia 635 – Camel,Accessories

 Devemos destacar que um arquivo de catálogo pode ser muito mais sofisticado. Por exemplo, você pode adicionar metadados sobre os produtos (nós os chamamos de *recursos de item*). Consulte a seção sobre o [formato de catálogo](http://go.microsoft.com/fwlink/?LinkID=760716) na Referência da API para obter mais detalhes sobre esse formato.

1. Vamos fazer o mesmo com os dados de uso. Você observará que a data de uso tem o formato `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16,288186200,2015/08/04T11:02:52,Purchase 0003BFFDD4C2148C,297833400,2015/08/04T11:02:50,Purchase 0003BFFDD4C2118D,297833300,2015/08/04T11:02:40,Purchase 00030000D16C4237,297833300,2015/08/04T11:02:37,Purchase 0003BFFDD4C20B63,297833400,2015/08/04T11:02:12,Purchase 00037FFEC8567FB8,297833400,2015/08/04T11:02:04,Purchase

Observe que os três primeiros elementos são obrigatórios. O tipo de evento é opcional. Confira o [formato de uso](http://go.microsoft.com/fwlink/?LinkID=760712) para obter mais informações sobre este tópico.

 > **Qual é a quantidade de dados de que você precisa?**
 <p>
 Isso depende muito dos dados de uso. O sistema aprende quando os usuários compram itens diferentes. Para algumas versões, como o FBT, é importante saber quais itens são comprados nas mesmas transações. (Nós os chamamos de *co-ocorrências*). Uma boa regra é que a maioria dos itens tenha 20 transações ou mais, portanto, se você tivesse 10.000 itens no catálogo, recomendaríamos que tivesse pelo menos 20 vezes esse número de transações, ou cerca de 200.000 transações. Mais uma vez, essa é uma apenas uma boa regra. Você precisará fazer experiências com seus dados.
</p>

<a name="Ex1Task3"></a>
#### Tarefa 3 - Criar um modelo de recomendações ####

Agora que você tem uma conta e dados, vamos criar seu primeiro modelo.

Nesta tarefa, você usará o aplicativo de exemplo para criar seu primeiro modelo.

1. Primeiro, você deve ter conhecimento da [Referência da API de Recomendações](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Baixe o [Aplicativo de Exemplo](http://go.microsoft.com/fwlink/?LinkID=759344) para uma pasta local.

1. Abra no Visual Studio a solução **RecommendationsSample.sln** localizada na pasta **C#**.

1. Abra o arquivo **SampleApp.cs**. Observe que as seguintes etapas fazem parte do arquivo:
 + Criar um modelo
 + Adicionar um arquivo de catálogo
 + Adicionar um arquivo de uso
 + Disparar uma criação para o Modelo
 + Obter uma recomendação com base em um par de itens
<p></p>

1. Substitua o valor para o campo **AccountKey** com a chave da Tarefa 1.

1. Percorra a solução e você verá como um modelo é criado.

1. Tente substituir os arquivos de catálogo e uso baixados para criar um novo modelo para a Microsoft Store ou para obter recomendações de livros. Você precisará alterar também o nome do modelo e os itens para os quais solicita recomendações.

1. Quando o modelo é criado, tome nota da **ID do modelo**, pois você precisará dela para solicitar recomendações em seu ambiente de produção.

>  Saiba mais sobre tipos de build e como avaliar a qualidade dos builds [aqui](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### Colocando seu modelo em produção! ###

Agora que você entende como criar um modelo e consumir recomendações, a próxima etapa é colocá-lo em produção em seu site ou aplicativo móvel ou integrá-lo a seu sistema de CRM ou ERP. É claro que cada uma dessas implementações seria diferente. Uma vez que a API de Recomendações é solicitada como um serviço da Web, você poderá chamá-la facilmente de qualquer um desses ambientes diferentes.

**Importante:** se quiser mostrar as recomendações de um cliente público (por exemplo, seu site de comércio eletrônico), você deverá criar um servidor proxy para fornecer as recomendações. Isso é importante para que a Chave da API não seja exposta a entidades externas (potencialmente não confiáveis).

Veja algumas ideias de locais onde você pode usar as Recomendações:

### Página do produto

**Recomendações de item**
<p>Se o modelo foi treinado com dados de compra, ele permitirá que seu cliente *descubra produtos que provavelmente serão de interesse para pessoas que compraram o item de origem*.</p>
<p>Se o modelo foi treinado com dados de clique, ele permitirá que seu cliente *descubra produtos que provavelmente serão visitados por pessoas que visitaram o item de origem*. Esse tipo de modelo pode retornar itens semelhantes.</p>

**Recomendações do tipo "Frequentemente comprados juntos"**
<p>Uma criação de itens frequentemente comprados juntos pode ser treinada, de modo que você pode obter conjuntos de itens que têm probabilidade de serem adquiridos em conjunto com o item de origem.</p>

### Página de conclusão de compra

**Recomendações de item**
<p>Um modelo de recomendações pode usar uma lista de itens como entrada. Assim, você poderia transmitir todos os itens no carrinho de compras como entrada para obter recomendações. Nesse caso, o modelo fornece recomendações que são de interesse considerando todos os itens no carrinho de compras.
</p>

### Página de aterrissagem

**Recomendações de usuário**
<p>
Um modelo de recomendações pode usar a ID de usuário como entrada. Ele usará o histórico de transações do usuário para fornecer recomendações personalizadas para o usuário especificado.
</p>

Confira a [Documentação sobre a obtenção de recomendações de item](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### O que vem a seguir?
Parabéns por ter chegado até aqui! Para saber mais, você pode visitar a [Referência da API de Recomendações](http://go.microsoft.com/fwlink/?LinkId=759348) completa. Se tiver dúvidas, não hesite em entrar em contato conosco em mlapi@microsoft.com

<!---HONumber=AcomDC_0928_2016-->