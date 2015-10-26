<properties
   pageTitle="Teste sua oferta VM para o Marketplace | Microsoft Azure"
   description="Entenda como testar sua imagem VM para o Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Teste sua oferta VM para o Marketplace em preparo

Preparo significa implantar a SKU em uma "área restrita" privada, em que você poderá testar e validar sua funcionalidade antes de publicá-la no Marketplace. A SKU aparecerá no preparo como se o cliente tivesse implantado. Sua imagem VM deve estar certificada para ser enviada para preparação.

## Etapa 1: Envie por push sua oferta de preparo

1. Clique em **Enviar por Push para o Preparo** na guia **Publicar**.

  ![desenho](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Corrija eventuais erros ou discrepâncias em que o portal de publicação pode notificá-lo neste momento.
3.	Forneça a lista de assinaturas do Azure que você usará para visualizar sua oferta no [Portal de Visualização do Azure](https://portal.azure.com) na caixa, como mostrado na captura de tela acima.
4. Faça logon para o [Portal de Visualização do Azure](http://portal.azure.com) usando uma das assinaturas do Azure acima indicadas na etapa anterior.
5. Encontre sua oferta e valide seus pontos de imagem VM:
  1. O conteúdo de marketing aparece corretamente na galeria

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. Implantação de ponta a ponta da imagem VM

> [AZURE.IMPORTANT]Sua oferta permanecerá no preparo até você notificar a Microsoft por meio do Portal de publicação [**Publicar** > **"Solicitar aprovação para envio à produção"**] que você está pronto para enviar por push para produção. Este é um momento ideal para que todos os membros da equipe verifiquem tudo no preparo para a sua oferta entrar em funcionamento.

> A replicação entre os data centers leva de 24 a 48 horas. Quando a replicação for concluída, a oferta estará listada no [Azure Marketplace](http://azure.microsoft.com/marketplace).

## Próximas etapas
Agora que você oferta está "preparada" e você testou a sua funcionalidade e conteúdo de marketing, você pode prosseguir para a oferta final e/ou a fase de publicação SKU, **Etapa 4**, [Implantar sua oferta para o Marketplace](marketplace-publishing-push-to-production.md)

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->