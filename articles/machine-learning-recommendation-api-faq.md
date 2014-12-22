<properties title="FAQ for setting up and using the Machine Learning Recommendations API" pageTitle="Perguntas frequentes para configurar e usar a API de recomendações de aprendizado de máquina | Azure" description="Microsoft RECOMMENDATIONS API built with Azure Machine Learning FAQ" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="jaymathe" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="jaymathe" /> 

# Perguntas frequentes para configurar e usar a API de recomendações de aprendizado de máquina
Versão 1.0

Sumário

* Quais são as RECOMENDAÇÕES?	
* O que posso fazer com as RECOMENDAÇÕES?	
* Existem limitações durante o período de lançamento?	
* Compra e cobrança	
	* Qual é o custo de RECOMENDAÇÕES durante o período de lançamento?	
	* Existem nos custos associados a fazer as RECOMENDAÇÕES rastrearem e/ou controlarem a atividade do usuário para mim?	
	* RECOMENDAÇÕES têm uma avaliação gratuita?	
	* Quando terminará o período de lançamento?	
	* Quanto custará quando ele se tornar GA (disponível ao público geral)?	
	* Quando eu serei cobrado por RECOMENDAÇÕES?	
	* Como atualizar para um nível superior de serviços?	
	* Quando a minha assinatura de RECOMENDAÇÕES terminará?	
	* Como cancelo minha assinatura de RECOMENDAÇÕES?	
* Guia de introdução a RECOMENDAÇÕES	
	* RECOMENDAÇÕES é para mim?	
	* Quais são os pré-requisitos para configurar RECOMENDAÇÕES?	
	* Como eu configuro RECOMENDAÇÕES pela primeira vez?	
	* Onde posso encontrar a documentação da API?	
	* Quais são as opções que eu tenho para carregar dados de uso e catálogo para RECOMENDAÇÕES?	
* Manutenção e suporte	
	* Que tamanho meu conjunto de dados pode ter?	
	* Onde posso obter suporte técnico para RECOMENDAÇÕES?	
	* Onde posso encontrar os termos de uso?	
* Legal	


## Quais são as RECOMENDAÇÕES?
Para organizações e empresas que confiam em RECOMENDAÇÕES para vendas cruzadas e agregadas para seus clientes, o recurso de RECOMENDAÇÕES de Aprendizado de Máquina do Microsoft Azure é um mecanismo de recomendações de autoatendimento sobre o Azure. É uma implementação de filtragem de colaboração usando fatoração de matriz como seu algoritmo central. Está acessível aos desenvolvedores de aplicativos como uma API através de uma interface HTTP RESTful. RECOMENDAÇÕES está atualmente em visualização pública, a caminho de se tornar um serviço disponível.

## O que posso fazer com as RECOMENDAÇÕES?
RECOMENDAÇÕES utiliza como entrada um item ou um conjunto de itens e retorna uma lista de recomendações relevantes. Por exemplo: um cliente de uma loja online clica em um produto. A loja online envia esse produto como uma entrada para RECOMENDAÇÕES, obtém uma lista de produtos em troca e decide qual desses produtos serão mostrados para o cliente. Você talvez queira usar RECOMENDAÇÕES para otimizar sua loja online ou até mesmo informar seu departamento de vendas interno ou central de atendimento.


## Existem limitações durante o período de lançamento?

* Número máximo de modelos por assinatura: 10
* Número máximo de itens que um catálogo pode conter: 100,000
* O tamanho máximo dos dados que podem ser enviado no POST (por exemplo, importar dados de catálogo, importar dados de uso) é de 200 MB
* O número de transações por segundo para uma compilação de modelo de recomendação que não esteja ativa é de ~2 TPS, apenas uma compilação de modelo de recomendação ativa pode conte até 20 TPS

## Compra e cobrança 
Mais informações sobre a compreensão de assinaturas podem ser encontradas aqui.

###Qual é o custo de RECOMENDAÇÕES durante o período de lançamento?
RECOMENDAÇÕES é um serviço por assinatura. O carregamento é feito com base no volume de transações por mês. Durante o período de inicialização, o serviço é gratuito e está restrito a 100.000 transações por mês.

###Existem nos custos associados a fazer as RECOMENDAÇÕES rastrearem e/ou controlarem a atividade do usuário para mim?
Não no momento.

###RECOMENDAÇÕES têm uma avaliação gratuita?
Durante o período de inicialização, o serviço é gratuito e está restrito a 100.000 transações por mês.

###Quando terminará o período de lançamento?
O período de inicialização e seus preços devem durar vários meses. Iremos notificar nossos usuários inscritos sobre as alterações futuras de status e preços.

###Quanto custará quando ele se tornar GA (disponível ao público geral)?
Preços de RECOMENDAÇÕES quando o produto se tornar disponível ainda será determinado. No caso de alterações de preço, os assinantes serão notificados.

###Quando eu serei cobrado por RECOMENDAÇÕES?
Uma assinatura paga é qualquer assinatura para a qual há uma taxa mensal. Quando você adquire uma assinatura paga, é cobrado imediatamente para uso do primeiro mês. Será cobrado o valor exibido ao lado da oferta da página de assinatura (além de impostos aplicáveis). A cobrança mensal é feita a cada mês na mesma data de calendário que a da primeira compra até você cancelar a assinatura. Para mais informações sobre a compreensão de assinaturas, clique aqui.

###Como atualizar para um nível superior de serviços?
Durante o período de inicialização, o serviço é restrito a 100.000 transações por mês e não pode ser atualizado para um nível superior.

Quando RECOMENDAÇÕES estiver disponível ao público geral - se você descobrir que sua assinatura não fornece transações suficientes, pode atualizar para uma assinatura com um limite superior de transações.

Quando você atualiza uma assinatura:

* Transações que permanecem em sua antiga assinatura não são adicionadas à sua nova assinatura. 
* Você paga o preço total para a nova assinatura, embora haja transações não utilizadas em sua antiga assinatura.

Processo para atualizar uma assinatura

* Entre no Marketplace.
* Clique na guia Meus Dados.
* Clique em Usar atrás da assinatura que deseja atualizar. 
* No painel à direita, estão todas as assinaturas disponíveis com sua a assinatura esmaecida. Clique no botão de opção para a assinatura para a qual deseja atualizar.
* Se você desejar atualizar, clique em OK na caixa de diálogo. Se não desejar atualizar, clique em Cancelar.

IMPORTANTE: Leia atentamente a caixa de diálogo antes de atualizar, uma vez que há implicações de cobrança e uso.

Para mais informações sobre a compreensão de assinaturas, clique [aqui](http://msdn.microsoft.com/pt-br/library/gg312164.aspx).

###Quando a minha assinatura de RECOMENDAÇÕES terminará?
Sua assinatura será finalizada quando você a cancelar. Se você desejar cancelar suas assinaturas, consulte as instruções abaixo.

###Como cancelo minha assinatura de RECOMENDAÇÕES?
Para cancelar sua assinatura, siga as etapas abaixo. Se sua assinatura atual for uma assinatura paga, ela continuará em vigor até o final do período de faturamento atual. Se você precisar de cancelamento seja efetiva imediatamente, fale conosco [aqui](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn).

Nenhum reembolso é fornecido se você cancelar antes do final de um período de cobrança ou para transações não utilizadas em um período de cobrança.

* Entre no Marketplace.
* Clique na guia Meus Dados.
* Localize a assinatura que deseja cancelar.
* Clique em Cancelar à direita do nome do conjunto de dados e status. Você pode usar essa assinatura até o final do período de faturamento atual ou até o limite de transação ser atingido - o que ocorrer primeiro.

Se desejar cancelar a assinatura imediatamente para poder comprar uma nova assinatura, acesse [aqui](https://support.microsoft.com/oas/default.aspx?gprid=17024&st=1&wfxredirect=1&sd=gn) para abrir um chamado com o suporte ao cliente.


## Guia de introdução a RECOMENDAÇÕES

###RECOMENDAÇÕES é para mim? 
RECOMENDAÇÕES do Aprendizado de Máquina do Microsoft Azure destina-se a organizações e empresas que dependem de recomendações para realizar vendas cruzadas e agregadas aos seus clientes. Se você tiver um site voltado ao cliente, uma equipe de vendas, uma equipe de vendas interna ou uma central de atendimento, e se você oferecer um catálogo de mais de algumas dezenas de produtos ou serviços - seu resultado final pode se beneficiar de consumo de RECOMENDAÇÕES. Experimentar com RECOMENDAÇÕES foi projetado para ser bastante simples. A versão baseada em API atual requer habilidades de programação básicas. Caso você precise de assistência, contate o fornecedor que desenvolveu o seu site. Se você tiver um departamento de TI interno ou desenvolvedor interno, eles poderão fazer RECOMENDAÇÕES funcionar para você. 

###Quais são os pré-requisitos para configurar RECOMENDAÇÕES?
RECOMENDAÇÕES requer que você tenha um log das opções de usuário no que diz respeito ao catálogo. No caso de tal um log não ser necessário e você ter um site voltado para o cliente, RECOMENDAÇÕES pode coletar a atividade de usuário para você. RECOMENDAÇÕES também requer um catálogo de produtos ou serviços. Caso você não tenha o catálogo, RECOMENDAÇÕES pode utilizar os dados de uso de clientes reais e extrair um catálogo. Um catálogo "implícito" não incluirá itens que não tenham sido "relatados" como parte das transações de usuário.

###Como eu configuro RECOMENDAÇÕES pela primeira vez?
Depois de se inscrever para RECOMENDAÇÕES, você deve usar a documentação da API  [aqui](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ) para configurar o serviço.

###Onde posso encontrar a documentação da API? 
A documentação da API está [aqui](https://onedrive.live.com/view.aspx?cid=8536718B52F71725&resid=8536718B52F71725!118&app=Word&authkey=!AOLb7aR6D0cVqMQ).

###Quais são as opções que eu tenho para carregar dados de uso e catálogo para RECOMENDAÇÕES?
Você tem duas opções para carregar os dados do catálogo e de uso: você pode exportar esses dados de seu sistema CRM ou outros logs e carregá-lo para RECOMENDAÇÕES, ou pode adicionar marcas ao seu site para controlar as atividades do usuário. Se a última opção for escolhida, os dados serão armazenados no Azure.

##Manutenção e suporte

###Que tamanho meu conjunto de dados pode ter?
Cada conjunto de dados pode conter até 100.000 itens de catálogo e até 2048 MB de dados de uso.
Além disso, uma assinatura pode conter até 10 conjuntos de dados (modelos).

###Onde posso obter suporte técnico para RECOMENDAÇÕES?
Suporte técnico está disponível [aqui](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MachineLearning).

###Onde posso encontrar os termos de uso?

Os termos de uso estão disponíveis [aqui](https://datamarket.azure.com/dataset/amla/recommendations#terms).

#Legal
Este documento é fornecido "no estado em que se encontra". Informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. 
Alguns exemplos aqui representados são fornecidos somente para fins de ilustração e são fictícios. Nenhuma associação ou conexão real é intencional ou deve ser inferida. 
Este documento não fornece a você nenhum direito legal a qualquer propriedade intelectual de qualquer produto da Microsoft. Você pode copiar e usar este documento para fins de consulta interna. 
(c) 2014 Microsoft. Todos os direitos reservados. 


