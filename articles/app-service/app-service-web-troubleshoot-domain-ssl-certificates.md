---
title: Solucionar problemas de domínio e certificado SSL em aplicativos Web do Azure | Microsoft Docs
description: Solucionar problemas de domínio e certificado SSL em aplicativos Web do Azure
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Solucionar problemas de domínio e certificado SSL em aplicativos Web do Azure

Este artigo lista os problemas comuns que você pode encontrar ao configurar o domínio ou o certificado SSL para seus aplicativos Web do Azure. Também descreve as possíveis causas e resoluções para esses problemas.

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**.

## <a name="certificate-problems"></a>Problemas de certificado

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Não é possível adicionar um certificado SSL a um aplicativo Web 

### <a name="symptom"></a>Sintoma

Quando você adiciona uma associação SSL, a seguinte mensagem de erro será exibida:

**Falha ao adicionar associação SSL. Não é possível definir o certificado para o VIP existente, pois outro VIP já usa esse certificado.**

### <a name="cause"></a>Causa

Esse problema pode ocorrer se você tiver várias associações SSL com base em IP para o mesmo endereço IP entre vários aplicativos Web. Por exemplo, o aplicativo Web A tem SSL com base em IP com certificado antigo. O aplicativo Web B tem SSL com base em IP com certificado novo para o mesmo endereço IP. Quando você atualiza a associação SSL do aplicativo Web com um certificado novo, ele falhará com esse erro, pois o mesmo endereço IP está sendo usado para outro aplicativo. 

### <a name="solution"></a>Solução 

Para corrigir esse problema, use um dos seguintes métodos:

- Exclua a associação SSL baseada em IP no aplicativo Web que usa o certificado antigo. 
- Crie uma nova associação SSL com base em IP que usa o novo certificado.

### <a name="unable-to-delete-a-certificate"></a>Não é possível excluir um certificado 

### <a name="symptom"></a>Sintoma

Quando você tentar excluir um certificado, a seguinte mensagem de erro é exibida:

**Não é possível excluir o certificado porque ele está atualmente sendo usado em uma associação SSL. A associação SSL deve ser removida antes de excluir o certificado.**

### <a name="cause"></a>Causa

Esse problema pode ocorrer se o certificado é usado por outro aplicativo Web.

### <a name="solution"></a>Solução

Remova a associação SSL para o certificado dos aplicativos Web. Em seguida, tente excluir o certificado. Se ainda não for possível excluir o certificado, limpe o cache do navegador de Internet, abra o Portal do Azure em uma nova janela do navegador. E em seguida, tente excluir o certificado.

### <a name="unable-to-purchase-an-app-service-certificate"></a>Não é possível adquirir um certificado de Serviço de Aplicativo 

### <a name="symptom"></a>Sintoma
Você não pode adquirir um [certificado de Serviço de Aplicativo](./web-sites-purchase-ssl-web-site.md) no Portal do Azure.

### <a name="cause-and-solution"></a>Causa e Solução
Esse problema pode ocorrer por qualquer um dos seguintes motivos:

- O plano do Serviço de Aplicativo é "Livre" ou "Compartilhado". Não oferecemos suporte SSL para esses tipos de preço. 

    **Solução**: atualize o Plano do Serviço de Aplicativo para o aplicativo Web para "Standard".

- A assinatura não tem um cartão de crédito válido.

    **Solução**: adicione um cartão de crédito à sua assinatura. 

- A oferta de assinatura não dá suporte para adquirir um certificado de Serviço de Aplicativo, como o Microsoft Student.  

    **Solução**: atualize sua assinatura. 

- A assinatura atingiu o limite máximo de compras que é permitido em uma assinatura.

    **Solução**: certificados de Serviço de Aplicativo têm um limite de 10 compras de certificado para os tipos de assinaturas pré-pagas e Contrato Enterprise. Para outros tipos de assinatura, o limite é 3. Para aumentar o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O certificado de Serviço de Aplicativo foi marcado como fraude. É exibida a seguinte mensagem de erro: "Seu certificado foi sinalizado para uma possível fraude. A solicitação está sendo examinada. Se o certificado não se tornar utilizável dentro de 24 horas.”

    **Solução**: se o certificado está marcado como fraude e não foi resolvido após 24 horas, então siga estas etapas:

    1. Faça logon no [Portal do Azure](https://portal.azure.com).
    2. Vá para **Certificados do Serviço de Aplicativo**, selecione o certificado.
    3. Selecione **Configuração de Certificado** > **Etapa 2: verificar** > **Verificação de domínio**. Isso envia uma notificação de email para o provedor de certificados do Azure para resolver o problema.

## <a name="domain-problems"></a>Problemas de domínio

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>Adquiriu um certificado SSL de domínio incorreto

### <a name="symptom"></a>Sintoma

Você adquiriu um certificado de Serviço de Aplicativo para o domínio incorreto e não é possível atualizar o certificado para usar o domínio correto.

### <a name="solution"></a>Solução

- Exclua o certificado e, em seguida, compre um novo.
- Se o certificado atual que usa o domínio incorreto está no estado "Emitido", então você também será cobrado por esse certificado. Certificados de Serviço de Aplicativo não são reembolsáveis, mas você pode contatar o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver se há outras opções. 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>O certificado de Serviço de Aplicativo foi renovado, mas ainda mostrará o certificado antigo 

### <a name="symptom"></a>Sintoma

O certificado de Serviço de Aplicativo foi renovado, mas o aplicativo Web que usa o certificado de Serviço de Aplicativo ainda está usando o certificado antigo. Além disso, você recebeu um aviso de que o protocolo HTTPS é necessário.

### <a name="cause"></a>Causa 
O Serviço de Aplicativo Web executa um trabalho em segundo plano a cada oito horas e sincroniza o recurso de certificado se houver alterações. Portanto, quando você gira ou atualiza um certificado, às vezes, o aplicativo ainda está recuperando o certificado antigo e não o certificado atualizado recentemente. Isso ocorre porque o trabalho para sincronizar o recurso de certificado ainda não foi executado. 
 
### <a name="solution"></a>Solução

Você pode forçar uma sincronização do certificado:

1. Faça logon no [Portal do Azure](https://portal.azure.com). Selecione os **Certificados do Serviço de Aplicativo** e selecione o certificado.
2. Clique em **Rechavear e Sincronizar** e, em seguida, em **Sincronizar**. Isso demora algum tempo para concluir. 
3. Quando a sincronização for concluída, você verá a notificação a seguir: "Todos os recursos atualizados com êxito com o certificado mais recente".

### <a name="domain-verification-is-not-working"></a>A verificação de domínio não está funcionando 

### <a name="symptom"></a>Sintoma 
O certificado do Serviço de Aplicativo requer a verificação de domínio antes do certificado estar pronto para uso. Quando você clica em **Verificar**, o processo falha.

### <a name="solution"></a>Solução
Verifique seu domínio manualmente adicionando um registro TXT:
 
1.  Acesse o provedor de Serviço de Nomes de Domínio (DNS) que hospeda o nome de domínio.
2.  Adicione um registro TXT para seu domínio que use o valor do token de domínio que é mostrado no Portal do Azure. 

Aguarde alguns minutos para que a propagação de DNS seja executada e clique no botão **Atualizar** para disparar a verificação. 

O método alternativo para verificar manualmente é o “método de Página da Web HTML” que pode ser usado para permitir que a autoridade de certificação confirme a propriedade do domínio para o qual o certificado foi emitido.

1.  Crie um arquivo HTML chamado {Domain Verification Token}.html. 
2.  O conteúdo deste arquivo deve ser o valor do Token de Verificação de Domínio.
3.  Carregue esse arquivo na raiz do servidor Web que está hospedando seu domínio
4.  Clique em **Atualizar** para verificar o status do certificado. Pode demorar alguns minutos até a verificação ser concluída.

Por exemplo, se você estiver comprando um certificado padrão para o azure.com com o Token de Verificação de Domínio ‘1234abcd’, uma solicitação da Web feita para http://azure.com/1234abcd.html deverá retornar 1234abcd. 

> [!IMPORTANT]
> A ordem de um certificado tem apenas 15 dias para concluir a operação de verificação de domínio. Depois de 15 dias, o certificado é negado pela autoridade de certificação e você não será cobrado pelo certificado. Nessa situação, exclua esse certificado e tente novamente.
>
> 

### <a name="unable-to-purchase-a-domain"></a>Não é possível comprar um domínio

### <a name="symptom"></a>Sintoma
Não é possível comprar um domínio do aplicativo Web ou de domínio de serviço de aplicativo no Portal do Azure.

### <a name="cause-and-solution"></a>Causa e solução

Esse problema ocorre por um dos seguintes motivos:

- Nenhum cartão de crédito na assinatura do Azure ou cartão de crédito inválido.

    **Solução**: adicione um cartão de crédito à sua assinatura se não tiver feito isso.

- Se você não for o proprietário da assinatura, não terá permissão para comprar o domínio.

    **Solução**: [adicione a função de proprietário](../billing/billing-add-change-azure-subscription-administrator.md) à sua conta ou entre em contato com o administrador da assinatura para obter permissões para comprar um domínio.
- Você atingiu o limite para comprar os domínios na sua assinatura. O limite atual é 20.

    **Solução**: para solicitar o aumento do limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O tipo de assinatura do Azure não oferece suporte a compra de domínio do Serviço de Aplicativo.

    **Solução**: atualize sua assinatura do Azure para outros tipos de assinatura como uma assinatura pré-paga.

### <a name="unable-to-add-a-hostname-to-web-app"></a>Não é possível adicionar um nome de host ao aplicativo Web 

### <a name="symptom"></a>Sintoma

Quando você adiciona um nome de host, o processo falhará em validar e verificar o domínio.

### <a name="cause"></a>Causa 

Esse problema ocorre por um dos seguintes motivos:

- Você não tem permissão para adicionar um nome do host.

    **Solução**: verifique com o administrador da assinatura para certificar-se de que você tem permissão para adicionar um nome do host.
- Não foi possível verificar a propriedade do domínio.

    **Solução**: verifique se o registro CNAME ou A estão configurados corretamente. Para mapear o domínio personalizado para o aplicativo Web, crie um Registro CNAME ou A. Se quiser usar o domínio raiz, você deve usar os registros A e TXT:

    |Tipo de registro|Host|Apontar para|
    |------|------|-----|
    |O |@|Endereço IP para o aplicativo Web|
    |TXT|@|<app-name>.azurewebsites.net|
    |CNAME|www|<app-name>.azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>DNS não pode ser resolvido

### <a name="symptom"></a>Sintoma

Você receberá uma mensagem de erro "O registro de DNS não pôde ser localizado".

### <a name="cause"></a>Causa
Esse problema ocorre por um dos seguintes motivos:

- O período de Vida Útil (TTL) não expirou. Verifique a configuração de DNS para seu domínio para determinar o valor de TTL e aguarde até que o período expire.
- A configuração do DNS está incorreta.

### <a name="solution"></a>Solução
- Aguarde 48 horas para que esse problema seja resolvido sozinho.
- Se você puder alterar a configuração de TTL em sua configuração de DNS, altere o valor para 5 minutos para ver se isso resolve o problema.
- Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o seu domínio aponta para o endereço IP do aplicativo Web. Se não estiver, configure o registro para o endereço IP correto do aplicativo Web.

### <a name="restore-a-deleted-domain"></a>Restaurar um domínio excluído 

### <a name="symptom"></a>Sintoma
O domínio não está mais visível no portal do Azure.

### <a name="cause"></a>Causa 
O domínio pode ter sido excluído acidentalmente pelo proprietário da assinatura.

### <a name="solution"></a>Solução
Se o seu domínio foi excluído há menos de sete dias, ele ainda não iniciou o processo de exclusão. Nesse caso, você pode comprar o mesmo domínio novamente no Portal do Azure na mesma assinatura (certifique-se de digitar o nome de domínio exato na caixa de pesquisa). Você não será cobrado novamente por este domínio. Se o domínio foi excluído há mais de sete dias, entre em contato com o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter ajuda para restaurar o domínio.

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>O domínio personalizado retorna 404 ou site inacessível 

### <a name="symptom"></a>Sintoma

Ao navegar para o site usando o nome de domínio personalizado, você pode receber a seguinte mensagem de erro:

**Erro 404 - aplicativo Web não encontrado.**


### <a name="cause-and-solution"></a>Causa e solução

**Causa 1** 

O domínio personalizado configurado não tem um registro CNAME ou A. 

**Solução para a causa 1**

- Se você adicionou um registro A, certifique-se de que um registro TXT também tenha sido adicionado. Para obter mais informações, consulte [Criar-o-registro-A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se você não precisa usar um domínio raiz para seu aplicativo Web, é recomendável usar um registro CNAME em vez de um registro A.
- Não use um registro CNAME e um A para o mesmo domínio. Isso pode causar conflitos e impedir que o domínio seja resolvido. 

**Causa 2** 

O navegador da Internet pode ainda estar armazenando o endereço IP antigo em cache para o seu domínio. 

**Solução para a Causa 2**

Limpe o navegador. Para dispositivos do Windows, você pode executar o comando `ipconfig /flushdns`. Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o seu domínio aponta para o endereço IP do aplicativo Web. 

### <a name="unable-to-add-subdomain"></a>Não é possível adicionar o subdomínio 

### <a name="symptom"></a>Sintoma

Você não pode adicionar um novo nome do host para um aplicativo Web para atribuir um subdomínio.

### <a name="solution"></a>Solução

- Verifique com o administrador da assinatura para certificar-se de que você tem permissões para adicionar um nome do host ao aplicativo Web.
- Se você precisar de mais subdomínios, recomendamos que você altere a hospedagem de domínio DNS do Azure. Usando o DNS do Azure, você pode adicionar 500 nomes do host ao seu aplicativo Web. Para saber mais, confira [Adicionar um nome de domínio personalizado](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















