<properties 
	pageTitle="Serviço de Aplicativo do Azure e seu impacto sobre os serviços existentes do Azure" 
	description="Explica como o novo Serviço de Aplicativo do Azure e seus recursos afetam os serviços existentes no Azure." 
	authors="yochayk" 
	writer="yochayk" 
	editor="yochayk" 
	manager="nirma" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="yochayk"/>


# Serviço de Aplicativo do Azure e serviços existentes do Azure

Este artigo descreve as alterações nos serviços existentes do Azure como parte da alteração para reunir vários serviços do Azure no [Serviço de Aplicativo do Azure](http://azure.microsoft.com/services/app-service/), uma nova oferta integrada.

## Visão geral 

O [Serviço de Aplicativo do Azure](http://azure.microsoft.com/services/app-service/) é um serviço de nuvem novo e exclusivo que habilita os desenvolvedores a criar aplicativos Web e móveis para qualquer plataforma e qualquer dispositivo. O Aplicativo de Serviço é uma solução integrada criada para simplificar funções de codificação repetidas, integrar-se a sistemas corporativos e de SaaS e automatizar processos de negócios, além de atender às suas necessidades de segurança, confiabilidade e escalabilidade.

Além de adicionar novos recursos poderosos, o Serviço de Aplicativo reúne em um único serviço combinado os seguintes serviços do Azure existentes: [Sites](http://azure.microsoft.com/services/websites/), [Serviços Móveis](http://azure.microsoft.com/services/mobile-services/) e [Serviços Biztalk](http://azure.microsoft.com/services/biztalk-services/).  O Serviço de Aplicativo permite hospedar os seguintes tipos de aplicativo: 

-   Aplicativos Web
-   Aplicativos Móveis
-   Aplicativos de API
-   Aplicativos Lógicos

A tabela a seguir explica como os serviços existentes do Azure são mapeados para o Serviço de Aplicativo e os tipos de aplicativos disponíveis nele.

<table>
<thead>
<tr class="header">
<th align="left", style="width:10%">Serviço existente do Azure</th>
<th align="left", style="width:10%">Serviço de Aplicativo do Azure</th>
<th align="left", style="width:80%">O que mudou</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Sites do Azure</td>
<td align="left">Aplicativos Web</td>
<td align="left"><li>Para Sites do Azure, o Aplicativo de Serviço se limita estritamente à alteração do nome  Sites para Aplicativos Web.
<p><li>Todas as suas instâncias existentes de Sites agora são Aplicativos Web no Aplicativo de Serviço.</p>
<p><li>Você pode acessar seus sites existentes por meio do <a href="http://go.microsoft.com/fwlink/?LinkId=529715">Portal do Azure</a>, em que encontrará todos os seus sites existentes em <em>Aplicativos Web</em>.</p>
<p><li><em>O Plano de Hospedagem na Web</em> agora é <em>Plano de Serviço de Aplicativo</em>. Um <em>Plano de Serviço de Aplicativo</em> pode hospedar qualquer tipo de aplicativo de Serviço de Aplicativo, como aplicativos Web, Móveis, Lógicos ou de API.</p>
<p><li>Os aplicativos Web do Serviço de Aplicativo do Azure têm Disponibilidade Geral.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/web/">Saiba mais sobre Aplicativos Web</a>.</p></td>
</tr>
<tr class="even">
<td align="left">Serviços Móveis do Azure</td>
<td align="left">Aplicativos Móveis</td>
<td align="left"><p><li>Os Serviços Móveis continuam disponíveis como um serviço autônomo e permanecem com suporte completo.</p>
<p><li>Os Aplicativos Móveis são um novo tipo de aplicativo do Serviço de Aplicativo, que integram toda a funcionalidade dos Serviços Móveis e muito mais. Os Aplicativos Móveis estão em visualização pública.</p>
<p><li>É fácil <a href="http://azure.microsoft.com/documentation/articles/app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview/">migrar dos Serviços Móveis para os Aplicativos Móveis</a>. Como os Aplicativos Móveis ainda estão no modo de visualização, ainda não são recomendados para a execução de aplicativos de produção.</p>
<p><li>Como parte do Serviço de Aplicativo, os Aplicativos Móveis obtêm novos recursos além dos Serviços Móveis, como  integração com sistemas locais e de SaaS, slots de preparo, Trabalhos Web, melhores opções de dimensionamento e muito mais.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/mobile/">Saiba mais sobre os Aplicativos Móveis</a>.</p>
</tr>
<tr class="odd">
<td align="left"></td>
<td align="left">Aplicativos de API</td>
<td align="left">
<p><li>Os Aplicativos de API são um novo tipo de aplicativo do Serviço de Aplicativo que permitem que você compile e consuma APIs na nuvem facilmente.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/api/">Saiba mais sobre os Aplicativos de API</a>.</p></td>
</tr>
<tr class="even">
<td align="left"></td>
<td align="left">Aplicativos Lógicos</td>
<td align="left">
<p><li>Os Aplicativos Lógicos são um novo tipo de aplicativo do Serviço de Aplicativo que permite automatizar facilmente o processo corporativo.</p>
<p><li><a href="http://azure.microsoft.com/services/app-service/logic/">Saiba mais sobre os Aplicativos Lógicos</a>.</p></td>
</tr>
<tr class="odd">
<td align="left">Serviços BizTalk do Azure</td>
<td align="left">Aplicativos da API do BizTalk</td>
<td align="left">
<li><p>Os Serviços BizTalk continuam disponíveis como um serviço autônomo e permanecem com suporte completo.</p>
<li><p>Todos os recursos dos Serviços BizTalk são integrados ao Serviço de Aplicativo como Aplicativos de API, habilitando os usuários a realizar cenários de integração de aplicativos empresariais e integração B2B com qualquer um dos tipos de aplicativos no Serviço de Aplicativo</p>
<li><p>Com os Aplicativos Lógicos, agora é possível automatizar processos corporativos usando uma experiência de design visual para criar fluxos de trabalho</p></td>
</tr>
</tbody>
</table>

Para saber mais, acesse a [documentação do Serviço de Aplicativo](http://azure.microsoft.com/documentation/services/app-service/).


<!--HONumber=49-->