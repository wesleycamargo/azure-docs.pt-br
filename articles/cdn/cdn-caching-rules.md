---
title: Controlar o comportamento de cache da CDN do Azure com regras de cache | Microsoft Docs
description: É possível utilizar as regras de cache da CDN para definir ou modificar o comportamento padrão de expiração do cache globalmente e com condições, como um caminho de URL e extensões de arquivo.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: v-deasim
ms.openlocfilehash: 09705893c50e56cce5d888db097d7b810624b5d8
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="control-azure-cdn-caching-behavior-with-caching-rules"></a>Controlar o comportamento de cache da CDN do Azure com regras de cache

> [!NOTE] 
> As regras de cache estão disponíveis apenas para perfis de **CDN Standard do Azure do Verizon** e **CDN Standard do Azure da Akamai**. Para perfis de **CDN Premium do Azure do Verizon**, é necessário usar o [mecanismo de regras da CDN do Azure](cdn-rules-engine.md) no portal **Gerenciar** para uma funcionalidade semelhante.
 
A CDN (Rede de Distribuição de Conteúdo do Microsoft Azure) oferece duas maneiras de controlar como seus arquivos são armazenados em cache: 

- Regras de cache: esse artigo descreve como é possível utilizar as regras de cache da CDN (rede de distribuição de conteúdo) para definir ou modificar o comportamento de expiração do cache padrão globalmente e com condições personalizadas, como um caminho de URL e extensão de arquivo. CDN do Azure fornece dois tipos de regras de cache:

   - Regras de cache globais: é possível definir uma regra de cache global para cada ponto de extremidade em seu perfil, que afeta todas as solicitações para o ponto de extremidade. A regra de cache global substitui todos os cabeçalhos de diretiva de cache HTTP, se configurado.

   - Regras de cache personalizadas: é possível definir uma ou mais regras de cache personalizadas para cada ponto de extremidade em seu perfil. As regras de cache personalizadas combinam caminhos específicos e extensões de arquivo, são processadas em ordem e substituem a regra de cache global, se definido. 

- Cache da cadeia de caracteres de consulta: é possível ajustar a forma como a CDN do Azure trata o armazenamento em cache para solicitações com cadeias de caracteres de consulta. Para obter mais informações, consulte [Controlar o comportamento do cache da CDN do Azure com cadeias de caracteres de consulta](cdn-query-string.md). Se o arquivo não for armazenável em cache, a configuração de cache da cadeia de caracteres de consulta não terá efeito, com base em regras de cache e comportamentos padrão da CDN.

Para obter informações sobre o comportamento de cache padrão e os cabeçalhos de diretivas de cache, consulte [Como funciona o cache](cdn-how-caching-works.md). 


## <a name="accessing-azure-cdn-caching-rules"></a>Acessando regras de cache da CDN do Azure

1. Abra o Portal do Azure, selecione um perfil da CDN e selecione um ponto de extremidade.

2. No painel esquerdo em Configurações, selecione **Regras de cache**.

   ![Botão de regras de cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-btn.png)

   A página **Regras de cache** é exibida.

   ![Página Regras de cache da CDN](./media/cdn-caching-rules/cdn-caching-rules-page.png)


## <a name="caching-behavior-settings"></a>Configurações de comportamento do cache
Para regras de cache globais e personalizadas, é possível especificar as seguintes configurações de **Comportamento do cache**:

- **Ignorar cache**: não armazena em cache e ignora os cabeçalhos de diretrizes de cache fornecidos pela origem.

- **Substituir**: ignora os cabeçalhos de diretivas de cache fornecidos pela origem; em vez disso, utiliza a duração do cache fornecida.

- **Definir se ausente**: aceita os cabeçalhos de diretiva de cache fornecidos pela origem, se existirem; caso contrário, usa a duração do cache fornecida.

![Regras de cache globais](./media/cdn-caching-rules/cdn-global-caching-rules.png)

![Personalizar regras de cache](./media/cdn-caching-rules/cdn-custom-caching-rules.png)

## <a name="cache-expiration-duration"></a>Duração da expiração do cache
Para regras de cache globais e personalizadas, é possível especificar a duração da expiração do cache em dias, horas, minutos e segundos:

- Para as configurações **Substituir** e **Definir se ausente**, configurações **Comportamento do cache**, as durações de cache válidas variam entre 0 segundos e 366 dias. Por um valor de 0 segundos, o CDN armazena em cache o conteúdo, mas deve revalidar cada solicitação com o servidor de origem.

- Para a configuração **Ignorar cache**, a duração do cache é definida automaticamente em 0 segundos e não pode ser alterada.

## <a name="custom-caching-rules-match-conditions"></a>As regras de cache personalizadas correspondem condições

Para regras de cache personalizadas, há duas condições de correspondência:
 
- **Caminho**: essa condição corresponde ao caminho da URL, excluindo o nome de domínio e fornece suporte ao símbolo curinga (\*). Por exemplo, _/myfile.html_, _/my/folder/*_ e _/my/images/*.jpg_. O comprimento máximo é de 260 caracteres.

- **Extensão**: essa condição corresponde à extensão do arquivo solicitado. Você pode fornecer uma lista de extensões de arquivo separadas por vírgulas para corresponder. Por exemplo, _.jpg_, _.mp3_ ou _.png_. O número máximo de extensões é 50 e o número máximo de caracteres por extensão é 16. 

## <a name="global-and-custom-rule-processing-order"></a>Ordem de processamento global e personalizado de regras
As regras de cache personalizadas e globais são processadas na seguinte ordem:

- As regras de cache globais têm precedência sobre o comportamento de cache padrão da CDN (configurações de cabeçalho de diretiva de cache HTTP). 

- As regras de cache personalizadas têm precedência sobre as regras de cache globais, onde elas se aplicam. As regras de cache personalizadas são processadas de forma geral de cima para baixo. Ou seja, se uma solicitação corresponder às duas condições, as regras na parte inferior da lista prevalecem sobre as regras na parte superior da lista. Portanto, é necessário colocar mais regras específicas na lista.

**Exemplo**:
- Regra de cache global: 
   - Comportamento do cache: **Substituir**
   - Duração da expiração do cache: 1 dia

- Regra de cache individual Nº 1:
   - Condição de correspondência: **Caminho**
   - Valor de correspondência: _/home/*_
   - Comportamento do cache: **Substituir**
   - Duração da expiração do cache: 2 dias

- Regra de cache personalizada Nº 2:
   - Condição de correspondência: **Extensão**
   - Valor de correspondência: _.html_
   - Comportamento do cache: **Definir se ausente**
   - Duração da expiração do cache: 3 dias

Quando essas regras são definidas, uma solicitação de _&lt;endpoint hostname&gt;_.azureedge.net/home/index.html dispara a regra de cache personalizado nº 2, que está definida para: **Definir se ausente** e 3 dias. Portanto, se o arquivo *index.html* tiver `Cache-Control` ou `Expires` cabeçalhos HTTP, eles serão aceitos; caso contrário, se esses cabeçalhos não estiverem configurados, o arquivo será armazenado em cache por 3 dias.

> [!NOTE] 
> Os arquivos que são armazenados em cache antes de uma alteração de regra mantêm a configuração da duração do cache de origem. Para reiniciar as durações de cache, será necessário [limpar o arquivo](cdn-purge-endpoint.md). Para pontos de extremidade da **CDN do Azure da Verizon**, poderá demorar até 90 minutos para que as novas regras de cache entrem em vigor.

## <a name="see-also"></a>Consulte também

- [Como funciona o cache](cdn-how-caching-works.md)
- [Tutorial: definir regras de cache da CDN do Azure](cdn-caching-rules-tutorial.md)
