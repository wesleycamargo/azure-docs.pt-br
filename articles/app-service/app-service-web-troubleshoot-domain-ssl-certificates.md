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
ms.openlocfilehash: 59a9011edef49494288716ab16f30e28e440293b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34195175"
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Solucionar problemas de domínio e certificado SSL em aplicativos Web do Azure

Este artigo lista os problemas comuns que você pode encontrar ao configurar um domínio ou o certificado SSL para os aplicativos Web do Azure. Adicionalmente, descreve as possíveis causas e soluções para esses problemas.

Se você precisar de mais ajuda em qualquer ponto deste artigo, contate os especialistas do Azure nos [fóruns do Azure e do Stack Overflow](https://azure.microsoft.com/support/forums/). Como alternativa, você pode registrar um incidente de suporte do Azure. Vá para o [Site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.

## <a name="certificate-problems"></a>Problemas de certificado

### <a name="you-cant-add-an-ssl-certificate-binding-to-a-web-app"></a>Não é possível adicionar uma associação de certificado SSL a um aplicativo Web 

#### <a name="symptom"></a>Sintoma

Quando você adiciona uma associação SSL, a seguinte mensagem de erro será exibida:

"Falha ao adicionar associação SSL. Não é possível definir o certificado para o VIP existente, pois outro VIP já usa esse certificado."

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se você tiver várias associações SSL com base em IP para o mesmo endereço IP entre vários aplicativos Web. Por exemplo, o aplicativo Web A tem um SSL com base em um IP com certificado antigo. O aplicativo Web B tem um SSL com base em IP com um certificado novo para o mesmo endereço IP. Quando você atualiza a associação SSL do aplicativo Web com o novo certificado, ele falha com esse erro porque o mesmo endereço IP está sendo usado para outro aplicativo. 

#### <a name="solution"></a>Solução 

Para corrigir esse problema, use um dos seguintes métodos:

- Exclua a associação SSL baseada em IP no aplicativo Web que usa o certificado antigo. 
- Crie uma nova associação SSL com base em IP que usa o novo certificado.

### <a name="you-cant-delete-a-certificate"></a>Não é possível excluir um certificado 

#### <a name="symptom"></a>Sintoma

Quando você tentar excluir um certificado, a seguinte mensagem de erro é exibida:

"Não é possível excluir o certificado porque ele está atualmente sendo usado em uma associação SSL. A associação SSL deve ser removida antes de excluir o certificado."

#### <a name="cause"></a>Causa

Esse problema pode ocorrer se outro aplicativo Web usar o certificado.

#### <a name="solution"></a>Solução

Remova a associação SSL para esse certificado dos aplicativos Web. Em seguida, tente excluir o certificado. Se você ainda não conseguir excluir o certificado, limpe o cache do navegador da Internet e reabra o portal do Azure em uma nova janela do navegador. Em seguida, tente excluir o certificado.

### <a name="you-cant-purchase-an-app-service-certificate"></a>Não é possível comprar um certificado do Serviço de Aplicativo 

#### <a name="symptom"></a>Sintoma
Não é possível comprar um [certificado do Serviço de Aplicativo do Azure](./web-sites-purchase-ssl-web-site.md) pelo portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução
Esse problema pode ocorrer por qualquer um dos seguintes motivos:

- O plano do Serviço de Aplicativo é Gratuito ou Compartilhado. Esses tipos de preços não dão suporte para SSL. 

    **Solução**: atualize o plano do Serviço de Aplicativo do aplicativo Web para Standard.

- A assinatura não possui um cartão de crédito válido.

    **Solução**: adicione um cartão de crédito à sua assinatura. 

- A oferta de assinatura não dá suporte para compra de um certificado do Serviço de Aplicativo, como o Microsoft Student.  

    **Solução**: atualize sua assinatura. 

- A assinatura alcançou o limite de compras permitidas em uma assinatura.

    **Solução**: certificados do Serviço de Aplicativo têm um limite de 10 compras de certificado para os tipos de assinatura EA e Pagamento Conforme o Uso. Para outros tipos de assinatura, o limite é 3. Para aumentar o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O certificado de Serviço de Aplicativo foi marcado como fraude. Você recebeu mensagem de erro a seguir: "Seu certificado foi sinalizado para uma possível fraude. A solicitação está sendo examinada. Se o certificado não se tornar utilizável dentro de 24 horas, entre em contato com o suporte do Azure."

    **Solução**: se o certificado estiver marcado como fraude e não for resolvido após 24 horas, siga estas etapas:

    1. Entre no [Portal do Azure](https://portal.azure.com).
    2. Vá para **Certificados do Serviço de Aplicativo** e selecione o certificado.
    3. Selecione **Configuração de Certificado** > **Etapa 2: verificar** > **Verificação de domínio**. Essa etapa envia uma notificação de email para o provedor de certificados do Azure para resolver o problema.

## <a name="domain-problems"></a>Problemas de domínio

### <a name="you-purchased-an-ssl-certificate-for-the-wrong-domain"></a>Você comprou um certificado SSL para o domínio incorreto

#### <a name="symptom"></a>Sintoma

Você comprou um certificado do Serviço de Aplicativo para o domínio incorreto. Não é possível atualizar o certificado para usar o domínio correto.

#### <a name="solution"></a>Solução

Exclua o certificado e, em seguida, compre um novo.

Se o certificado atual que usa o domínio incorreto estiver no estado "Emitido", você também será cobrado pelo certificado. Certificados de Serviço de Aplicativo não são reembolsáveis, mas você pode contatar o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para ver se há outras opções. 

### <a name="an-app-service-certificate-was-renewed-but-the-web-app-shows-the-old-certificate"></a>Um certificado do Serviço de Aplicativo foi renovado, mas o aplicativo Web mostra o certificado antigo 

#### <a name="symptom"></a>Sintoma

O certificado do Serviço de Aplicativo foi renovado, mas o aplicativo Web que usa o certificado do Serviço de Aplicativo ainda está usando o certificado antigo. Além disso, você recebeu um aviso de que o protocolo HTTPS é necessário.

#### <a name="cause"></a>Causa 
O recurso de Aplicativos Web do Serviço de Aplicativo do Azure executa uma tarefa em segundo plano a cada oito horas e sincroniza o recurso de certificado se houver alguma alteração. Ao girar ou atualiza um certificado, às vezes, o aplicativo ainda está recuperando o certificado antigo e não o certificado atualizado recentemente. O motivo é que o trabalho para sincronizar o recurso de certificado ainda não foi executado. 
 
#### <a name="solution"></a>Solução

Você pode forçar uma sincronização do certificado:

1. Entre no [Portal do Azure](https://portal.azure.com). Selecione os **Certificados do Serviço de Aplicativo** e, em seguida, selecione o certificado.
2. Selecione **Rechaveamento e Sincronização** e, em seguida, selecione **Sincronizar**. A sincronização demora algum tempo para concluir. 
3. Quando a sincronização for concluída, você verá a notificação a seguir: "Todos os recursos atualizados com êxito com o certificado mais recente."

### <a name="domain-verification-is-not-working"></a>A verificação de domínio não está funcionando 

#### <a name="symptom"></a>Sintoma 
O certificado do Serviço de Aplicativo requer a verificação de domínio antes do certificado estar pronto para uso. Ao selecionar **Verificar**, o processo falha.

#### <a name="solution"></a>Solução
Verifique seu domínio manualmente adicionando um registro TXT:
 
1.  Acesse o provedor de Serviço de Nomes de Domínio (DNS) que hospeda o nome de domínio.
2.  Adicione um registro TXT ao domínio que usa o valor do token de domínio exibido no portal do Azure. 

Aguarde alguns minutos para que a propagação de DNS seja executada e, em seguida, selecione o botão **Atualizar** para disparar a verificação. 

Como uma alternativa, é possível usar o método de páginas da Web em HTML para verificar o domínio manualmente. Esse método permite que a autoridade de certificação confirme a propriedade do domínio do domínio para o qual o certificado é emitido.

1.  Crie um arquivo HTML nomeado {token de verificação de domínio}.html. O conteúdo desse arquivo deve ser o valor do token de verificação de domínio.
3.  Carregue esse arquivo na raiz do servidor Web que hospeda o domínio.
4.  Selecione **Atualizar** para verificar o status do certificado. Pode demorar alguns minutos até a verificação ser concluída.

Por exemplo, se você estiver comprando um certificado padrão do azure.com utilizando o token de verificação de domínio 1234abcd, uma solicitação da Web feita para http://azure.com/1234abcd.html deverá retornar 1234abcd. 

> [!IMPORTANT]
> A ordem de um certificado tem apenas 15 dias para concluir a operação de verificação de domínio. Após 15 dias, a autoridade de certificação negará o certificado e você não será cobrado pelo certificado. Nessa situação, exclua esse certificado e tente novamente.
>
> 

### <a name="you-cant-purchase-a-domain"></a>Não é possível comprar um domínio

#### <a name="symptom"></a>Sintoma
Não é possível comprar um domínio do domínio do Serviço de Aplicativo ou Aplicativos Web no portal do Azure.

#### <a name="cause-and-solution"></a>Causa e solução

Esse problema ocorre por um dos seguintes motivos:

- Não há cartão de crédito na assinatura do Azure ou o cartão de crédito é inválido.

    **Solução**: adicione um cartão de crédito à sua assinatura.

- Você não é o proprietário da assinatura, portanto, não tem permissão para comprar um domínio.

    **Solução**: [adicione a função de Proprietário](../billing/billing-add-change-azure-subscription-administrator.md) à sua conta. Ou contate o administrador da assinatura para obter permissão para comprar um domínio.
- Você atingiu o limite para comprar os domínios na sua assinatura. O limite atual é 20.

    **Solução**: para solicitar um aumento até o limite, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- O tipo de assinatura do Azure não dá suporte para compra de um domínio do Serviço de Aplicativo.

    **Solução**: atualize a assinatura do Azure para outro tipo de assinatura, como uma assinatura de Pagamento Conforme o Uso.

### <a name="you-cant-add-a-host-name-to-a-web-app"></a>Não é possível adicionar um nome do host a um aplicativo Web 

#### <a name="symptom"></a>Sintoma

Ao adicionar um nome do host, o processo falha ao validar e verificar o domínio.

#### <a name="cause"></a>Causa 

Esse problema ocorre por um dos seguintes motivos:

- Você não tem permissão para adicionar um nome do host.

    **Solução**: solicite ao administrador da assinatura a conceder-lhe permissão para adicionar um nome do host.
- Não foi possível verificar a propriedade do domínio.

    **Solução**: verifique se o registro A ou CNAME está configurado corretamente. Para mapear um domínio personalizado para o aplicativo Web, crie um registro A ou um registro CNAME. Se quiser utilizar um domínio raiz, use os registros TXT e A:

    |Tipo de registro|Host|Apontar para|
    |------|------|-----|
    |O |@|Endereço IP para um aplicativo Web|
    |TXT|@|<app-name>.azurewebsites.net|
    |CNAME|www|<app-name>.azurewebsites.net|

### <a name="dns-cant-be-resolved"></a>DNS não pode ser resolvido

#### <a name="symptom"></a>Sintoma

Você recebeu a mensagem de erro a seguir:

"O registro DNS não pôde ser localizado."

#### <a name="cause"></a>Causa
Esse problema ocorre por um dos seguintes motivos:

- O período de TTL (vida útil) não expirou. Verifique a configuração de DNS para seu domínio para determinar o valor de TTL e aguarde até que o período expire.
- A configuração do DNS está incorreta.

#### <a name="solution"></a>Solução
- Aguarde 48 horas para que esse problema seja resolvido sozinho.
- Se você puder alterar a configuração de TTL em sua configuração de DNS, altere o valor para 5 minutos para ver se isso resolve o problema.
- Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o domínio aponta para o endereço IP do aplicativo Web. Se isso não estiver, configure o registro A para o endereço IP correto do aplicativo Web.

### <a name="you-need-to-restore-a-deleted-domain"></a>É necessário restaurar um domínio excluído 

#### <a name="symptom"></a>Sintoma
O domínio não está mais visível no portal do Azure.

#### <a name="cause"></a>Causa 
O proprietário da assinatura pode ter excluído acidentalmente o domínio.

#### <a name="solution"></a>Solução
Se o domínio foi excluído há menos de sete dias, o domínio ainda não iniciou o processo de exclusão. Nesse caso, você poderá comprar o mesmo domínio novamente no portal do Azure com a mesma assinatura. (Certifique-se de digitar o nome de domínio exato na caixa de pesquisa.) Você não será cobrado novamente por esse domínio. Se o domínio foi excluído há mais de sete dias, contate o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter ajuda na restauração do domínio.

### <a name="a-custom-domain-returns-a-404-error"></a>Um domínio personalizado retorna um erro 404 

#### <a name="symptom"></a>Sintoma

Ao navegar para o site usando o nome de domínio personalizado, você pode receber a seguinte mensagem de erro:

"Erro 404 - aplicativo Web não encontrado."


#### <a name="cause-and-solution"></a>Causa e solução

**Causa 1** 

O domínio personalizado configurado não tem um registro CNAME ou A. 

**Solução para a causa 1**

- Se você adicionou um registro A, verifique se um registro TXT também foi adicionado. Para obter mais informações, consulte [Criar o registro A](./app-service-web-tutorial-custom-domain.md#create-the-a-record).
- Se você não precisar usar o domínio raiz para o aplicativo Web, é recomendável usar um registro CNAME em vez de um registro A.
- Não use um registro CNAME e um registro A para o mesmo domínio. Isso pode causar um conflito e impedir que o domínio seja resolvido. 

**Causa 2** 

O navegador da Internet ainda pode estar armazenando o endereço IP antigo em cache para o domínio. 

**Solução para a Causa 2**

Limpe o navegador. Para dispositivos do Windows, você pode executar o comando `ipconfig /flushdns`. Use [WhatsmyDNS.net](https://www.whatsmydns.net/) para verificar se o domínio aponta para o endereço IP do aplicativo Web. 

### <a name="you-cant-add-a-subdomain"></a>Não é possível adicionar um subdomínio 

#### <a name="symptom"></a>Sintoma

Não é possível adicionar um novo nome do host a um aplicativo Web para atribuir um subdomínio.

#### <a name="solution"></a>Solução

- Verifique com o administrador de assinatura para certificar-se de que você tem permissões para adicionar um nome do host ao aplicativo Web.
- Se você precisar de mais subdomínios, é recomendável alterar a hospedagem de domínio para DNS do Azure. Usando o DNS do Azure, será possível adicionar 500 nomes do host ao aplicativo Web. Para obter mais informações, consulte [Adicionar um subdomínio](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/).











 


















