---
title: "Introdução à CDN do Azure | Microsoft Docs"
description: "Este tópico mostra como habilitar a CDN (Rede de Distribuição de Conteúdo do Azure). O tutorial o orienta ao longo da criação de um novo perfil da CDN e do ponto de extremidade."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2018
ms.author: mazha
ms.openlocfilehash: f1681b0796885cd6dc880303485edb97c15cda71
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="getting-started-with-azure-cdn"></a>Introdução à CDN do Azure
Este artigo descreve como habilitar a [Rede de Distribuição de Conteúdo (CDN)](cdn-overview.md) do Azure criando um novo perfil e ponto de extremidade de CDN.

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN
Um perfil CDN é um conjunto de pontos de extremidade CDN. Cada perfil pode conter um ou mais pontos de extremidade CDN. Para organizar seus pontos de extremidade CDN por domínio de Internet, aplicativo Web ou algum outro critério, use vários perfis.

> [!NOTE]
> Uma assinatura do Azure tem limites padrão para os seguintes recursos:
> - O número de perfis CDN que podem ser criados
> - O número de pontos de extremidade que podem ser criados em um perfil CDN 
> - O número de domínios personalizados que podem ser mapeados para um ponto de extremidade
>
> Para saber mais sobre limites de assinatura de CDN, veja [Limites CDN](https://docs.microsoft.com/azure/azure-subscription-service-limits#cdn-limits).
>
> Os preços da CDN são aplicados no nível de perfil CDN. Portanto, para usar uma combinação dos tipos de preço da CDN do Azure, você precisará de vários perfis de CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto de extremidade CDN
**Para criar um novo ponto de extremidade CDN**

1. No [portal do Azure](https://portal.azure.com), navegue até seu perfil CDN. Você pode ter fixado ao painel na etapa anterior. Em caso negativo, encontre-o selecionando **Todos os serviços** e selecionando **Perfis de CDN**. No painel **Perfis de CDN**, selecione o perfil ao qual você pretende adicionar seu ponto de extremidade. 
   
    O painel Perfil de CDN é exibido.
   
    ![Perfil CDN][cdn-profile-settings]

2. Selecione o **Ponto de extremidade**.
   
    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]
   
    O painel **Adicionar um ponto de extremidade** é exibido.
   
    ![Painel Adicionar ponto de extremidade][cdn-add-endpoint]

3. Em **Nome**, insira um nome exclusivo para o novo ponto de extremidade de CDN. Esse nome será usado para acessar os recursos armazenados em cache no domínio `<endpointname>.azureedge.net`.

4. Em **Tipo de origem**, selecione um tipo de origem. Selecione **Armazenamento** para uma conta de armazenamento do Azure, **Serviço de nuvem** para um Serviço de Nuvem do Azure, **Aplicativo Web** para um Aplicativo Web do Azure ou **Origem personalizada** para qualquer outra origem de servidor Web acessível publicamente (hospedado no Azure ou em outro lugar).
   
    ![Tipo de origem CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)

5. Em **Nome de host de origem**, selecione ou insira o domínio de origem. O menu suspenso lista todas as origens disponíveis do tipo especificado na etapa 4. Se você tiver selecionado **Origem personalizada** como o tipo de origem, insira o domínio de sua origem personalizada.
    
6. Em **Caminho de origem**, insira o caminho até os recursos que você deseja armazenar em cache, ou deixe em branco para permitir o armazenamento em cache de qualquer recurso no domínio especificado na etapa 5.
    
7. Em **Cabeçalho de host de origem**, insira o cabeçalho de host que você deseja que a CDN do Azure envie com cada solicitação, ou deixe o padrão.
   
   > [!WARNING]
   > Alguns tipos de origens, como o Armazenamento do Azure e Aplicativos Web, exigem que o cabeçalho de host corresponda ao domínio da origem. A menos que tenha uma origem que exija um cabeçalho de host diferente do seu domínio, você deverá deixar o valor padrão.
   > 
    
8. Para **Protocolo** e **Porta de origem**, especifique os protocolos e as portas usadas para acessar os recursos na origem. É necessário selecionar pelo menos um protocolo (HTTP ou HTTPS). Use o domínio fornecido pela CDN (`<endpointname>.azureedge.net`) para acessar o conteúdo HTTPS. 
   
   > [!NOTE]
   > O valor da **Porta de origem** determina somente a porta usada pelo ponto de extremidade para recuperar as informações da origem. O ponto de extremidade em si só estará disponível para os clientes finais nas portas HTTP e HTTPS (80 e 443) padrão, independentemente do valor de **Porta de origem**.  
   > 
   > Os pontos de extremidade na **CDN do Azure do Akamai** não permitem o intervalo de portas TCP completo para portas de origens. Para obter uma lista das portas de origem que não são permitidas, confira [CDN do Azure das Portas de Origem Permitidas Akamai](https://msdn.microsoft.com/library/mt757337.aspx).  
   > 
   > Quando você acessar o conteúdo da CDN usando HTTPS, há as seguintes restrições:
   > 
   > * Use o certificado SSL fornecido pela CDN. Não há suporte a certificados de terceiros.
   > * O suporte a HTTPS para domínios personalizados do Azure CDN está disponível apenas com produtos **CDN do Azure da Verizon** (Standard e Premium). Ele não tem suporte em produtos **Azure CDN do Akamai**. Para saber mais, veja [Configurar o HTTPS em um domínio personalizado CDN do Azure](cdn-custom-ssl.md).
    
9. Selecione **Adicionar** para criar um novo ponto de extremidade.
   
   Depois que o ponto de extremidade é criado, ele aparece na lista de pontos de extremidade do perfil.
    
   ![Ponto de extremidade CDN][cdn-endpoint-success]
    
   > [!IMPORTANT]
   > Como o registro demora um pouco para se propagar, o ponto de extremidade não fica imediatamente disponível para uso. Para perfis da **CDN do Azure da Akamai** , a propagação normalmente é concluída em um minuto. Para perfis da **Azure CDN da Verizon**, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.
    > 
    > Se você tentar usar o nome de domínio da CDN antes que a configuração do ponto de extremidade seja propagada para os POPs, talvez você receba um status de resposta HTTP 404. Se passaram várias horas desde que você criou o ponto de extremidade e ainda está recebendo um status de resposta 404, veja [Solução de problemas dos pontos de extremidade CDN retornando status 404](cdn-troubleshoot-endpoint.md).
    > 
    > 

## <a name="see-also"></a>Veja também
* [Controle do comportamento do cache de solicitações com cadeias de caracteres de consulta](cdn-query-string.md)
* [Como mapear o conteúdo da CDN para um domínio personalizado](cdn-map-content-to-custom-domain.md)
* [Pré-carregar ativos em um ponto de extremidade da CDN do Azure](cdn-preload-endpoint.md)
* [Limpar um ponto de extremidade CDN do Azure](cdn-purge-endpoint.md)
* [Solucionando problemas dos pontos de extremidade CDN retornando status 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
