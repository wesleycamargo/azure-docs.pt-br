---
title: "Recuperação de desastres de área geográfica de Hubs de Eventos do Azure | Microsoft Docs"
description: "Como usar regiões geográficas para fazer failover e executar a recuperação de desastre nos Hubs de Eventos do Azure"
services: event-hubs
documentationcenter: 
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: sethm
ms.openlocfilehash: 94c2782b3166fbc65ae755291a82a2a14556b96f
ms.sourcegitcommit: ccb84f6b1d445d88b9870041c84cebd64fbdbc72
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2017
---
# <a name="azure-event-hubs-geo-disaster-recovery-preview"></a>Recuperação de desastres de área geográfica dos Hubs de Evento do Azure (versão prévia)

Quando datacenters regionais enfrentam tempo de inatividade, é essencial para o processamento de dados continuar a operar em uma região ou datacenter diferente. Assim, *a recuperação de desastre em área geográfica* e a *replicação geográfica* são recursos importantes para qualquer empresa. Os Hubs de Eventos do Azure dão suporte à recuperação de desastre de área geográfica e à replicação geográfica no nível do namespace. 

O recurso de recuperação de desastre de área geográfica dos Hubs de Evento do Azure é uma solução de recuperação de desastre. Os conceitos e o fluxo de trabalho descrito neste artigo se aplicam a cenários de desastre e não a falhas transitórias ou temporárias.

Para uma discussão detalhada sobre a recuperação de desastre no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications). 

## <a name="terminology"></a>Terminologia

**Emparelhamento**: o namespace primário é conhecido como *ativo* e recebe mensagens. O namespace de failover é *passivo* e não recebe mensagens. Os metadados entre os dois estão sincronizados, para que ambos possam aceitar mensagens continuamente sem qualquer alteração no código do aplicativo. Estabelecer a configuração de recuperação de desastres entre a região ativa e a região passiva é conhecido como *emparelhamento* das regiões.

**Alias**: um nome para uma configuração de recuperação de desastres que você configurou. O alias fornece uma única cadeia de conexão estável do FQDN (Nome de Domínio Totalmente Qualificado). Aplicativos usam essa cadeia de conexão de alias para conectarem-se a um namespace.

**Metadados**: refere-se a nomes de hub de eventos, grupos de consumidores, partições, unidades de taxa de transferência, entidades e propriedades associadas ao namespace.

## <a name="enable-geo-disaster-recovery"></a>Habilitar a recuperação de desastres de área geográfica

Habilite a recuperação de desastres de área geográfica de Hubs de Eventos em três etapas: 

1. Crie um emparelhamento geográfico, que cria uma cadeia de conexão de alias e proporciona replicação de metadados em tempo real. 
2. Atualize as cadeias de conexão de cliente existente para o alias criado na etapa 1.
3. Inicie o failover: o emparelhamento de replicação geográfica é interrompido e o alias aponta para o namespace secundário como seu novo namespace primário.

A figura a seguir mostra este fluxo de trabalho:

![Fluxo de emparelhamento de área geográfica][1] 

### <a name="step-1-create-a-geo-pairing"></a>Etapa 1: criar um emparelhamento de área geográfica

Para criar um emparelhamento entre duas regiões, é necessário um namespace primário e um namespace secundário. Em seguida, você cria um alias para formar o par de área geográfica. Depois que os namespaces estão emparelhados a um alias, os metadados são replicados periodicamente nos dois namespaces. 

O código a seguir mostra como fazer isso:

```csharp
ArmDisasterRecovery adr = await client.DisasterRecoveryConfigs.CreateOrUpdateAsync(
                                    config.PrimaryResourceGroupName,
                                    config.PrimaryNamespace,
                                    config.Alias,
                                    new ArmDisasterRecovery(){ PartnerNamespace = config.SecondaryNamespace});
```

### <a name="step-2-update-existing-client-connection-strings"></a>Etapa 2: atualizar as cadeias de conexão de cliente existentes

Depois que o emparelhamento de replicação geográfica for concluído, as cadeias de conexão que apontam para os namespaces primários devem ser atualizadas para que apontem para a cadeia de conexão do alias. Obtenha as cadeias de conexão conforme mostrado no exemplo a seguir:

```csharp
var accessKeys = await client.Namespaces.ListKeysAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, "RootManageSharedAccessKey");
var aliasPrimaryConnectionString = accessKeys.AliasPrimaryConnectionString;
var aliasSecondaryConnectionString = accessKeys.AliasSecondaryConnectionString;
```

### <a name="step-3-initiate-a-failover"></a>Etapa 3: iniciar um failover

Se ocorrer um desastre ou se você quiser iniciar um failover para o namespace secundário, metadados e dados começarão a fluir para o namespace secundário. Uma vez que os aplicativos usam as cadeias de caracteres de conexão de alias, mais nenhuma ação é necessária, uma vez que eles começam automaticamente a ler e gravar nos hubs de eventos no namespace secundário. 

O código a seguir mostra como disparar o failover:

```csharp
await client.DisasterRecoveryConfigs.FailOverAsync(config.SecondaryResourceGroupName,
                                                   config.SecondaryNamespace, config.Alias);
```

Depois que o failover for concluído e você precisar dos dados presentes no namespace primário, para extrair os dados, você deve usar uma cadeia de conexão explícita para os hubs de eventos no namespace primário.

### <a name="other-operations-optional"></a>Outras operações (opcionais)

Você também pode interromper o emparelhamento de área geográfica ou excluir um alias, conforme mostrado no código a seguir. Observe que, para excluir uma cadeia de conexão de alias, primeiro você deve interromper o emparelhamento de área geográfica:

```csharp
// Break pairing
await client.DisasterRecoveryConfigs.BreakPairingAsync(config.PrimaryResourceGroupName,
                                                       config.PrimaryNamespace, config.Alias);

// Delete alias connection string
// Before the alias is deleted, pairing must be broken
await client.DisasterRecoveryConfigs.DeleteAsync(config.PrimaryResourceGroupName,
                                                 config.PrimaryNamespace, config.Alias);
```

## <a name="considerations-for-public-preview"></a>Considerações sobre a visualização pública

Observe as seguintes considerações para esta versão:

1. A capacidade de recuperação de desastres de área geográfica está disponível apenas nas regiões Centro-Norte dos EUA e Centro-Sul dos EUA. 
2. O recurso tem suporte apenas para namespaces recém-criados.
3. Para a versão prévia, somente a replicação de metadados está habilitada. Os dados reais não são replicados.
4. Com a versão prévia, não há custo para habilitar o recurso. No entanto, os namespaces primário e secundário incorrerão em encargos para as unidades de taxa de transferência reservadas.

## <a name="next-steps"></a>Próximas etapas

* O [exemplo de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) apresenta um fluxo de trabalho simples que cria um emparelhamento de área geográfica e inicia um failover para um cenário de recuperação de desastre.
* A [referência da API REST](/rest/api/eventhub/disasterrecoveryconfigs) descreve as APIs para executar a configuração de recuperação de desastres de replicação geográfica.

Para saber mais sobre Hubs de Eventos, acesse os seguintes links:

* Introdução com um [Tutorial de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-send.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
* [Aplicativos de exemplo que usam Hub de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/eh-geodr1.png

