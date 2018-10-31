---
title: Métricas personalizadas no Azure Monitor
description: Saiba mais sobre as métricas personalizadas no Azure Monitor e como elas são modeladas.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1bdf1e1f5e58ecb0939d5876e0cef349e32de517
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344737"
---
# <a name="custom-metrics-in-azure-monitor"></a>Métricas personalizadas no Azure Monitor

Ao implantar recursos e aplicativos no Azure, é melhor começar a coletar dados telemétricos para obter percepções sobre sua integridade e desempenho. O Azure disponibiliza alguns dados telemétricos prontos para usar conforme os recursos são implantados. Eles são chamados de métricas padrão ou de plataforma. No entanto, essas métricas são limitadas por natureza. Você pode coletar alguns indicadores de desempenho personalizados ou métricas específicas de negócios para fornecer informações mais aprofundadas.
Essas métricas “personalizadas” podem ser coletadas pela telemetria do aplicativo, por um agente em execução em seus recursos do Azure ou até mesmo no sistema de monitoramento de fora para dentro, para serem enviadas diretamente ao Azure Monitor. Depois de publicadas no Azure Monitor, você pode navegar, consultar e fazer alertas para métricas personalizadas de seus recursos e aplicativos do Azure comparando-as com as métricas padrão emitidas pelo Azure.

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas
As Métricas Personalizadas podem ser enviadas para o Azure Monitor por vários métodos.
- Instrumente seu aplicativo usando o SDK do Application Insights e envio de telemetria personalizada para o Azure Monitor 
- Instalação da extensão de diagnóstico do Windows em sua [VM do Azure](metrics-store-custom-guestos-resource-manager-vm.md), [Conjunto de Dimensionamento de Máquinas Virtuais](metrics-store-custom-guestos-resource-manager-vmss.md), [VM clássica](metrics-store-custom-guestos-classic-vm.md) ou [Serviço de nuvem clássico](metrics-store-custom-guestos-classic-cloud-service.md) e envio de contadores de desempenho para o Azure Monitor 
- Instale o [agente InfluxDB Telegraf](metrics-store-custom-linux-telegraf.md) em sua VM Linux do Azure e envio de métricas usando o plug-in de saída do Azure Monitor
- Envie métricas personalizadas [diretamente à API REST do Azure Monitor](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

Ao enviar as métricas personalizadas para o Azure Monitor, cada ponto de dados (ou valor) relatado deve conter as seguintes informações:

### <a name="authentication"></a>Autenticação
Para enviar métricas personalizadas ao Azure Monitor, a entidade que envia a métrica deve ter um token válido do Azure Active Directory no cabeçalho da solicitação “Portador”. Há algumas maneiras para adquirir um token de portador válido:
1. [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) – fornece uma identidade a um recurso do Azure em si (como uma VM). A MSI foi projetada para fornecer aos recursos de permissões para executar determinadas operações, por exemplo, permitir que um recurso emita métricas sobre si mesmo. Um recurso (ou sua MSI) pode receber permissões de “Editor de Métricas de Monitoramento” em outro recurso, permitindo que a MSI emita métricas também para outros recursos.
2. [Entidade de Serviço do AAD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals): o cenário aqui é um aplicativo do AAD (serviço) que pode receber permissões para emitir métricas sobre um recurso do Azure.
Para autenticar a solicitação, o Azure Monitor valida o token de aplicativo usando chaves públicas do AAD. A função “Editor de Métricas de Monitoramento” existente já tem essa permissão, a qual está disponível no portal do Azure. A entidade de serviço, dependendo dos recursos para os quais ela emitirá métricas personalizadas, poderá receber a função “Editor de Métricas de Monitoramento” no escopo necessário (assinatura, grupo de recursos ou recurso específico).

> [!NOTE]
> Ao solicitar um token do AAD para emitir as métricas personalizadas, verifique se o público-alvo/recurso para o qual o token está sendo solicitado é https://monitoring.azure.com/ (não se esqueça de incluir '/' à direita)

### <a name="subject"></a>Assunto
Essa propriedade indica a ID de recurso do Azure para a qual a métrica personalizada é relatada. Essas informações serão codificadas na URL da chamada à API que está sendo feita. Cada API só pode enviar valores de métrica para um único recurso do Azure.

> [!NOTE]
> Não é possível emitir métricas personalizadas em relação à ID do recurso de um grupo de recursos ou assinatura.
>
>

### <a name="region"></a>Região
Essa propriedade captura a região do Azure de implantação do recurso para o qual você está emitindo métricas. As métricas devem ser emitidas para o ponto de extremidade regional do Azure Monitor que está na mesma região em que o recurso está implantado. Por exemplo, métricas personalizadas para uma VM implantada no Oeste dos EUA devem ser enviadas para o ponto de extremidade regional do Azure Monitor do Oeste dos EUA. As informações de região também estão codificadas na URL da chamada à API.

> [!NOTE]
> Durante a versão prévia pública, as métricas personalizadas só ficam disponibilizadas em um subconjunto de regiões do Azure. Uma lista de regiões com suporte é fornecida em uma seção posterior deste artigo.
>
>

### <a name="timestamp"></a>Timestamp
Cada ponto de dados enviado ao Azure Monitor deve estar marcado com um carimbo de data/hora. Esse carimbo captura a data e a hora em que o valor da métrica foi medido/coletado. O Azure Monitor aceitará dados de métrica com carimbos de data/hora de no máximo 20 minutos no passado ou até 5 minutos no futuro.

### <a name="namespace"></a>Namespace
Namespaces são uma maneira de categorizar ou agrupar métricas semelhantes. Os namespaces permitem isolar grupos de métricas que podem coletar insights ou indicadores de desempenho diferentes. Por exemplo, você poderia ter um namespace chamado *ContosoMemoryMetrics*, que utilizou métricas de rastreamento de uso de memória para analisar o perfil de seu aplicativo, e outro namespace chamado *ContosoAppTransaction*, para rastrear todas as métricas de transações de usuário em seu aplicativo.

### <a name="name"></a>NOME
O nome da métrica que está sendo relatada. Normalmente, o nome é descritivo para ajudar a identificar o que está sendo medido. Por exemplo, uma métrica que está medindo o número de bytes de memória em uso em determinada VM pode ter um nome de métrica como “Bytes de Memória em Uso”.

### <a name="dimension-keys"></a>Chaves de dimensão
Uma dimensão é um par de chaves/valores que ajuda a descrever outras características sobre a métrica que está sendo coletada. As características adicionais permitem coletar mais informações sobre a métrica para gerar insights mais aprofundados. Por exemplo, a métrica “Bytes da Memória em Uso” poderia ter uma chave de dimensão chamada “Processo”, que captura quantos bytes de memória cada processo em uma VM está consumindo. Isso permite filtrar a métrica para ver quantos processos específicos de memória estão sendo usados ou identificar os cinco principais processos por uso de memória.
Cada métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores da dimensão
Ao relatar um ponto de dados de métrica, para cada chave de dimensão na métrica relatada há um valor de dimensão correspondente. Por exemplo, se você quisesse relatar a memória usada pelo ContosoApp na sua VM:

* O nome da métrica seria *Bytes de Memória em Uso*
* A chave de dimensão seria *Processo*
* O valor de dimensão seria *ContosoApp.exe*

Ao publicar um valor de métrica, você pode especificar apenas um valor de dimensão por chave de dimensão. Se você coletar o mesmo Uso de memória para vários processos na VM, poderá relatar diversos valores de métrica para esse carimbo de data/hora. Cada valor de métrica especificaria um valor de dimensão diferente para a chave de dimensão Processo.

### <a name="metric-values"></a>Valores métricos
O Azure Monitor armazena todas as métricas em intervalos com granularidade de um minuto. Compreendemos que uma métrica talvez tenha de ser coletada várias vezes (por exemplo, Utilização da CPU) ou medida em muitos eventos diferentes (por exemplo, Latências de transação de entrada) em um minuto específico. Para limitar o número de valores brutos que você precisa emitir e pagar no Azure Monitor, é possível pré-agregar os valores localmente e emiti-los:

* Mín.: o valor mínimo de todas as amostras/medidas observado durante o minuto
* Máx.: o valor máximo de todas as amostras/medidas observado durante o minuto
* Soma: a soma de todos os valores observados de todas as amostras/medidas durante o minuto
* Contagem: a quantidade de exemplos/medidas calculados durante o minuto

Por exemplo, se houve quatro transações de entrada em seu aplicativo durante um dado minuto e as latências medidas resultantes para cada uma foram:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

A publicação de métrica resultante para o Azure Monitor será:
* Mín.: 4
* Máx.: 16
* Soma: 40
* Contagem: 4

Se seu aplicativo não é capaz de pré-agregar localmente e precisa emitir cada exemplo ou evento separado imediatamente após a coleta, você pode emitir os valores de medida brutos.
Por exemplo, sempre que uma transação de entrada ocorrer em seu aplicativo, você poderá publicar uma métrica para o Azure Monitor com apenas uma medida. Portanto, para uma transação de entrada que levou 12 ms, a publicação da métrica seria:
* Mín.: 12
* Máx.: 12
* Soma: 12
* Contagem: 1

Esse processo permite que você emita vários valores para a mesma combinação de métrica + dimensão durante determinado minuto. O Azure Monitor, em seguida, usará todos os valores brutos emitidos para um dado minuto e os agregará.

### <a name="sample-custom-metric-publication"></a>Publicação de métrica personalizada de exemplo
No exemplo a seguir, você criará uma métrica personalizada chamada “Bytes de Memória em Uso” no namespace da métrica “Perfil de Memória” para uma máquina virtual. A métrica tem uma única dimensão chamada “Processo”. Para o determinado carimbo de data/hora, vamos emitir valores de métrica para dois processos diferentes:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]
> o Application Insights — a extensão WAD (Diagnóstico do Microsoft Azure) — e o agente InfluxData Telegraf já estão configurados para emitir os valores de métrica em relação ao ponto de extremidade regional correto, além de executar todas as propriedades acima em cada emissão.
>
>

## <a name="custom-metric-definitions"></a>Definições de métrica personalizada
Não é necessário definir uma métrica personalizada predefinida no Azure Monitor antes que ela seja emitida. Como cada ponto de dados de métrica publicado contém o namespace, o nome e as informações de dimensão, na primeira vez em que uma métrica personalizada é emitida para o Azure Monitor, uma definição de métrica é criada automaticamente. Essa definição de métrica é encontrada em qualquer recurso para o qual métrica foi emitida por meio das definições de métrica.

> [!NOTE]
> O Azure Monitor ainda não dá suporte à definição de “Unidades” para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Usando métricas personalizadas
Depois que as métricas personalizadas são enviadas para o Azure Monitor, você pode procurá-las no portal do Azure, consultá-las por meio das APIs REST do Azure Monitor ou criar alertas nelas para poder ser notificado quando determinadas condições forem atendidas.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procurar suas métricas personalizadas no portal do Azure
1.  Vá para o [Portal do Azure](https://portal.azure.com)
2.  Selecione a folha Monitor
3.  Clique em Métricas
4.  Selecione um recurso para o qual você tenha emitido métricas personalizadas
5.  Selecione o namespace de métricas da métrica personalizada
6.  Selecione a métrica personalizada

## <a name="supported-regions"></a>Regiões com Suporte
Durante a versão prévia pública, a capacidade de publicar métricas personalizadas só está disponível para um subconjunto de regiões do Azure. Isso significa que as métricas só podem ser publicadas para recursos em alguma região com suporte. A tabela a seguir lista o conjunto de regiões do Azure com suporte para métricas personalizadas e as métricas de ponto de extremidade correspondentes para recursos que deveriam ser publicadas nelas.

|Região do Azure|Prefixo de ponto de extremidade regional|
|---|---|
|Leste dos EUA|https://eastus.monitoring.azure.com/|
|Centro-Sul dos Estados Unidos|https://southcentralus.monitoring.azure.com/|
|Centro-Oeste dos EUA|https://westcentralus.monitoring.azure.com/|
|Oeste dos EUA 2|https://westus2.monitoring.azure.com/|
|Sudeste Asiático|https://southeastasia.monitoring.azure.com/|
|Norte da Europa|https://northeurope.monitoring.azure.com/|
|Europa Ocidental|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Cotas e limites
O Azure Monitor impõe os limites de uso a seguir sobre as métricas personalizadas.

|Categoria|Limite|
|---|---|
|Série temporal ativa/assinaturas/região|50.000|
|Chaves de Dimensão por métrica|10|
|Tamanho da cadeia de caracteres para namespaces de métrica, nomes de métrica, chaves de dimensão e valores de dimensão|256 caracteres|
Uma série temporal ativa é definida como qualquer combinação exclusiva de métrica, chave de dimensão e valor de dimensão que tenha tido valores de métrica publicados nas últimas 12 horas.

## <a name="next-steps"></a>Próximas etapas
Usar métricas personalizadas de serviços diferentes 
 - [Máquina Virtual](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Conjunto de Dimensionamento de Máquinas Virtuais](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Máquina Virtual (Clássico)](metrics-store-custom-guestos-classic-vm.md)
 - [Máquina Virtual Linux usando o agente Telegraf](metrics-store-custom-linux-telegraf.md)
 - [API REST](metrics-store-custom-rest-api.md)
 - [Serviço de nuvem (clássico)](metrics-store-custom-guestos-classic-cloud-service.md)
 