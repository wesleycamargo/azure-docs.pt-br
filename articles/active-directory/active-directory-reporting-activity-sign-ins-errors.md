---
title: Códigos de erro no relatório de atividade de entrada no portal do Azure Active Directory | Microsoft Docs
description: Referência de códigos de erro no relatório de atividade de entrada.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/31/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: cb636db4e6e2097f494fcbc7a6584f0172514b95
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34698502"
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Códigos de erro no relatório de atividade de entrada no portal do Azure Active Directory

Com as informações fornecidas pelo relatório de entradas do usuário, você encontra respostas para perguntas como:

- Quem entrou usando o Azure Active Directory?
- Quais aplicativos foram conectados?
- Quais entradas apresentaram falhas e por quê?

Este artigo lista os códigos de erro e as descrições relacionadas. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como exibir entradas com falha? 

O seu primeiro ponto de entrada para todos os dados de atividades de entrada é **[Entradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)** na seção **Atividade** do **Azure Active**.


![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Atividade de entrada")

Em seu relatório de entradas, você pode exibir todas as falhas de entradas selecionando **Falha** como **Status de Entrada**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Atividade de entrada")

Clique em um item na lista exibida para abrir a folha **Detalhes da Atividade: Entradas**. Essa exibição fornece todos os detalhes que o Azure Active Directory controla sobre entradas, incluindo o **código de erro de conexão** e um **motivo da falha**.

![Atividade de entrada](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Atividade de entrada")


Como alternativa ao uso do portal do Azure para acessar os dados sobre entradas, você também pode usar a [API de relatório](active-directory-reporting-api-getting-started-azure-portal.md).


A seção a seguir fornece uma visão geral completa de todos os possíveis erros e as descrições relacionadas. 

## <a name="error-codes"></a>Códigos do Erro


|Erro|DESCRIÇÃO|
|---|---|
|20001|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|20012|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|20033|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|40008|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|40009|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|40014|Há um problema com seu provedor de identidade federada. Entre em contato com seu IDP para resolver esse problema.|
|50000|Há um problema com nosso serviço de entrada. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) para resolver esse problema.|
|50001|O nome da entidade de serviço não foi encontrado neste locatário. Isso poderá acontecer se o aplicativo não tiver sido instalado pelo administrador do locatário. Ou a entidade de segurança do recurso não foi encontrada no diretório ou é inválida.|
|50002|Falha ao entrar devido ao acesso de proxy restrito no locatário. Se for sua própria política de locatário, é possível alterar as configurações restritas do locatário para corrigir esse problema|
|50003|Falha ao entrar devido à ausência de chave de assinatura ou de certificado. Isso pode ocorre porque não existia nenhuma chave de assinatura configurada no aplicativo. Confira as resoluções descritas em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se ainda houver problemas, entre em contato com o proprietário ou o administrador do aplicativo|
|50005|O usuário tentou fazer logon em um dispositivo de uma plataforma para a qual não há suporte atualmente por meio da política de acesso condicional|
|50006| Falha na verificação de assinatura devido a uma assinatura inválida. Confira a resolução descrita em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Se ainda houver problemas, entre em contato com o proprietário ou o administrador do aplicativo|
|50007|O certificado de criptografia do parceiro não foi encontrado para esse aplicativo. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) da Microsoft para corrigir isso|
|50008|A instrução de declaração SAML está ausente ou foi configurada incorretamente no token. Entre em contato com seu provedor de federação.|
|50010|Falha na validação do URI de audiência para o aplicativo pois nenhuma audiência do token foi configurada. Entre em contato com o proprietário do aplicativo|
|50011|O endereço de resposta está ausente, foi configurado incorretamente ou não coincide com os endereços de resposta configurados para o aplicativo. Teste a resolução listada em [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se ainda houver problemas, entre em contato com o proprietário ou o administrador do aplicativo|
|50013|A asserção é inválida devido a vários motivos: – O emissor do token não coincide com a versão da API dentro de seu intervalo de tempo válido -expired -malformed; – O token de atualização na asserção não é um token de atualização principal.|
|50017|Falha na validação de certificação pelos seguintes motivos:<ul><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Não é possível localizar o CrlSegment esperado</li><li>Não é possível localizar o certificado de emissão na lista de certificados confiáveis</li><li>Ponto de distribuição de CRL delta é configurado sem um ponto de distribuição de CRL correspondente</li><li>Não é possível recuperar segmentos CRL válidos devido a um problema de tempo limite</li><li>Não é possível baixar o CRL</li></ul>Entre em contato com o administrador de locatário.|
|50020|Usuário não autorizado – não é possível emitir tokens devido a um problema de versão – o nome do emissor não for especificado – problemas com o nome do emissor (nulo – comprimento máximo). Entre em contato com o proprietário do aplicativo|
|50027|Token JWT inválido pelos seguintes motivos:<ul><li>não contém declaração nonce, subdeclaração</li><li>incompatibilidade do identificador do assunto</li><li>declaração duplicada nas declarações de idToken</li><li>emissor inesperado</li><li>audiência inesperada</li><li>não está dentro de seu intervalo de tempo válido </li><li>o formato do token não é apropriado</li><li>falha na verificação de assinatura do token de ID externa do emissor.</li></ul>Entre em contato com o proprietário do aplicativo|
|50029|URI inválida – o nome de domínio contém caracteres inválidos. Entre em contato com o administrador de locatário.|
|50034|O usuário não existe no diretório. Entre em contato com seu administrador de locatário.|
|50042|O sal necessário para gerar um identificador de par está ausente na entidade de segurança. Entre em contato com o administrador de locatário.|
|50048|O assunto não é compatível com o emissor de declaração na declaração do cliente. Entre em contato com o administrador de locatário.|
|50050|A solicitação está malformada. Entre em contato com o proprietário do aplicativo.|
|50053|A conta está bloqueada porque o usuário tentou entrar muitas vezes com uma ID de usuário ou senha incorreta.|
|50055|Senha inválida; a senha inserida expirou.|
|50056|Senha inválida ou nula – A senha não existe no repositório para esse usuário|
|50057|A conta de usuário está desabilitada. A conta foi desabilitada por um administrador.|
|50058|O aplicativo tentou executar uma entrada silenciosa, e o usuário não pode entrar silenciosamente. O aplicativo precisa iniciar um fluxo interativo, dando aos usuários uma opção para entrar. Entre em contato com o proprietário do aplicativo.|
|50059|O usuário não existe no diretório. Entre em contato com seu administrador de locatário|
|50061|A solicitação de saída é inválida. Entre em contato com o proprietário do aplicativo|
|50072|Os usuários devem se registrar na autenticação de dois fatores (interativa)|
|50074|O usuário não passou no desafio de MFA.|
|50076|O usuário não passou no desafio de MFA (não interativo)|
|50079|O usuário precisa se registrar para uma autenticação de dois fatores (logons não interativos)|
|50085|O token de atualização precisa de logon do IDP social. Faça com que o usuário tente entrar novamente com o nome de usuário-senha|
|50089|O token de fluxo expirou – Falha na autenticação. Faça com que o usuário tente entrar novamente com o nome de usuário-senha|
|50097|Autenticação de dispositivo necessária – As declarações DeviceId e DeviceAltSecId são nulas OU não existe nenhum dispositivo que corresponda ao identificador de dispositivo|
|50099|A assinatura JWT do pacote é inválida. Entre em contato com o proprietário do aplicativo.|
|50105|O usuário conectado não está atribuído a uma função para o aplicativo conectado. Atribuir o usuário ao aplicativo. Para obter mais informações: [https://docs.microsoft.com/en-us/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/en-us/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|O objeto de realm de federação solicitado não existe. Entre em contato com o administrador de locatário.|
|50120|Problema com o cabeçalho do JWT. Entre em contato com o administrador de locatário.|
|50124|A transformação de declarações contém parâmetros de entrada inválidos. Entre em contato com o administrador de locatário para atualizar a política.|
|50125|Entrada foi interrompida devido a uma redefinição de senha ou a entrada de registro de senha|
|50126|Nome de usuário ou senha inválida ou Nome de usuário ou senha local inválida.|
|50127|Usuário precisa instalar um aplicativo do agente para obter acesso a esse conteúdo.|
|50128|Nome de domínio inválido – Nenhuma informação de identificação de locatário foi encontrada na solicitação nem está implícita em quaisquer credenciais fornecidas|
|50129|Dispositivo não está ingressado no local de trabalho - ingresso no local é necessário para registrar o dispositivo.|
|50130|O valor de declaração não pode ser interpretado como método de autenticação conhecido|
|50131|Usado em vários erros de acesso condicional. Por exemplo Estado de dispositivo Windows inválido, solicitação bloqueada devido a atividades suspeitas, decisões de política de acesso e política de segurança.|
|50132|As credenciais foram revogadas pelos seguintes motivos:<ul><li>O artefato de SSO é inválido ou expirou</li><li>Sessão não atualizada o suficiente para o aplicativo</li><li>Uma solicitação de entrada silenciosa foi enviada, mas a sessão do usuário com o Azure AD é inválida ou expirou.</li></ul>|
|50133|A sessão é inválida devido à expiração ou alteração de senha recente.|
|50135|É necessária a alteração de senha devido ao risco da conta|
|50136|Redirecionar a sessão msa ao aplicativo – Sessão de MSA única detectada |
|50140|Esse erro ocorreu devido à interrupção “Mantenha-me conectado” quando o usuário estava entrando. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes. |
|50143|Incompatibilidade de sessão – A sessão é inválida porque o locatário do usuário não corresponde a dica de domínio devido a diferentes recursos. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) com a ID de correlação, a ID da solicitação e o código de erro para obter mais detalhes.|
|50144|A senha do Active Directory do usuário expirou. Gere uma nova senha para o usuário ou o solicite que o usuário final use a ferramenta de redefinição de autoatendimento|
|50146|É necessário configurar esse aplicativo com uma chave de assinatura específica do aplicativo. Ele não está configurado com uma, ou a chave expirou ou ainda não é válida. Entre em contato com o proprietário do aplicativo|
|50148|O code_verifier não coincide com o code_challenge fornecido na solicitação de autorização para PKCE. Contate o desenvolvedor do aplicativo. |
|50155|Falha da autenticação do dispositivo para esse usuário|
|50158|O desafio de segurança externa não foi atendido|
|50161|As declarações enviadas pelo provedor externo não são suficientes ou há ausência da declaração solicitada para o provedor externo|
|50166|Falha ao enviar solicitação ao provedor de declarações|
|50169|O domínio não é um realm configurado do namespace do serviço atual.|
|50172|O provedor de declarações externas não foi aprovado. Entre em contato com o administrador de locatário|
|50173|Novo token de autenticação necessário. Faça com que o usuário entre novamente usando novas credenciais|
|50177|Não há suporte para o desafio externo para usuários de passagem|
|50178|Não há suporte para o controle de sessão para usuários de passagem|
|50180|Necessária a autenticação integrada do Windows. Habilite o locatário para um SSO contínuo.|
|51001|Dica de domínio não está presente no Identificador de Segurança Local – UPN local|
|51004|A conta de usuário não existe no diretório.|
|51006|Necessária a autenticação integrada do Windows. Usuário conectado usando o token de sessão com ausência da declaração wia. Solicite ao usuário que refaça o logon.|
|52004|Usuário não forneceu autorização para acessar os recursos do LinkedIn. |
|53000|Política de acesso condicional requer um dispositivo compatível, e o dispositivo não é compatível. Peça ao usuário que registre seus dispositivos com um provedor MDM aprovado, como o Intune|
|53001|A política de acesso condicional requer um dispositivo com ingresso no domínio, e o dispositivo não o é. Peça ao usuário que use um dispositivo com ingresso no domínio|
|53002|O aplicativo usado não é aprovado para o acesso condicional. O usuário precisa usar um dos aplicativos da lista de aplicativos aprovados para uso para obter acesso.|
|53003|Acesso foi bloqueado devido a políticas de acesso condicional.|
|53004|O usuário precisa concluir o processo de registro de autenticação multifator antes de acessar este conteúdo. O usuário deve se registrar para a autenticação multifator.|
|65001|O aplicativo X não tem permissão para acessar o aplicativo Y ou a permissão foi revogada. Outra possibilidade: o usuário ou administrador não aceitou usar o aplicativo com a ID X. Envie uma solicitação de autorização interativa para esse usuário e recurso. Outra possibilidade: o usuário ou administrador não aceitou usar o aplicativo com a ID X. Envie uma solicitação de autorização ao administrador do seu locatário para atuar em nome do aplicativo: Y para o recurso: Z.|
|65004|O usuário se recusou a consentir para acessar o aplicativo. Peça que o usuário tente entrar novamente e dê consentimento ao aplicativo|
|65005|A lista de acesso a recursos exigida pelo aplicativo não contém aplicativos detectáveis pelo recurso ou O aplicativo cliente solicitou acesso a recursos, os quais não foram especificados na lista de acesso a recursos exigida ou O serviço Graph retornou uma solicitação inválida ou o recurso não foi encontrado. Se o aplicativo tem suporte para SAML, pode ser que você o tenha configurado com o identificador incorreto (entidade). Experimente a resolução listada para SAML usando o link abaixo: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list)|
|70000|Concessão inválida pelos seguintes motivos:<ul><li>A asserção do SAML 2.0 solicitada tem método de confirmação de entidade inválido</li><li>Não há suporte para o fluxo de aplicativo OnBehalfOf na V2</li><li>O token de atualização primário não está assinado com a chave da sessão</li><li>Token de atualização externo inválido</li><li>A concessão do acesso foi obtida para um locatário diferente.</li></ul>|
|70001|O aplicativo de nome X não foi encontrado no locatário de nome Y. Isso pode acontecer se o aplicativo com identificador X não foi instalado pelo administrador do locatário ou aceito por algum usuário no locatário. Você pode ter configurado incorretamente o valor do identificador para o aplicativo ou enviado a solicitação de autenticação ao locatário errado|
|70002|O aplicativo retornou credenciais de cliente inválidas. Entre em contato com o proprietário do aplicativo|
|70003|O aplicativo retornou um tipo de concessão sem suporte. Entre em contato com o proprietário do aplicativo|
|70004|O aplicativo retornou um URI de redirecionamento inválido. O endereço de redirecionamento especificado pelo cliente não corresponde a nenhum endereço configurado ou a quaisquer endereços na lista de aprovação OIDC. Entre em contato com o proprietário do aplicativo|
|70005|O aplicativo retornou um tipo de resposta sem suporte pelos seguintes motivos:<ul><li>o tipo de resposta “token” não está habilitado para o aplicativo</li><li>o tipo de resposta “id_token” requer escopo “OpenID” – contém um valor de parâmetro OAuth sem suporte no wctx codificado</li></ul>Entre em contato com o proprietário do aplicativo|
|70007|O aplicativo retornou um valor sem suporte de “response_mode” ao solicitar um token. Entre em contato com o proprietário do aplicativo|
|70008|O código de autorização fornecido ou o token de atualização expirou -revoked. Peça ao usuário para tentar entrar novamente|
|70011|O escopo solicitado pelo aplicativo é inválido. Entre em contato com o proprietário do aplicativo|
|70012|Ocorreu um erro de servidor ao autenticar um usuário MSA (consumidor). Tente novamente. Se continuar a falhar, [abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) |
|70018|Código de verificação inválido devido ao usuário ter digitado o código de usuário incorreto para o fluxo de código do dispositivo. A autorização não foi aprovada|
|70019|O código de verificação expirou. Peça ao usuário para tentar entrar novamente|
|70037|Fornecida a resposta de desafio incorreta. Sessão de autenticação remota negada.|
|75001|Ocorreu um erro durante a associação de mensagem SAML.|
|75003|O aplicativo retornou um erro relacionado à associação sem suporte (resposta de protocolo do SAML não pode ser enviada por meio de associações que não sejam HTTP POST). Entre em contato com o proprietário do aplicativo|
|75005|O Azure AD não oferece suporte para a solicitação SAML enviada pelo aplicativo para logon único. Entre em contato com o proprietário do aplicativo|
|75008|A solicitação do aplicativo foi negada porque a solicitação SAML teve um destino inesperado. Entre em contato com o proprietário do aplicativo|
|75011|O método de autenticação com o qual o usuário foi autenticado no serviço não corresponde ao método de autenticação solicitado. Entre em contato com o proprietário do aplicativo|
|75016|A solicitação de autenticação SAML2 tem o NameIdPolicy inválido. Entre em contato com o proprietário do aplicativo|
|80001|O Agente de Autenticação não pode se conectar ao Active Directory. Verifique se que o agente de autenticação está instalado em um computador de ingresso no domínio que tenha a linha de visão para um DC que pode atender à solicitação de logon do usuário.|
|80002|Erro interno. A solicitação de validação de senha do atingiu o tempo limite. Não foi possível enviar a solicitação de autenticação para o serviço de identidade híbrida interno. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro|
|80003|Resposta inválida recebida pelo Agente de Autenticação. Ocorreu um erro desconhecido durante a tentativa de autenticação do Active Directory local. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80005|Agente de Autenticação: um erro desconhecido ocorreu ao processar a resposta do Agente de Autenticação. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) para obter mais detalhes sobre o erro.|
|80007|O Agente de Autenticação não pode validar a senha do usuário.|
|80010|O Agente de Autenticação não pode descriptografar a senha. |
|80011|O Agente de Autenticação não consegue recuperar a chave de criptografia.|
|80012|Os usuários tentaram fazer logon fora dos horários permitidos (isso é especificado no AD)|
|80013|A tentativa de autenticação não pôde ser concluída devido à diferença de horário entre o computador executando o agente de autenticação e o AD. Corrija os problemas de sincronização de horário|
|80014|O Agente de Autenticação atingiu o tempo limite. [Abra um tíquete de suporte](active-directory-troubleshooting-support-howto.md) com o código de erro, a ID de correlação e Datetime para obter mais detalhes sobre o erro|
|81001|O tíquete Kerberos do usuário é muito grande. Isso pode acontecer se o usuário estiver em muitos grupos e, portanto, o tíquete do Kerberos contém um número excessivo de associações de grupo. Reduza as associações de grupo do usuário e tente novamente.|
|81005|Pacote de autenticação sem suporte|
|81007|O locatário não está habilitado para SSO contínuo|


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira a [Introdução aos relatórios de atividade de entrada no portal do Azure Active Directory](active-directory-reporting-activity-sign-ins.md).
