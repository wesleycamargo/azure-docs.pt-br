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
   ms.date="08/01/2016"
   ms.author="hascipio" />

# Teste sua oferta VM para o Azure Marketplace em preparo

Preparo significa implantar a SKU em uma "área restrita" privada, em que você poderá testar e validar sua funcionalidade antes de publicá-la no Marketplace. A SKU aparece no preparo como se o cliente tivesse implantado. Sua imagem VM deve estar certificada para ser enviada para preparação.

## Etapa 1: Envie por push sua oferta de preparo

1. Na guia **Publicar** clique em **Enviar por Push para o preparo**.

    ![desenho](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. Se o Portal de publicação notificar qualquer erro, corrija-o.
3.	Na caixa de diálogo **Quem pode acessar sua oferta preparada?** insira a lista de assinaturas do Azure que você usará para visualizar sua oferta no [portal de visualização do Azure](https://portal.azure.com).

    >[AZURE.NOTE] No caso de Máquinas Virtuais e modelos de Solução, **não** coloque em lista de branca assinaturas do tipo CSP, DreamSpark ou Azure via Open.


    > No caso de Máquinas Virtuais, quando você clica no botão **ENVIAR PARA PREPARO**, as etapas a seguir são executadas em segundo plano. Você poderá exibir o andamento de cada etapa na guia PUBLICAR do portal de Publicação. Verifique essa página regularmente (até que o status mostre PREPARADO) para obter informações sobre falhas que precisem ser corrigidas por você.

    > - Inicialmente, sua solicitação é enviada à equipe de certificação, que valida o VHD. No entanto, se sua solicitação teve apenas alteração de marketing, a etapa de certificação será ignorada.
    > - Uma vez concluída a certificação, a replicação da oferta começará em todos os datacenters do Azure. Geralmente, uma replicação leva de 24 a 48 horas para ser concluída, mas pode levar até uma semana dependendo do tamanho do VHD. No entanto, se sua solicitação teve apenas alteração de marketing, a replicação será mais rápida.
    > - Quando a replicação for concluída, sua oferta será disponibilizada no [portal do Azure](http:/portal.azure.com). Nessa ocasião, o status da oferta passará para PREPARADO no portal de Publicação. Uma oferta preparada só ficará visível no [portal do Azure](http:/portal.azure.com) usando as identificações de email associadas à assinatura com as quais a oferta foi preparada.

4. Entre no [Portal de Visualização do Azure](https://portal.azure.com) usando uma das assinaturas do Azure acima indicadas na etapa anterior.
5. Encontre sua oferta e valide seus pontos de imagem VM:
  - Certifique-se de que o conteúdo de marketing aparece corretamente no Marketplace.
  - Implantação de ponta a ponta da imagem VM.

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [AZURE.IMPORTANT] Sua oferta permanecerá no preparo até você notificar a Microsoft por meio do Portal de Publicação [guia **Publicar** > clique no botão **"Solicitar Aprovação para Envio à Produção"**] que você está pronto para enviar por push para produção. Este é um momento ideal para que todos os membros da sua equipe verifiquem tudo no preparo para que sua oferta seja listada.

> A plataforma de preparo foi desenvolvida para que o editor teste oferta no modo de visualização. É enfaticamente desaconselhável usar essa plataforma para fins comerciais.

## Próximas etapas
Agora que a sua oferta está "preparada" e você testou a sua funcionalidade e conteúdo de marketing, prossiga para a oferta final e/ou a fase de publicação SKU, **Etapa 4**: [Implantar sua oferta no Marketplace](marketplace-publishing-push-to-production.md).

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=AcomDC_0803_2016-->