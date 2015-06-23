<properties 
	pageTitle="Usando o castLabs para fornecer licenças DRM para os serviços de mídia do Azure" 
	description="Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licenças do castLabs." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="juliako"/>


#Usando o castLabs para fornecer licenças DRM para os serviços de mídia do Azure

##Visão geral

Este artigo descreve como é possível usar os serviços de mídia do Azure (AMS) para oferecer um fluxo dinamicamente é criptografado pelo AMS com os DRMs do PlayReady e Widevine. A licença do PlayReady vem do servidor de licenças do PlayReady dos serviços de mídia e a licença do Widevine é fornecida pelo servidor de licenças do **castLabs**.

O diagrama a seguir demonstra uma arquitetura de integração de alto nível dos serviços de mídia do Azure e do castLabs.

![Página Escala](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##Configuração típica do sistema

- O conteúdo de mídia é armazenado em AMS.
- As IDs de chave de chaves de conteúdo são armazenados em castLabs e AMS.
- O castLabs e o AMS têm autenticação de token interna. As seções a seguir discutem os tokens de autenticação. 
- Quando um cliente solicita o fluxo em vídeo, o conteúdo é criptografado dinamicamente com **Criptografia comum** (CENC) e empacotado dinamicamente pelo AMS para alguns (ou todos) os protocolos especificados: Smooth streaming, HLS ou DASH. 
- A licença do PlayReady é recuperada do servidor de licença do AMS e a licença Widevine é recuperada do servidor de licenças castLabs. 
- O Media Player decide automaticamente quais licenças buscar com base na capacidade de plataforma do cliente. 

##Geração de token de autenticação para obter uma licença

O castLabs e o AMS oferecem suporte ao formato de token JWT (JSON Web Token) usado para autorizar uma licença.

###Token JWT no AMS 

A tabela a seguir descreve o token JWT no AMS.

<table border="1">
<tr><td>Emissor</td><td>Cadeia de caracteres do emissor do Secure Token Service (STS) escolhido</td></tr>
<tr><td>Público-alvo</td><td>Cadeia de caracteres do público-alvo do STS usado</td></tr>
<tr><td>Declarações</td><td>Um conjunto de declarações</td></tr>
<tr><td>NotBefore</td><td>Validade inicial do token</td></tr>
<tr><td>Expira</td><td>Validade final do token</td></tr>
<tr><td>SigningCredentials</td><td>A chave que é compartilhada entre o servidor de licenças do PlayReady, servidor de licenças do castLabs e STS, pode ser simétrica ou assimétrica.</td></tr>
</table>

###Token JWT no castLabs

A tabela a seguir descreve o token JWT no castLabs.

<table border="1">
<tr><td>optData</td><td>Uma cadeia de caracteres JSON que contém informações sobre você. </td></tr>
<tr><td>crt</td><td>Uma cadeia de caracteres JSON que contém informações sobre o ativo, suas informações de licença e seus direitos de reprodução.</td></tr>
<tr><td>iat</td><td>A data e horário correntes na época.</td></tr>
<tr><td>jti</td><td>Um identificador exclusivo sobre esse token (cada token pode ser usado apenas uma vez no sistema do castLabs).</td></tr>
</table>

##Configuração da solução de exemplo 

A [solução de exemplo](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) consiste em dois projetos:

-	Um aplicativo de console que pode ser usado para definir as restrições de DRM em um ativo já incluído para o PlayReady e o Widevine.
-	Um aplicativo da Web que distribui os tokens, que poderia ser visto como uma versão MUITO SIMPLIFICADA de um STS.


Para usar o aplicativo de console.

1.	Altere o app.config para configurar credenciais AMS, credenciais do castLabs, configuração de STS e chave compartilhada.
2.	Carregue um ativo no AMS.
3.	Obtenha o UUID do ativo carregado e altere a linha 32 no arquivo Program.cs:

		 var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.	Use um AssetId para nomear o ativo no sistema do castLabs (linha 44 no arquivo Program.cs).

	Você deve definir o AssetId para o **castLabs**; ele precisa ser uma sequência alfanumérica e exclusiva.

5.	Execute o programa.


Para usar o aplicativo Web (STS):

1.	Altere o web.config para configurar o ID do comerciante do castlabs, a configuração de STS e a chave compartilhada.
2.	Implante os Sites do Azure.
3.	Navegue até o site.

##Reproduzir um vídeo

Para reproduzir um vídeo criptografado com criptografia comum (PlayReady e Widevine), é possível usar o [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html). Ao executar o aplicativo de console, a ID de chave de conteúdo e a URL de manifesto são exibidos.

1.	Abra uma nova guia e inicie seu STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2.	Vá para [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
3.	Cole na URL de streaming.
4.	Clique na caixa de seleção **Opções avançadas**.
5.	Selecione PlayReady na lista suspensa **Proteção**.
6.	Cole o token que você obteve no seu STS na caixa de texto do token.
7.	Atualize o player.
8.	O vídeo deve estar em reprodução.

Para reproduzir o vídeo protegido em HTML5 com o Chrome com o player castLabs, entre em contato com o castLabs para obter acesso ao player. Quando você tem acesso, há duas coisas para as quais deve estar atento:

1.	O player do castLabs precisa ter acesso ao arquivo de manifesto MPEG-DASH, de forma a ser acrescentado (formato = mpd-tempo-csf) ao seu arquivo de manifesto para obter o arquivo de manifesto de MPEG-DASH, em vez do padrão Smooth Streaming.

2.	O servidor de licença do castLab não precisa do prefixo "Portador =" na frente do token. Nesse caso, remova-o antes de enviar o token.

<!---HONumber=58--> 