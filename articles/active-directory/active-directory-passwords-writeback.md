---
title: SSPR do Azure AD com write-back de senha | Microsoft Docs
description: "Usar o Azure AD e o Azure AD Connect para o write-back de senhas para um diretório local"
services: active-directory
keywords: "Gerenciamento de senha do Active Directory, gerenciamento de senha, autoatendimento de redefinição de senha do Azure AD"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: b4a14d3c79f93988eeac1525da09cf70dc2de634
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2018
---
# <a name="password-writeback-overview"></a>Visão geral de write-back de senha

Com o write-back de senha, você pode configurar o Azure Active Directory (Azure AD) para o write-back de senhas para o Active Directory local. O write-back de senha remove a necessidade de configurar e gerenciar uma solução complicada de autoatendimento de redefinição de senha (SSPR) local e fornece uma maneira conveniente baseada em nuvem para que os usuários redefinam suas senhas locais onde quer que estejam. O write-back de senha é um componente do [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) que pode ser habilitado e usado pelos assinantes atuais das [Edições do Azure Active Directory](active-directory-whatis.md) Premium.

O write-back de senha fornece os seguintes recursos:

* **Fornece comentários sem atraso** – o write-back de senha é uma operação síncrona. Os usuários serão notificados imediatamente se suas senhas não atenderem à política ou se não puderem ser redefinidas nem alteradas por qualquer motivo.
* **É compatível com a redefinição de senhas de usuários que usam os AD FS (Serviços de Federação do Active Directory) ou outras tecnologias de federação**: com o write-back de senha, desde que as contas dos usuários federados sejam sincronizadas no locatário do Azure AD, eles poderão gerenciar suas senhas do Active Directory local da nuvem.
* **É compatível com a redefinição de senhas de usuários que usam a** [sincronização de hash de senha](./connect/active-directory-aadconnectsync-implement-password-synchronization.md): quando o serviço de redefinição de senha detecta que uma conta de usuário sincronizado está habilitada para a sincronização de hash de senha, redefinimos a senha local e na nuvem dessa conta simultaneamente.
* **É compativel com alterações de senhas no painel de acesso e no Office 365**: quando os usuários federados ou sincronizados com senha alteram suas senhas expiradas ou não expiradas, fazemos o write-back das senhas para seu ambiente do Active Directory local.
* **É compatível com write-back de senhas quando um administrador as redefine no portal do Azure**: sempre que um administrador redefine uma senha de usuário no [portal do Azure](https://portal.azure.com), se esse usuário for federado ou sincronizado com senha, definiremos a senha escolhida pelo administrador no Active Directory local também. Essa funcionalidade não é compatível atualmente com o Portal de Administração do Office.
* **Impõe políticas de senha do Active Directory local**: quando um usuário redefine sua senha, verificamos se ela atende à sua política do Active Directory local antes de confirmá-la nesse diretório. Essa revisão inclui a verificação do histórico, da complexidade, do tempo, dos filtros de senha e de qualquer outra restrição de senha que você tenha definido no Active Directory local.
* **Não exige nenhuma regra de firewall de entrada**: o write-back de senha usa uma retransmissão do Barramento de Serviço do Azure como um canal de comunicação subjacente. Você não precisa abrir portas de entrada no firewall para que esse recurso funcione.
* **Sem suporte em contas de usuário existentes em grupos protegidos no Active Directory local**: para obter mais informações sobre grupos protegidos, consulte [Contas e grupos protegidos no Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Como funciona o write-back de senha

Quando um usuário federado ou sincronizado com hash de senha redefine ou altera sua senha na nuvem, ocorre o seguinte:

1. Verificamos que tipo de senha o usuário tem. Se virmos que a senha é gerenciada localmente:
   * Verificaremos se o serviço de write-back está instalado e em execução. Se ele estiver em execução, deixaremos que o usuário continue.
   * Se o serviço de write-back não estiver em execução, informaremos ao usuário que sua senha não pode ser redefinida no momento.
2. Em seguida, o usuário passa pelas entradas de autenticação pertinentes e alcança a página de **redefinição de senha**.
3. O usuário seleciona uma nova senha e a confirma.
4. Quando o usuário seleciona **Enviar**, criptografamos a senha em texto sem formatação com uma chave simétrica que foi criada durante o processo de instalação de write-back.
5. Depois de criptografar a senha, nós a incluímos em um conteúdo que é enviado por um canal HTTPS para a retransmissão do barramento de serviço específica ao locatário (que também configuramos para você durante o processo de instalação de write-back). Esta retransmissão é protegida por uma senha gerada aleatoriamente que somente a sua instalação local sabe.
6. Depois que a mensagem chega ao barramento de serviço, o ponto de extremidade de redefinição de senha é automaticamente reativado e vê que há uma solicitação de redefinição pendente.
7. O serviço procurará o usuário em questão usando o atributo de âncora de nuvem. Para que essa pesquisa funcione:

    * O objeto de usuário deve existir no espaço conector do Active Directory.
    * O objeto de usuário deve estar vinculado ao objeto de MV (metaverso) correspondente.
    * O objeto de usuário deve estar vinculado ao objeto de do conector do Azure Active Directory correspondente.
    * O vínculo do objeto de conector do Active Directory com o MV deve ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` no vínculo. <br> <br>
    Quando a chamada vem da nuvem, o mecanismo de sincronização usa o atributo **cloudAnchor** para procurar o objeto de espaço do conector do Azure Active Directory. Ele então segue o vínculo de volta para o objeto de MV e segue o vínculo de volta para o objeto do Active Directory. Como pode haver vários objetos do Active Directory (várias florestas) para o mesmo usuário, o mecanismo de sincronização depende do vínculo `Microsoft.InfromADUserAccountEnabled.xxx` para escolher o correto.

    > [!Note]
    > Como resultado dessa lógica, o Azure AD Connect deve conseguir se comunicar com o Emulador PDC (controlador de domínio primário). Se você precisar habilitar isso manualmente, poderá conectar o Azure AD Connect ao emulador PDC. Clique com o botão direito do mouse nas **propriedades** do conector de sincronização do Active Directory, em seguida, selecione **configurar partições de diretório**. Em seguida, procure a seção **configurações de conexão do controlador de domínio** e marque a caixa **usar somente controladores de domínio preferenciais**. Mesmo se o controlador de domínio preferencial não for um emulador PDC, o Azure AD Connect tentará se conectar ao PDC para fazer o write-back de senha.

8. Depois que a conta de usuário é encontrada, tentamos redefinir a senha diretamente na floresta do Active Directory pertinente.
9. Se a operação de definição de senha for bem-sucedida, informaremos ao usuário que sua senha foi alterada.
    > [!NOTE]
    > Caso a senha do usuário seja sincronizada com o Azure AD usando a sincronização de senha, há uma possibilidade de que a política de senha local seja mais fraca do que a política de senha na nuvem. Nesse caso, ainda impomos a política local, seja ela qual for e, em vez disso, permitimos que a sincronização de hash de senha sincronize o hash dessa senha. Essa política garante que a política local seja imposta na nuvem, independentemente de você usar a sincronização de senha ou a federação para fornecer o logon único.

10. Se a operação de definição de senha falhar, retornaremos o erro para o usuário e o deixamos tentar novamente. A operação poderá falhar porque:
    * O serviço foi desativado.
    * A senha selecionada não atendeu às políticas da organização.
    * Nós não pudemos localizar o usuário no Active Directory local.

    Temos uma mensagem específica para muitos desses casos e informamos ao usuário o que ele pode fazer para resolver o problema.

## <a name="configure-password-writeback"></a>Configurar o write-back de senha

Recomendamos que você use o recurso de atualização automática do [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) se desejar usar o write-back de senha.

Não há mais suporte para DirSync e Azure AD Sync como uma maneira de habilitar o write-back de senha. Para obter mais informações para ajudá-lo com a transição, consulte [Atualizar do DirSync e Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

As etapas a seguir pressupõem que você já tenha configurado o Azure AD Connect em seu ambiente usando as configurações [Expressas](./connect/active-directory-aadconnect-get-started-express.md) ou [Personalizadas](./connect/active-directory-aadconnect-get-started-custom.md).

1. Para configurar e habilitar o write-back de senha, entre no servidor do Azure AD Connect e inicie o assistente de configuração do **Azure AD Connect**.
2. Na página de **Boas-vindas**, selecione **Configurar**.
3. Na página **Tarefas adicionais**, selecione **Personalizar opções de sincronização** e, em seguida, selecione **Próximo**.
4. Na página **Conectar-se ao Azure AD**, insira uma credencial de administrador global e escolha **Próximo**.
5. Nas páginas de filtragem **Conectar diretórios** e **Domínio/OU**, selecione **Próximo**.
6. Na página **Recursos opcionais**, selecione a caixa ao lado de **Write-back de senha** e selecione **Próximo**.
   ![Habilitar write-back de senha no Azure AD Connect][Writeback]
7. Na página **Pronto para configurar**, clique em **Configurar** e aguarde a conclusão do processo.
8. Quando você vir a configuração terminar, selecione **Sair**.

Para tarefas comuns de solução de problemas relacionadas ao write-back de senha, consulte a seção [Solucionar problemas de write-back de senha](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) em nosso artigo de solução de problemas.

## <a name="active-directory-permissions"></a>Permissões do Active Directory

A conta especificada no utilitário do Azure AD Connect deve ter os seguintes itens configurados para estar no escopo para SSPR:

* **Redefinir senha** 
* **Alterar senha** 
* **Permissões de gravação** em `lockoutTime`  
* **Permissões de gravação** em `pwdLastSet`
* **Direitos estendidos** em:
   * O objeto raiz de *cada domínio* na floresta
   * As unidades organizacionais (OUs) do usuário que você deseja que estejam no escopo para SSPR

Se não tiver certeza sobre a qual conta a conta descrita acima se refere, abra a interface do usuário da configuração do Azure Active Directory Connect e selecione a opção **Exibir configuração atual**. A conta à qual você precisa adicionar a permissão é listada em **Diretórios Sincronizados**.

Se você definir essas permissões, a conta de serviço de MA de cada floresta poderá gerenciar senhas em nome das contas de usuário nessa floresta. 

> [!IMPORTANT]
> Se você não atribuir essas permissões, mesmo que o write-back pareça estar configurado corretamente, os usuários verão erros ao tentar gerenciar suas senhas locais na nuvem.
>

> [!NOTE]
> Pode levar até uma hora ou mais para que essas permissões sejam replicadas em todos os objetos no diretório.
>

Para configurar as permissões apropriadas para que ocorra o write-back de senha, conclua as etapas a seguir:

1. Abra Usuários e Computadores do Active Directory com uma conta que tem as permissões de administração de domínio apropriadas.
2. No menu **Exibir**, verifique se a opção **Recursos avançados** está ativada.
3. No painel esquerdo, clique com o botão direito do mouse no objeto que representa a raiz do domínio e escolha **Propriedades** > **Segurança** > **Avançado**.
4. Na guia **Permissões**, selecione **Adicionar**.
5. Selecione a conta à qual as permissões estão sendo aplicadas (na configuração do Azure AD Connect).
6. Na lista suspensa **Aplica-se a**, selecione os objetos **Usuário descendente**.
7. Em **Permissões**, marque as caixas para o seguinte:
    * **Redefinir senha**
    * **Alterar senha**
    * **Gravar lockoutTime**
    * **Gravar pwdLastSet**
8. Selecione **Aplicar/OK** para aplicar as alterações e sair das caixas de diálogo abertas.

## <a name="licensing-requirements-for-password-writeback"></a>Requisitos de licenciamento do write-back de senha

Para obter informações sobre o licenciamento, consulte [Licenças necessárias para o write-back de senha](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) ou os seguintes sites:

* [Site de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Modos de autenticação locais com suporte para write-back de senha

O write-back de senha oferece suporte para os seguintes tipos de senha de usuário:

* **Usuários somente de nuvem**: o write-back de senha não se aplica nesse caso, pois não há uma senha local.
* **Usuários sincronizados com senha**: write-back de senha com suporte.
* **Usuários federados**: suporte para write-back de senha.
* **Usuários de autenticação de passagem**: suporte para write-back de senha.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Operações de administrador e usuário compatíveis com write-back de senha

É feito o write-back das senhas em todas as seguintes situações:

* **Operações do usuário final com suporte**
  * Qualquer operação de alteração de senha voluntária de autoatendimento do usuário final
  * Qualquer operação para forçar o autoatendimento de alteração de senha do usuário final, por exemplo, expiração de senha
  * Qualquer redefinição de senha de autoatendimento do usuário final originada do [portal de redefinição de senha](https://passwordreset.microsoftonline.com)
* **Operações do administrador com suporte**
  * Qualquer operação de alteração de senha voluntária de autoatendimento do administrador
  * Qualquer operação para forçar o autoatendimento de alteração de senha do administrador, por exemplo, expiração de senha
  * Qualquer redefinição de senha de autoatendimento do administrador originada do [portal de redefinição de senha](https://passwordreset.microsoftonline.com)
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [portal do Azure](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Operações de administrador e usuário sem suporte para write-back de senha

*Não* é feito o write-back das senhas em nenhuma das seguintes situações:

* **Operações do usuário final sem suporte**
  * Qualquer usuário final que redefine sua própria senha usando o PowerShell versão 1, versão 2 ou a API do Graph do Azure AD
* **Operações do administrador sem suporte**
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador no [portal de gerenciamento do Office](https://portal.office.com)
  * Qualquer redefinição de senha do usuário final iniciada pelo administrador do PowerShell versão 1, versão 2 ou da API do Graph do Azure AD

Estamos trabalhando para remover essas limitações, não temos ainda uma linha do tempo específica que possamos divulgar.

## <a name="password-writeback-security-model"></a>Modelo de segurança de write-back de senha

O write-back de senha é um serviço altamente seguro. Para garantir que suas informações estarão protegidas, habilitamos um modelo de segurança com quatro camadas que é descrito abaixo:

* **Retransmissão do barramento de serviço específica ao locatário**
  * Quando você configura o serviço, configuramos uma retransmissão do barramento de serviço específica ao locatário que é protegida por uma senha forte gerada aleatoriamente à qual a Microsoft nunca tem acesso.
* **Chave de criptografia de senha criptograficamente forte e bloqueada**
  * Depois que a retransmissão do barramento de serviço é criada, criamos uma chave simétrica forte que usamos para criptografar a senha quando ela é transmitida. Essa chave existe apenas no repositório secreto de sua empresa na nuvem, que é amplamente bloqueado e auditado, assim como qualquer outra senha no diretório.
* **Segurança de camada de transporte (TLS) padrão do setor**
  1. Quando uma operação de redefinição ou alteração de senha ocorre na nuvem, criptografamos a senha de texto sem formatação com sua chave pública.
  2. Nós a colocamos em uma mensagem HTTPS, que é enviada por um canal criptografado usando certificados SSL da Microsoft para a retransmissão do barramento de serviço.
  3. Depois que a mensagem chega ao barramento de serviço, o agente local é ativado e se autentica no barramento de serviço usando a senha forte gerada anteriormente.
  4. O agente local recebe a mensagem criptografada e a descriptografa usando a chave privada que geramos.
  5. O agente local tenta definir a senha por meio da API SetPassword do AD DS. Essa etapa é o que nos permite impor a política de senha local do Active Directory (como complexidade, duração, histórico e filtros) na nuvem.
* **Políticas de expiração de mensagem** 
  * Caso a mensagem fique no barramento de serviço devido ao serviço local estar desativado, ela atingirá o tempo limite e será removida após alguns minutos. O tempo limite e a remoção da mensagem aumenta ainda mais a segurança.

### <a name="password-writeback-encryption-details"></a>Detalhes de criptografia de write-back de senha

Depois que um usuário envia uma redefinição de senha, a solicitação de redefinição passa por várias etapas de criptografia antes de chegar no seu ambiente local. Essas etapas de criptografia garantem a máxima segurança e confiabilidade do serviço. Elas são descritas da seguinte maneira:

* **Etapa 1: criptografia de senha com chave RSA de 2048 bits**: depois que um usuário envia uma senha para write back local, a senha enviada em si é criptografada com uma chave RSA de 2048 bits.
* **Etapa 2: criptografia em nível de pacote com AES-GCM**: todo o pacote (senha + metadados necessários) é criptografado usando AES-GCM. Essa criptografia impede que qualquer pessoa com acesso direto ao canal do barramento de serviço subjacente exiba ou viole o conteúdo.
* **Etapa 3: toda comunicação ocorre via TLS/SSL**: toda comunicação com o Barramento de Serviço ocorre em um canal SSL/TLS. Essa criptografia protege o conteúdo de terceiros não autorizados.
* **Substituição da chave automática a cada seis meses**: a cada seis meses, ou sempre que o write-back de senha for desabilitado e habilitado novamente no Azure AD Connect. Nós revertemos automaticamente todas as chaves para garantir a máxima segurança do serviço.

### <a name="password-writeback-bandwidth-usage"></a>Uso de largura de banda de write-back de senha

O write-back de senha é um serviço de largura de banda baixa que envia solicitações de volta para o agente local somente nas seguintes circunstâncias:

* Duas mensagens são enviadas quando o recurso for habilitado ou desabilitado por meio do Azure AD Connect.
* Uma mensagem é enviada a cada cinco minutos como uma pulsação de serviço enquanto o serviço está em execução.
* Duas mensagens são enviadas sempre que uma nova senha é enviada:
    * A primeira mensagem é uma solicitação para executar a operação.
    * A segunda mensagem contém o resultado da operação e é enviada nas seguintes circunstâncias:
        * Sempre que uma nova senha é enviada durante uma redefinição de senha de autoatendimento do usuário.
        * Sempre que uma nova senha é enviada durante uma operação de alteração de senha do usuário.
        * Sempre que uma nova senha é enviada durante uma redefinição de senha do usuário iniciada pelo administrador (somente nos portais de administração do Azure).

#### <a name="message-size-and-bandwidth-considerations"></a>Considerações sobre a largura de banda e o tamanho da mensagem

O tamanho de cada mensagem descrita anteriormente normalmente é inferior a 1 KB. Mesmo sob cargas extremas, o próprio serviço de write-back de senha consome alguns quilobits por segundo de largura de banda. Como cada mensagem é enviada em tempo real, somente quando for solicitado por uma operação de atualização de senha, e como o tamanho da mensagem é bem pequeno, o uso da largura de banda da capacidade de write-back será efetivamente muito pequeno para ter qualquer impacto real mensurável.

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
