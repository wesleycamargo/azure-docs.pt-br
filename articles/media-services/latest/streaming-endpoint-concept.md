---
title: Pontos de extremidade de streaming nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são os pontos de extremidade de streaming e como são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/16/2019
ms.author: juliako
ms.openlocfilehash: 4a29da2b070133f87ca5fdab0be607368c83790f
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2019
ms.locfileid: "59999443"
---
# <a name="streaming-endpoints"></a>Ponto de extremidade de streaming

No AMS (Serviços de Mídia do Microsoft Azure), a entidade [Ponto de Extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints) representa um serviço de streaming que pode fornecer conteúdo diretamente a um aplicativo de player cliente ou a uma CDN (Rede de Distribuição de Conteúdo) para distribuição adicional. O fluxo de saída de um serviço de **Ponto de Extremidade de Streaming** pode ser uma transmissão ao vivo ou um Ativo de vídeo sob demanda em sua conta dos Serviços de Mídia. Quando você cria uma conta de Serviços de Mídia, um Ponto de Extremidade de Streaming **padrão** é criado em um estado parado. Não é possível excluir o Ponto de Extremidade de Streaming **padrão**. Ponto de Extremidade de Streaming adicionais podem ser criados na conta. 

> [!NOTE]
> Para começar a transmitir vídeos, é necessário iniciar o **Ponto de extremidade de streaming** do qual deseja transmitir o vídeo. 
>  
> Você será cobrado apenas quando seu ponto de extremidade de streaming estiver em estado de execução.

## <a name="naming-convention"></a>Convenção de nomenclatura

Para o ponto de extremidade padrão: `{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

Para pontos de extremidade adicionais: `{EndpointName}-{AccountName}-{DatacenterAbbreviation}.streaming.media.azure.net`

## <a name="types"></a>Tipos  

Há dois tipos de **Ponto de extremidade de streaming**: Standard e **Premium**. O tipo é definido pelo número de unidades de escala (`scaleUnits`) alocadas para o ponto de extremidade de streaming. 

A tabela descreve os tipos:  

|Type|Unidades de escala|DESCRIÇÃO|
|--------|--------|--------|  
|**Ponto de Extremidade de Streaming Standard** (recomendado)|0|O padrão de ponto de extremidade de Streaming é uma **padrão** de tipo, mas pode ser alterado para o tipo Premium.<br/> O tipo padrão é a opção recomendada para virtualmente todos os cenários de transmissão e tamanhos de público-alvo. O tipo **Standard** dimensiona automaticamente a largura de banda de saída. A taxa de transferência desse tipo de ponto de extremidade de Streaming é até 600 Mbps. Fragmentos de vídeo armazenado em cache na CDN, não use a largura de banda do ponto de extremidade de Streaming.<br/>Para clientes com requisitos extremamente exigentes, os Serviços de Mídia oferecem pontos de extremidade de streaming **Premium**, que podem ser usados para dimensionar a capacidade dos maiores públicos-alvo da Internet. Se você espera grandes públicos e visualizadores simultâneos, entre em contato conosco em amsstreaming\@microsoft.com para obter orientação sobre como se você precisa mover para o **Premium** tipo. |
|**Ponto de Extremidade de Streaming Premium**|>0|Os pontos de extremidade do streaming **Premium** são adequados para as cargas de trabalho avançadas, fornecendo uma capacidade de largura de banda dimensionável e dedicada. É possível mudar para um tipo **Premium**, ajustando `scaleUnits`. `scaleUnits` fornece capacidade de saída dedicada que pode ser comprada em incrementos de 200 Mbps. Ao usar o tipo **Premium**, cada unidade habilitada fornece capacidade adicional de largura de banda ao aplicativo. |
 
## <a name="comparing-streaming-types"></a>Comparando tipos de streaming

### <a name="features"></a>Recursos

Recurso|Standard|Premium
---|---|---
Gratuito pelos primeiros 15 dias| Sim |Não 
Produtividade |Até 600 Mbps quando a Azure CDN não é usada. Escala com CDN.|200 Mbps por UA (unidade de streaming). Escala com CDN.
CDN|Azure CDN, CDN de terceiros ou sem CDN.|Azure CDN, CDN de terceiros ou sem CDN.
A cobrança é rateada| Diário|Diário
Criptografia dinâmica|Sim|Sim
Empacotamento dinâmico|Sim|Sim
Escala|Escala verticalmente automaticamente com a taxa de transferência de destino.|Unidades de streaming adicionais
Host de filtragem/G20/personalizado da IP <sup>1</sup>|Sim|Sim
Download progressivo|Sim|Sim
Uso recomendado |Recomendado para a grande maioria dos cenários de streaming.|Uso profissional.<br/>Se você achar que pode ter necessidades além do Standard. Contate-nos (amsstreaming@microsoft.com) se você espera ter uma audiência simultânea superior a 50.000 visualizadores.

<sup>1</sup> só é usado diretamente no ponto de extremidade de Streaming quando o CDN não está habilitado no ponto de extremidade.

## <a name="properties"></a>propriedades 

Esta seção fornece detalhes sobre algumas das propriedades de fluxo contínuo do ponto de extremidade. Para exemplos de como criar um novo ponto de extremidade de streaming e descrições de todas as propriedades, consulte [Ponto de Extremidade de Streaming](https://docs.microsoft.com/rest/api/media/streamingendpoints/create). 

- `accessControl` – Usado para definir as seguintes configurações de segurança para esse ponto de extremidade de streaming: Chaves de autenticação de cabeçalho de assinatura do Akamai e endereços IP que têm permissão para se conectar a esse ponto de extremidade.<br />Essa propriedade só pode ser definida quando `cdnEnabled` é definido como false.
- `cdnEnabled` -Indica se a integração de CDN do Azure para esse ponto de extremidade de streaming é ativado (desativado por padrão). Se você definir `cdnEnabled` como verdadeiro, as seguintes configurações serão desabilitadas: `customHostNames` e `accessControl`.
  
    Nem todos os data centers dão suporte para integração da CDN do Azure. Para verificar se seu data center tem a integração da CDN do Azure disponível, faça o seguinte:
 
  - Tente definir o `cdnEnabled` como true.
  - Verifique o resultado retornado para um `HTTP Error Code 412` (PreconditionFailed) com uma mensagem de "Propriedade CdnEnabled de ponto de extremidade de Streaming não pode ser definida como true, pois a funcionalidade de CDN não está disponível na região atual." 

    Se você receber esse erro, o data center não dará suporte. Você deve experimentar outro data center.
- `cdnProfile` -Quando `cdnEnabled` é definido como true, você também pode passar `cdnProfile` valores. `cdnProfile` é o nome do perfil CDN no qual o ponto de extremidade CDN será criado. Você pode fornecer um cdnProfile existente ou usar um novo. Se o valor for NULL e `cdnEnabled` for verdadeiro, o valor padrão "AzureMediaStreamingPlatformCdnProfile" será usado. Se o perfil `cdnProfile` fornecido já existir, um ponto de extremidade será criado sob ele. Se o perfil não existe, um novo perfil automaticamente será criado.
- `cdnProvider` -Quando CDN está habilitado, você também pode passar `cdnProvider` valores. `cdnProvider` controla qual provedor será usado. Atualmente, três valores são suportados: "StandardVerizon", "PremiumVerizon" e "StandardAkamai". Se nenhum valor for fornecido e `cdnEnabled` for true, "StandardVerizon" é usado (ou seja, o valor padrão).
- `crossSiteAccessPolicies` – Usado para especificar as políticas de acesso entre sites para vários clientes. Para obter mais informações, consulte [Especificação de arquivo de política entre domínios](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) e [Disponibilizando um serviço entre limites de domínios](https://msdn.microsoft.com/library/cc197955\(v=vs.95\).aspx).<br/>As configurações se aplicam somente para Smooth Streaming.
- `customHostNames` – Usado para configurar um ponto de extremidade de Streaming para aceitar o tráfego direcionado para um nome de host personalizado.  Essa propriedade é válida para Standard e pontos de extremidade de Streaming Premium e pode ser definida quando `cdnEnabled`: false.
    
    A propriedade do nome de domínio deve ser confirmada pelos serviços de mídia. Os serviços de mídia verifica a propriedade de nome de domínio, exigindo um `CName` registro que contém a ID da conta dos serviços de mídia como um componente a ser adicionado ao domínio em uso. Por exemplo, para "sports.contoso.com" ser usado como um nome do host personalizado para o ponto de extremidade de streaming, um registro para `<accountId>.contoso.com` deve ser configurado para apontar para um dos nomes do host de verificação dos Serviços de Mídia. O nome do host de verificação é composto por verifydns.\<mediaservices-dns-zone>. 

    A seguir estão as zonas DNS a ser usado no registro de verificação para diferentes regiões do Azure.
  
  - América do Norte, Europa, Singapura, RAE de Hong Kong, Japão:
      
    - `media.azure.net`
    - `verifydns.media.azure.net`
      
  - China:
        
    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`
        
    Por exemplo, um `CName` registro que mapeia "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" para "verifydns.media.azure.net" prova que a ID de serviços de mídia 945a4c4e-28ea-45cd-8ccb-a519f6b700ad tem, portanto, a propriedade do domínio contoso.com, permite que qualquer nome em contoso.com seja usado como um nome de host personalizado para um ponto de extremidade de streaming dessa conta. Para localizar o valor da ID do Serviço de Mídia, acesse o [portal do Azure](https://portal.azure.com/) e selecione a conta de Serviço de Mídia. O **ID da conta** aparece no canto superior direito da página.
        
    Se houver uma tentativa de definir um nome do host personalizado sem uma verificação adequada do registro `CName`, a resposta DNS falhará e, em seguida, será armazenada em cache por algum tempo. Depois que um registro adequado estiver em vigor, poderá demorar um pouco até que a resposta armazenada em cache seja revalidada. Dependendo do provedor DNS do domínio personalizado, pode levar de alguns minutos a uma hora para revalidar o registro.
        
    Além de `CName` que mapeia `<accountId>.<parent domain>` para `verifydns.<mediaservices-dns-zone>`, você deve criar outro `CName` que mapeia o nome de host personalizado (por exemplo, `sports.contoso.com`) ao nome de host de sua mídia dos serviços de Streaming do ponto de extremidade (por exemplo, `amstest-usea.streaming.media.azure.net`).
 
    > [!NOTE]
    > Localizado no mesmo data center, os pontos de extremidade de streaming não podem compartilhar o mesmo nome de host personalizado.

    Atualmente, os serviços de mídia não dá suporte a SSL com domínios personalizados. 
    
- `maxCacheAge` -Substitui o cache HTTP de max-age padrão controle cabeçalho definido pelo ponto de extremidade de streaming nos fragmentos de mídia e manifestos sob demanda. O valor é definido em segundos.
- `resourceState` -

    - Parado – o estado inicial de um ponto de extremidade de Streaming após a criação
    - Iniciando - faz a transição para o estado de execução
    - Em execução - é capaz de transmitir conteúdo aos clientes
    - Colocação em escala - escala unidades estão sendo aumentaram ou diminuíram
    - Parando - faz a transição para estado de parado
    - Excluindo - está sendo excluído
    
- `scaleUnits` -Fornece capacidade de egresso dedicada que pode ser comprada em incrementos de 200 Mbps. Se você precisar mudar para um tipo **Premium**, ajuste `scaleUnits`.

## <a name="working-with-cdn"></a>Trabalhando com CDN

Na maioria dos casos, é necessário ter a CDN habilitada. No entanto, se você estiver antecipando uma simultaneidade máxima menor que 500 visualizadores, então, é recomendável desabilitar a CDN, já que a CDN dimensiona melhor com simultaneidade.

### <a name="considerations"></a>Considerações

* O ponto de extremidade de streaming `hostname` e a URL de streaming permanecem os mesmos, independentemente de você habilitar a CDN ou não.
* Se você precisar de capacidade de testar seu conteúdo com ou sem CDN, você pode criar outro Streaming de ponto de extremidade CDN não esteja habilitada.

### <a name="detailed-explanation-of-how-caching-works"></a>Explicação detalhada de como o armazenamento em cache funciona

Não há nenhum valor de largura de banda específico ao adicionar a CDN porque a quantidade de largura de banda necessária para um ponto de extremidade de streaming habilitado para CDN varia. Depende muito do tipo de conteúdo, da popularidade, das taxas de bits e dos protocolos. A CDN está apenas armazenando em cache o que está sendo solicitado. Isso significa que o conteúdo popular será atendido diretamente pela CDN – contanto que o fragmento de vídeo seja armazenado em cache. É provável que o conteúdo ao vivo seja armazenado em cache, porque normalmente há muitas pessoas assistindo exatamente o mesmo conteúdo. O conteúdo sob demanda pode ser um pouco mais complicado porque você pode ter algum conteúdo que seja popular e outro que não seja. Se você tem milhões de ativos de vídeo em que nenhum deles é popular (apenas 1 ou 2 visualizadores por semana), mas tem milhares de pessoas assistindo a todos os diferentes vídeos, a CDN torna-se muito menos eficaz. Com essa perda no cache, você aumenta a carga no ponto de extremidade de streaming.
 
Também é necessário considerar como o streaming adaptável funciona. Cada fragmento de vídeo individual é armazenado em cache como sua própria entidade. Por exemplo, se a primeira vez que um determinado vídeo é assistido e a pessoa ignora assistindo apenas alguns segundos de trechos aleatórios, apenas os fragmentos de vídeo associados ao que a pessoa assistiu são armazenados em cache no CDN. Com o streaming adaptável, você normalmente tem de 5 a 7 taxas de bits diferentes de vídeo. Se uma pessoa estiver assistindo a uma taxa de bits e outra pessoa estiver assistindo a uma taxa de bits diferente, cada uma delas será armazenada separadamente na CDN. Mesmo que duas pessoas estejam assistindo a mesma taxa de bits, é possível que estejam transmitindo por stream em diferentes protocolos. Cada protocolo (HLS, MPEG-DASH, Smooth Streaming) é armazenado em cache separadamente. Portanto, cada taxa de bits e protocolo são armazenados em cache separadamente e apenas os fragmentos de vídeo que foram solicitados são armazenados em cache.

### <a name="enable-azure-cdn-integration"></a>Habilitar a integração do Azure CDN

Após o provisionamento de um ponto de extremidade de Streaming com o CDN habilitado lá é um tempo de espera definido nos serviços de mídia antes de atualização de DNS é feita para mapear o ponto de extremidade de Streaming para o ponto de extremidade CDN.

Se quiser desabilitar/habilitar a CDN depois, o ponto de extremidade de streaming deverá estar no estado **interrompido**. Para que a integração da CDN do Azure seja habilitada e as alterações estejam ativas em todos os POPs da CDN talvez sejam necessárias até duas horas. No entanto, é possível iniciar o ponto de extremidade de streaming e o fluxo sem interrupções do ponto de extremidade de streaming e, assim que a integração estiver concluída, o fluxo é fornecido a partir da CDN. Durante o período de provisionamento seu ponto de extremidade de streaming estará no estado **iniciando** e você pode observar a degradação do desempenho.

Quando o ponto de extremidade de streaming padrão é criado, ele é configurado por padrão com Verizon Standard. Você pode configurar provedores de Verizon Premium ou Standard da Akamai usando APIs REST. 

A integração da CDN é habilitada em todos os datacenters do Azure, exceto nas regiões da China e do Governo Federal.

> [!IMPORTANT]
> A integração dos Serviços de Mídia do Azure à CDN do Azure é implementada da **Verizon na CDN do Azure** para pontos de extremidade de streaming padrão. Os pontos de extremidade de streaming Premium podem ser configurados usando todos os **tipos de preço e provedores da CDN do Azure**. Para obter mais informações sobre os recursos da CDN do Azure, consulte [Visão geral da CDN](../../cdn/cdn-overview.md).

### <a name="determine-if-dns-change-has-been-made"></a>Determinar se a alteração de DNS foi feita

Você pode determinar se a alteração de DNS tiver sido feita em um ponto de extremidade de Streaming (o tráfego está sendo direcionado para a CDN do Azure) usando https://www.digwebinterface.com. Se os resultados têm nomes de domínio azureedge.net nos resultados, o tráfego agora está sendo apontado para o CDN.

## <a name="next-steps"></a>Próximas etapas

O exemplo [neste repositório](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) mostra como iniciar o ponto de extremidade de streaming padrão com .NET.

