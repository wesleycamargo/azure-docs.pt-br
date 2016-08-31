<properties
   pageTitle="Obter informações sobre o consumo de recursos do Microsoft Azure | Microsoft Azure"
   description="Fornece uma visão geral conceitual das APIs de Uso de cobrança do Azure e RateCard, que são usadas para fornecer informações sobre o consumo de recursos e as tendências do Azure."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# Obtenha informações sobre o consumo de recursos do Microsoft Azure

Os clientes e parceiros exigem a capacidade de prever com precisão e gerenciar os custos do Azure. Quando eles passam de um Capex para um modelo Opex, eles precisam também da capacidade de fazer showback versus a análise de chargeback, bem como fornecer fidelidade de modo de estimativa e cobrança, especialmente para implantações em nuvens grandes.

O uso de recursos do Azure e APIs de cartão de taxa discutidos neste aborda essas necessidades, permitindo novas informações sobre o consumo de recursos do Azure.

## Apresentando o uso de recursos do Azure e APIs RateCard

O uso de recursos do Azure e APIs RateCard são implementados como um provedor de recursos, como parte da família de APIs expostas pelo Gerenciador de Recursos do Azure.

### API de uso de recursos do Azure (visualização)
Os clientes e parceiros podem usar a API de uso de recursos do Azure para obter seus dados de consumo previstos do Azure. Os recursos incluem:

- **Controle de Acesso baseado em Funções do Azure** - os clientes e parceiros podem configurar suas políticas de acesso no [portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](powershell-install-configure.md) para especificarem quais usuários ou aplicativos podem ter acesso aos dados de uso da assinatura. Os chamadores devem usar tokens padrão do Active Directory do Azure para autenticação. O chamador também deve ser adicionado à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma determinada assinatura do Azure.

- **Agregações diárias ou por hora** - os chamadores podem especificar se eles desejam seus dados de uso do Azure em buckets por hora ou buckets diários. O padrão é diário.

- **Metadados da instância fornecidos (inclui as marcações do recurso)** – os detalhes no nível da instância, como o uri de recurso totalmente qualificado (/subscriptions/{subscription-id}/..), juntamente com as informações do grupo de recursos e marcações do recurso serão fornecidos na resposta. Isso ajuda os clientes a alocar de forma determinista e programaticamente o uso por marcas, para casos de uso como cobrança cruzada.

- **Metadados de recurso fornecidos** -Detalhes de recurso como nome do medidor, categoria do medidor, subcategoria do medidor, unidade e região serão passados na resposta, para fornecer um melhor entendimento do que foi consumido. Também estamos trabalhando para alinhar a terminologia de metadados de recursos entre o portal do Azure, o CSV de uso do Azure, o CSV de cobrança EA e outras experiências públicas, para permitir que os clientes correlacionem dados entre as experiências.

- **Uso para todos os tipos de oferta** – Os dados de uso estarão acessíveis para todos os tipos de oferta incluindo pré-pago, MSDN, investimento e crédito monetário e EA, entre outros.

### API RateCard de Recursos do Azure (visualização)
Clientes e parceiros podem usar a API RateCard de Recursos do Azure para obter a lista de recursos do Azure disponíveis, juntamente com as informações estimadas de preço para cada. Os recursos incluem:

- **Controle de Acesso baseado em Funções do Azure** - Os clientes e parceiros podem configurar suas políticas de acesso no [portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](powershell-install-configure.md) para especificarem quais usuários ou aplicativos podem ter acesso aos dados do RateCard. Os chamadores devem usar tokens padrão do Active Directory do Azure para autenticação. O chamador também deve ser adicionado à função Leitor, Proprietário ou Colaborador para obter acesso aos dados de uso para uma determinada assinatura do Azure.

- **Suporte para pré-pago, MSDN, ofertas de investimento e crédito monetário (EA não possui suporte)** - Esta API fornece informações de taxa no nível da oferta do Azure versus nível da assinatura. O chamador dessa API deve passar as informações de oferta para obter taxas e detalhes do recurso. Como as ofertas de EA possuem taxas personalizadas por registro, seremos capazes de fornecer as taxas EA neste momento.

## Cenários

Aqui estão alguns dos cenários possíveis com a combinação das APIs de Uso e RateCard:

- **Gasto do Azure durante o mês** - Os clientes podem usar as APIs de Uso e RateCard em combinação para obter melhores resultados sobre o seu gasto de nuvem durante o mês, analisando os buckets horários e diários das estimativas de custo e de uso.

- **Configurar alertas** – Os clientes e parceiros podem configurar alertas baseados em recursos ou monetários com base em seu consumo de nuvem obtendo o consumo previsto e o custo estimado usando a API de Uso e RateCard.

- **Previsão de fatura** – Os clientes e parceiros podem obter seu consumo estimado e gasto de nuvem e aplicar algoritmos de aprendizagem de máquina para prever qual seria sua fatura no final do ciclo de cobrança.

- **Análise de custo de pré-consumo** – Os clientes também podem usar a API RateCard para prever quanl seria a sua fatura se fossem mover as cargas de trabalho para o Azure, fornecendo os números de uso desejados. Se os clientes têm cargas de trabalho existentes em outras nuvens ou nuvens privadas, eles também podem mapear seu uso com as taxas do Azure para obter uma melhor estimativa do seu gasto estimado do Azure. Isso fornece uma exibição avançada do que pode ser obtido por meio da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/), como (por exemplo), nossos parceiros de cobrança fornecem a capacidade de oferta de tabela dinâmica e comparar/contrastar entre tipos diferentes de oferta além de pré-pago, incluindo o investimento e crédito monetário. As APIs também fornecem a capacidade de alterar as estimativas de custo por região, permitindo que o tipo de análise hipotética necessária para tomar decisões de implantação, como implantar recursos em diferentes controladores de domínio em todo o mundo possam ter um impacto direto no custo total.

- **Hipóteses** -

	- Os clientes e parceiros podem determinar se seria mais econômico executar suas cargas de trabalho em outra região, ou em outra configuração do recurso do Azure. Os custos do recursos do Azure podem ser diferentes com base na região do Azure no qual estão sendo executados, e isso permite que os clientes e parceiros obtenham otimizações de custo.

	- Os clientes e parceiros também podem determinar se outro tipo de oferta do Azure oferece uma melhor taxa em um recurso do Azure.

## Soluções de parceiros

[As APIs de Uso e RateCard do Microsoft Azure APIs permitem Cloudyn para fornecer ITFM para clientes](billing-usage-rate-card-partner-solution-cloudyn.md) descrevem a experiência de integração oferecida pelo parceiro de API de cobrança do Azure [Cloudyn](https://www.cloudyn.com/microsoft-azure/). Este artigo fornece uma abordagem detalhada sobre sua experiência, incluindo um breve vídeo que mostra como um cliente do Azure pode usar Cloudyn e APIs de cobrança do Azure para ter uma ideia dos seus dados de consumo do Azure.

[Integração da API de cobrança do Microsoft Azure e Cloud Cruiser](billing-usage-rate-card-partner-solution-cloudcruiser.md) descreve como o [Express do Cloud Cruiser para Azure Pack](http://www.cloudcruiser.com/partners/microsoft/) funciona diretamente do portal do WAP, permitindo que os clientes gerenciem diretamente os aspectos operacionais e financeiros do seu Microsoft Azure particular ou hospedado públicamente em nuvem a partir de uma interface de usuário único.

## Próximas etapas
+ Confira a [Referência da API REST de Cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais detalhes sobre as duas APIs, que fazem parte do conjunto de APIs fornecidas pelo Gerenciador de Recursos do Azure.
+ Se você gostaria de se aprofundar no exemplo de código, confira nossos exemplos de código da API de Cobrança do Microsoft Azure em [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## Saiba mais
+ Consulte o artigo [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md) para saber mais sobre o Gerenciador de Recursos do Azure.
+ Para obter informações adicionais sobre o pacote de ferramentas necessárias para ajudá-lo a compreender os gastos de nuvem, consulte o artigo da Gartner [Guia de mercado para as ferramentas de gestão financeira de TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!---HONumber=AcomDC_0817_2016-->