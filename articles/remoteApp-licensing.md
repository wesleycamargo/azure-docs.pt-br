<properties title="Azure RemoteApp licensing" pageTitle="Licenciamento do RemoteApp do Azure" description="Saiba mais sobre o licenciamento do RemoteApp do Azure" metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Como o licenciamento funciona no RemoteApp do Azure?


Você configurou seu serviço de RemoteApp do Azure, criou seus modelos e está pronto para publicar aplicativos para seus usuários. Mas ainda há uma última coisa a descobrir: licenciamento. Como funciona o licenciamento para os aplicativos que são compartilhados por meio do RemoteApp?

Você pode oferecer qualquer aplicativo ou programa para o qual você tem a licença. 

Como parte da sua assinatura do RemoteApp do Azure não será necessário adquirir licenças do Windows e/ou CAL de área de trabalho remota, elas estão todas incluídas no custo da assinatura.

Se você quiser usar a imagem de modelo do Office 365 que vem com o RemoteApp do Azure, você deve ter um plano *existente* do Office 365 ProPlus. O mesmo é verdadeiro para qualquer aplicativo do Office 365 que você publique usando um modelo personalizado. Você precisa ativar os aplicativos com sua própria assinatura. Isso é verdadeiro para assinaturas de avaliação e pagas. Se você quiser usar a imagem de modelo do Office 365 durante a avaliação, *e você ainda não tem uma assinatura *, vá para a página do Office 365 para [Inscrever-se](https://go.microsoft.com/fwlink/p/?LinkID=403802) para uma assinatura de avaliação. Consulte [Como o RemoteApp e o Office funcionam juntos?](http://azure.microsoft.com/pt-br/documentation/articles/remoteapp-o365/) para obter mais informações.

Se, durante o período de avaliação, você não desejar obter uma assinatura de avaliação do Office 365, use a imagem de modelo do Office 2013 Professional Plus que vem com o RemoteApp. Essa imagem de modelo só pode ser usada por 30 dias e não pode ser convertida em uma coleção paga. Após o término do período de avaliação, você precisará excluir a coleção e criar uma nova se você quiser usar o Office 2013 no futuro.  

Isso faz sentido, certo? Você pode publicar qualquer aplicativo que você está legalmente qualificado para compartilhar. E você precisa certificar-se de que você realmente tem direito a compartilhar seus programas.

Observe que você não pode usar um contrato de licença de Volume ou CAL em uma coleção na nuvem. Você *pode* usar um contrato de licença de Volume para ativar aplicativos em sua coleção híbrida (com exceção do Office). Você precisa instalá-los em sua imagem de modelo a partir da mídia de licença de Volume. Siga as informações do fornecedor do aplicativo para instalar licenças em um ambiente de área de trabalho remota.

<!--HONumber=35.2-->
