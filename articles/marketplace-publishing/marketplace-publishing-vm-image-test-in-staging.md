<properties
   pageTitle="Teste sua oferta VM para o Marketplace | Microsoft Azure"
   description="Entenda como testar sua imagem VM para o Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Teste sua oferta VM para o Azure Marketplace em preparo

Preparo significa implantar a SKU em uma "área restrita" privada, em que você poderá testar e validar sua funcionalidade antes de publicá-la no Marketplace. A SKU aparece no preparo como se o cliente tivesse implantado. Sua imagem VM deve estar certificada para ser enviada para preparação.

## Etapa 1: Envie por push sua oferta de preparo

1. Na guia **Publicar** clique em **Enviar por Push para o preparo**.

  ![desenho](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Se o Portal de publicação notificar qualquer erro, corrija-o.
3.	Na caixa de diálogo **Quem pode acessar sua oferta preparada?** insira a lista de assinaturas do Azure que você usará para visualizar sua oferta no [portal de visualização do Azure](https://portal.azure.com).
4. Entre no [Portal de Visualização do Azure](https://portal.azure.com) usando uma das assinaturas do Azure acima indicadas na etapa anterior.
5. Encontre sua oferta e valide seus pontos de imagem VM:
  1. Certifique-se de que o conteúdo de marketing aparece corretamente no Marketplace.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. Implantação de ponta a ponta da imagem VM.

> [AZURE.IMPORTANT] Sua oferta permanecerá no preparo até você notificar a Microsoft por meio do Portal de publicação [**Publicar** > **"Solicitar aprovação para envio à produção"**] que você está pronto para enviar por push para produção. Este é um momento ideal para que todos os membros da sua equipe verifiquem tudo no preparo para a sua oferta entrar em funcionamento.

> A replicação entre os data centers leva até 48 horas. Quando a replicação estiver concluída, sua oferta estará listada no [Azure Marketplace](https://azure.microsoft.com/marketplace/).

## Próximas etapas
Agora que a sua oferta está "preparada" e você testou a sua funcionalidade e conteúdo de marketing, prossiga para a oferta final e/ou a fase de publicação SKU, **Etapa 4**: [Implantar sua oferta no Marketplace](marketplace-publishing-push-to-production.md).

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0128_2016-->