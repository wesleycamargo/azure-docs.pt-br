<properties 
 pageTitle="Planos e Cobrança no Agendador do Azure" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>
 
# Planos e Cobrança no Agendador do Azure

## Planos de Coleção de Trabalho

Coleções de trabalho são a entidade faturável no Agendador do Azure. As coleções de trabalho contém vários trabalhos e vêm em três planos: Gratuito, Standard e Premium, que são descritos abaixo.

|**Plano de Coleção de Trabalho**|**Número máximo de trabalhos por Coleção de Trabalhos**|**Recorrência Máxima**|**Máximo de Coleções de Trabalho por Assinatura**|**Limites**|
|:---|:---|:---|:---|:---|
|**Gratuito**|5 trabalhos por coleção de trabalhos|Uma vez por hora. Não é possível executar trabalhos com uma frequência maior do que uma vez por hora|É permitida até 1 coleção de trabalho gratuita por assinatura|Não é possível usar [objeto de saída de autorização HTTP](scheduler-outbound-authentication.md)
|**Standard**|50 trabalhos por coleção de trabalhos|Uma vez por minuto. Não é possível executar trabalhos com uma frequência maior do que uma vez por minuto|São permitidas até 100 coleções de trabalho standard por assinatura|Acesso ao conjunto completo de recursos do Agendador|
|**Premium**|50 trabalhos por coleção de trabalhos|Uma vez por minuto. Não é possível executar trabalhos com uma frequência maior do que uma vez por minuto|São permitidas até 10.000 coleções de trabalhos premium por assinatura. <a href="mailto:wapteams@microsoft.com">Entre em contato conosco</a>para obter mais informações.|Acesso ao conjunto completo de recursos do Agendador|

## Atualizações e Downgrades de Planos de Coleção de Trabalhos

Você pode atualizar ou fazer downgrade de um plano de coleção de trabalhos a qualquer momento entre os planos Gratuito, Standard e Premium. No entanto, ao fazer o downgrade para uma coleção de trabalhos gratuita, o downgrade pode falhar por um dos seguintes motivos:

- Já existe uma coleção de trabalhos gratuita na assinatura
- Um trabalho na coleção de trabalhos tem uma recorrência maior que o permitido para trabalhos em coleções de trabalhos gratuitas. A recorrência máxima permitida em uma coleção de trabalhos gratuita é de uma vez por hora
- Há mais de 5 trabalhos na coleção de trabalhos
- Um trabalho na coleção de trabalhos tem uma ação HTTP ou HTTPS que usa um [objeto de saída de autorização HTTP](scheduler-outbound-authentication.md)

## Planos de cobrança e do Azure

As assinaturas não são cobradas para coleções de trabalhos gratuitas. Se você tiver mais de 100 coleções de trabalhos standard (10 unidades de cobrança standard), então é melhor ter todas as coleções de trabalhos no plano premium.

Se você tiver uma coleção de trabalhos standard e uma coleção de trabalhos premium, será cobrada uma unidade de cobrança standard _e_ uma unidade de cobrança premium. As listas de serviço do Agendador com base no número de coleções de trabalhos ativos que são definidos como standard ou premium; isso é explicado com mais detalhes nas próximas duas seções.

## Unidades faturáveis Standard

Uma unidade faturável padrão pode incluir até 10 coleções de trabalhos standard. Como um conjunto de trabalhos standard pode ter até 50 trabalhos por coleção de trabalhos, uma unidade de cobrança standard permite que uma assinatura tenha até 500 trabalhos, até quase 22 milhões de execuções de trabalho por mês.

Se você tiver entre 1 e 10 coleções de trabalhos standard, você será cobrado por 1 unidade de cobrança standard. Se você tiver entre 11 e 20 coleções de trabalhos standard, você será cobrado por 2 unidades de cobrança standard. Se você tiver entre 21 e 30 coleções de trabalhos standard, você será cobrado por 3 unidades de cobrança standard e assim por diante.

## Unidades faturáveis Premium

Uma unidade faturável premium pode incluir até 10.000 coleções de trabalhos premium. Como um conjunto de trabalhos premium pode ter até 50 trabalhos por coleção de trabalhos, uma unidade de cobrança standard permite que uma assinatura tenha até 500.000 trabalhos, até quase 22 bilhões de execuções de trabalho por mês.

Se você tiver entre 1 e 10.000 coleções de trabalhos premium, você será cobrado por 1 unidade de cobrança premium. Se você tiver entre 10.001 e 20.000 coleções de trabalhos premium, você será cobrado por 2 unidades de cobrança premium e assim por diante.

Portanto, coleções de trabalhos premium têm a mesma funcionalidade que as coleções de trabalhos standard, mas fornecem uma quebra de preço no caso do seu aplicativo precisar de muitas coleções de trabalhos.

## Status de Cobrança e Ativo

As coleções de trabalhos estão sempre ativas, a menos que a assinatura inteira tenha entrado em algum estado temporário desabilitado devido a problemas de cobrança. A única maneira de garantir que uma coleção de trabalhos não será cobrada é defini-la para o plano _Gratuito_ ou excluir a coleção de trabalhos.

Você pode desativar todos os trabalhos em um conjunto de trabalhos em uma única operação, isso não altera o status de cobrança da coleção de trabalhos; a coleção de trabalhos _ainda_ será cobrada. Da mesma forma, coleções de trabalhos vazias são consideradas ativas e serão cobradas.

## Preços

Para obter detalhes sobre preços, confira [Preços do Agendador](https://azure.microsoft.com/pricing/details/scheduler/).

## Consulte também
 

 [O que é o Agendador?](scheduler-intro.md)
 
 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Introdução à utilização do Agendador no Portal do Azure](scheduler-get-started-portal.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Alta disponibilidade e confiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, padrões e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)
 
  

  

<!---HONumber=AcomDC_0128_2016-->