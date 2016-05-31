<properties
    pageTitle="Licenciamento do RemoteApp do Azure | Microsoft Azure"
    description="Saiba como funciona o licenciamento no RemoteApp do Azure."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/18/2016"
    ms.author="elizapo" />


# Como o licenciamento funciona no RemoteApp do Azure?


Você configurou seu serviço de RemoteApp do Azure, criou seus modelos e está pronto para publicar aplicativos para seus usuários. Mas ainda há uma última coisa a descobrir: licenciamento. Como funciona o licenciamento para o RemoteApp e os aplicativos que você compartilha por meio do RemoteApp?

O RemoteApp não requer quaisquer licenças do Windows ou CALs de Área de Trabalho Remota. Sua assinatura cuida do RemoteApp em si. (Verifique os detalhes dos [planos de preços](https://azure.microsoft.com/pricing/details/remoteapp).)

Se você usar uma das imagens incluídas na sua assinatura, você pode compartilhar qualquer um dos aplicativos instalados na imagem sem a necessidade de uma licença separada. Por exemplo, se você usar a imagem de modelo do Windows Server 2012 R2 para compilar sua coleção, você pode compartilhar o System Center Endpoint Protection com seus usuários. As únicas exceções a essa regra são o Office 365 ProPlus, que requer uma assinatura separada, e o Office 2013, que não pode ser compartilhado em uma coleção de produção.

Se quiser usar a imagem de modelo do Office 365 que vem com o RemoteApp, você deve ter um plano *existente* do Office 365 ProPlus. O mesmo é verdadeiro para qualquer aplicativo do Office 365 que você publique usando um modelo personalizado. Você precisa ativar os aplicativos com sua própria assinatura. Isso é verdadeiro para assinaturas de avaliação e pagas. Se você quiser usar a imagem de modelo do Office 365 durante a avaliação, *e você ainda não tiver uma assinatura*, acesse a página do Office 365 para [Inscrever-se](https://go.microsoft.com/fwlink/p/?LinkID=403802) para uma assinatura de avaliação. Consulte [Como o RemoteApp e o Office funcionam juntos?](remoteapp-o365.md) para obter mais informações.

Se, durante o período de avaliação, você não desejar obter uma assinatura de avaliação do Office 365, use a imagem de modelo do Office 2013 Professional Plus que vem com o RemoteApp. Essa imagem de modelo só pode ser usada por 30 dias e não pode ser convertida em uma coleção paga.

Para outros aplicativos, você precisará certificar-se de que tenha a licença para compartilhá-los.

Isso faz sentido, certo? Você pode publicar qualquer aplicativo que você está legalmente qualificado para compartilhar. E você precisa certificar-se de que você realmente tem direito a compartilhar seus programas.

Observe que você não pode usar um contrato de licença de Volume ou CAL em uma coleção na nuvem. Você *pode* usar um contrato de Licença de Volume para ativar aplicativos em sua coleção híbrida (com exceção do Office). Você precisa instalá-los em sua imagem de modelo a partir da mídia de licença de Volume. Siga as informações do fornecedor do aplicativo para instalar licenças em um ambiente de área de trabalho remota.

<!---HONumber=AcomDC_0525_2016-->