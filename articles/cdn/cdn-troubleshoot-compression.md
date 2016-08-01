<properties
	pageTitle="CDN: solucionando problemas de compactação de arquivo"
	description="Solucione problemas com a compactação de arquivo CDN."
	services="cdn"
	documentationCenter=""
	authors="camsoper"
	manager="erikre"
	editor=""/>

<tags
	ms.service="cdn"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/14/2016"
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

> [AZURE.TIP] Assim como ocorre com a implantação de novos pontos de extremidade, alterações na configuração da CDN demoram um pouco para serem propagadas pela rede. Na maioria dos casos, você verá as alterações aplicadas em até 90 minutos. Se esta for a primeira vez que você configura a compactação do ponto de extremidade CDN, será necessário considerar uma espera de 1 a 2 horas para garantir que as configurações de compactação são propagadas para os POPs.

### Verificar a solicitação

Primeiro, devemos fazer uma verificação de integridade rápida na solicitação. É possível usar as [ferramentas de desenvolvedor](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do navegador para exibir as solicitações feitas no momento.

- Verifique se a solicitação está sendo enviada para a URL do ponto de extremidade, `<endpointname>.azureedge.net`, e não para sua origem.
- Verifique se a solicitação contém um cabeçalho **Accept-Encoding** e se o valor desse cabeçalho contém **gzip**, **deflate** ou **bzip2**.

> [AZURE.NOTE] Os perfis da **CDN do Azure do Akamai** somente dão suporte à codificação **gzip**.

![Cabeçalhos da solicitação CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### Verificar as configurações de compactação (perfil CDN Standard)

> [AZURE.NOTE] Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN Standard do Azure da Verizon** ou **CDN Standard do Azure do Akamai**.

Navegue até seu ponto de extremidade no [Portal do Azure](https://portal.azure.com) e clique no botão **Configurar**.

- Verifique se a compactação está habilitada.
- Verifique se o tipo MIME do conteúdo a ser compactado está incluído na lista de formatos compactados.

![Configurações de compactação CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### Verificar as configurações de compactação (perfil CDN Premium)

> [AZURE.NOTE] Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN Premium do Azure da Verizon**.

Navegue até seu ponto de extremidade no [Portal do Azure](https://portal.azure.com) e clique no botão **Gerenciar**. O portal suplementar será aberto. Passe o ponteiro do mouse sobre a guia **HTTP Grande** e passe o ponteiro do mouse sobre o submenu **Configurações de Cache**. Clique em **Compactação**.

- Verifique se a compactação está habilitada.
- Verifique se a lista **Tipos de Arquivo** contém uma lista separada por vírgula (sem espaços) de tipos MIME.
- Verifique se o tipo MIME do conteúdo a ser compactado está incluído na lista de formatos compactados.

![Configurações de compactação premium CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### Verificar se que o conteúdo está armazenado em cache

> [AZURE.NOTE] Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN do Azure da Verizon** (Standard ou Premium).

Usando as ferramentas de desenvolvedor do navegador, verifique os cabeçalhos de resposta para garantir que o arquivo está armazenado em cache na região em que está sendo solicitado.

- Verifique o cabeçalho de resposta **Server**. O cabeçalho deve ter o formato **Plataforma (POP/ID do Servidor)**, como mostrado no exemplo abaixo.
- Verifique o cabeçalho de resposta **X-Cache**. No cabeçalho, deve-se ler **HIT**.

![Cabeçalhos de resposta CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### Verificar se o arquivo atende aos requisitos de tamanho

> [AZURE.NOTE] Essa etapa se aplica somente se o seu perfil de CDN é um perfil da **CDN do Azure da Verizon** (Standard ou Premium).

Para ser elegível para compactação, um arquivo deve atender aos seguintes requisitos de tamanho:

- Maior que 128 bytes.
- Menor que 1 MB.

### Verifique a solicitação no servidor de origem por um cabeçalho **Via**

O cabeçalho HTTP **Via** indica ao servidor Web que a solicitação está sendo passada por um servidor proxy. Por padrão, os servidores Web do Microsoft IIS não compactam as respostas quando a solicitação contém um cabeçalho **Via**. Para substituir esse comportamento, execute o seguinte procedimento:

- **IIS 6**: [Defina HcNoCompressionForProxies="FALSE" nas propriedades do IIS Metabase](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 and up**: [Defina **noCompressionForHttp10** e **noCompressionForProxies** como False na configuração do servidor](http://www.iis.net/configreference/system.webserver/httpcompression)

<!---HONumber=AcomDC_0720_2016-->