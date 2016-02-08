<properties
  pageTitle="Perguntas frequentes sobre o Pacote do IoT do Azure | Microsoft Azure"
  description="Perguntas frequentes do Pacote IoT."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
   
# Perguntas frequentes

### Quantas instâncias do Banco de Dados de Documentos posso provisionar em uma assinatura?

Cinco. Você pode criar um tíquete de suporte para aumentar esse limite, mas por padrão, só será possível provisionar cinco instâncias do Banco de Dados de Documentos por assinatura. Como resultado, você só poderá provisionar até cinco as soluções pré-configuradas de monitoramento remotas em uma determinada assinatura.

### Quantas APIs do Bing Mapas Gratuitas posso provisionar em uma assinatura?

Duas. Você pode criar apenas duas APIs do Bing Mapas Gratuitas em uma assinatura. A solução de monitoramento remota é configurada por padrão com uma API do Bing Maps Gratuita. Como resultado, você só poderá provisionar até duas soluções de monitoramento remotas em uma assinatura sem modificações.

### Qual é a diferença entre excluir um grupo de recursos no portal do Azure e clicar em excluir em uma solução pré-configurada no site azureiotsuite.com?

- Se você excluir a solução pré-configurada no site [azureiotsuite.com][lnk-azureiotsuite], excluirá todos os recursos provisionados na criação da solução pré-configurada; se você tiver adicionado outros recursos ao grupo de recursos, eles também serão excluídos. 

- Se você excluir o grupo de recursos no [portal do Azure][lnk-azure-portal], excluirá apenas os recursos desse grupo de recursos; também será necessário excluir o aplicativo do Active Directory do Azure associado à solução pré-configurada no [portal clássico do Azure][lnk-classic-portal].

### Como posso excluir um locatário do AAD?

Consulte a postagem do blog de Eric Golpe, [Passo a passo da exclusão de um locatário do AD do Azure][lnk-delete-aad-tennant].


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_0128_2016-->