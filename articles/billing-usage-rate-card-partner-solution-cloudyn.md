<properties
   pageTitle="APIs de uso e do RateCard do Microsoft Azure permitem que o Cloudyn forneça ITFM para os clientes | Microsoft Azure"
   description="Fornece uma perspectiva exclusiva do parceiro de cobrança Cloudyn do Microsoft Azure, em suas experiências para integrar as APIs de cobrança do Azure em seus produtos. Isso é especialmente útil para clientes do Azure e Cloudyn que estejam interessados em usar/experimentar o Cloudyn para serviços do Azure."
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
   ms.date="02/19/2016"
   ms.author="mobandyo;bryanla"/>

# APIs de Uso do Microsoft Azure e RateCard permitem que o Cloudyn forneça ITFM para os clientes 

Cloudyn, um parceiro de desenvolvimento da Microsoft e líder no fornecimento de recursos de gerenciamento de nuvem, foi escolhido para um modo de visualização particular das novas APIs RateCard e de Uso de Recursos do Microsoft Azure. A API de uso fornece acesso aos dados de consumo do Azure estimado para uma assinatura. A API RateCard fornece informações completas de preços de todos os serviços do Azure, para clientes não que não fazem parte do Enterprise Agreement (EA). Quando integradas, essas APIs fornecem uma base de informações completa para inseração em ferramentas gestão financeira de TI (ITFM), como as fornecidas pelo Cloudyn.

## Introdução 

A chamada "multiplicação" de dados da API de uso com dados da API RateCard (preço de uso [unidades] [$unit] = uso e custo detalhados) gera informações de cobrança mais granulares, precisas e confiáveis para o Azure de hoje.

![Visão geral de ITFM][1]

Consumir essas APIs fornece informações importantes sobre o uso de clientes e os custos, permitindo que o Cloudyn analise as contas de clientes de uma maneira simple, através de programação e para realizar várias tarefas de ITFM para seus clientes.

## Integração do Cloudyn com as APIs de Uso e RateCard
A API RateCard requer vários parâmetros de entrada, como informações de região, moeda e localidade – mas o mais importante é OfferDurableID, que especifica o tipo de oferta do Azure que o cliente está usando (pré-pago, planos de compromisso herdado de 6 e 12 meses, ofertas de MSDN, ofertas de MPN, ofertas promocionais e outros). O OfferDurableID pode ser encontrado no [portal de Cobrança e Uso do Azure](https://account.windowsazure.com/Subscriptions), sob a "ID da oferta" para uma assinatura específica.

Após o registro nos serviços do [Cloudyn para o Azure](https://www.cloudyn.com/microsoft-azure/), os clientes podem adicionar seu código OfferDurableID, que permite ao Cloudyn receber suas informações de preços relevantes por meio da API RateCard. Informações sobre os diferentes tipos de ofertas podem ser encontradas na página de [detalhes da oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/).

![Visão geral do mecanismo de IFTM do Cloudyn][2]

O Cloudyn usa as APIs de Uso e RateCard, além da API de Desempenho do Azure, para criar camadas adicionais de visualização, análise, alerta, relatórios, gerenciamento de custos e recomendações viáveis, fornecendo aos clientes do Azure uma ferramenta de ITFM de nuvem de empresa confiável.

## Casos de uso de IFTM do Cloudyn habilitados pela integração da API de Uso e a RateCard 
Casos de uso comuns de IFTM do Cloudyn habilitados pela integração da API de Uso e a RateCard incluem:

+ **Análise de custo** - Permite que os custos de nuvem sejam divididos para qualquer dimensão de identificação nativa (provedor, serviço, conta, região, etc.). O uso das APIs de Uso e RateCard do Azure facilitam essa tarefa, fornecendo a análise mais granular de uso e dados de custo por conta, que é, em seguida, agrupado e filtrado pelo Cloudyn e apresentada ao usuário, em formato de tabela ou gráfico.

![Gráfico de pizza de análise de custo][3]

+ **Custo alocação 360** - Permite aos gerentes de finanças e de TI descobrir a divisão de custo real, drivers e tendências de sua implantação na nuvem. Ele ainda permite aos gerentes associar facilmente as despesas de implantação com unidades de negócios, departamentos, regiões e muito mais, fornecendo informações sem precedentes sobre os custos de nuvem e promovendo showbacks e estornos de empresa. O uso das APIs de Uso e RateCard do Azure servirão como entrada para o mecanismo de alocação de custo do Cloudyn, que complementa as APIs através da definição de métodos e lógica de negócios para alocar recursos marcados ou desmarcados.

![Gráfico de alocação de custos 360][4]

+ **Dimensionamento de baixo custo** -Fornece recomendações de dimensionamento correto para máquinas virtuais subutilizadas, reduzindo as despesas do cliente em computadores provisionados em excesso ou muito grandes. Ele faz isso examinando a CPU da máquina virtual e métricas de RAM (por meio da API de Desempenho), das horas de tempo de execução (por meio do uso da API) e o custo (por meio da API RateCard). A Cloudyn fornece recomendações de dimensionamento correto com base nos recursos de CPU ou memória RAM subutilizados (desempenho) e calcula a economia estimada multiplicando o delta de preço (RateCard) entre as VMs pela utilização em tempo real (Uso) da máquina subutilizada.

![Custos reduzidos de dimensionamento][5]

+ **Recomendações de portabilidade de nuvem** - Fornece consultoria financeira sobre a portabilidade de nuvem. Examina os custos atuais do usuário dos recursos de nuvem que são implantados nos fornecedores principais de nuvem e compara o custo de uma implantação equivalente no Azure. Ela também fornece recomendações granulares, por recurso, financeiras para o Azure. Após avaliar a implantação equivalente necessária no Azure (com base nas preferências do usuário e métricas de desempenho), o Cloudyn usa a API RateCard para avaliar o custo da implantação equivalente no Azure.

+ **Relatórios de desempenho** - Habilitados pela API de desempenho do Azure, esses relatórios fornecem um conjunto de recursos de utilização de CPU e RAM para recomendações de otimização. Abaixo está um exemplo de relatório de utilização de instância, apresentando a divisão de instância por utilização média da CPU.

![Relatórios de desempenho][6]

+ **Gerenciador de categoria** - Um recurso poderoso do Cloudyn que organiza os recursos de nuvem que não estão organizados. Ele fornece aos usuários a liberdade de criar suas próprias categorias exclusivas (marcas) para medir e relatar de forma eficaz o que está de acordo com as práticas de negócio. Além disso, os usuários podem facilmente regular e categorizar a marcação inconsistente (por exemplo, erros de digitação e outras discrepâncias) e detectar automaticamente os recursos marcados para uma atribuição de custo precisa.

![Gerente de categoria][7]

## Vídeo 

Aqui está um breve vídeo que mostra como um cliente do Azure pode o usar Cloudyn para Azure e as APIs de cobrança do Azure, para ter uma ideia de seus dados de consumo do Azure.
 
> [AZURE.VIDEO cloudyn-provides-cloud-itfm-tools-via-microsoft-azure-apis]


## Próximas etapas

+ Inicie uma avaliação gratuita do [Cloudyn para o Azure](https://www.cloudyn.com/microsoft-azure/) para ver como você pode obter transparência de custos com relatórios personalizados e análises para sua implantação de nuvem do Microsoft Azure.
+ Consulte [informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) para uma visão geral das APIs de Uso e RateCard do Azure.
+ Confira a [referência da API REST de Cobrança do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, que fazem parte do conjunto de APIs fornecidas pelo Gerenciador de Recursos do Azure.
+ Se você gostaria de se aprofundar no exemplo de código, confira nossos exemplos de código da API de Cobrança do Microsoft Azure em [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## Saiba mais
+ Para saber mais sobre as ofertas do Microsoft Azure Enterprise Agreement (EA), visite [licenciamento do Azure para a empresa](https://azure.microsoft.com/pricing/enterprise-agreement/)
+ Consulte o artigo [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md) para saber mais sobre o Gerenciador de Recursos do Azure.
+ Para obter informações adicionais sobre o pacote de ferramentas necessário para ajudá-lo a compreender o gasto de nuvens, consulte o artigo da Gartner [Guia de mercado para as ferramentas de gestão financeira gerenciamento de TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Overview.png
[2]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-ITFM-Engine-Overview.png
[3]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Analysis-Pie-Chart.png
[4]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Allocation-360-Chart.png
[5]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Cost-Effective-Sizing.png
[6]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Performance-Reports.png
[7]: ./media/billing-usage-rate-card-partner-solution-cloudyn/Cloudyn-Category-Manager.png

<!---HONumber=AcomDC_0803_2016-->