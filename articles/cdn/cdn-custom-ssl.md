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
ms.date: 05/01/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: f64f25713dd05ece018138624a06c225218f68e2
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Tutorial: Configurar HTTPS em um domínio personalizado da CDN do Azure

> [!IMPORTANT]
> Este recurso não está disponível com produtos **CDN Standard do Azure da Akamai**. Para obter uma comparação dos recursos de CDN, confira [Visão geral da CDN do Azure](cdn-features.md).

Este tutorial mostra como habilitar o protocolo HTTPS para um domínio personalizado que está associado um ponto de extremidade da Rede de Distribuição de Conteúdo (CDN) do Azure. Usando o protocolo HTTPS em seu domínio personalizado (por exemplo, https:\//www.contoso.com), você garante que seus dados confidenciais são fornecidos com segurança por meio da criptografia SSL quando ele são enviada pela Internet. HTTPS fornece confiabilidade, autenticação e protege seus aplicativos Web contra ataques. 

A CDN do Azure dá suporte a HTTPS em um nome do host do ponto de extremidade da CDN por padrão. Por exemplo, se você criar um ponto de extremidade da CDN (como https:\//contoso.azureedge.net), o HTTPS será habilitado automaticamente.  

Alguns dos principais atributos do recurso HTTPS são:

- Sem custo adicional: não há custos de aquisição ou renovação do certificado e não há custo adicional para tráfego HTTPS. Você paga apenas pelo GB de saída da CDN.

- Habilitação simples: o provisionamento com um clique está disponível no [portal do Azure](https://portal.azure.com). Você também pode usar a API REST ou outras ferramentas de desenvolvedor para habilitar o recurso.

- Gerenciamento de certificado completo está disponível: todos os certificados de aquisição e gerenciamento são manipulados para você. Os certificados são provisionados e renovados automaticamente antes da expiração, o que remove os riscos de interrupção do serviço devido à expiração de um certificado.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por CDN 
> - Usar o seu próprio certificado
> - Validar o domínio
> - Desabilite o protocolo HTTPS em seu domínio personalizado.

## <a name="prerequisites"></a>pré-requisitos

Antes de concluir as etapas deste tutorial, crie primeiro um perfil CDN e pelo menos um ponto de extremidade da CDN. Para saber mais, confira [Início Rápido: Criar um perfil da CDN do Azure e um ponto de extremidade](cdn-create-new-endpoint.md).

Além disso, você deve associar um domínio personalizado da CDN do Azure no ponto de extremidade da CDN. Para saber mais, consulte [Tutorial: Adicionar um domínio personalizado ao seu ponto de extremidade da CDN do Azure](cdn-map-content-to-custom-domain.md)

## <a name="option-1-default-enable-the-https-feature-with-a-cdn-managed-certificate"></a>Opção 1 (padrão): habilitar o recurso HTTPS com um certificado gerenciado por CDN  

Com essa opção, o recurso HTTPS personalizado pode ser ativado com apenas alguns cliques. A CDN do Azure lida completamente com tarefas de gerenciamento de certificado, como aquisição e renovação. Depois que você habilitar o recurso, o processo será iniciado imediatamente. Se o domínio personalizado já estiver mapeado para o ponto de extremidade de CDN, nenhuma ação adicional será necessária. A CDN do Azure processará as etapas e concluirá a solicitação automaticamente. No entanto, se o domínio personalizado for mapeado em outro lugar, você deverá usar o email para validar sua propriedade de domínio.

Para habilitar HTTPS em um domínio personalizado, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), navegue até o seu perfil **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.

2. Na lista de pontos de extremidade da CDN, selecione o ponto de extremidade que contém seu domínio personalizado.

    ![Lista de pontos de extremidade](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    A página **Ponto de extremidade** é exibida.

3. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    ![Lista de domínios personalizados](./media/cdn-custom-ssl/cdn-custom-domain.png)

    A página de **Domínio personalizado** é exibida.

4. Em Tipo de gerenciamento de certificado, selecione **CDN gerenciado**.

4. Selecione **Ativado** para habilitar HTTPS.

    ![Status de HTTPS de domínio personalizado](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)


## <a name="option-2-enable-the-https-feature-with-your-own-certificate"></a>Opção 2: habilitar o recurso HTTPS com seu próprio certificado 
 
Você pode usar seu próprio certificado na CDN do Azure para entregar o conteúdo via HTTPS. Esse processo é feito por meio de uma integração com o Azure Key Vault. O Azure Key Vault permite que os clientes armazenem seus certificados com segurança. O serviço de CDN do Azure utiliza esse mecanismo seguro para obter o certificado. Usar seu próprio certificado requer algumas etapas adicionais.

### <a name="step-1-prepare-your-azure-key-vault-account-and-certificate"></a>Etapa 1: preparar a conta e o certificado do Azure Key Vault
 
1. Azure Key Vault: você deve ter uma conta do Azure Key Vault em execução sob a mesma assinatura que o perfil de CDN do Azure e os pontos de extremidade de CDN desejados para habilitar o HTTPS personalizado. Crie uma conta do Azure Key Vault caso você não tenha uma.
 
2. Certificados do Azure Key Vault: se já tiver um certificado, você poderá carregá-lo diretamente na sua conta do Azure Key Vault ou criar um novo certificado diretamente pelo Azure Key Vault a partir de uma das Autoridades de Certificação (CA) de parceiros às quais o Azure Key Vault se integra. 

### <a name="step-2-grant-azure-cdn-access-to-your-key-vault"></a>Etapa 2: conceder à CDN do Azure acesso ao seu cofre de chaves
 
Você deve conceder permissão de CDN do Azure para acessar os certificados (segredos) em sua conta do Azure Key Vault.
1. Em sua conta do Key Vault, em CONFIGURAÇÕES, selecione **Políticas de acesso** e depois **Adicionar novo** para criar uma nova política.

    ![Criar nova política de acesso](./media/cdn-custom-ssl/cdn-new-access-policy.png)

    ![Configurações de política de acesso](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

2. Em **Selecionar entidade de segurança**, procure e selecione **CDN do Azure**.

3. Em **Permissões do segredo**, selecione **Obter** para permitir que a CDN execute essas permissões para obter e listar os certificados. 

4. Selecione **OK**. 

    A CDN do Azure pode acessar este cofre de chaves e os certificados (segredos) que estão armazenados nesse cofre.
 
### <a name="step-3-select-the-certificate-for-azure-cdn-to-deploy"></a>Etapa 3: selecionar o certificado para a CDN do Azure implantar
 
1. Retorne ao portal da CDN do Azure e selecione o perfil e o ponto de extremidade de CDN desejados para habilitar o HTTPS personalizado. 

2. Na lista de domínios personalizados, selecione o domínio personalizado para o qual você deseja habilitar o HTTPS.

    A página de **Domínio personalizado** é exibida.

3. Em Tipo de gerenciamento de certificado, selecione **Usar meu próprio certificado**. 

    ![Configurar seu certificado](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Selecione um cofre de chaves, o certificado (segredo) e a versão do certificado.

    A CDN do Azure lista as seguintes informações: 
    - As contas do Key Vault para sua ID de assinatura. 
    - Os certificados (segredos) no cofre de chaves selecionado. 
    - As versões de certificado disponíveis. 
 
5. Selecione **Ativado** para habilitar HTTPS.


## <a name="validate-the-domain"></a>Validar o domínio

Se você já tiver um domínio personalizado em uso que é mapeado para o ponto de extremidade personalizado com um registro CNAME, vá para  
[O domínio personalizado é mapeado para o ponto de extremidade da CDN](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). Caso contrário, se a entrada de registro CNAME para o ponto de extremidade não existe ou contém o subdomínio cdnverify, vá para [Domínio personalizado não está mapeado para o ponto de extremidade da CDN](#custom-domain-is-not-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>O domínio personalizado é mapeado para o ponto de extremidade da CDN por meio de um registro CNAME

Quando tiver adicionado um domínio personalizado ao seu ponto de extremidade, você criou um registro CNAME na tabela de DNS do seu registrador de domínio para mapear para o nome de host do ponto de extremidade da CDN. Se esse registro CNAME ainda existir e não contiver o subdomínio cdnverify, a autoridade de certificação (CA) DigiCert o usará para validar a propriedade do seu domínio personalizado. 

O registro CNAME deve estar no formato a seguir, em que *Nome* é o nome de domínio personalizado e *Valor* é o nome de host do ponto de extremidade da CDN:

| NOME            | type  | Valor                 |
|-----------------|-------|-----------------------|
| www.contoso.com | CNAME | contoso.azureedge.net |

Para obter mais informações sobre os registros CNAME, consulte [criar o registro de DNS CNAME](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain#create-the-cname-dns-records).

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

1. No [portal do Azure](https://portal.azure.com), navegue até o seu perfil **CDN Standard do Azure da Microsoft**, **CDN Standard do Azure da Verizon** ou **CDN Premium do Azure da Verizon**.

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

    Com a **CDN do Azure da Verizon**, um certificado de Nomes Alternativos da Entidade (SAN) fornecido pela DigiCert é usado. Um certificado SAN pode proteger vários nomes de domínio totalmente qualificados com um certificado. Com a **CDN Standard do Azure da Microsoft**, um único certificado fornecido pelo DigiCert é usado.

2. Você usa TLS/SSL de SNI ou baseado em IP?

    A **CDN do Azure da Verizon** usa TLS/SSL baseado em IP. A **CDN Standard do Azure da Microsoft** usa TLS/SSL de SNI.

3. *E se eu não receber o email de verificação de domínio do DigiCert?*

    Se você tiver uma entrada CNAME para seu domínio personalizado que aponte diretamente para seu nome do host do ponto de extremidade (e você não estiver usando o nome de subdomínio cdnverify), você não receberá um email de verificação de domínio. A validação ocorre automaticamente. Caso contrário, se você não tem uma entrada CNAME e você ainda não recebeu um email em até 24 horas, entre em contato com o suporte da Microsoft.

4. *Usar um certificado SAN é menos seguro do que um certificado dedicado?*
    
    Um certificado SAN segue os mesmos padrões de criptografia e segurança de um certificado dedicado. Todos os certificados SSL emitidos usam SHA-256 para uma maior segurança do servidor.

5. *Posso usar um HTTPS de domínio personalizado com a CDN do Azure por meio do Akamai?*

    Atualmente, este recurso não está disponível com perfis **CDN Standard do Azure da Akamai**. A Microsoft está trabalhando para dar suporte a esse recurso nos próximos meses.

6. *É necessário ter um registro de Autorização de Autoridade de Certificação em meu provedor DNS?*

    Não, um registro de Autorização de Autoridade de Certificação não é necessário no momento. No entanto, caso você tenha um, ele deverá incluir o DigiCert como uma AC válida.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> - Habilite o protocolo HTTPS em seu domínio personalizado.
> - Usar um certificado gerenciado por CDN 
> - Usar o seu próprio certificado
> - Valide o domínio.
> - Desabilite o protocolo HTTPS em seu domínio personalizado.

Avance para o próximo tutorial para aprender como configurar a colocação de cache em um domínio personalizado da CDN.

> [!div class="nextstepaction"]
> [Tutorial: definir regras de cache da CDN do Azure](cdn-caching-rules-tutorial.md)

