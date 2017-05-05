---
title: "Perguntas frequentes sobre a Autenticação Multifator do Azure | Microsoft Docs"
description: "Perguntas frequentes e respostas relacionadas à Autenticação Multifator do Azure. O Multi-Factor Authentication é um método de verificar a identidade de um usuário que exige mais do que um nome de usuário e uma senha. Ele fornece uma camada adicional de segurança para conexões e transações do usuário."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 12b67d92fb955d9d20b0e86a38baa47a3717a333
ms.lasthandoff: 03/31/2017

---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Perguntas frequentes sobre a Autenticação Multifator do Azure
Estas perguntas frequentes respondem a perguntas comuns sobre a Autenticação Multifator do Azure e sobre o uso do serviço de Autenticação Multifator. Ele é dividido em perguntas sobre o serviço em geral, modelos, experiências do usuário, de cobrança e solução de problemas. 

## <a name="general"></a>Geral
**P: Como o Servidor Azure Multi-Factor Authentication lida com os dados do usuário?**

Com o Servidor de Autenticação Multifator, os dados do usuário são armazenados apenas em servidores locais. Nenhum dado de usuário persistente é armazenado na nuvem. Quando o usuário executa a verificação em duas etapas, o Servidor de Autenticação Multifator envia dados ao serviço de nuvem da Autenticação Multifator do Azure para autenticação. A comunicação entre o Servidor de Autenticação Multifator e o serviço de nuvem da Autenticação Multifator usa o protocolo SSL (Secure Sockets Layer) ou TLS (Transport Layer Security) pela porta de saída 443.

Quando solicitações de autenticação são enviadas ao serviço de nuvem, dados são coletados para relatórios de autenticação e uso. Os campos de dados incluídos em logs de verificação em duas etapas são:

* **ID Exclusiva** (ou o nome de usuário, ou a ID do Servidor Multi-Factor Authentication local)
* **Nome e Sobrenome** (opcional)
* **Endereço de Email** (opcional)
* **Número de Telefone** (ao usar uma chamada de voz ou autenticação SMS)
* **Token de Dispositivo** (ao usar a autenticação de aplicativos móveis)
* **Modo de autenticação**
* **Resultado da autenticação**
* **Nome do Servidor Multi-Factor Authentication**
* **IP do Servidor Multi-Factor Authentication**
* **IP do Cliente** (se disponível)

Os campos opcionais podem ser configurados no Servidor de Autenticação Multifator.

O resultado de verificação (sucesso ou negação) e o motivo se ele foi negado, é armazenado com os dados de autenticação. Esses dados estão disponíveis em relatórios de uso e de autenticação.

## <a name="billing"></a>Cobrança
A maioria das perguntas de cobranças podem ser respondidas consultando o [Página de preços de Autenticação Multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) ou a documentação sobre [Como obter Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md).

**P: é a minha organização cobrada para enviar as chamadas telefônicas e mensagens de texto que são usadas para autenticação?**

Não, você não será cobrado por ligações individuais realizadas ou mensagens de texto enviadas aos usuários por meio da Autenticação Multifator do Azure. Se você usar um provedor MFA por autenticação, você será cobrado para cada autenticação, mas não para o método usado.

Os usuários podem ser cobrados por chamadas telefônicas ou mensagens de texto recebidas, de acordo com seu serviço de telefone pessoal.

**P: O modelo de cobrança por usuário me cobra por todos os usuários habilitados ou somente pelos que executaram a verificação em duas etapas?**

A cobrança se baseia no número de usuários configurados para usar a Autenticação Multifator, tenham eles executado uma verificação ou não naquele mês. 

**P: Como funciona a cobrança do Multi-Factor Authentication?**

Quando você compra a Autenticação Multifator do Azure como um serviço autônomo (criando um provedor MFA por usuário ou por autenticação), a assinatura do Azure da organização é cobrada mensalmente com base no uso. Esse modelo de cobrança é semelhante às listas como o Azure para uso de máquinas virtuais e sites.

Quando você adquire uma assinatura para Autenticação Multifator do Azure (como uma licença anual por usuário ou como parte de um Office 365, o Azure AD Premium ou o pacote Enterprise Mobility + Security), sua organização só paga a taxa anual de licença para cada usuário.

Saiba mais sobre suas opções em [Como obter a Autenticação Multifator do Azure](multi-factor-authentication-versions-plans.md).

**P: Existe uma versão gratuita da Autenticação Multifator do Azure?**

Em alguns casos, sim. 

A Autenticação Multifator para administradores do Azure oferece um subconjunto de recursos do Azure MFA sem custo adicional para acessar os serviços online da Microsoft, incluindo os portais de administrador do Azure e o Office 365. Essa oferta aplica-se aos administradores do Azure em instâncias do Azure Active Directory que não tem a versão completa do Azure MFA através de uma licença MFA, um pacote ou um provedor de baseado em consumo autônomo. Se seus administradores usarem a versão gratuita, crie um provedor da Autenticação Multifator atualiza todos os administradores e usuários no diretório que estão configurados para obter a Autenticação Multifator na versão completa da Autenticação Multifator do Azure.

A Autenticação Multifator para usuários do Office 365 oferece um subconjunto de recursos do Azure MFA sem custo adicional para acessar os serviços online da Microsoft, incluindo o Exchange Online, o SharePoint Online e outros serviços do Office 365. Essa oferta aplica-se aos usuários que tenham uma licença do Office 365 atribuída, quando a instância correspondente do Azure Active Directory não tem a versão completa do Azure MFA por meio de uma licença MFA, um pacote ou um provedor de baseado em consumo autônomo. 

**P: Minha organização pode alternar entre os modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?**

Se sua organização adquirir o MFA como um serviço autônomo de cobrança com base em consumo, escolha um modelo de cobrança ao criar um provedor MFA. Depois que um provedor MFA for criado, você não poderá alterar o modelo de cobrança. No entanto, você pode excluir o provedor MFA e crie um novo com um modelo de cobrança diferente. 

Quando é criado um provedor MFA, podem ser vinculado a um Azure Active Directory (também conhecido como "locatário do Azure AD"). Se o provedor de MFA atual é vinculado a um locatário do Azure AD, com segurança pode excluir o provedor MFA e crie um novo provedor de MFA que está vinculado ao mesmo locatário Azure AD. Como alternativa, se você tiver adquirido suficiente MFA, Azure AD Premium, ou licenças do Premium (EMS) para cobrir todos os usuários que estão habilitados para MFA, você pode excluir o provedor MFA completamente.

Se, no entanto, seu provedor MFA não está vinculado a um locatário do Azure AD, ou vincular o novo provedor MFA para um anúncio do Azure diferentes locatário, as configurações de usuário e opções de configuração não serão transferidas para o novo provedor MFA. Além disso, os servidores existentes de MFA do Azure precisará ser reativado usando credenciais de ativação gerados por meio do novo provedor de MFA. Reativar os servidores MFA para vinculá-los para o novo provedor de MFA não afete de telefonema e autenticação de mensagens de texto, mas as notificações de aplicativo móvel deixará de funcionar para todos os usuários até que eles reativarem o aplicativo móvel.

Saiba mais sobre provedores MFA em [Introdução a um provedor de Autenticação Multifator do Azure](multi-factor-authentication-get-started-auth-provider.md).

**P: Minha organização pode trocar alternar entre a cobrança baseada no consumo e assinaturas (um modelo baseado em licenças) a qualquer momento?**

Em alguns casos, sim. 

Se o diretório tiver um provedor de Autenticação Multifator do Azure *por usuário*, você pode adicionar licenças do MFA. Os usuários com licenças não são somados a cobrança de baseado em consumo por usuário. Os usuários sem licenças ainda podem ser habilitados para MFA através do provedor MFA. Se você comprar e atribuir licenças para todos os usuários configurados para usar a Autenticação Multifator, poderá excluir o provedor de Autenticação Multifator do Azure. Você sempre pode criar outro provedor MFA por usuário, se você tiver mais usuários do que licenças no futuro. 

Se o diretório tiver um provedor de Autenticação Multifator do Azure *por autenticação*, será sempre cobrado por cada autenticação, desde que o provedor do MFA esteja vinculado à sua assinatura. Você pode atribuir licenças MFA para os usuários, mas você ainda será cobrado para cada solicitação de verificação em duas etapas, se se trata de uma pessoa com uma licença MFA atribuída ou não. 

**P: Minha organização precisa usar e sincronizar identidades para usar o Multi-Factor Authentication?**

Se a sua organização usa um modelo de cobrança baseado em consumo, o Azure Active Directory é opcional, mas não é necessário. Se seu provedor MFA não estiver vinculado a um locatário do Azure AD, só será possível implantar o Servidor de Autenticação Multifator ou o SDK da Autenticação Multifator do Azure local.

O Azure Active Directory é necessário para o modelo de licença porque as licenças são adicionadas ao locatário do Azure AD quando você compra e as atribui aos usuários no diretório.

## <a name="manage-and-support-user-accounts"></a>Gerenciar e dar suporte a contas de usuário

**P: o que devo dizer a meus usuários fazer se não receber uma resposta em seu telefone, ou não tiver o seu telefone com eles?**

Espero que todos os usuários configuraram mais de um método de verificação. Peça que ele tente entrar novamente, mas selecione um método de verificação diferente na página de entrada. 

Você pode apontar os usuários a [guia de solução de problemas do usuário final](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**P: o que devo fazer se um dos meus usuários não é possível obter sua conta?**

Você pode redefinir a conta do usuário fazendo com que ele refaça o processo de registro. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: o que devo fazer se um dos meus usuários perder um telefone que está usando senhas de aplicativo?**

Para evitar acesso não autorizado, exclua as senhas do todos os usuários aplicativo. Depois que o usuário tiver outro dispositivo, ele poderá recriar as senhas. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: E se um usuário não conseguir entrar em aplicativos que não são acessados por navegador?**

Se sua organização usa ainda clientes herdados e você [permitido o uso de senhas de aplicativo](multi-factor-authentication-whats-next.md#app-passwords), em seguida, os usuários não conseguem entrar nesses clientes herdados com seu nome de usuário e senha. Em vez disso, eles precisam [configurar senhas de aplicativo](./end-user/multi-factor-authentication-end-user-app-passwords.md). Os usuários devem limpar (excluir) suas informações de logon, reinicie o aplicativo e entre com seu nome de usuário e *senha de aplicativo* em vez de regulares de senha. 

Se sua organização não tiver clientes herdados, você não deve permitir que os usuários criem senhas de aplicativo. 

> [!NOTE]
> Autenticação moderna para clientes do Office 2013
> 
> Senhas de aplicativo são necessárias apenas para aplicativos que não suportam autenticação moderna. Clientes do Office 2013 oferece suporte aos protocolos de autenticação moderna, mas precisam ser configurados. Os clientes do Office mais recentes automaticamente dão suporte a protocolos de autenticação moderna. Para saber mais, confira [Office 2013 modern authentication public preview announcement (Anúncio da visualização pública da autenticação moderna do Office 2013)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: meus usuários dizem que, às vezes, eles não recebem a mensagem de texto, ou eles respondem às mensagens de texto bidirecional mas a verificação de tempo limite.**

A entrega de mensagens de texto e o recebimento de respostas em SMS bidirecional não são garantidos, pois há fatores incontroláveis que podem afetar a confiabilidade do serviço. Esses fatores incluem o país de destino, a operadora de celular e a intensidade do sinal.

Se os usuários geralmente têm problemas de forma segura, receber mensagens de texto, informe ao usar o método de chamada telefônica ou aplicativo móvel. O aplicativo móvel pode receber notificações em conexões de Wi-Fi e celular. Além disso, o aplicativo móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem sinal. O aplicativo Microsoft Authenticator está disponível para [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073) e [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Se você precisa usar mensagens de texto, é recomendável usar SMS unidirecional em vez de SMS bidirecional, quando possível. O SMS unidirecional é mais confiável e impede os usuários de incorrerem em cobranças globais de SMS ao responderem a uma mensagem de texto que foi enviada de outro país.

**P: posso alterar a quantidade de tempo que meus usuários precisam inserir o código de verificação de uma mensagem de texto antes do sistema expira?**

Em alguns casos, Sim. Você pode definir a configuração de tempo limite para mensagens de texto bidirecional no Azure MFA Server 7.0 e superior. 

Por padrão, o servidor MFA do Azure armazena senhas únicas para 300 segundos (5 minutos). Se o usuário insere seu código depois de 300 segundos, a autenticação será negada. Você pode ajustar o tempo limite configurando uma chave do registro. 

1. Vá para HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Crie uma chave de Registro DWORD chamada **pfsvc_pendingSmsTimeoutSeconds** e definir o tempo em segundos que você deseja que o servidor Azure MFA para armazenar senhas de uso únicas.

Para mensagens de texto unidirecional, servidor MFA armazena senhas únicas para 300 segundos e os MFA com base em nuvem no Azure AD armazena para 180 segundos. Essa configuração não é configurável. 

**P: Posso usar tokens de hardware com o Servidor Multi-Factor Authentication?**

Se estiver usando o Servidor de Autenticação Multifator, você poderá importar tokens de terceiros com TOTP (senha de uso único) baseados no tempo OATH (Autenticação Aberta) e usá-los com a verificação em duas etapas.

Será possível usar tokens ActiveIdentity que sejam TOTP OATH se você colocar a chave secreta em um arquivos CSV e o importar para o Servidor de Autenticação Multifator do Azure. É possível usar tokens OATH com o ADFS (Serviços de Federação do Active Directory), a autenticação baseada em formulários IIS (Servidor de Informações da Internet) e o RADIUS (Remote Authentication Dial-In User Service), quando o sistema cliente puder processar respostas de desafio de acesso.

Você pode importar tokens OATH TOTP de terceiros com os seguintes formatos:  

- PSKC (Contêiner Portátil de Chave Simétrica)  
- CSV se o arquivo contiver um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo  

**P: Posso usar o Servidor Azure Multi-Factor Authentication para proteger os Serviços de Terminal?**

Sim, mas se você estiver usando o Windows Server 2012 R2 ou posterior apenas você pode proteger os serviços de Terminal usando a área de trabalho remota (Gateway RD).

As alterações de segurança no Windows Server 2012 R2 mudaram a forma como o Servidor de Autenticação Multifator do Azure se conecta ao pacote de segurança LSA (Autoridade de Segurança Local) no Windows Server 2012 e versões anteriores. Para versões dos Serviços de Terminal no Windows Server 2012 ou anteriores, você pode [proteger um aplicativo com a Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver usando o Windows Server 2012 R2, você precisará do RD Gateway.

**P: Eu configurei um ID do Chamador no Servidor MFA, mas os usuários ainda recebem chamadas de Autenticação Multifator de um chamador anônimo.**

Quando as chamadas do Multi-Factor Authentication são feitas por meio de rede telefônica pública, às vezes, elas são roteadas por uma operadora que não é compatível com a ID de chamadas. Sendo assim, a ID de chamadas não é garantida, mesmo que o sistema do Multi-Factor Authentication sempre a envie.

**P: por que meus usuários estão sendo solicitados para registrar as informações de segurança?**
Há vários motivos que os usuários podem ser solicitados para registrar as informações de segurança:

- O usuário foi habilitado para MFA pelo administrador no Azure AD, mas não tem informações de segurança ainda registradas para sua conta. 
- O usuário ativou autoatendimento para redefinição de senha no Azure AD. As informações de segurança ajudará a redefinir sua senha no futuro, se ele esquecerem.
- O usuário acessou um aplicativo que tem uma política de acesso condicional para exigir a MFA e não foi registrado anteriormente para MFA.
- O usuário está registrando um dispositivo com o Azure AD (incluindo a junção do Azure AD) e sua organização exigir MFA para registro de dispositivo, mas o usuário não foi registrado anteriormente para MFA.
- O usuário está gerando Windows Hello para a empresa no Windows 10 (que exige MFA) e não foi registrado anteriormente para MFA.
- A organização criou e ativado uma diretiva de registro de MFA que foi aplicada ao usuário.
- O usuário registrado para MFA anteriormente, mas escolher um método de verificação que um administrador como desabilitado. O usuário, portanto, deve passar pelo registro MFA novamente para selecionar um novo método de verificação padrão.


## <a name="errors"></a>Erros
**P: o que os usuários deverão fazer se receberem uma mensagem de erro "A solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativo móvel?**

Diga siga este procedimento para remover a conta do aplicativo móvel e adicione-a novamente:

1. Acesse o [seu perfil no portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta organizacional.
2. Escolha **Verificação de Segurança Adicional**.
3. Remova a conta existente do aplicativo móvel.
4. Clique em **Configurar**e siga as instruções para reconfigurar o aplicativo móvel.

**P: o que os usuários deverão fazer se receberem uma mensagem de erro 0x800434D4L ao entrar em um aplicativo que não é de navegador?**

O erro 0x800434D4L ocorre ao tentar entrar em um aplicativo não-navegador, instalado em um computador local, que não funciona com a conta que exigem a verificação em duas etapas. 

Uma solução alternativa para isso é ter contas de usuário separadas para operações administrativas e não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, saiba como [fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Próximas etapas
Se sua pergunta não foi respondida aqui, deixe-a nos comentários na parte inferior da página. Ou então, aqui estão algumas opções adicionais para obter ajuda:

* Pesquise a [Base de Dados de Conhecimento de Suporte da Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) para obter soluções para problemas técnicos comuns.
* Pesquise e procure perguntas e respostas técnicas da comunidade ou faça sua própria pergunta nos [fóruns do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Se você for um cliente herdado do PhoneFactor e tiver perguntas ou precisar de ajuda para redefinir uma senha, use o link [redefinição de senha](mailto:phonefactorsupport@microsoft.com) para abrir um caso de suporte.
* Entre em contato com um profissional de suporte por meio do [suporte do Servidor de Autenticação Multifator do Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, é útil incluir o máximo possível de informações sobre o problema. As informações que você pode fornecer incluem a página em que viu o erro, o código de erro específico, a ID da sessão específica e a ID do usuário que viu o erro.


