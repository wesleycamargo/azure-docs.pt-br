---
title: Alterações no log de fluxo do grupo de segurança de rede do Azure | Microsoft Docs
description: Saiba mais sobre as alterações no log de fluxo do grupo de segurança de rede do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: b3a5ca929c83f70c31d667c2d9c811623691cff8
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40180388"
---
# <a name="network-security-group-flow-logs--version-2--upcoming-changes"></a>Alterações futuras - versão 2 – logs de fluxo de grupo de segurança de rede

O formato dos logs de fluxo do grupo de segurança de rede está mudando, a partir de 15 de setembro de 2018. Os campos adicionados fornecem informações sobre o estado do fluxo e contagens incrementais de bytes e pacotes transferidos em cada direção. Aplicativos que analisam logs de fluxo serão afetados por essa alteração e precisam ser atualizados de acordo. Este artigo descreve os detalhes da alteração.

## <a name="what-is-changing"></a>O que está mudando?

A versão dos logs de fluxo do grupo de segurança de rede mudará de "Version": 1 para "Version": 2, com campos adicionais adicionados à propriedade *flowTuples* nos logs. Detalhes sobre o formato são fornecidos em [Como um fluxo é modelado na versão 2](#how-is-a-flow-modeled-in-version-2).

### <a name="version-1-flow-tuple-format"></a>Formato de tupla de fluxo de versão 1

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,D"
```

### <a name="version-2-flow-tuple-format"></a>Formato de tupla de fluxo de versão 2

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186"
```

## <a name="when-will-this-change-take-place"></a>Quando essa alteração ocorrerá?

Esta alteração entrará em vigor a partir de **15 de setembro de 2018** na região do Centro-Oeste dos EUA. O lançamento da mudança nas regiões de nuvem pública será concluído até 31 de novembro de 2018, após o qual apenas os logs da versão 2 estarão disponíveis.

## <a name="am-i-affected-by-the-change"></a>Estou afetado pela alteração?

Você pode ser afetado por essa alteração se criar ou usar um aplicativo que processe os dados do log de fluxo do grupo de segurança de rede.

### <a name="if-i-use-traffic-analytics"></a>Se eu usar a análise de tráfego?

 Não. O Traffic Analytics não será afetado durante a transição do registro de fluxo da versão 1 para a versão 2. Os aprimoramentos em breve aproveitarão as informações adicionais de sessão e largura de banda fornecidas nos logs de fluxo da versão 2.

### <a name="if-im-using-third-party-tooling"></a>Se eu estiver usando ferramentas de terceiros?

A alteração no formato de log foi comunicada antecipadamente com todos os [parceiros de observador de rede](https://azure.microsoft.com/services/network-watcher). Entre em contato com seu fornecedor para obter detalhes.

### <a name="if-i-have-built-a-custom-application-or-integration"></a>Se eu criei uma aplicação ou integração personalizada?

**Sim**, você precisará modificar seu aplicativo para processar os Logs de fluxo do NSG no novo formato.

## <a name="what-is-the-new-flow-logging-format"></a>O que é o novo formato de registro em log de fluxo?

Versão do fluxo de logs 2 contém tuplas de fluxo no seguinte formato:

```
"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1,103,1,186".
```

A tabela a seguir fornece nomes e descrições de propriedades para a tupla de fluxo da versão 2 do grupo de segurança de rede. Registros de exemplo completo que podem ser encontrados em [versão 2 exemplo de evento](#version2sampleevent).

| Nome da propriedade                        | Valor           | DESCRIÇÃO                                                                                                                                                 |
| ------------------------------------ | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Carimbo de data/hora                           | 1493763938      | O carimbo de hora correspondente à entrada de fluxo. Formato de ÉPOCA do UNIX é usado.                                                                                       |
| Endereço IP de origem                    | 185.170.185.105 |                                                                                                                                                             |
| Endereço IP de destino               | 10.2.0.4        |                                                                                                                                                             |
| Porta de origem                          | 35370           |                                                                                                                                                             |
| Porta de destino                     | 23              |                                                                                                                                                             |
| Protocolo do tráfego                     | T               | **T**: TCP e **U**: UDP.                                                                                                                                  |
| Direção do fluxo de tráfego               | I               | **Eu**: o tráfego de entrada e **s**: o tráfego de saída.                                                                                                         |
| Decisão de tráfego                     | O                | **Um**: fluxo foi permitido e **1!d**: fluxo foi negado.                                                                                                         |
| Estado de fluxo                           | C               | **B**: início, quando um fluxo é criado. As estatísticas não são fornecidas. **C**: continuando um fluxo contínuo. As estatísticas são fornecidas em intervalos de 5 minutos. **E**: final, quando um fluxo é encerrado. Estatísticas são fornecidas.                                                                                                                                                        |
| Pacotes - origem ao destino      | 1               | O número total de pacotes TCP e UDP enviados da origem para destino desde a última atualização.                                                                  |
| Bytes enviados - origem ao destino   | 103             | O número total de bytes de pacote TCP e UDP enviados da origem para o destino desde a última atualização. O número total de bytes de pacote TCP e UDP enviados da origem para o destino desde os últimos bytes updatePacket inclui o cabeçalho e a carga útil do pacote.         |
| Pacotes enviados - destino para origem | 1               | O número total de pacotes TCP e UDP enviados do destino para a origem desde a última atualização.                                                                  |
| Bytes enviados - destino para origem   | 186             | Os bytes de pacote incluem cabeçalho de pacote e carga útil. Os bytes de pacote incluem cabeçalho de pacote e carga útil.             |

## <a name="how-is-a-flow-modeled-in-version-2"></a>Como um fluxo é modelado na versão 2?

Versão 2 dos logs apresenta o estado de fluxo. O estado de fluxo *B* é registrado quando um fluxo é iniciado. Estado de fluxo *C* e o estado de fluxo *E* são os estados que marcam a continuação de um fluxo e encerramento de fluxo, respectivamente. Os estados *C* e *E* contêm informações de largura de banda de tráfego.

**Exemplo**: fluxo de tuplas de uma conversa TCP entre 185.170.185.105:35370 e 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

### <a name="how-does-the-format-differ-from-version-1"></a>Como o formato difere da versão 1?

Na versão 1, uma tupla de fluxo ["1493763938,185.170.185.105,10.2.0.4,35370,23, T, I, D"] é criada toda vez que um fluxo é estabelecido ou tentado ser estabelecido (caso de negação).

### <a name="how-are-bytes-and-packets-accounted-for"></a>Como são bytes e pacotes contabilizados?

Para os estados de fluxo de continuação *C* e final *E*, as contagens de bytes e pacotes são contagens agregadas do tempo do registro da tupla de fluxo anterior. Referenciando a conversação de exemplo anterior, o número total de pacotes transferidos é 1021 + 52 + 8005 + 47 = 9125. O número total de bytes transferidos é 588096 + 29952 + 4610880 + 27072 = 5256000.

### <a name="if-my-application-doesnt-understand-the-traffic-bandwidth-fields-how-does-version-2-affect-the-application"></a>Se meu aplicativo não entender os campos de largura de banda de tráfego, como a versão 2 afeta o aplicativo?

Os aplicativos que usam o log de fluxo do grupo de segurança de rede da versão 1 geralmente contam o número de fluxos exclusivos. Se nenhuma alteração na análise for feita para considerar o estado do fluxo, você poderá ver um aumento impreciso no número de fluxos relatados. A contagem de fluxos exclusivos pode ser realizada ignorando as tuplas de fluxo nos estados de fluxo *C* e *E*.

## <a name="can-i-control-the-version-format-i-receive"></a>É possível controlar o formato da versão que recebo?

 Não. Ativar e desativar os logs de fluxo não afetará o formato de saída dos logs. A mudança dos logs da versão 1 para a versão 2 ocorrerá em uma região por região. Quando uma região está atualizando da versão 1 para a versão 2, você pode ver logs de fluxo NSG em ambos os formatos. Após a conclusão do rollout, somente os logs da versão 2 estarão disponíveis.

## <a name="while-the-change-occurs-can-i-see-both-formats-for-the-same-network-security-group"></a>Enquanto a alteração ocorre, posso ver os dois formatos para o mesmo grupo de segurança de rede?

Sim. Dentro de uma região, a transição ocorrerá em uma base de endereço por mac. Como um grupo de segurança de rede pode ser aplicado a muitas máquinas, você poderá ver os logs em ambos os formatos gravados no armazenamento. Os logs serão a versão 1 ou 2.

## <a name="will-i-see-duplicate-data"></a>Haverá dados duplicados?

Não haverá duplicação de dados de registro de fluxo nos formatos. Um fluxo será gravado no formato da Versão 1 ou Versão 2, enquanto a alteração ocorrer.

## <a name="how-can-i-test-the-new-format-ahead-of-time"></a>Como testar o novo formato antecipadamente?

Sim. Você pode fazer o [download](https://aka.ms/nsgflowlogsv2blobsample) de um arquivo de log de fluxo de amostra 2 da versão a ser usado para testar sua solução.

## <a name="are-there-changes-to-configuration-and-management-of-network-security-group-flow-logging"></a>Há alterações na configuração e no gerenciamento do log de fluxo de grupo de segurança de rede?

 Não. O suporte para contas de armazenamento do Azure em assinaturas que compartilham o mesmo locatário do Active Directory do Azure foi [lançado](https://azure.microsoft.com/blog/new-azure-network-watcher-integrations-and-network-security-group-flow-logging-updates/) no início deste ano. Essa alteração ajuda a reduzir o número de contas de armazenamento necessárias ao gerenciar logs de fluxo de grupo de segurança de rede.

## <a name="questions-or-feedback"></a>Fazer perguntas ou comentários?

Estamos ansiosos para ouvir sobre sua experiência com os logs de fluxo de grupo de segurança de rede e com o Network Watcher. Você pode fornecer sugestões ou comentários, online ou por email no AzureNetworkWatcher@microsoft.com.

## <a name="version-2-sample"></a>Exemplo de versão 2

```json
{

"records": [

{

"time": "2018-08-03T17:00:54.5657764Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315592,10.1.1.6,204.79.197.200,62375,80,T,O,A,B,,,,",

"1533315595,10.1.1.6,204.79.197.200,62373,80,T,O,A,E,30,1784,92,123631",

"1533315597,10.1.1.6,204.79.197.200,62376,80,T,O,A,B,,,,",

"1533315601,10.1.1.6,204.79.197.200,62374,80,T,O,A,E,13,866,87,123079",

"1533315603,10.1.1.6,204.79.197.200,62377,80,T,O,A,B,,,,",

"1533315604,10.1.1.6,204.79.197.200,62375,80,T,O,A,E,33,1946,90,123247",

"1533315608,10.1.1.6,204.79.197.200,62378,80,T,O,A,B,,,,",

"1533315610,10.1.1.6,204.79.197.200,62376,80,T,O,A,E,20,1244,92,123355",

"1533315613,10.1.1.6,204.79.197.200,62379,80,T,O,A,B,,,,",

"1533315616,10.1.1.6,204.79.197.200,62377,80,T,O,A,E,24,1460,92,123355",

"1533315619,10.1.1.6,204.79.197.200,62380,80,T,O,A,B,,,,",

"1533315622,10.1.1.6,204.79.197.200,62378,80,T,O,A,E,23,1406,93,123409",

"1533315624,10.1.1.6,204.79.197.200,62381,80,T,O,A,B,,,,",

"1533315628,10.1.1.6,204.79.197.200,62379,80,T,O,A,E,16,1028,88,123133",

"1533315630,10.1.1.6,204.79.197.200,62382,80,T,O,A,B,,,,",

"1533315631,10.1.1.6,204.79.197.200,62380,80,T,O,A,E,13,866,87,123079",

"1533315635,10.1.1.6,204.79.197.200,62384,80,T,O,A,B,,,,",

"1533315637,10.1.1.6,204.79.197.200,62381,80,T,O,A,E,15,974,86,123025",

"1533315640,10.1.1.6,204.79.197.200,62385,80,T,O,A,B,,,,",

"1533315643,10.1.1.6,204.79.197.200,62382,80,T,O,A,E,16,1028,88,123139",

"1533315646,10.1.1.6,204.79.197.200,62386,80,T,O,A,B,,,,",

"1533315649,10.1.1.6,204.79.197.200,62384,80,T,O,A,E,21,1298,92,123355",

"1533315651,10.1.1.6,204.79.197.200,62387,80,T,O,A,B,,,,"

]

}

]

}

]

}

},

{

"time": "2018-08-03T17:01:54.5668880Z",

"systemId": "bbd48473-8ce0-4834-99bb-2e13b9b23ff8",

"category": "NetworkSecurityGroupFlowEvent",

"resourceId":
"/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FLOWLOGSV2DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/MULTITIERAPP2-NSG",

"operationName": "NetworkSecurityGroupFlowEvents",

"properties": {

"Version": 2,

"flows": [

{

"rule": "DefaultRule_DenyAllInBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315685,80.211.83.37,10.1.1.6,45321,81,T,I,D,B,,,,"

]

}

]

},

{

"rule": "DefaultRule_AllowInternetOutBound",

"flows": [

{

"mac": "002248034CC3",

"flowTuples": [

"1533315655,10.1.1.6,204.79.197.200,62385,80,T,O,A,E,20,1244,87,123079",

"1533315657,10.1.1.6,204.79.197.200,62388,80,T,O,A,B,,,,",

"1533315658,10.1.1.6,204.79.197.200,62386,80,T,O,A,E,26,1568,92,123355",

"1533315662,10.1.1.6,204.79.197.200,62389,80,T,O,A,B,,,,",

"1533315664,10.1.1.6,204.79.197.200,62387,80,T,O,A,E,15,974,88,123133",

"1533315667,10.1.1.6,204.79.197.200,62390,80,T,O,A,B,,,,",

"1533315670,10.1.1.6,204.79.197.200,62388,80,T,O,A,E,18,1136,88,123139",

"1533315673,10.1.1.6,204.79.197.200,62391,80,T,O,A,B,,,,",

"1533315676,10.1.1.6,204.79.197.200,62389,80,T,O,A,E,14,920,87,123079",

"1533315678,10.1.1.6,204.79.197.200,62392,80,T,O,A,B,,,,",

"1533315679,10.1.1.6,204.79.197.200,62390,80,T,O,A,E,31,1838,94,123739",

"1533315684,10.1.1.6,204.79.197.200,62393,80,T,O,A,B,,,,",

"1533315685,10.1.1.6,204.79.197.200,62391,80,T,O,A,E,28,1676,101,141199",

"1533315689,10.1.1.6,204.79.197.200,62394,80,T,O,A,B,,,,",

"1533315691,10.1.1.6,204.79.197.200,62392,80,T,O,A,E,21,1298,93,123409",

"1533315694,10.1.1.6,204.79.197.200,62395,80,T,O,A,B,,,,",

"1533315697,10.1.1.6,204.79.197.200,62393,80,T,O,A,E,26,1568,91,123393",

"1533315700,10.1.1.6,204.79.197.200,62396,80,T,O,A,B,,,,",

"1533315703,10.1.1.6,204.79.197.200,62394,80,T,O,A,E,14,920,89,123187",

"1533315705,10.1.1.6,204.79.197.200,62397,80,T,O,A,B,,,,",

"1533315706,10.1.1.6,204.79.197.200,62395,80,T,O,A,E,13,866,87,123079",

"1533315711,10.1.1.6,204.79.197.200,62398,80,T,O,A,B,,,,"

]

}

]

}

]

}

}

]

}
```

## <a name="version-1-sample"></a>Exemplo de versão 1

```json
{ 

    "records": [ 

        { 

            "time": "2017-02-16T22:00:32.8950000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A", 

                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A", 

                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A", 

                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A" 

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        }, 

        { 

            "time": "2017-02-16T22:01:32.8960000Z", 

            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434", 

            "category": "NetworkSecurityGroupFlowEvent", 

            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG", 

            "operationName": "NetworkSecurityGroupFlowEvents", 

            "properties": { 

                "Version": 1, 

                "flows": [ 

                    { 

                        "rule": "DefaultRule_DenyAllInBound", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D" 

                                ] 

                            } 

                        ] 

                    }, 

                    { 

                        "rule": "UserRule_default-allow-rdp", 

                        "flows": [ 

                            { 

                                "mac": "000D3AF8801A", 

                                "flowTuples": [ 

                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",

                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",

                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"

                                ] 

                            } 

                        ] 

                    } 

                ] 

            } 

        } 

    ] 
}
```