---
title: SSPR do Azure AD com write-back de senha | Microsoft Docs
description: "Usando o Azure AD e o Azure AD Connect para o write-back de senhas no diretório local"
services: active-directory
keywords: "Gerenciamento de senhas do Active Directory, gerenciamento de senhas, autoatendimento de redefinição de senha do Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 9733774570f3148e0092f42c1321b4fac1c80b54
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2017
---
# <a name="password-writeback-overview"></a>Visão geral de write-back de senha

O write-back de senha permite que você configure o Azure AD para o write-back de senhas no Active Directory local. Ele remove a necessidade de configurar e gerenciar uma solução complicada de autoatendimento de redefinição de senha local e fornece uma maneira conveniente baseada em nuvem para que os usuários redefinam suas senhas locais onde quer que estejam. O write-back de senha é um componente do [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) que pode ser habilitado e usado pelos assinantes atuais das [Edições do Azure Active Directory](active-directory-editions.md) Premium.

O write-back de senha fornece os seguintes recursos:

* **Comentários sem atraso** – o write-back de senha é uma operação síncrona. Os usuários serão notificados imediatamente se suas senhas não atenderem à política ou se não puderem ser redefinidas nem alteradas por qualquer motivo.
* **Dá suporte à redefinição de senhas de usuários que usam o AD FS ou outras tecnologias de federação** – com o write-back de senha, desde que as contas dos usuários federados sejam sincronizadas no locatário do Azure AD, eles poderão gerenciar suas instalações senhas do AD local na nuvem.
* **Dá suporte à redefinição de senhas de usuários que usam a [sincronização de hash de senha](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)** – quando o serviço de redefinição de senha detecta que uma conta de usuário sincronizado está habilitada para a sincronização de hash de senha, redefinimos a senha local e na nuvem dessa conta simultaneamente.
* **Dá suporte à alteração de senhas no painel de acesso e no Office 365** – quando os usuários federados ou sincronizados com senha alteram suas senhas expiradas ou não expiradas, fazemos o write-back das senhas no ambiente do AD local.
* **Dá suporte ao write-back de senhas quando um administrador as redefine no portal do Azure** – sempre que um administrador redefine uma senha de usuário no [portal do Azure](https://portal.azure.com), se esse usuário for federado ou sincronizado com senha, definiremos a senha escolhida pelo administrador no AD local também. Isso não é suportado atualmente no Portal de Administração do Office.
* **Impõe políticas de senha do AD local** – quando um usuário redefine sua senha, verificamos se ele atende à política do AD local antes de confirmá-la nesse diretório. Isso inclui histórico, complexidade, tempo, filtros de senha e qualquer outra restrição de senha que você tenha definido no AD local.
* **Não exige regras de firewall de entrada** – o write-back de senha usa uma retransmissão do Barramento de Serviço do Azure como um canal de comunicação subjacente, o que significa que você não precisa abrir nenhuma porta de entrada no firewall para que esse recurso funcione.
* **Sem suporte em contas de usuário existentes em grupos protegidos no Active Directory local** – para obter mais informações sobre grupos protegidos, consulte [Contas e grupos protegidos no Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Como funciona o write-back de senha

Quando um usuário federado ou sincronizado com hash de senha redefine ou altera sua senha na nuvem, ocorre o seguinte:

1. Verificamos que tipo de senha o usuário tem.
    * Se virmos que a senha é gerenciada localmente
        * Verificaremos se o serviço de write-back está instalado e em execução; nesse caso, permitimos que o usuário continue
        * Se o serviço de write-back não estiver em execução, informamos ao usuário que sua senha não pode ser redefinida no momento
2. Em seguida, o usuário passa pelas entradas de autenticação pertinentes e alcança a tela de redefinição de senha.
3. O usuário seleciona uma nova senha e a confirma.
4. Ao clicar em enviar, criptografamos a senha em texto sem formatação com uma chave simétrica que foi criada durante o processo de instalação de write-back.
5. Depois de criptografar a senha, nós a incluímos em um conteúdo que é enviado por um canal HTTPS para a retransmissão do barramento de serviço específica ao locatário (que também configuramos para você durante o processo de instalação de write-back). Esta retransmissão é protegida por uma senha gerada aleatoriamente que somente a sua instalação local sabe.
6. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente reativado e vê que há uma solicitação de redefinição pendente.
7. O serviço procurará o usuário em questão usando o atributo de âncora de nuvem. Para que essa pesquisa funcione:

    * O objeto de usuário deve existir no espaço conector do AD
    * O objeto de usuário deve estar vinculado ao objeto de MV correspondente
    * O objeto de usuário deve estar vinculado ao objeto de conector do AAD correspondente.
    * O vínculo do objeto de conector do AD com o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx`. <br> <br>
    Quando a chamada é proveniente da nuvem, o mecanismo de sincronização usa o atributo cloudAnchor para pesquisar o objeto de espaço conector do AAD, segue o vínculo de volta para o objeto de MV e segue o vínculo de volta para o objeto do AD. Como pode haver vários objetos do AD (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do `Microsoft.InfromADUserAccountEnabled.xxx` link para escolher o correto.

    > [!Note]
    > Como resultado dessa lógica, o Azure AD Connect deve conseguir se comunicar com o Emulador PDC para que o write-back de senha funcione. Se você precisar habilitar isso manualmente, poderá conectar o Azure AD Connect ao Emulador PDC clicando com o botão direito do mouse nas **propriedades** do conector de sincronização do Active Directory e selecionando **configurar partições de diretório**. Em seguida, procure a seção **configurações de conexão do controlador de domínio** e marque a caixa **usam somente controladores de domínio preferenciais**. Mesmo se o controlador de domínio preferencial não for um emulador PDC, o Azure AD Connect tentará se conectar ao PDC para fazer o write-back de senha.

8. Depois que a conta de usuário é encontrada, tentamos redefinir a senha diretamente na floresta do AD pertinente.
9. Se a operação de definição de senha for bem-sucedida, informaremos ao usuário que sua senha foi alterada.
    > [!NOTE]
    > Caso a senha do usuário seja sincronizada com o Azure AD usando a sincronização de senha, há uma possibilidade de que a política de senha local seja mais fraca do que a política de senha na nuvem. Nesse caso, ainda impomos a política local, seja ela qual for e, em vez disso, permitimos que a sincronização de hash de senha sincronize o hash dessa senha. Isso garante que a política local é imposta na nuvem, independentemente de você usar a sincronização de senha ou a federação para fornecer o logon único.

10. Se a operação de definição de senha falhar, retornamos o erro para o usuário e o deixamos tentar novamente.
    * A operação poderá falhar devido aos seguintes
        * O serviço foi desativado
        * A senha selecionada não atendeu às políticas da organização
        * Não conseguimos encontrar o usuário no AD local

    Temos uma mensagem específica para muitos desses casos e informamos ao usuário o que podem fazer para resolver o problema.

## <a name="configuring-password-writeback"></a>Configurando o write-back de senha

Recomendamos que você use o recurso de atualização automática do [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) se desejar usar o write-back de senha.

Não há mais suporte para DirSync e Sincronização do Azure AD como meios de habilitar write-back de senha. O artigo [Atualização do DirSync e do Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md) tem mais informações para ajudá-lo na transição.

As etapas a seguir pressupõem que você já tenha configurado o Azure AD Connect em seu ambiente usando as configurações [Expressas](./connect/active-directory-aadconnect-get-started-express.md) ou [Personalizadas](./connect/active-directory-aadconnect-get-started-custom.md).

1. Para configurar e habilitar o write-back de senha, faça logon no servidor do Azure AD Connect e inicie o assistente de configuração **Azure AD Connect**.
2. Na tela de Boas-Vindas, clique em **Configurar**.
3. Na tela Tarefas adicionais, clique em **Personalizar opções de sincronização** e, em seguida, escolha **Próximo**.
4. Na tela Conectar-se ao Azure AD, insira uma credencial de Administrador Global e escolha **Próximo**.
5. Nas telas Conectar seus diretórios e Filtragem de domínio e UO, você pode escolher **Próximo**.
6. Na tela de Recursos opcionais, marque a caixa ao lado de **Write-back de senha** e clique em **Próximo**.
   ![Habilitar write-back de senha no Azure AD Connect][Writeback]
7. Na tela Pronto para configurar, clique em **Configurar** e aguarde a conclusão do processo.
8. Ao visualizar Configuração concluída, clique em **Sair**

Para tarefas comuns de solução de problemas relacionadas ao write-back de senha, consulte a seção [Solucionar problemas de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) em nosso artigo de solução de problemas.

## <a name="active-directory-permissions"></a>Permissões do Active Directory

A conta especificada no utilitário Azure AD Connect deve ter Permissões de Redefinição de Senha, Alteração de Senha, Permissões de Gravação em lockoutTime e Permissões de Gravação em pwdLastSet, direitos estendidos no objeto raiz de **cada domínio** nessa floresta **OU** nas UOs do usuário que você deseja que estejam no escopo do SSPR.

Se não tiver certeza sobre qual conta a descrição acima se refere, abra a interface do usuário da configuração do Azure Active Directory Connect e clique na opção Exibir configuração atual. A conta à qual você precisa adicionar a permissão é listada em “Diretórios Sincronizados”

Definir essas permissões permite que a conta de serviço MA de cada floresta gerencie senhas em nome das contas de usuário na floresta. **Se você não atribuir essas permissões, mesmo que o write-back pareça estar configurado corretamente, os usuários verão erros ao tentar gerenciar suas senhas locais na nuvem.**

> [!NOTE]
> Pode levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos no diretório.
>

Para configurar as permissões apropriadas para que ocorra o write-back de senha

1. Abra Usuários e Computadores do Active Directory com uma conta que tem as permissões de administração de domínio apropriadas
2. No menu Exibir, verifique se a opção Recursos Avançados está ativada
3. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e escolha Propriedades
    * Clique na guia Segurança
    * Em seguida, clique em Avançado.
4. Na guia Permissões, clique em Adicionar
5. Selecione a conta à qual as permissões estão sendo aplicadas (na instalação do Azure AD Connect)
6. Na caixa suspensa Aplica-se a, selecione Objetos Descendentes de Usuário
7. Em Permissões, marque as caixas para o seguinte
    * Unexpire-Password
    * Redefinir senha
    * Alterar senha
    * Gravar lockoutTime
    * Gravar pwdLastSet
8. Clique em Aplicar/OK para aplicar e sair das caixas de diálogo abertas.

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento do write-back de senha

Para obter informações sobre o licenciamento, consulte [Licenças necessárias para o write-back de senha](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) ou os seguintes sites

* [Site de Preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Secure Productive Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Modos de autenticação locais com suporte para write-back de senha

O write-back de senha funciona para os seguintes tipos de senha de usuário:

* **Usuários somente de nuvem**: o write-back de senha não se aplica nesse caso, pois não há uma senha local
* **Usuários sincronizados com senha**: write-back de senha com suporte
* **Usuários federados**: suporte para write-back de senha
* **Usuários de autenticação de passagem**: suporte para write-back de senha

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>Operações de administrador e usuário com suporte para write-back de senha

É feito o write-back das senhas em todas as seguintes situações:

* **Operações do usuário final com suporte**
  * Qualquer operação de alteração de senha voluntária de autoatendimento do usuário final
  * Qualquer operação para forçar o autoatendimento de alteração de senha do usuário final (por exemplo, expiração de senha)
  * Qualquer redefinição de senha de autoatendimento do usuário final do [Portal de Redefinição de Senha](https://passwordreset.microsoftonline.com)
* **Operações do administrador com suporte**
  * Qualquer operação de alteração de senha voluntária de autoatendimento do administrador
  * Qualquer operação para forçar o autoatendimento de alteração de senha do administrador (por exemplo, expiração de senha)
  * Qualquer redefinição de senha de autoatendimento do administrador do [Portal de Redefinição de Senha](https://passwordreset.microsoftonline.com)
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [portal clássico do Azure](https://manage.windowsazure.com)
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [portal do Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Operações de administrador e usuário sem suporte para write-back de senha

**Não** é feito o write-back das senhas em nenhuma das seguintes situações:

* **Operações do usuário final sem suporte**
  * Qualquer usuário final que redefine sua própria senha usando o PowerShell v1, v2 ou a API do Graph do Azure AD
* **Operações do administrador sem suporte**
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [portal de gerenciamento do Office](https://portal.office.com)
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador do PowerShell v1, v2 ou da API do Graph do Azure AD

Embora estejamos trabalhando para remover essas limitações, não temos ainda uma linha do tempo específica que possamos divulgar.

## <a name="password-writeback-security-model"></a>Modelo de segurança de write-back de senha

O write-back de senha é um serviço altamente seguro.  Para garantir que suas informações estarão protegidas, habilitamos um modelo de segurança com 4 camadas que é descrito abaixo.

* **Retransmissão do barramento de serviço específica ao locatário**
  * Quando você configura o serviço, configuramos uma retransmissão do barramento de serviço específica ao locatário que é protegida por uma senha forte gerada aleatoriamente à qual a Microsoft nunca tem acesso.
* **Chave de criptografia de senha criptograficamente forte e bloqueada**
  * Depois que a retransmissão do barramento de serviço é criada, criamos uma chave simétrica forte que usamos para criptografar a senha quando ela é transmitida. Essa chave existe apenas no repositório secreto de sua empresa na nuvem, que é amplamente bloqueado e auditado, assim como qualquer senha no diretório.
* **TLS padrão da indústria**
  1. Quando uma operação de redefinição ou alteração de senha ocorre na nuvem, criptografamos a senha de texto sem formatação com sua chave pública.
  2. Depois, nós a colocamos em uma mensagem HTTPS, que é enviada por um canal criptografado usando certificados SSL da Microsoft para a retransmissão do barramento de serviço.
  3. Depois que a mensagem chega ao Barramento de Serviço, o agente local é ativado e se autentica no Barramento de Serviço usando a senha forte gerada anteriormente.
  4. O agente local recebe a mensagem criptografada e a descriptografa usando a chave privada que geramos.
  5. Em seguida, o agente local tenta definir a senha por meio da API SetPassword do AD DS.
     * Essa etapa é o que nos permite impor a política de senha local do AD (complexidade, duração, histórico, filtros, etc.) na nuvem.
* **Políticas de expiração de mensagem** 
  * Caso a mensagem fique no Barramento de Serviço devido ao serviço local estar desativado, ela atingirá o tempo limite e será removida após alguns minutos para aumentar ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de criptografia de write-back de senha

A seguir, são descritas as etapas de criptografia pelas quais uma solicitação de redefinição de senha passa depois que um usuário a envia, antes que ela chegue ao ambiente local, para garantir a segurança e a confiabilidade máximas do serviço.

* **Etapa 1 - criptografia de senha com chave RSA de 2048 bits** - depois que um usuário envia uma senha para ser gravada no local, primeiro, a senha enviada em si é criptografada com uma chave RSA de 2048 bits.
* **Etapa 2 - criptografia em nível de pacote com AES-GCM** - em seguida, todo o pacote (senha + metadados necessários) é criptografado usando AES-GCM. Essa criptografia impede que qualquer pessoa com acesso direto ao canal do Barramento de Serviço subjacente exiba/viole o conteúdo.
* **Etapa 3 – toda a comunicação ocorre via TLS/SSL** – toda a comunicação com o Barramento de Serviço ocorre em um canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
* **Substituição de chave automática a cada seis meses** – automaticamente, a cada seis meses, ou sempre que o write-back de senha for desabilitado/habilitado novamente no Azure AD Connect, substituímos automaticamente todas essas chaves para garantir a segurança máxima do serviço.

### <a name="password-writeback-bandwidth-usage"></a>Uso de largura de banda de write-back de senha

O write-back de senha é um serviço de largura de banda baixa que envia solicitações de volta para o agente local somente nas seguintes circunstâncias:

1. Duas mensagens são enviadas quando o recurso é habilitado ou desabilitado por meio do Azure AD Connect.
2. Uma mensagem é enviada a cada cinco minutos como uma pulsação de serviço enquanto o serviço está em execução.
3. Duas mensagens são enviadas sempre que uma nova senha é enviada
    * Primeira mensagem, como uma solicitação para executar a operação
    * Segunda mensagem, que contém o resultado da operação e é enviada nas seguintes circunstâncias:
        * Sempre que uma nova senha é enviada durante uma redefinição de senha de autoatendimento do usuário.
        * Sempre que uma nova senha é enviada durante uma operação de alteração de senha do usuário.
        * Sempre que uma nova senha é enviada durante uma redefinição de senha do usuário iniciada pelo administrador (somente nos portais de administração do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre a largura de banda e o tamanho da mensagem

O tamanho de cada uma das mensagens descritas acima normalmente é inferior a 1 kb; mesmo sob cargas extremas, o próprio serviço de write-back de senha consome alguns quilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, somente quando for solicitado por uma operação de atualização de senha, e como o tamanho da mensagem é bem pequeno, o uso da largura de banda da capacidade de write-back será efetivamente muito pequeno para ter qualquer impacto real mensurável.

## <a name="next-steps"></a>Próximas etapas

* [Como concluir uma implementação do SSPR com êxito?](active-directory-passwords-best-practices.md)
* [Redefinir ou alterar sua senha](active-directory-passwords-update-your-own-password.md).
* [Registro para redefinição de senha de autoatendimento](active-directory-passwords-reset-register.md).
* [Você tem uma pergunta sobre licenciamento?](active-directory-passwords-licensing.md)
* [Quais dados são usados pelo SSPR e quais dados você deve preencher para seus usuários?](active-directory-passwords-data.md)
* [Quais métodos de autenticação estão disponíveis para os usuários?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Quais são as opções de política com o SSPR?](active-directory-passwords-policy.md)
* [Como faço para informar sobre a atividade no SSPR?](active-directory-passwords-reporting.md)
* [Quais são todas as opções no SSPR e o que elas significam?](active-directory-passwords-how-it-works.md)
* [Acho que algo não está funcionando. Como faço para solucionar o problema no SSPR?](active-directory-passwords-troubleshoot.md)
* [Tenho uma pergunta que não foi respondida em nenhum lugar](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Habilitar write-back de senha no Azure AD Connect"
