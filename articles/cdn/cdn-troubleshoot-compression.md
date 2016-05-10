<properties
	pageTitle="CDN: solucionando problemas de compactação de arquivo"
	description="Solucione problemas com a compactação de arquivo CDN."
	services="cdn"
	documentationCenter=".NET"
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/21/2016" 
	ms.author="casoper"/>
    
# Solucionando problemas de compactação de arquivo CDN

Este artigo ajuda você a solucionar problemas com a [compactação de arquivo CDN](cdn-improve-performance.md).

Se precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure e do Stack Overflow no MSDN](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## Sintoma

A compactação do ponto de extremidade está habilitada, mas os arquivos estão sendo retornados descompactados.

## Causa

Há várias causas possíveis, incluindo:

- O conteúdo solicitado não está qualificado para compactação.
- A compactação não está habilitada para o tipo de arquivo solicitado.
- A solicitação HTTP não incluía um cabeçalho solicitando um tipo de compactação válido.

## Etapas para solucionar problemas

### Verificar a solicitação

Primeiro, devemos fazer uma verificação de integridade rápida na solicitação. Você pode usar as [ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do navegador para exibir as solicitações sendo feitas.

- Verifique se a solicitação está sendo enviada para a URL do ponto de extremidade, `<endpointname>.azureedge.net`, e não sua origem.
- Verifique se a solicitação contém um cabeçalho **Accept-Encoding** cabeçalho e se o valor para esse cabeçalho contém **gzip**, **defalte** ou **bzip2**.

![Cabeçalhos da solicitação CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### Verificar as configurações de compactação (perfil CDN Standard)

> [AZURE.NOTE] Esta etapa se aplicará apenas se seu perfil CDN estiver no tipo de preço **Standard**.

Navegue até seu ponto de extremidade no [Portal do Azure](https://portal.azure.com) e clique no botão **Configurar**.

- Verifique se a compactação está habilitada.
- Verifique se o tipo MIME do conteúdo a ser compactado está incluído na lista de formatos compactados.

![Configurações de compactação CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### Verificar as configurações de compactação (perfil CDN Premium)

> [AZURE.NOTE] Esta etapa se aplica apenas se seu perfil CDN estiver no tipo de preço **Premium**.

Navegue até seu ponto de extremidade no [Portal do Azure](https://portal.azure.com) e clique no botão **Gerenciar**. O portal suplementar será aberto. Passe o ponteiro do mouse sobre a guia **HTTP Grande** e passe o ponteiro do mouse sobre o submenu **Configurações de Cache**. Clique em **Compactação**.

- Verifique se a compactação está habilitada.
- Verifique se a lista **Tipos de Arquivo** contém uma lista separada por vírgulas de tipos MIME.
- Verifique se o tipo MIME do conteúdo a ser compactado está incluído na lista de formatos compactados.

![Configurações de compactação premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)


### Verificar se que o conteúdo está armazenado em cache

Usando as ferramentas de desenvolvedor do navegador, verifique os cabeçalhos de resposta para garantir que o arquivo está armazenado em cache na região em que está sendo solicitado.

- Verifique o cabeçalho de resposta **Server**. O cabeçalho deve ter o formato **Plataforma (POP/ID do servidor)**, como mostrado no exemplo a seguir.
- Verifique o cabeçalho de resposta **X-Cache**. O cabeçalho deve ler **HIT**.  

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### Verificar se o arquivo atende aos requisitos de tamanho

Para ser elegível para compactação, um arquivo deve atender aos seguintes requisitos de tamanho:

- Maior que 128 bytes.
- Menor que 1 MB.

<!---HONumber=AcomDC_0427_2016-->