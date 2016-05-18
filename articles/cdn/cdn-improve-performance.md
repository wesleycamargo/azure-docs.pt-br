<properties
	pageTitle="CDN - melhorar o desempenho ao compactar arquivos"
	description="Você pode aumentar a velocidade de transferência de arquivos e melhorar o desempenho do carregamento de páginas ao compactar seus arquivos."
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
	ms.date="04/26/2016" 
	ms.author="casoper"/>

# Melhorar o desempenho ao compactar arquivos

Este tópico descreve como aumentar a velocidade de transferência de arquivos e melhorar o desempenho do carregamento de páginas ao compactar os arquivos.

Há duas maneiras de a CDN poder oferecer suporte à compactação:

- Você pode habilitar a compactação no servidor de origem e, nesse caso, a CDN oferecerá suporte à compactação por padrão e entregará arquivos compactados aos clientes.
- Você pode habilitar a compactação diretamente nos servidores de borda da CDN e, nesse caso, a CDN compactará os arquivos e os servirá para os usuários finais.

## Habilitando a compactação

> [AZURE.NOTE] As camadas CDN Standard e Premium fornecem a mesma funcionalidade de compactação, mas a interface do usuário varia. Para saber mais sobre as diferenças entre as camadas CDN Standard e Premium, confira [Visão geral da CDN do Azure](cdn-overview.md).

### Camada padrão

1. Na folha Perfil CDN, clique no ponto de extremidade da CDN que deseja gerenciar.

	![Pontos de extremidade da folha Perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)

	A folha do ponto de extremidade da CDN se abre.

2. Clique no botão **Configurar**.

	![botão gerenciar da folha Perfil CDN](./media/cdn-file-compression/cdn-config-btn.png)

	A folha Configuração da CDN se abre.

3. Habilitar **Compactação**.

	![Opções de compactação da CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Use os tipos padrão ou modifique a lista removendo ou adicionando tipos de arquivo.

5. Após fazer suas alterações, clique no botão **Salvar**.

### Camada premium

1. Na folha do perfil CDN, clique no botão **Gerenciar**.

	![botão gerenciar da folha Perfil CDN](./media/cdn-file-compression/cdn-manage-btn.png)

	O portal de gerenciamento da CDN é aberto.

2. Passe o ponteiro do mouse sobre a guia **HTTP grande** e passe o ponteiro do mouse sobre o submenu **Configurações de Cache**. Clique em **Compactação**.

	As opções de compactação são exibidas.

	![Compactação de arquivos](./media/cdn-file-compression/cdn-compress-files.png)

3. Habilite a compactação clicando no botão de opção **Compactação Habilitada**. Insira os tipos MIME que você deseja compactar como uma lista delimitada por vírgula (sem espaços) na caixa de texto **Tipos de Arquivo**.

4. Depois de fazer as alterações, clique no botão **Atualizar**.


## Processo de compactação

### Visão geral

1. O solicitante envia uma solicitação de conteúdo.

2. Um servidor de borda verifica se há um cabeçalho **Accept-Encoding**.
	1. Se tiver sido incluído, esse cabeçalho identificará o método de compactação solicitado.
		> [AZURE.NOTE] Os métodos de compactação com suporte são **gzip**, **deflate** e **bzip2**.
	2. Se estiver ausente, esse tipo de solicitação será servido em um formato não compactado.
	
3.	O POP de borda mais próximo verifica o status do cache, o método de compactação e se ele ainda tem uma vida útil válida.
	1.	**Perda** no Cache: se a versão solicitada não for armazenada em cache, a solicitação será encaminhada para a origem.
	2.	**Ocorrência** no Cache: se a versão solicitada for armazenada em cache com o método de compactação solicitado, o servidor de borda imediatamente fornecerá o conteúdo compactado ao cliente.
	3.	**Ocorrência** no Cache: se o arquivo for armazenado em cache com métodos de compactação diferentes, o servidor de borda transcodificará o ativo para o método de compactação solicitado.
	4.	**Ocorrência** no Cache: se o arquivo for armazenado em cache em um formato descompactado, será executada uma verificação para determinar se a solicitação é qualificada para a compactação no servidor de borda (veja a observação abaixo). Se estiver qualificado, o servidor de borda compactará o arquivo e o servirá ao cliente. Caso contrário, ela retornará o conteúdo não compactado.
		
> [AZURE.IMPORTANT] Para se qualificar para a compactação, um arquivo deve:
>
> - Ser maior que 128 bytes.
> - Ser menor que 1 MB.
> - Ser um tipo MIME que foi [configurado para compactação](#enabling-compression).

### Tabelas

As tabelas abaixo descrevem o comportamento de compactação da CDN para cada cenário.

#### Compactação desabilitada ou arquivo não qualificado para compactação

|Formato solicitado|Arquivo armazenado em cache|Resposta da CDN|Observações|
|----------------|-----------|------------|-----|
|Compactado|Compactado|Compactado|CDN transcodifica entre os formatos com suporte|
|Compactado|Não compactado|Não compactada| |	
|Compactado|Não armazenado em cache|Compactada ou descompactada|Depende da resposta de origem|
|Não compactado|Compactado|Não compactada|CDN voltará até a origem para a versão não compactada|
|Não compactado|Não compactado|Não compactada| |	
|Não compactado|Não armazenado em cache|Não compactada| |

#### Compactação habilitada ou arquivo qualificado para compactação

|Formato solicitado|Arquivo armazenado em cache|Resposta da CDN|Observações|
|----------------|-----------|------------|-----|
|Compactado|Compactado|Compactado|CDN transcodifica entre os formatos com suporte|
|Compactado|Não compactado|Compactado|CDN executa compactação|
|Compactado|Não armazenado em cache|Compactado|CDN executará compactação se a origem for retornada descompactada|
|Não compactado|Compactado|Não compactada|CDN executa descompactação|
|Não compactado|Não compactado|Não compactada| |	
|Não compactado|Não armazenado em cache|Não compactada| |	


## Observações

1. Assim como ocorre com a implantação de novos pontos de extremidade, alterações na configuração da CDN demoram um pouco para serem propagadas pela rede. Na maioria dos casos, você verá as alterações aplicadas em até 90 minutos. Se esta for a primeira vez que você configura a compactação do ponto de extremidade CDN, será necessário considerar uma espera de 1 a 2 horas para garantir que as configurações de compactação são propagadas para os POPs antes de solucionar problemas.
2. Somente uma versão de arquivo (compactado ou não compactado) será armazenada em cache no servidor de borda. Uma solicitação para uma versão diferente resultará na transcodificação do conteúdo no servidor de borda.
3. Para pontos de extremidade de streaming habilitado para a CDN de Serviços de Mídia, a compactação está habilitada por padrão para os seguintes tipos de conteúdo: application/vnd.ms-sstr+xml,application/dash+xml,application/vnd.apple.mpegurl,application/f4m+xml. Você não pode habilitar/desabilitar a compactação para os tipos mencionados usando o portal do Azure.  
4. Embora seja possível, não é recomendável aplicar a compactação a formatos compactados, como ZIP, MP3, MP4, JPG, etc.

## Consulte também
- [Solucionando problemas de compactação de arquivo CDN](cdn-troubleshoot-compression.md)    

<!---HONumber=AcomDC_0504_2016-->