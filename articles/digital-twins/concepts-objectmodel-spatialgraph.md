---
title: Noções básicas sobre modelos de objeto e grafos de inteligência espacial dos Gêmeos Digitais | Microsoft Docs
description: Como usar Gêmeos Digitais do Azure para modelar relacionamentos entre pessoas, locais e dispositivos
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c1d66e0b58567244f8c1406ee258c9311994ff20
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215099"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Noções básicas sobre modelos de objeto e grafos de inteligência espacial dos Gêmeos Digitais

O recurso Gêmeos Digitais do Azure é um serviço de IoT do Azure que possibilita representações virtuais abrangentes de ambientes físicos e dispositivos, sensores e pessoas associados. Isso melhora o desenvolvimento organizando conceitos específicos do domínio em modelos úteis, que estão, em si, situados em um grafo de inteligência espacial. Esses conceitos modelam fielmente os relacionamentos e as interações entre pessoas, espaços e dispositivos.

_Modelos de Objeto de Gêmeos Digitais_ descrevem conceitos, categorias e propriedades específicos do domínio. Modelos predefinidos pelos usuários que buscam adaptar a solução às suas necessidades específicas. Juntos, esses Modelos de Objeto de Gêmeos Digitais predefinidos formam uma _Ontologia_. Uma ontologia de construção inteligente descreveria regiões, locais, andares, escritórios, zonas, salas de conferência e salas de foco. Uma ontologia de rede elétrica descreveria várias estações de energia, subestações, recursos de energia e clientes. Essas ontologias e Modelos de Objeto de Gêmeos Digitais permitem personalizar Gêmeos Digitais do Azure para necessidades e cenários diversos.

Com _Modelos de Objeto de Gêmeos Digitais_ e _Ontologia_ em vigor, é possível preencher um _Grafo Espacial_. Grafos espaciais são representações virtuais de muitas relações entre espaços, dispositivos e pessoas relevantes para uma solução de IoT. O diagrama a seguir mostra um exemplo de um grafo espacial usando uma ontologia de construção inteligente.

![Construção de grafo espacial de Gêmeos Digitais][1]

<a id="model"></a>

O grafo espacial reúne usuários, dispositivos, sensores e espaços. Cada um é vinculado de modo a modelar o mundo real: o local 43 tem quatro andares, cada um com várias áreas diferentes. Os usuários estão associados com suas estações de trabalho e recebem acesso a partes do grafo.  Por exemplo, um administrador teria direitos para fazer alterações ao grafo espacial enquanto um visitante poderia ter direito apenas para exibir determinados dados de criação.

## <a name="digital-twins-object-models"></a>Modelos de objeto de Gêmeos Digitais

Modelos de objeto de Gêmeos Digitais dão suporte a estas categorias principais de objetos:

- **Espaços** são locais físicos ou virtuais, por exemplo `Tenant`, `Customer`, `Region`, `Venue`.
- **Dispositivos** são equipamentos virtuais ou físicos, por exemplo `AwesomeCompany Device`, `Raspberry Pi 3`.
- **Sensores** são objetos que detectam eventos, por exemplo `AwesomeCompany Temperature Sensor`, `AwesomeCompany Presence Sensor`.
- **Usuários** identificam ocupantes e suas características.

Outras categorias de objetos são:

- **Recursos** são anexados a um espaço e geralmente representam os recursos do Azure a serem usados por objetos no grafo espacial, por exemplo `IoTHub`.
- **Blobs** são anexados a objetos (como espaços, dispositivos, sensores e usuários) e usados como arquivos com o tipo MIME e metadados, por exemplo `maps`, `pictures`, `manuals`.
- **Tipos Estendidos** são enumerações extensíveis que aumentam as entidades com características específicas, por exemplo `SpaceType`, `SpaceSubtype`.
- **Ontologias** representam um conjunto de tipos estendidos, por exemplo `Default`, `Building`, `BACnet`, `EnergyGrid`.
- **Chaves e Valores de Propriedade** são características personalizadas de usuários, dispositivos, sensores e espaços. Podem ser usados além de características internas, por exemplo `DeltaProcessingRefreshTime` como Chave e `10` como Valor.
- **Funções** são conjuntos de permissões atribuídas a usuários e dispositivos no grafo espacial, por exemplo `Space Administrator`, `User Administrator`, `Device Administrator`.
- As **Atribuições de Função** são a associação entre uma função e um objeto no grafo espacial, por exemplo, concedendo uma permissão de entidade de serviço para gerenciar um espaço no grafo espacial ou de um usuário.
- **Repositórios de Chaves de Segurança** fornecem as chaves de segurança para todos os dispositivos na hierarquia em um dado objeto de espaço para permitir que o dispositivo comunique-se com segurança com o serviço de Gêmeos Digitais.
- **UDFs**, ou **Funções Definidas pelo Usuário**, permitem processamento de telemetria do sensor personalizável no grafo espacial. Por exemplo, uma UDF pode definir um valor do sensor, executar lógica personalizada com base em leituras de sensor definir a saída para um espaço, anexar metadados a um espaço e enviar notificações quando as condições predefinidas são atendidas. Atualmente, UDFs podem ser escritos em JavaScript.
- **Correspondentes** são objetos que determinam quais UDFs serão executadas para uma mensagem de telemetria determinada.
- **Pontos de Extremidade** são os locais para os quais as mensagens de telemetria e eventos de Gêmeos Digitais podem ser roteados, por exemplo `Event Hub`, `Service Bus`, `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Grafo de inteligência espacial

**Grafo espacial** é o grafo hierárquico de espaços, dispositivos e pessoas definido no **Modelo de objeto de Gêmeos Digitais**. O grafo espacial dá suporte para _herança_, _filtragem_, _travessia_, _escalabilidade_ e _extensibilidade_. Os usuários podem gerenciar e interagir com seu grafo espacial com uma coleção de APIs REST (veja abaixo).

O usuário que implanta um serviço de Gêmeos Digitais na sua assinatura se torna o administrador global do nó raiz, concedendo automaticamente acesso completo a toda a estrutura. Este usuário, em seguida, pode provisionar espaços no graph usando a API de espaço. Dispositivos poderiam ser provisionados usando a API, sensores poderiam ser provisionados usando a API etc. Também oferecemos [ferramentas de software livre](https://github.com/Azure-Samples/digital-twins-samples-csharp) para provisionar o grafo em massa.

A _herança_ do grafo aplica-se a permissões e propriedades que descendem de um nó pai para todos os nós abaixo dele. Por exemplo, quando uma função é atribuída a um usuário em um determinado nó, o usuário tem permissões da função para o nó determinado e todos os nós abaixo dele. Além disso, cada chave de propriedade e tipo estendido definido para um determinado nó será herdado por todos os nós abaixo desse nó.

A _filtragem_ de grafo permite aos usuários restringir os resultados de solicitação por IDs, nome, tipos, subtipos, espaço pai, espaços associados, tipos de dados de sensor, chaves e valores de propriedade, *travessia*, *minLevel*, *maxLevel*, e outros parâmetros de filtro OData.

A _travessia_ de grafo permite aos usuários navegar no gráfico espacial usando sua profundidade e sua amplitude. Para profundidade, é possível percorrer o grafo usando parâmetros de navegação de baixo para cima ou de cima para baixo *travessa*, *minLevel*, *maxLevel*. Para amplitude, é possível navegar no grafo para obter nós irmãos diretamente anexados a um espaço pai ou um de seus descendentes. Ao consultar um objeto, você pode obter todos os objetos relacionados com relacionamentos com o objeto usando o parâmetro *inclui* das APIs GET.

O recurso Gêmeos Digitais do Azure garante a _escalabilidade_ do grafo, portanto, pode atender suas cargas de trabalho do mundo real. O recurso Gêmeos Digitais pode ser usado para representar grandes portfólios de imóveis, infraestrutura, dispositivos, sensores, telemetria e muito mais.

A _extensibilidade_ do grafo permite que os usuários personalizem os Modelos de Objeto de Gêmeos Digitais subjacentes com novos tipos e ontologias. Os dados dos gêmeos digitais também podem ser aprimorados com valores e propriedades extensíveis.

### <a name="spatial-intelligence-graph-management-apis"></a>APIs de Gerenciamento de grafo de inteligência espacial

Depois de implantar Gêmeos Digitais do Azure usando o [portal do Azure](https://portal.azure.com), a URL do [Swagger](https://swagger.io/tools/swagger-ui/) das APIs de Gerenciamento é gerada automaticamente e será exibida na seção **Visão Geral** do portal do Azure com o seguinte formato:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Nome do Atributo Personalizado | Substitua por |
| --- | --- |
| *yourInstanceName* | O nome da sua instância de Gêmeos Digitais do Azure |
| *yourLocation* | Em qual região do servidor de sua instância está hospedada |

 É possível ver o formato de URL completo em uso na imagem abaixo:

![API de Gerenciamento do Portal de Gêmeos Digitais][2]

Para obter mais detalhes sobre como usar Grafos de Inteligência Espacial, visite a versão prévia de APIs de Gerenciamento de Gêmeos Digitais do Azure.

> [!TIP]
> Uma versão prévia do Swagger é fornecida para demonstrar o conjunto de recursos da API.
> Ele está hospedado no [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Saiba mais sobre [como usar o Swagger](how-to-use-swagger.md).

Todas as chamadas à API devem ser autenticadas usando [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). As APIs seguem as [convenções de Diretrizes da API REST da Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A maioria das APIs que retorna coleções é compatível com opções de consulta do sistema [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a conectividade do dispositivo e como enviar mensagens de telemetria ao serviço de Gêmeos Digitais do Azure, leia [Entrada de telemetria e conectividade do dispositivo de Gêmeos Digitais do Azure](concepts-device-ingress.md).

Para saber mais sobre restrições e limitações da API de Gerenciamento, leia [Limitações e gerenciamento de API de Gêmeos Digitais do Azure](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
