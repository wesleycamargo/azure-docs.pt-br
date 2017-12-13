---
title: "Controlar o comportamento de cache da Rede de Distribuição de Conteúdo com regras de cache | Microsoft Docs"
description: "É possível utilizar as regras de cache da CDN para definir ou modificar o comportamento padrão de expiração do cache globalmente e com condições, como um caminho de URL e extensões de arquivo."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 8f89ef5a1763d5fc4ad09a9aeae89ccf683138c7
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2017
---
# <a name="control-azure-content-delivery-network-caching-behavior-with-caching-rules"></a>Controlar o comportamento de cache da Rede de Distribuição de Conteúdo com regras de cache

> [!NOTE] 
> As regras de cache estão disponíveis apenas para **CDN do Azure da Verizon Standard** e **CDN do Azure do Akamai padrão**. Para **CDN do Azure da Verizon Premium**, é possível usar o [mecanismo de regras da CDN do Azure](cdn-rules-engine.md) no portal **Gerenciar** para uma funcionalidade semelhante.
 
A Rede de Distribuição de Conteúdo do Microsoft Azure oferece duas maneiras de controlar como seus arquivos são armazenados em cache: 

- Regras de cache: esse artigo descreve como é possível utilizar as regras de cache da CDN (rede de distribuição de conteúdo) para definir ou modificar o comportamento de expiração do cache padrão globalmente e com condições personalizadas, como um caminho de URL e extensão de arquivo. CDN do Azure fornece dois tipos de regras de cache:
   - Regras de cache globais: é possível definir uma regra de cache global para cada ponto de extremidade em seu perfil, que afeta todas as solicitações para o ponto de extremidade. A regra de cache global substitui todos os cabeçalhos de diretiva de cache HTTP, se configurado.
   - Regras de cache personalizadas: é possível definir uma ou mais regras de cache personalizadas para cada ponto de extremidade em seu perfil. As regras de cache personalizadas combinam caminhos específicos e extensões de arquivo, são processadas em ordem e substituem a regra de cache global, se definido. 

- Cache da cadeia de caracteres de consulta: é possível ajustar a forma como a CDN do Azure trata o armazenamento em cache para solicitações com cadeias de caracteres de consulta. Para obter mais informações, consulte [Controlar o comportamento do cache da CDN do Azure com cadeias de caracteres de consulta](cdn-query-string.md). Se o arquivo não for armazenável em cache, a configuração de cache da cadeia de caracteres de consulta não terá efeito, com base em regras de cache e comportamentos padrão da CDN.

Para obter informações sobre o comportamento de cache padrão e os cabeçalhos de diretivas de cache, consulte [Como funciona o cache](cdn-how-caching-works.md).

## <a name="tutorial"></a>Tutorial

Como configurar as regras de cache da CDN:

1. Abra o Portal do Azure, selecione um perfil da CDN e selecione um ponto de extremidade.
2. No painel esquerdo em Configurações, clique em **Cache**.
3. Crie uma regra de cache global da seguinte maneira:
   1. Em **Regras de cache globais**, defina **Comportamento de cache de cadeia de caracteres de consulta** para **Ignorar cadeias de caracteres**.
   2. Definir **Comportamento do cache** para **Definir se ausente**.
   3. Para **Duração da expiração do cache**, digite 10 no campo **Dias**.

       A regra de cache global afeta todos as solicitações para o ponto de extremidade. Essa regra aceita os cabeçalhos de diretivas de cache de origem, se existirem (`Cache-Control` ou `Expires`); caso contrário, se eles não forem especificados, ele define o cache para 10 dias. 

4. Crie uma regra de cache personalizada da seguinte maneira:
    1. Em **Regras de cache personalizadas**, defina **Condição de correspondência** para **Caminho** e **Valor de correspondência** para `/images/*.jpg`.
    2. Defina o **Comportamento do cache** para **Substituir** e digite 30 no campo **Dias**.
       
       Essa regra de cache personalizada define uma duração de cache de 30 dias em qualquer `.jpg` arquivo de imagem na pasta `/images` do seu ponto de extremidade. Ele substitui quaisquer cabeçalhos HTTP `Cache-Control` ou `Expires` enviados pelo servidor de origem.

  ![Caixa de diálogo de regras de cache](./media/cdn-caching-rules/cdn-caching-rules-dialog.png)

> [!NOTE] 
> Os arquivos que são armazenados em cache antes de uma alteração de regra mantêm a configuração da duração do cache de origem. Para reiniciar as durações de cache, será necessário [limpar o arquivo](cdn-purge-endpoint.md). Para pontos de extremidade da **CDN do Azure da Verizon**, poderá demorar até 90 minutos para que as regras de cache entrem em vigor.

## <a name="reference"></a>Referência

### <a name="caching-behavior-settings"></a>Configurações de comportamento do cache
Para regras de cache globais e personalizadas, é possível especificar as seguintes configurações de **Comportamento do cache**:

- **Ignorar cache**: não armazena em cache e ignora os cabeçalhos de diretrizes de cache fornecidos pela origem.
- **Substituir**: ignora os cabeçalhos de diretivas de cache fornecidos pela origem; em vez disso, utiliza a duração do cache fornecida.
- **Definir se ausente**: aceita os cabeçalhos de diretiva de cache fornecidos pela origem, se existirem; caso contrário, usa a duração do cache fornecida.

### <a name="cache-expiration-duration"></a>Duração da expiração do cache
Para regras de cache globais e personalizadas, é possível especificar a duração da expiração do cache em dias, horas, minutos e segundos:

- Para as configurações **Substituir** e **Definir se ausente**, configurações **Comportamento do cache**, as durações de cache válidas variam entre 0 segundos e 366 dias. Por um valor de 0 segundos, o CDN armazena em cache o conteúdo, mas deve revalidar cada solicitação com o servidor de origem.
- Para a configuração **Ignorar cache**, a duração do cache é definida automaticamente em 0 segundos e não pode ser alterada.

### <a name="custom-caching-rules-match-conditions"></a>As regras de cache personalizadas correspondem condições

Para regras de cache personalizadas, há duas condições de correspondência:
 
- **Caminho**: essa condição corresponde ao caminho da URL, excluindo o nome de domínio e fornece suporte ao símbolo curinga (\*). Por exemplo, `/myfile.html`, `/my/folder/*` e `/my/images/*.jpg`. O comprimento máximo é de 260 caracteres.

- **Extensão**: essa condição corresponde à extensão do arquivo solicitado. Você pode fornecer uma lista de extensões de arquivo separadas por vírgulas para corresponder. Por exemplo, `.jpg`, `.mp3` ou `.png`. O número máximo de extensões é 50 e o número máximo de caracteres por extensão é 16. 

### <a name="global-and-custom-rule-processing-order"></a>Ordem de processamento global e personalizado de regras
As regras de cache personalizadas e globais são processadas na seguinte ordem:

- As regras de cache globais têm precedência sobre o comportamento de cache padrão da CDN (configurações de cabeçalho de diretiva de cache HTTP). 

- As regras de cache personalizadas têm precedência sobre as regras de cache globais, onde elas se aplicam. As regras de cache personalizadas são processadas de forma geral de cima para baixo. Ou seja, se uma solicitação corresponder às duas condições, as regras na parte inferior da lista prevalecem sobre as regras na parte superior da lista. Portanto, é necessário colocar mais regras específicas na lista.

**Exemplo**:
- Regra de cache global: 
   - Comportamento do cache: **Substituir**
   - Duração da expiração do cache: 1 dia

- Regra de cache individual Nº 1:
   - Condição de correspondência: **Caminho**
   - Valor de correspondência: `/home/*`
   - Comportamento do cache: **Substituir**
   - Duração da expiração do cache: 2 dias

- Regra de cache personalizada Nº 2:
   - Condição de correspondência: **Extensão**
   - Valor de correspondência: `.html`
   - Comportamento do cache: **Definir se ausente**
   - Duração da expiração do cache: 3 dias

Quando essas regras são definidas, uma solicitação de `<endpoint>.azureedge.net/home/index.html` dispara a regra de cache padrão Nº 2, que está configurada para: **Definir se ausente** e 3 dias. Portanto, se o `index.html` arquivo tiver `Cache-Control` ou `Expires` cabeçalhos HTTP, eles são aceitos; caso contrário, se esses cabeçalhos não estiverem configurados, o arquivo será armazenado em cache por 3 dias.

