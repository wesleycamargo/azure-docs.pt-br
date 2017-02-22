---
title: "Perguntas frequentes sobre a Autenticação Multifator do Azure | Microsoft Docs"
description: "Fornece uma lista de perguntas frequentes e respostas relacionadas ao Azure Multi-Factor Authentication. O Multi-Factor Authentication é um método de verificar a identidade de um usuário que exige mais do que um nome de usuário e uma senha. Ele fornece uma camada adicional de segurança para conexões e transações do usuário."
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
ms.date: 01/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 9617cd00ed4a5f8f867542238e5008a9a17663c9
ms.openlocfilehash: 1e01bdc099af865e01eb2784cf367e482300ade8


---
# <a name="azure-multi-factor-authentication-faq"></a>Perguntas frequentes sobre o Azure Multi-Factor Authentication
Estas perguntas frequentes respondem às perguntas comuns sobre o Azure Multi-Factor Authentication e como usar o serviço Multi-Factor Authentication, incluindo perguntas sobre o modelo de cobrança e usabilidade.

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

O resultado de verificação (sucesso ou negação) e o motivo pelo qual ele foi negado são armazenados com os dados de autenticação e estão disponíveis em relatórios de uso e de autenticação.

## <a name="billing"></a>Cobrança
A maioria das perguntas sobre cobrança pode ser respondida consultando a [página de Preços de Autenticação Multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**Minha organização será cobrada por ligações ou mensagens de texto usadas para autenticar meus usuários?**

As organizações não são cobradas por ligações individuais realizadas ou mensagens de texto enviadas aos usuários por meio da Autenticação Multifator do Azure. Os proprietários de telefones podem ser cobrados por chamadas telefônicas ou mensagens de texto recebidas, de acordo com seu serviço de telefone pessoal.

**P: O modelo de cobrança por usuário se baseia no número de usuários que estão configurados para usar o Multi-Factor Authentication ou no número de usuários que executam verificações?**

A cobrança é baseada no número de usuários configurados para usar o Multi-Factor Authentication.

**P: Como funciona a cobrança do Multi-Factor Authentication?**

Quando você usa o modelo "por usuário" ou "por autenticação", o Azure MFA é um recurso baseado em consumo. Os encargos são cobrados na assinatura do Azure da organização, assim como máquinas virtuais, sites etc.

Ao usar o modelo de licença, as licenças do Azure Multi-Factor Authentication são compradas e atribuídas aos usuários, assim como o Office 365 e outros produtos por assinatura.

Saiba mais sobre suas opções em [Como funciona como a Autenticação Multifator do Azure](multi-factor-authentication-how-it-works.md#how-to-get-azure-multi-factor-authentication)

**P: Existe uma versão gratuita do Azure Multi-Factor Authentication para administradores?**

Em alguns casos, sim. A Autenticação Multifator para Administradores do Azure oferece um subconjunto de recursos do Azure MFA sem custo adicional. Essa oferta aplica-se aos membros do grupo de Administradores Globais do Azure em instâncias do Azure Active Directory que não estão vinculadas a um provedor de Autenticação Multifator do Azure baseado em consumo. Usar um provedor do Multi-Factor Authentication atualiza todos os administradores e usuários no diretório que estão configurados para usar o Multi-Factor Authentication na versão completa do Azure Multi-Factor Authentication.

**P: Existe uma versão gratuita do Azure Multi-Factor Authentication para usuários do Office 365?**

Em alguns casos, sim. A Autenticação Multifator para Office 365 oferece um subconjunto de recursos do Azure MFA sem custo adicional. Essa oferta aplica-se aos usuários que têm uma licença do Office 365 atribuída, quando um provedor de Autenticação Multifator do Azure baseado em consumo não foi vinculado à instância correspondente do Azure Active Directory. Usar o provedor do Multi-Factor Authentication atualiza todos os administradores e usuários no diretório que estão configurados para usar o Multi-Factor Authentication na versão completa do Azure Multi-Factor Authentication.

**P: Minha organização pode alternar entre os modelos de cobrança de consumo por usuário e por autenticação a qualquer momento?**

Sua organização escolhe um modelo de cobrança quando ela cria um recurso. Você não pode alterar um modelo de cobrança depois que o recurso é provisionado. No entanto, é possível criar outro recurso da Autenticação Multifator para substituir o original. As opções de configuração e as definições do usuário não podem ser transferidas para o novo recurso.

**P: Minha organização pode alternar entre a cobrança de consumo e o modelo de licença a qualquer momento?**

Quando as licenças são adicionadas a um diretório que já tem um provedor do Azure Multi-Factor Authentication por usuário, a cobrança com base no consumo é subtraída pelo número de licenças possuídas. Se todos os usuários configurados para usar o Multi-Factor Authentication tiverem recebido licenças, o administrador poderá excluir o provedor do Azure Multi-Factor Authentication.

Você não pode combinar cobrança de consumo por autenticação com um modelo de licença. Quando um provedor do Multi-Factor Authentication por autenticação é vinculado a um diretório, a organização é cobrada por todas as solicitações de verificação de Multi-Factor Authentication, independentemente das licenças possuídas.

**P: Minha organização precisa usar e sincronizar identidades para usar o Multi-Factor Authentication?**

Quando uma organização usa um modelo de cobrança baseado em consumo, o Azure Active Directory não é necessário. A vinculação de um Provedor de Autenticação Multifator a um diretório é opcional. Se sua organização não estiver vinculada a um diretório, ela poderá implantar o Servidor Multi-Factor Authentication ou o SDK do Azure Multi-Factor Authentication local.

O Azure Active Directory é necessário para o modelo de licença porque as licenças são adicionadas ao diretório quando você compra e as atribui aos usuários no diretório.

## <a name="usability"></a>Facilidade de uso
**P: O que um usuário deverá fazer se ele não receber uma resposta por telefone ou se o telefone não estiver disponível para ele?**

Se o usuário configurou um telefone de backup, ele deverá tentar novamente e selecionar esse telefone quando solicitado na página de entrada. Se o usuário não tiver outro método configurado, o administrador da organização poderá atualizar o número atribuído ao telefone principal do usuário.

**P: O que o administrador deverá fazer se um usuário contatá-lo para tratar de uma conta que ele não consegue mais acessar?**

O administrador pode redefinir a conta do usuário pedindo que ele refaça o processo de registro. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: O que um administrador deverá fazer se o telefone de um usuário que está usando senhas de aplicativo for perdido ou roubado?**

O administrador pode excluir todas as senhas de aplicativo do usuário para impedir acesso não autorizado. Depois que o usuário tiver outro dispositivo, ele poderá recriar as senhas. Saiba mais sobre [como gerenciar configurações de usuário e dispositivo com o Azure Multi-Factor Authentication na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: E se o usuário não conseguir entrar em aplicativos que não são acessados por navegador?**

Um usuário que está configurado para usar o Multi-Factor Authentication requer uma senha de aplicativo para entrar em alguns aplicativos sem acesso por navegador. Um usuário precisar limpar (excluir) as informações de conexão, reiniciar o aplicativo e entrar usando seu nome de usuário e a senha do aplicativo.

Obtenha mais informações sobre como criar senhas de aplicativo e outra [ajuda com senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md).

> [!NOTE]
> Autenticação moderna para clientes do Office 2013
> 
> Os clientes do Office 2013 (incluindo Outlook) são compatíveis com novos protocolos de autenticação. Você pode configurar o Office 2013 para permitir o Multi-Factor Authentication. Depois de configurar o Office 2013, as senhas de aplicativo não são necessárias para clientes do Office 2013. Para saber mais, confira [Office 2013 modern authentication public preview announcement (Anúncio da visualização pública da autenticação moderna do Office 2013)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
> 
> 

**P: O que um usuário deverá fazer se não receber uma mensagem de texto ou se responder a uma mensagem de texto bidirecional, mas o tempo de verificação se esgotar?**

A entrega de mensagens de texto e o recebimento de respostas em SMS bidirecional não são garantidos, pois há fatores incontroláveis que podem afetar a confiabilidade do serviço. Esses fatores incluem o país de destino, a operadora de celular e a intensidade do sinal.

Em vez disso, os usuários que enfrentam dificuldades para receber mensagens de texto de forma confiável devem selecionar o método de chamada telefônica ou aplicativo móvel. O aplicativo móvel pode receber notificações em conexões de Wi-Fi e celular. Além disso, o aplicativo móvel pode gerar códigos de verificação mesmo quando o dispositivo não tem sinal. O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se você precisa usar mensagens de texto, é recomendável usar SMS unidirecional em vez de SMS bidirecional, quando possível. O SMS unidirecional é mais confiável e impede os usuários de incorrerem em cobranças globais de SMS ao responderem a uma mensagem de texto que foi enviada de outro país.

**P: Posso usar tokens de hardware com o Servidor Multi-Factor Authentication?**

Se estiver usando o Servidor de Autenticação Multifator, você poderá importar tokens de terceiros com TOTP (senha de uso único) baseados no tempo OATH (Autenticação Aberta) e usá-los com a verificação em duas etapas.

Será possível usar tokens ActiveIdentity que sejam TOTP OATH se você colocar o arquivo de chave secreta em um arquivos CSV e o importar para o Servidor de Autenticação Multifator. É possível usar tokens OATH com o ADFS (Serviços de Federação do Active Directory), o RADIUS (Remote Authentication Dial-In User Service), quando o sistema cliente puder processar respostas de desafio de acesso e com a autenticação baseada em formulários IIS (Servidor de Informações da Internet).

Você pode importar tokens OATH TOTP de terceiros com os seguintes formatos:  

* PSKC (Contêiner Portátil de Chave Simétrica)  
* CSV se o arquivo contiver um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo  

**P: Posso usar o Servidor Azure Multi-Factor Authentication para proteger os Serviços de Terminal?**

Sim, mas, se você estiver usando o Windows Server 2012 R2 ou posterior, apenas usando o Gateway de Área de Trabalho Remota (Gateway RD).

As alterações de segurança no Windows Server 2012 R2 mudaram a forma como o Servidor Azure Multi-Factor Authentication se conecta ao pacote de segurança LSA (Autoridade de Segurança Local) no Windows Server 2012 e versões anteriores. Para versões dos Serviços de Terminal no Windows Server 2012 ou anteriores, você pode [proteger um aplicativo com a Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver usando o Windows Server 2012 R2, você precisará do RD Gateway.

**P: Por que um usuário receberia uma ligação anônima do Multi-Factor Authentication depois de configurar uma ID de chamada?**

Quando as chamadas do Multi-Factor Authentication são feitas por meio de rede telefônica pública, às vezes, elas são roteadas por uma operadora que não é compatível com a ID de chamadas. Sendo assim, a ID de chamadas não é garantida, mesmo que o sistema do Multi-Factor Authentication sempre a envie.

## <a name="errors"></a>Erros
**P: o que os usuários deverão fazer se receberem uma mensagem de erro "A solicitação de autenticação não é para uma conta ativada" ao usar notificações de aplicativo móvel?**

Diga siga este procedimento para remover a conta do aplicativo móvel e adicione-a novamente:

1. Acesse o [seu perfil no portal do Azure](https://account.activedirectory.windowsazure.com/profile/) e entre com sua conta organizacional.
2. Escolha **Verificação de Segurança Adicional**.
3. Remova a conta existente do aplicativo móvel.
4. Clique em **Configurar**e siga as instruções para reconfigurar o aplicativo móvel.

**P: o que os usuários deverão fazer se receberem uma mensagem de erro 0x800434D4L ao entrar em um aplicativo que não é de navegador?**

Atualmente, um usuário pode usar a verificação de segurança adicional apenas com aplicativos e serviços que ele pode acessar por meio do navegador. Os aplicativos que não usam navegador (também conhecidos como *aplicativos cliente avançados*) instalados em um computador local, como o Windows PowerShell, não funcionam com contas que exigem a verificação de segurança adicional. Nesse caso, é possível que o usuário veja o aplicativo gerar um erro 0x800434D4L.

Uma solução alternativa para isso é ter contas de usuário separadas para operações administrativas e não administrativas. Posteriormente, você pode vincular caixas de correio entre a conta administrativa e a conta não administrativa para que seja possível entrar no Outlook usando a conta não administrativa. Para obter mais detalhes sobre isso, saiba como [fornecer a um administrador a capacidade de abrir e exibir o conteúdo da caixa de correio de um usuário](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Próximas etapas
Se sua pergunta não foi respondida aqui, deixe-a nos comentários na parte inferior da página. Ou então, aqui estão algumas opções adicionais para obter ajuda:

* Pesquise a [Base de Dados de Conhecimento de Suporte da Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) para obter soluções para problemas técnicos comuns.
* Pesquise e procure perguntas e respostas técnicas da comunidade ou faça sua própria pergunta nos [fóruns do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Se você for um cliente herdado do PhoneFactor e tiver perguntas ou precisar de ajuda para redefinir uma senha, use o link [redefinição de senha](mailto:phonefactorsupport@microsoft.com) para abrir um caso de suporte.
* Entre em contato com um profissional de suporte por meio do [suporte do Servidor de Autenticação Multifator do Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, é útil incluir o máximo possível de informações sobre o problema. As informações que você pode fornecer incluem a página em que viu o erro, o código de erro específico, a ID da sessão específica e a ID do usuário que viu o erro.




<!--HONumber=Jan17_HO1-->


