<properties
   pageTitle="Teste sua oferta de modelo de solução para o Marketplace | Microsoft Azure"
   description="Saiba como testar a oferta do modelo de solução para o Azure Marketplace."
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
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# Testar a oferta de modelo de solução em preparo
Preparo significa implantar a sua oferta em uma "área restrita" privada, em que você poderá testar e verificar sua funcionalidade antes de publicá-la para produção. A oferta aparecerá no preparo como se o cliente tivesse implantado. Sua oferta **deve estar certificada para ser enviada para preparação**.

Depois que a oferta é preparada, você pode exibir e testar a oferta no [Portal de Visualização do Azure](https://ms.portal.azure.com/).

Siga as etapas abaixo para enviar por push sua oferta de preparo e realizar os testes no [Portal de Visualização do Azure](https://ms.portal.azure.com/).

1.	Navegue até o [Portal de publicação](https://publish.windowsazure.com)-> selecione a guia **Modelos de solução** -> sua oferta -> **Publicar** -> clique em **"Enviar por push para preparação"**
2.	Fornece a lista de assinaturas do Azure que você usará para visualizar e testar sua oferta.
3.	Faça logon no Portal de Visualização do Azure usando a mesma ID de assinatura com a qual você preparou a oferta na etapa anterior.
4.	Execute pelo menos uma rodada de testes no Portal de Visualização do Azure nos pontos mencionados a seguir.
  -	O conteúdo de marketing aparece corretamente na galeria
  -	Implantação de ponta a ponta da topologia
  -	Executar testes de desempenho e teste de estresse
  -	Certifique-se de que a sua topologia segue as práticas recomendadas.

## Próxima etapa
Se você estiver satisfeito com os resultados e em seguida, você pode prosseguir para a fase de publicação de oferta final, **Etapa 4**, [Implantar sua oferta para o Marketplace](marketplace-publishing-push-to-production.md). Caso contrário, faça alterações na sua oferta e solicite a certificação novamente.

> [AZURE.NOTE]A certificação não é necessária para mudanças de conteúdo de marketing.

## Consulte também
- [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->