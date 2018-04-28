---
title: Tutorial - Configurar HTTPS em um domínio personalizado da CDN do Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como habilitar ou desabilitar o HTTPS no ponto de extremidade da CDN do Azure com um domínio personalizado.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/12/2018
ms.author: rli
ms.custom: mvc
ms.openlocfilehash: a8f2da5a68552c35a55a7bbb764afc7b36af6962
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Este tutorial mostra como habilitar o protocolo HTTP para um domínio personalizado que está associado um ponto de extremidade da Rede de Distribuição de Conteúdo (CDN) do Azure. Usando o protocolo HTTPS em seu domínio personalizado (por exemplo, https:\//www.contoso.com), você garante que seus dados confidenciais são fornecidos com segurança por meio da criptografia SSL quando ele são enviada pela Internet. HTTPS fornece confiabilidade, autenticação e protege seus aplicativos Web contra ataques. O fluxo de trabalho para habilitar HTTPS é simplificado com a habilitação de apenas um clique e o gerenciamento do certificado completo, sem nenhum custo adicional.

A CDN do Azure dá suporte a HTTPS em um nome do host do ponto de extremidade da CDN por padrão. Por exemplo, se você criar um ponto de extremidade da CDN (como https:\//contoso.azureedge.net), o HTTPS será habilitado automaticamente.  

Alguns dos principais atributos do recurso HTTPS são:

- Sem custo adicional: não há custos de aquisição ou renovação do certificado e não há custo adicional para tráfego HTTPS. Você paga apenas pelo GB de saída da CDN.

- Habilitação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo: todos os certificados de aquisição e gerenciamento são manipulados para você. Os certificados são provisionados e renovados automaticamente antes da expiração, o que remove os riscos de interrupção do serviço devido à expiração de um certificado.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Habilitar o protocolo HTTPS em seu domínio personalizado
> - Validar o domínio
> - Desabilitar o protocolo HTTPS em seu domínio personalizado

## <a name="prerequisites"></a>pré-requisitos

Antes de concluir as etapas deste tutorial, crie primeiro um perfil CDN e pelo menos um ponto de extremidade da CDN. Para saber mais, confira [Início Rápido: Criar um perfil da CDN do Azure e um ponto de extremidade](cdn-create-new-endpoint.md).

Além disso, você deve associar um domínio personalizado da CDN do Azure no ponto de extremidade da CDN. Para saber mais, consulte [Tutorial: Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md)

## <a name="enable-the-https-feature"></a>Habilitar o recurso HTTPS

Para habilitar HTTPS em um domínio personalizado, siga estas etapas:

1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.

2. Na lista de pontos de extremidade da CDN, selecione o ponto de extremidade que contém seu domínio personalizado.

    ![Lista de pontos de extremidade](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    A página **Ponto de extremidade** é exibida.

3. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain.png)

    A página de **Domínio personalizado** é exibida.

4. Selecione **Ativado** para habilitar o HTTPS e, em seguida, clique em **Aplicar**.

    ![Status de HTTPS de domínio personalizado](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="validate-the-domain"></a>Validar o domínio

Se você já tiver um domínio personalizado em uso que é mapeado para o ponto de extremidade personalizado com um registro CNAME, vá para  
[O domínio personalizado é mapeado para o ponto de extremidade da CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Caso contrário, se a entrada de registro CNAME para o ponto de extremidade não existe ou contém o subdomínio cdnverify, vá para [Domínio personalizado não está mapeado para o ponto de extremidade da CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>O domínio personalizado é mapeado para o ponto de extremidade da CDN por meio de um registro CNAME

Quando tiver adicionado um domínio personalizado ao seu ponto de extremidade, você criou um registro CNAME na tabela de DNS do seu registrador de domínio para mapear para o nome de host do ponto de extremidade da CDN. Se esse registro CNAME ainda existir e não contiver o subdomínio cdnverify, a autoridade de certificação (CA) DigiCert o usará para validar a propriedade do seu domínio personalizado. 

O registro CNAME deve estar no formato a seguir, em que *Nome* é o nome de domínio personalizado e *Valor* é o nome de host do ponto de extremidade da CDN:

| NOME            | type  | Valor                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Para obter mais informações sobre os registros CNAME, consulte [criar o registro de DNS CNAME](https://docs.microsoft.com/en-us/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records).

Se o registro CNAME estiver no formato correto, o DigiCert automaticamente verificará seu nome de domínio personalizado e o adicionará ao certificado de Nomes Alternativos da Entidade (SAN). O DigitCert não enviará um email de verificação, e você não precisará aprovar sua solicitação. O certificado é válido por um ano e será renovado automaticamente antes de expirar. Avance para a [Aguardar a propagação](#wait-for-propagation). 

A validação automática geralmente leva alguns minutos. Se você não vir o seu domínio validado em uma hora, abra um tíquete de suporte.

>[!NOTE]
>Caso você tenha um registro de CAA (Autorização de Autoridade de Certificação) com o provedor DNS, ele deverá incluir o DigiCert como uma AC válida. Um registro de CAA permite que os proprietários do domínio especifiquem com seus provedores DNS quais ACs estão autorizadas a emitir certificados para seus domínios. Se uma AC receber um pedido de um certificado para um domínio que tem um registro de CAA e a AC não estiver listada como um emissor autorizado, ela será proibida de emitir o certificado para esse domínio ou subdomínio. Para obter informações sobre como gerenciar registros CAA, consulte [Gerenciar registros CAA](https://support.dnsimple.com/articles/manage-caa-record/). Para uma ferramenta de registro CAA, consulte [Ajuda do registro CAA](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>O domínio personalizado não é mapeado para o ponto de extremidade da CDN

Se a entrada do registro CNAME do ponto de extremidade não existir mais ou contiver o subdomínio cdnverify, siga o restante das instruções nesta etapa.

Depois de habilitar o HTTPS em seu domínio personalizado, a AC (autoridade de certificação) do DigiCert valida a propriedade de seu domínio entrando em contato com o inscrito, de acordo com as informações do inscrito [WHOIS](http://whois.domaintools.com/) do domínio. O contato é feito pelo endereço de email (por padrão) ou pelo número de telefone listado no registro WHOIS. Conclua a validação completa de domínio antes de ativar o HTTPS no seu domínio personalizado. Você tem seis dias úteis para aprovar o domínio. As solicitações que não foram aprovadas em até seis dias úteis são canceladas automaticamente. 

![Registro WHOIS](./media/cdn-custom-ssl/whois-record.png)

O DigiCert também envia um email de verificação para endereços de email adicionais. Se as informações do inscrito WHOIS forem particulares, verifique se é possível aprovar diretamente por meio de um dos seguintes endereços:

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

Você deverá receber um email em poucos minutos, semelhante ao exemplo a seguir, solicitando que você aprove a solicitação. Se estiver usando um filtro de spam, adicione admin@digicert.com à sua lista de permissões. Se você não receber um email em até 24 horas, contate o suporte da Microsoft.
    
![Email de validação de domínio](./media/cdn-custom-ssl/domain-validation-email.png)

Quando você clicar no link de aprovação, será direcionado para o seguinte formulário de aprovação online: 
    
![Formulário de validação de domínio](./media/cdn-custom-ssl/domain-validation-form.png)

Siga as instruções do formulário; você tem duas opções de verificação:

- Você pode aprovar todos os pedidos futuros feitos por meio da mesma conta para o mesmo domínio raiz; por exemplo, contoso.com. Essa abordagem é recomendada se você pretende adicionar domínios personalizados adicionais para o mesmo domínio raiz.

- Você pode aprovar apenas o nome do host específico usado nesta solicitação. Uma aprovação adicional é necessária para as solicitações posteriores.

Após a aprovação, o DigiCert adiciona seu nome de domínio personalizado ao certificado da SAN. O certificado é válido por um ano e será renovado automaticamente antes de expirar.

## <a name="wait-for-propagation"></a>Aguardar a propagação

Depois da validação do nome de domínio, é necessário de 6 a 8 horas para a ativação do recurso HTTPS de domínio personalizado. Quando o processo for concluído, o status HTTPS Personalizado no portal do Azure será definido como **Habilitado** e as quatro etapas da operação da caixa de diálogo do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado agora está pronto para usar o HTTPS.

![Habilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você habilita o HTTPS. Depois de habilitar o HTTPS, quatro etapas de operação são exibidas na caixa de diálogo do domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais da subetapa são exibidos na etapa conforme ela avança. Nem todas essas subetapas ocorrerão. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Etapa da operação | Detalhes da subetapa da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| | Sua solicitação HTTPS está sendo enviada. |
| | Sua solicitação HTTPS foi enviada com êxito. |
| 2 Validação de domínio | O domínio é validado automaticamente se CNAME for mapeado para o ponto de extremidade da CDN. Caso contrário, uma solicitação de verificação será enviada ao email listado no registro do seu domínio (WHOIS inscrito). Verifique o domínio assim que possível. |
| | Sua propriedade do domínio foi validada com êxito. |
| | A solicitação de validação da propriedade do domínio expirou (provavelmente, o cliente não respondeu dentro de 6 dias). O HTTPS não será habilitado no domínio. * |
| | A solicitação de validação da propriedade do domínio foi rejeitada pelo cliente. O HTTPS não será habilitado no domínio. * |
| 3 Provisionamento de certificado | No momento, a autoridade de certificação está emitindo o certificado necessário para habilitar o HTTPS em seu domínio. |
| | O certificado foi emitido e está sendo implantado na rede CDN. Isso pode levar até 6 horas. |
| | O certificado foi implantado com êxito na rede CDN. |
| 4 Concluído | O HTTPS foi habilitado com êxito em seu domínio. |

\* Essa mensagem não é exibida, a menos que tenha ocorrido um erro. 

Se ocorrer um erro antes que a solicitação seja enviada, a seguinte mensagem de erro será exibida:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="clean-up-resources---disable-https"></a>Limpar recursos - desabilitar HTTPS

Nas etapas anteriores, você habilitou o protocolo HTTPS em seu domínio personalizado. Se você não deseja mais usar seu domínio personalizado com HTTPS, você pode desabilitar o HTTPS, executando as seguintes etapas:

### <a name="disable-the-https-feature"></a>Desabilitar o recurso HTTPS 

1. No [Portal do Azure](https://portal.azure.com), navegue até o perfil **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.

2. Na lista de pontos de extremidade, clique no ponto de extremidade que contém seu domínio personalizado.

3. Clique no domínio personalizado no qual você deseja desabilitar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Clique em **Desativado** para desabilitar o HTTPS e, em seguida, clique em **Aplicar**.

    ![Caixa de diálogo personalizada HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Aguardar a propagação

Depois que o recurso HTTPS do domínio personalizado for desabilitado, poderá levar até 6-8 horas para que ele entre em vigor. Quando o processo for concluído, o status HTTPS Personalizado no portal do Azure será definido como **Desabilitado** e as três etapas da operação da caixa de diálogo do domínio personalizado serão marcadas como concluídas. Seu domínio personalizado não pode mais usar o HTTPS.

![Desabilitar a caixa de diálogo do HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Andamento da operação

A tabela a seguir mostra o andamento da operação que ocorre quando você desabilita o HTTPS. Depois de desabilitar o HTTPS, três etapas de operação são exibidas na caixa de diálogo Domínio personalizado. À medida que cada etapa fica ativa, detalhes adicionais são exibidos na etapa. Depois que uma etapa for concluída com êxito, uma marca de seleção verde é exibida ao lado dela. 

| Andamento da operação | Detalhes da operação | 
| --- | --- |
| 1 Enviando a solicitação | Enviando a solicitação |
| 2 Desprovisionamento de certificado | Excluindo o certificado |
| 3 Concluído | Certificado excluído |

## <a name="frequently-asked-questions"></a>Perguntas frequentes

1. *Quem é o provedor de certificado e que tipo de certificado é usado?*

    A Microsoft usa o certificado SAN (Nomes Alternativos da Entidade) fornecido pelo DigiCert. Um certificado SAN pode proteger vários nomes de domínio totalmente qualificados com um certificado.

2. *Posso usar o meu certificado dedicado?*
    
    Atualmente não, mas está previsto.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Se você tiver uma entrada CNAME para seu domínio personalizado que aponte diretamente para seu nome do host do ponto de extremidade (e você não estiver usando o nome de subdomínio cdnverify), você não receberá um email de verificação de domínio. A validação ocorre automaticamente. Caso contrário, se você não tem uma entrada CNAME e você ainda não recebeu um email em até 24 horas, entre em contato com o suporte da Microsoft.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança de um certificado dedicado. Todos os certificados SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *Posso usar um HTTPS de domínio personalizado com a CDN do Azure por meio do Akamai?*

    Atualmente, esse recurso só está disponível com o CDN do Azure da Verizon. A Microsoft está trabalhando para dar suporte a esse recurso com a CDN do Azure por meio do Akamai nos próximos meses.

6. *É necessário ter um registro de Autorização de Autoridade de Certificação em meu provedor DNS?*

    Não, um registro de Autorização de Autoridade de Certificação não é necessário no momento. No entanto, caso você tenha um, ele deverá incluir o DigiCert como uma AC válida.


## <a name="next-steps"></a>Próximas etapas

O que você aprendeu:

> [!div class="checklist"]
> - Habilitar o protocolo HTTPS em seu domínio personalizado
> - Validar o domínio
> - Desabilitar o protocolo HTTPS em seu domínio personalizado

Avance para o próximo tutorial para aprender como configurar a colocação de cache em um domínio personalizado da CDN.

> [!div class="nextstepaction"]
> [Controlar o comportamento de cache da CDN do Azure com regras de cache](cdn-caching-rules.md)

