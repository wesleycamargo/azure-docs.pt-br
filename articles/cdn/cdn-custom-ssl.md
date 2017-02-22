---
title: "Como habilitar HTTPS em um domínio personalizado CDN do Azure | Microsoft Docs"
description: "Saiba como habilitar HTTPS em seu ponto de extremidade CDN do Azure com um domínio personalizado."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: aea6f0fedb447e953b9db40342e5091bd35486af
ms.openlocfilehash: 9b80ae8c247480e69025b86fac322166a4d5cb4c


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>Como ativar HTTPS em um domínio personalizado CDN do Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

O suporte a HTTPS para domínios personalizados CDN do Azure permite distribuir conteúdo seguro via SSL usando seu nome de domínio próprio para aumentar a segurança dos dados em trânsito. O fluxo de trabalho de ponta a ponta para habilitar HTTPS para o seu domínio personalizado é simplificado com a habilitação de apenas um clique e o gerenciamento do certificado completo, sem nenhum custo adicional.

É fundamental para garantir a privacidade e a integridade dos dados de todos os seus dados confidenciais de aplicativos Web em trânsito. Usar o protocolo HTTPS garante que os seus dados confidenciais são criptografados quando enviados através da internet. Ele fornece confiabilidade, autenticação e protege seus aplicativos Web contra ataques. Atualmente, o CDN do Azure oferece suporte a HTTPS em um ponto de extremidade CDN. Por exemplo, se você criar um ponto de extremidade CDN do CDN do Azure (por exemplo, https://contoso.azureedge.net), o HTTPS será habilitado por padrão. Com HTTPS de domínio personalizado, você também pode habilitar a entrega segura para um domínio personalizado (por exemplo, https://www.contoso.com). 

Estes são alguns dos atributos principais do recurso HTTPS:

- Sem custo adicional: não há custos de aquisição ou renovação do certificado e não há custo adicional para tráfego HTTPS. Você paga apenas por GB de saída do CDN.

- Ativação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo: todos os certificados de aquisição e gerenciamento são manipulados para você. Certificados são automaticamente provisionados e renovados antes da expiração. Isso remove completamente os riscos de interrupção do serviço como resultado de uma expiração do certificado.

>[!NOTE] 
>Antes de habilitar o suporte a HTTPS, você deve estabelecer uma [domínio personalizado CDN do Azure ](./cdn-map-content-to-custom-domain.md).

## <a name="step-1-enabling-the-feature"></a>Etapa 1: habilitação do recurso 

1. No [portal do Azure](https://portal.azure.com), navegue até o seu perfil CDN Verizon padrão ou premium.

2. Na lista de pontos de extremidade, clique no ponto de extremidade que contém seu domínio personalizado.

3. Clique no domínio personalizado que deseja habilitar o HTTPS.

    ![Folha de ponto de extremidade](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Clique em **Ligar** para habilitar o HTTPS e salvar a alteração.

    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>Etapa 2: validação de domínio

>[!IMPORTANT] 
>Conclua a validação completa de domínio antes de ativar o HTTPS no seu domínio personalizado. Você tem 6 dias úteis para aprovar o domínio. A solicitação será cancelada se não tiver aprovação em 6 dias úteis.  

Depois de habilitar o HTTPS em seu domínio personalizado, o nosso provedor de certificado HTTPS DigiCert validará a propriedade do seu domínio entrando em contato, com base nas informações de registro WHOIS, com o solicitante através de email (por padrão) ou telefone. Se as informações sobre o inscrito WHOIS forem privadas, o DigiCert enviará o email de verificação para **admin@*<your-domain-name.com>***.

Ao receber o email, você tem duas opções de verificação:

1. Aprovar todos os pedidos futuros feitos com a mesma conta para o mesmo domínio raiz, por exemplo, consoto.com. Essa é uma abordagem recomendada se você planeja adicionar mais domínios personalizados no futuro para o mesmo domínio raiz.
 
2. Você pode aprovar apenas o nome do host específico usado nesta solicitação. Uma aprovação adicional será necessária para as solicitações seguintes.

    Email de exemplo:
    
    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/domain-validation-email-example.png)

Após a aprovação, o DigiCert adicionará seu nome de domínio personalizado ao certificado SAN. O certificado será válido por um ano e será automaticamente renovado antes de expirar.

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>Etapa 3: aguarde a propagação e comece a usar o recurso

Depois da validação do nome de domínio, leva de 6 a 8 horas para a ativação do recurso HTTPS de domínio personalizado. Depois da conclusão do processo, o status "HTTPS personalizado" no portal do Azure é definido para "Habilitado". O HTTPS com seu domínio personalizado está pronto para uso.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. *Quem é o provedor de certificado e que tipo de certificado é usado?*

    Usamos o certificado de nomes de alternativos da entidade (SAN) fornecido pela DigiCert. Um certificado SAN pode proteger vários nomes de domínio totalmente qualificados com um certificado.

2. *Posso usar o meu certificado dedicado?*
    
    Atualmente não, mas está previsto.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Entre em contato com a Microsoft se você não receber um email dentro de 24 horas.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança que um certificado dedicado. Todos os certificados SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *Eu posso usar HTTPS do domínio personalizado com o CDN do Azure do Akamai?*

    Atualmente, esse recurso só está disponível com o CDN do Azure da Verizon. Estamos trabalhando para oferecer suporte a esse recurso com o CDN do Azure do Akamai nos próximos meses.


## <a name="next-steps"></a>Próximas etapas

- Saiba como configurar um [domínio personalizado no seu ponto de extremidade do CDN do Azure](./cdn-map-content-to-custom-domain.md)





<!--HONumber=Feb17_HO1-->


