---
title: Uso da CDN do Azure | Microsoft Docs
description: Este tópico mostra como habilitar a CDN (Rede de Distribuição de Conteúdo) para o Azure. O tutorial o orienta ao longo da criação de um novo perfil da CDN e do ponto de extremidade.
services: cdn
documentationcenter: ''
author: camsoper
manager: erikre
editor: ''

ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2016
ms.author: casoper

---
# Usando o Azure CDN
Este tópico explica a habilitação do Azure CDN por meio da criação de um novo perfil e um ponto de extremidade CDN.

> [!IMPORTANT]
> Para obter uma introdução sobre o funcionamento da CDN, bem como uma lista de recursos, confira a [Visão geral da CDN](cdn-overview.md).
> 
> 

## Criar um novo perfil CDN
Um perfil CDN é um conjunto de pontos de extremidade CDN. Cada perfil contém um ou mais pontos de extremidade CDN. Você pode usar vários perfis para organizar seus pontos de extremidade CDN por domínio de Internet, aplicativo Web ou algum outro critério.

> [!NOTE]
> Por padrão, uma única assinatura do Azure é limitada a oito perfis da CDN. Cada perfil da CDN é limitado a 10 pontos de extremidade da CDN.
> 
> Os preços da CDN são aplicados no nível de perfil CDN. Se você quiser usar uma combinação dos tipos de preço do Azure CDN, precisará de vários perfis CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## Criar um novo ponto de extremidade CDN
**Para criar um novo ponto de extremidade CDN**

1. No [Portal do Azure](https://portal.azure.com), navegue até seu perfil CDN. Você pode ter fixado ao painel na etapa anterior. Se não, você poderá encontrá-lo clicando em **Procurar**, em **perfis CDN** e clicando no perfil ao qual você pretende adicionar o ponto de extremidade.
   
    A folha do perfil CDN é exibida.
   
    ![Perfil CDN][cdn-profile-settings]
2. Clique no botão **Adicionar Ponto de Extremidade**.
   
    ![Adicionar botão de ponto de extremidade][cdn-new-endpoint-button]
   
    A folha **Adicionar um ponto de extremidade** é exibida.
   
    ![Adicionar folha de ponto de extremidade][cdn-add-endpoint]
3. Insira um **Nome** para esse ponto de extremidade CDN. Esse nome será usado para acessar os recursos armazenados em cache no domínio `<endpointname>.azureedge.net`.
4. No menu suspenso **Tipo de origem**, selecione o tipo de origem. Selecione **Armazenamento** para uma conta de armazenamento do Azure, **Serviço de nuvem** para um Serviço de Nuvem do Azure, **Aplicativo Web** para um Aplicativo Web do Azure ou **Origem personalizada** para qualquer outra origem de servidor Web acessível publicamente (hospedado no Azure ou em outro lugar).
   
    ![Tipo de origem CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
5. No menu suspenso **Nome do host de origem**, selecione ou digite o domínio de origem. O menu suspenso listará todas as origens disponíveis do tipo especificado na etapa 4. Se você selecionou *Origem personalizada* como o **Tipo de origem**, você digitará no domínio de sua origem personalizada.
6. Na caixa de texto **Caminho de origem**, digite o caminho para os recursos que você deseja armazenar em cache, ou deixe em branco para permitir o cache de qualquer recurso no domínio especificado na etapa 5.
7. No **Cabeçalho de host de origem**, digite o cabeçalho de host que você deseja que o CDN envie com cada solicitação ou deixe o padrão.
   
   > [!WARNING]
   > Alguns tipos de origens, como o Armazenamento do Azure e Aplicativos Web, exigem que o cabeçalho de host corresponda ao domínio da origem. A menos que tenha uma origem que exija um cabeçalho de host diferente do seu domínio, você deverá deixar o valor padrão.
   > 
   > 
8. Para **Protocolo** e **Porta de origem**, especifique os protocolos e as portas usadas para acessar os recursos na origem. É necessário selecionar pelo menos um protocolo (HTTP ou HTTPS).
   
   > [!NOTE]
   > A **Porta de origem** afeta somente a porta que o ponto de extremidade usa para recuperar as informações da origem. O ponto de extremidade em si só estará disponível para os clientes finais nas portas HTTP e HTTPS (80 e 443) padrão, independentemente da **Porta de origem**.
   > 
   > Os pontos de extremidade **Azure CDN do Akamai** não permitem o intervalo de portas TCP completo para origens. Para obter uma lista das portas de origem que não são permitidas, confira [CDN do Azure das Portas de Origem Permitidas Akamai](https://msdn.microsoft.com/library/mt757337.aspx).
   > 
   > O acesso a conteúdo da CDN usando HTTPS tem as seguintes restrições:
   > 
   > * Você deve usar o certificado SSL fornecido pela CDN. Não há suporte a certificados de terceiros.
   > * Você deve usar o domínio fornecido pela CDN (`<endpointname>.azureedge.net`) para acessar o conteúdo HTTPS. O suporte a HTTPS não está disponível para CNAMEs (nomes de domínio personalizados), pois a CDN não dá suporte a certificados personalizados no momento.
   > 
   > 
9. Clique no botão **Adicionar** para criar um novo ponto de extremidade.
10. Depois que o ponto de extremidade é criado, ele aparece em uma lista de pontos de extremidade do perfil. O modo de exibição de lista mostra a URL a ser usada para acessar o conteúdo armazenado em cache, bem como o domínio de origem.
    
    ![Ponto de extremidade CDN][cdn-endpoint-success]
    
    > [!IMPORTANT]
    > O ponto de extremidade não estará imediatamente disponível para o uso, pois o registro demora um pouco para se propagar pela CDN. Para perfis <b>Azure CDN do Akamai</b>, a propagação geralmente é concluída em um minuto. Para perfis <b>Azure CDN da Verizon</b>, a propagação geralmente é concluída em 90 minutos, mas em alguns casos pode levar mais tempo.
    > 
    > Os usuários que tentarem usar o nome de domínio CDN antes que a configuração do ponto de extremidade seja propagada para os POPs receberão códigos de resposta HTTP 404. Se passaram várias horas desde que você criou o ponto de extremidade e ainda está recebendo respostas 404, consulte [Solucionando problemas dos pontos de extremidade CDN retornando status 404](cdn-troubleshoot-endpoint.md).
    > 
    > 

## Consulte também
* [Controle do comportamento do cache de solicitações com cadeias de caracteres de consulta](cdn-query-string.md)
* [Como mapear o conteúdo da CDN para um domínio personalizado](cdn-map-content-to-custom-domain.md)
* [Pré-carregar ativos em um ponto de extremidade da CDN do Azure](cdn-preload-endpoint.md)
* [Limpar um ponto de extremidade CDN do Azure](cdn-purge-endpoint.md)
* [Solucionando problemas dos pontos de extremidade CDN retornando status 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png

<!---HONumber=AcomDC_0803_2016-->