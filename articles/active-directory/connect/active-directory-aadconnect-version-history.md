---
title: "Azure AD Connect: histórico de liberação de versão | Microsoft Docs"
description: "Este artigo lista todas as versões do Azure AD Connect e do Azure AD Sync"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
ms.translationtype: Human Translation
ms.sourcegitcommit: 7c4d5e161c9f7af33609be53e7b82f156bb0e33f
ms.openlocfilehash: 7983ffa4bf7cb63f985d1a8f14d6dd834e679cf5
ms.contentlocale: pt-br
ms.lasthandoff: 05/04/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: histórico de lançamento de versão
A equipe do Azure AD (Azure Active Directory) atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi projetado para ajudá-lo a controlar as versões que foram lançadas e compreender se você precisa atualizar para a versão mais recente ou não.

Esta é uma lista de tópicos relacionados:


Tópico |  Detalhes
--------- | --------- |
Etapas para atualizar do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](active-directory-aadconnect-upgrade-previous-version.md) do Azure AD Connect.
Permissões necessárias | Para obter permissões necessárias para aplicar uma atualização, veja [contas e permissões](./active-directory-aadconnect-accounts-permissions.md#upgrade).
Baixar| [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="114860"></a>1.1.486.0
Lançamento: abril de 2017

**Problemas corrigidos:**
* Corrigido o problema em que o Azure AD Connect não será instalado com êxito na versão localizada do Windows Server.

## <a name="114840"></a>1.1.484.0
Lançamento: abril de 2017

**Problemas conhecidos:**

* Essa versão do Azure AD Connect não será instalada com êxito se as seguintes condições forem todas verdadeiras:
   1. Você está executando a atualização DirSync in-loco ou a nova instalação do Azure AD Connect.
   2. Você está usando uma versão localizada do Windows Server em que o nome do grupo de Administradores interno no servidor não é "Administradores".
   3. Você pode usar o SQL Server 2012 Express LocalDB padrão instalado com o Azure AD Connect ou usar seu próprio SQL completo.

**Problemas corrigidos:**

Sincronização do Azure AD Connect
* Corrigido um problema em que o agendador de sincronização ignora a etapa de sincronização inteiro se um ou mais conectores estiver sem o perfil de execução para essa etapa de sincronização. Por exemplo, você adicionou manualmente um conector usando Soynchronization Service Manager sem criar um perfil de execução de Importação Delta para ele. Essa correção garante que o agendador de sincronização continua a executar Importação Delta para outros conectores.
* Corrigido um problema em que o Serviço de Sincronização imediatamente para de processar um perfil de execução quando ele encontra um problema com uma das etapas de execução. Essa correção garante que o Serviço de Sincronização ignora essa etapa de execução e continua processando o restante. Por exemplo, você tem um perfil de execução de Importação Delta para seu conector do AD com várias etapas de execução (uma para cada domínio do AD local). O Serviço de Sincronização executará a Importação Delta com os outros domínios do AD, mesmo que um deles tenha problemas de conectividade de rede.
* Corrigido um problema que faz com que a atualização do Azure AD Connector seja ignorada durante a Atualização Automática.
* Corrigido um problema que faz com que o Azure AD Connect determine incorretamente se o servidor é um controlador de domínio durante a instalação o que, por sua vez, faz com que a atualização do DirSync falhe.
* Corrigido um problema que faz com que a atualização in-loco de DirSync não crie perfis de execução para o Azure AD Connector.
* Corrigido um problema em que a interface de usuário do Synchronization Service Manager não responde ao tentar configurar o Generic LDAP Connector.

Gerenciamento dos AD FS
* Corrigido um problema em que o assistente do Azure AD Connect falha se o nó primário do AD FS foi movido para outro servidor.

SSO da Área de Trabalho
* Corrigido um problema no assistente do Azure AD Connect em que a tela de entrada não permite habilitar o recurso de SSO da Área de Trabalho se você escolheu a Sincronização de Senha como a opção de Entrada durante a nova instalação.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Azure AD Connect Sync agora oferecre suporte para uso da Conta de Serviço Virtual, Conta de Serviço Gerenciado e Conta de Serviço Gerenciado de Grupo como sua conta de serviço. Isso se aplica à nova instalação do Azure AD Connect, somente. Ao instalar o Azure AD Connect:
    * Por padrão, o assistente do Azure AD Connect criará uma Conta de Serviço Virtual e utilizará como sua conta de serviço.
    * Se você estiver instalando em um controlador de domínio, o Azure AD Connect reverterá para o comportamento anterior onde criará uma conta de usuário de domínio e utilizará como sua conta de serviço.
    * É possível substituir o comportamento padrão fornecendo uma das seguintes opções:
      * Uma Conta de Serviço Gerenciado de Grupo
      * Uma Conta de Serviço Gerenciado
      * Uma conta de usuário do domínio
      * Uma conta de usuário local
* Anteriormente, se você atualizasse para uma nova versão do Azure AD Connect contendo atualizações de conexão ou alterações na regra de sincronização, o Azure AD Connect dispararia um ciclo de sincronização completo. Agora, o Azure AD Connect dispara seletivamente a etapa de Importação Completa somente para conectores com atualização e a etapa de Sincronização Completa somente para conectores com alterações de regra de sincronização.
* Anteriormente, o limite de exclusão de exportação só se aplicava às exportações disparadas pelo Agendador de Sincronização. Agora, o recurso é estendido para incluir exportações disparadas manualmente pelo cliente usando o Synchronization Service Manager.
* No seu locatário do Azure AD, há uma configuração de serviço que indica se o recurso de Sincronização de Senha está habilitado para o seu locatário ou não. Anteriormente, era fácil que configuração de serviço fosse configurado incorretamente pelo Azure AD Connect quando havia um servidor de preparo e um ativo. Agora, o Azure AD Connect tentará manter a configuração de serviço consistente com o servidor ativo do Azure AD Connect, somente.
* O assistente do Azure AD Connect agora detecta e retorna um aviso se o AD local não tiver Lixeira do AD ativada.
* Anteriormente, Exportar para o Azure AD expirava e falhava se o tamanho combinado dos objetos no lote excedesse um determinado limite. Agora, o Serviço de Sincronização tentará reenviar os objetos em lotes menores se o problema for encontrado.
* O aplicativo de Gerenciamento de Chaves de Serviço de Sincronização foi removido do Menu Iniciar do Windows. O Gerenciamento de chave de criptografia continuará a oferecer suporte por meio da interface de linha de comando usando miiskmu.exe. Para obter informações sobre o gerenciamento de chave de criptografia, consulte o artigo [Abandonando a chave de criptografia de sincronização do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-change-serviceacct-pass#abandoning-the-azure-ad-connect-sync-encryption-key).
* Anteriormente, se você alterava a senha da conta de serviço de sincronização do Azure AD Connect, o Serviço de Sincronização não poderia iniciar corretamente até que você tivesse abandonado a chave de criptografia e reinicializado a senha da conta do serviço de sincronização do Azure AD Connect. Agora, isso não é mais necessário.

SSO da Área de Trabalho

* O assistente do Azure AD Connect não exige mais porta 9090 para ser aberta na rede ao configurar a Autenticação de Passagem e SSO de Área de Trabalho. Somente a porta 443 é exigida. 

## <a name="114430"></a>1.1.443.0
Lançamento: março de 2017

**Problemas corrigidos:**

Sincronização do Azure AD Connect
* Correção de um problema que causa uma falha do assistente do Azure AD Connect se o nome de exibição do Conector do Azure AD não contém o domínio onmicrosoft.com inicial atribuído ao locatário do Azure AD.
* Correção de um problema que causa uma falha do assistente do Azure AD Connect ao estabelecer uma conexão com o banco de dados SQL quando a senha da Conta de Serviço de Sincronização contém caracteres especiais, como apóstrofo, dois-pontos e espaço.
* Correção de um problema que faz com que o erro “A dimagem tem uma âncora diferente da imagem” ocorra em um servidor do Azure AD Connect no modo de preparo, após a exclusão temporária de um objeto do AD local da sincronização e sua inclusão novamente na sincronização.
* Correção de um problema que faz com que o erro “O objeto localizado pelo DN é um fantasma” ocorra em um servidor do Azure AD Connect no modo de preparo, após a exclusão temporária de um objeto do AD local da sincronização e sua inclusão novamente na sincronização.

Gerenciamento dos AD FS
* Correção de um problema no qual o assistente do Azure AD Connect não atualiza a configuração do AD FS nem define as declarações corretas no objeto de confiança de terceira parte confiável após a configuração de um ID de Logon Alternativo.
* Correção de um problema no qual o assistente do Azure AD Connect não consegue manipular corretamente os servidores do AD FS cujas contas de serviço são configuradas usando o formato userPrincipalName, em vez do formato sAMAccountName.

Autenticação de Passagem
* Correção de um problema que causa uma falha do assistente do Azure AD Connect se a Autenticação de Passagem é selecionada, mas o registro de seu conector falha.
* Correção de um problema que faz com que o assistente do Azure AD Connect ignore as verificações de validação no método de conexão selecionado quando o recurso SSO da Área de Trabalho está habilitado.

Redefinição de senha
* Correção de um problema que pode fazer com que o servidor do Azure AAD Connect não tente se reconectar se a conexão for interrompida por um firewall ou proxy.

**Novos recursos/melhorias:**

Sincronização do Azure AD Connect
* Agora, o cmdlet Get-ADSyncScheduler retorna uma nova propriedade booliana chamada SyncCycleInProgress. Se o valor retornado for verdadeiro, isso significa que há um ciclo de sincronização agendado em andamento.
* A pasta de destino de armazenamento dos logs de instalação e configuração do Azure AD Connect foi movida de %localappdata%\AADConnect para %programdata%\AADConnect, a fim de melhorar a acessibilidade aos arquivos de log.

Gerenciamento dos AD FS
* Adição de suporte para atualização do Certificado SSL do Farm do AD FS.
* Adição de suporte para gerenciamento do AD FS 2016.
* Agora é possível especificar uma gMSA (Conta de Serviço Gerenciado de Grupo) existente durante a instalação do AD FS.
* Agora é possível configurar o SHA-256 como o algoritmo de hash de assinatura para o objeto de confiança de terceira parte confiável do Azure AD.

Redefinição de senha
* Introdução de aprimoramentos para permitir que o produto funcione em ambientes com regras de firewall mais rígidas.
* Aumento na confiabilidade de conexão com o Barramento de Serviço do Azure.

## <a name="113800"></a>1.1.380.0
Lançamento: dezembro de 2016

**Problema corrigido:**

* Foi corrigido o problema em que a regra de declaração de issuerid para AD FS (Serviços de Federação do Active Directory) está ausente desta compilação.

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Lançamento: dezembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração de issuerid é necessária se você está associando vários domínios ao Azure AD (Azure Active Directory). Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar regra de declaração issuerid, confira este artigo sobre [Multiple Domain Support for Federating with Azure AD](active-directory-aadconnect-multiple-domains.md) (Suporte a vários domínios para federação com o Azure AD).

**Problema corrigido:**

* Se a porta 9090 não estiver aberta para a conexão de saída, a atualização ou a instalação do Azure AD Connect falhará.

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Lançamento: dezembro de 2016

**Problemas conhecidos:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração issuerid será necessária se você estiver federando vários domínios com o Azure AD. Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, confira este artigo sobre [Suporte a vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).
* A porta 9090 deve estar aberta para a saída para que a instalação seja concluída.

**Novos recursos:**

* Autenticação de Passagem (Visualização).

>[!NOTE]
>Esta compilação não está disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Lançamento: novembro de 2016

**Problema conhecido:**

* A regra de declaração issuerid para o AD FS está ausente dessa compilação. A regra de declaração issuerid será necessária se você estiver federando vários domínios com o Azure AD. Se estiver usando o Azure AD Connect para gerenciar a implantação local do AD FS, atualizar para essa compilação removerá a regra de declaração issuerid existente da configuração do AD FS. Você pode contornar o problema adicionando a regra de declaração issuerid após a instalação/atualização. Para obter detalhes sobre como adicionar a regra de declaração issuerid, confira este artigo sobre [Suporte a vários domínios para federação com o Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problemas corrigidos:**

* Algumas vezes, instalar o Azure Connect AD falha porque não é possível criar uma conta de serviço local cuja senha satisfaça o nível de complexidade especificado pela política de senha da organização.
* Foi corrigido um problema em que as regras de associação não são avaliadas novamente quando um objeto no espaço do conector simultaneamente fica fora do escopo de uma regra de junção e tornam-se em escopo para outro. Isso pode ocorrer se você tiver duas ou mais regras de junção cujas condições de junção são mutuamente exclusivas.
* Corrigido um problema em que regras de sincronização de entrada (do Azure AD) que não contêm regras de união não são processadas se elas tiverem valores de precedência inferiores daqueles que contém regras de associação.

**Aperfeiçoamentos:**

* Adicionado suporte para instalar o Azure AD Connect no Windows Server 2016 Standard ou posterior.
* Foi adicionado suporte para usar o SQL Server 2016 como banco de dados remoto para o Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lançamento: agosto de 2016

**Problemas corrigidos:**

* As alterações no intervalo de sincronização não ocorrerão até após a conclusão do próximo ciclo de sincronização.
* O assistente do Azure AD Connect não aceita uma conta do Azure AD cujo nome de usuário começa com um sublinhado (\_).
* Falha do Assistente do Azure AD Connect ao autenticar a conta do Azure AD se a senha da conta contém muitos caracteres especiais. A mensagem de erro "Não foi possível validar as credenciais. Ocorreu um erro inesperado.” é retornado.
* Desinstalar o servidor de preparo desabilita a sincronização de senha no locatário do Azure AD e causa a falha da sincronização de senha com o servidor ativo.
* A sincronização de senha falha em casos incomuns quando não há hash de senha armazenado no usuário.
* Quando o servidor do Azure AD Connect está habilitado para o modo de preparo, o write-back de senha não é temporariamente desabilitado.
* O assistente do Azure AD Connect não mostra a sincronização de senha real e a configuração de write-back de senha quando o servidor está no modo de preparo. Ele sempre os mostra como desabilitados.
* As alterações de configuração na sincronização de senha e no write-back de senha não são mantidas pelo Assistente do Azure AD Connect quando o servidor está no modo de preparo.

**Aperfeiçoamentos:**

* Cmdlet Start-ADSyncSyncCycle atualizado para indicar se pode iniciar um novo ciclo de sincronização com êxito ou não.
* Cmdlet Stop-ADSyncSyncCycle adicionado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Cmdlet Stop-ADSyncScheduler atualizado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Ao configurar [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md) no Assistente do Azure AD Connect, o atributo do Azure AD do tipo "Cadeia de caracteres Teletex" pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançamento: junho de 2016

**Problemas corrigidos e aperfeiçoamentos:**

* O Azure AD Connect agora pode ser instalado em um servidor compatível com FIPS.
  * Para sincronização de senha, confira [Sincronização de senha e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Corrigido um problema em que um nome NetBIOS não pôde ser resolvido para o FQDN no Active Directory Connector.

## <a name="111800"></a>1.1.180.0
Lançamento: maio de 2016

**Novos recursos:**

* Avisa e ajuda a verificar os domínios se você não fizer isso antes de executar o Azure AD Connect.
* Adicionado suporte ao [Microsoft Cloud Alemanha](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Adicionado suporte à infraestrutura em [nuvem do Microsoft Azure Governamental](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) mais recente com novos requisitos de URL.

**Problemas corrigidos e aperfeiçoamentos:**

* Adicionada filtragem ao Editor de regra de sincronização para tornar mais fácil localizar as regras de sincronização.
* Desempenho aprimorado ao excluir um espaço de conector.
* Corrigido um problemas quando o mesmo objeto foi excluído e adicionado na mesma execução (chamado excluir/adicionar).
* Uma regra de sincronização desabilitada não pode mais reabilitar objetos e atributos incluídos na atualização ou na atualização de esquema de diretório.

## <a name="111300"></a>1.1.130.0
Lançamento: abril de 2016

**Novos recursos:**

* Suporte adicionado para atributos com valores múltiplos para [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).
* Suporte adicionado para mais variações de configuração de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) serem consideradas qualificadas para atualização.
* Adicionados alguns cmdlets para o [agendador personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançamento: março de 2016

**Problemas corrigidos:**

* Foi garantido que a Instalação expressa não possa ser usada no Windows Server 2008 (pré-R2), já que a sincronização de senha não tem suporte neste sistema operacional.
* A atualização do DirSync com uma configuração de filtro personalizado não funcionou conforme o esperado.
* Ao atualizar para uma versão mais recente e se não houver alterações na configuração, a importação/sincronização completa não deverá ser agendada.

## <a name="111100"></a>1.1.110.0
Lançamento: fevereiro de 2016

**Problemas corrigidos:**

* A atualização de versões anteriores não funcionará se a instalação não estiver na pasta padrão C:\Arquivos de Programas.
* Se você instalar e desmarcar **Iniciar o processo de sincronização...** no fim do assistente de instalação, executar o assistente de instalação uma segunda vezs não habilitará o agendador.
* O agendador não funciona conforme o esperado em servidores em que o formato de data/hora en-US não é usado. Ele também bloqueará `Get-ADSyncScheduler` para retornar as horas corretas.
* Se você instalou uma versão anterior do Azure AD Connect com o ADFS como a opção de entrada e atualização, não será possível executar o assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Lançamento: fevereiro de 2016

**Novos recursos:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) para clientes de configurações Expressas.
* Suporte para o administrador global usando a Autenticação Multifator do Azure e o Privileged Identity Management no assistente de instalação.
  * Você precisará permitir que o proxy também permita o tráfego para https://secure.aadcdn.microsoftonline-p.com se usar a Autenticação Multifator.
  * Você precisa adicionar https://secure.aadcdn.microsoftonline-p.com à lista de sites confiáveis para que a Autenticação Multifator funcione corretamente.
* Permita alterar o método de logon do usuário após a instalação inicial.
* Permita [Filtragem de Domínio e UO](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no assistente de instalação. Isso também permite conectar-se às florestas em que nem todos os domínios estão disponíveis.
* O [Agendador](active-directory-aadconnectsync-feature-scheduler.md) é interno ao mecanismo de sincronização.

**Recursos promovidos da visualização para GA:**

* [Write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md).
* [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).

**Novos recursos de visualização:**

* O novo intervalo de ciclo de sincronização padrão é de 30 minutos. Costumava ser três horas para todas as versões anteriores. Adiciona suporte para alterar o comportamento do [agendador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas corrigidos:**

* A página Verificar domínios DNS nem sempre reconheceu os domínios.
* Solicita credenciais de administrador de domínio ao configurar o AD FS.
* As contas locais do AD não são reconhecidas pelo assistente de instalação se estiverem localizadas em um domínio com uma árvore DNS diferente do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Lançamento: dezembro de 2015

**Problemas corrigidos:**

* A sincronização de senha poderá não funcionar quando você alterar as senhas no AD DS (Active Directory Domain Services), mas funcionará quando você definir uma senha.
* Quando você tiver um servidor proxy, a autenticação do Azure AD poderá falhar durante a instalação ou se uma atualização for cancelada na página de configuração.
* A atualização de uma versão anterior do Azure AD Connect com uma instância completa do SQL Server falhará se você não for um SA (administrador do sistema) do SQL Server.
* A atualização de uma versão anterior do Azure AD Connect com um SQL Server remoto mostra o erro "Não é possível acessar o banco de dados SQL do ADSync".

## <a name="1091250"></a>1.0.9125.0
Lançamento: novembro de 2015

**Novos recursos:**

* Pode reconfigurar a relação de confiança entre o ADFS e o AD do Azure.
* Pode atualizar o esquema do Active Directory e gerar regras de sincronização.
* Pode desabilitar uma regra de sincronização.
* Pode definir "AuthoritativeNull" como um novo literal em uma regra de sincronização.

**Novos recursos de visualização:**

* [Azure AD Connect Health para sincronização](../connect-health/active-directory-aadconnect-health-sync.md).
* Suporte para sincronização de senha dos [Serviços de Domínio do AD do Azure](../active-directory-passwords-update-your-own-password.md) .

**Novo cenário com suporte:**

* Dá suporte a várias organizações local do Exchange. Para obter mais informações, confira [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx).

**Problemas corrigidos:**

* Problemas de sincronização de senha:
  * Um objeto movido de fora do escopo para dentro do escopo não terá sua senha sincronizada. Isso inclui a UO e a filtragem de atributo.
  * Selecionar uma nova UO para incluir na sincronização não exige uma sincronização de senha completa.
  * Quando um usuário desabilitado é habilitado, a senha não é sincronizada.
  * A fila de repetição de senha é infinita e o limite anterior de 5.000 objetos a ser retirado foi removido.
<<<<<<< HEAD <<<<<<< HEAD
  * [Solução de problemas avançada](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
=======
  * [Solução de problemas avançada](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).
>>>>>>> <a name="487b660b6d3bb5ce9e64b6fdbde2ae621cb91922"></a>487b660b6d3bb5ce9e64b6fdbde2ae621cb91922
=======
  * [Solução de problemas avançada](active-directory-aadconnectsync-troubleshoot-password-synchronization.md).
>>>>>>> 4b2e846c2cd4615f4e4be7195899de11e3957c83
* Não é possível se conectar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo que é usado para filtragem de grupo após a instalação inicial.
* Não cria mais um novo perfil do usuário no servidor do Azure AD Connect para cada usuário que faz uma alteração de senha com write-back de senha habilitada.
* Não é possível usar valores Inteiros Longos em escopos de regras de sincronização.
* A caixa de seleção "write-back de dispositivo" permanecerá desabilitada se houver controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Lançamento: agosto de 2015

**Novos recursos:**

* O assistente de instalação do Azure AD Connect agora está localizado para todos os idiomas do Windows Server.
* Suporte adicionado para desbloqueio de contas ao usar o gerenciamento de senhas do AD do Azure.

**Problemas corrigidos:**

* O assistente de instalação do Azure AD Connect falha se outro usuário continuar a instalação em vez da pessoa que iniciou a instalação inicialmente.
* Se uma desinstalação anterior do Azure AD Connect falhar ao desinstalar o Azure AD Connect Sync corretamente, não será possível reinstalá-lo.
* Não será possível instalar o Azure AD Connect usando a Instalação expressa se o usuário não estiver no domínio raiz da floresta ou se uma versão diferente do inglês do Active Directory for usada.
* Se o FQDN da conta de usuário do Active Directory não puder ser resolvido, é mostrada a mensagem enganosa de erro "Falha ao confirmar o esquema".
* Se a conta usada no Active Directory Connector for alterada fora do assistente, o assistente falhará em execuções posteriores.
* O Azure AD Connect às vezes não pode instalar em um controlador de domínio.
* Não é possível habilitar e desabilitar o "Modo de preparo" se os atributos de extensão forem adicionados.
* O write-back de senha falha em algumas configurações devido a uma senha incorreta no Active Directory Connector.
* DirSync não poderá ser atualizado se um DN (nome diferenciado) for usado na filtragem de atributos.
* Uso excessivo de CPU ao usar a redefinição de senha.

**Recursos de visualização removidos:**

* O recurso de visualização [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback) foi temporariamente removido com base nos comentários de nossos clientes da visualização. Ela será adicionada novamente mais tarde, depois que abordarmos os comentários fornecidos.

## <a name="1086410"></a>1.0.8641.0
Lançamento: junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de Azure AD Sync para Azure AD Connect.

**Novos recursos:**

* Instalação das [configurações expressas](active-directory-aadconnect-get-started-express.md)
* Pode [configurar o AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* É possível [atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Apresentação do [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode)

**Novos recursos de visualização:**

* [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md)
* [Extensões de diretório](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Lançamento: maio de 2015

**Novo Requisito:**

* O Azure AD Sync agora requer a instalação do .NET Framework versão 4.5.1.

**Problemas corrigidos:**

* O write-back de senha do Azure AD está falhando com um erro de conectividade do Barramento de Serviço do Azure.

## <a name="104910413"></a>1.0.491.0413
Lançamento: abril de 2015

**Problemas corrigidos e aperfeiçoamentos:**

* O Active Directory Connector não processa exclusões corretamente se a Lixeira estiver habilitada e se existirem vários domínios na floresta.
* O desempenho das operações de importação foi aprimorado para o Active Directory Connector do Azure.
* Quando um grupo excedeu o limite de associação (por padrão, o limite é definido como 50.000 objetos), o grupo foi excluído do Azure Active Directory. Com o novo comportamento, o grupo não é excluído, um erro é gerado e novas alterações de associação não são exportadas.
* Não é possível provisionar um novo objeto se uma exclusão de preparo com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos são marcados para sincronização durante uma sincronização delta, embora não haja alteração preparada no objeto.
* Forçar uma sincronização de senha também remove a lista preferencial do DC.
* CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novos recursos:**

* Um ingresso agora pode se conectar ao tipo de objeto "ANY" na MV.

## <a name="104850222"></a>1.0.485.0222
Lançamento: fevereiro de 2015

**Aperfeiçoamentos:**

* Desempenho aprimorado de importação.

**Problemas corrigidos:**

* A Sincronização de Senha respeita o atributo cloudFiltered usado pela filtragem de atributo. Os objetos filtrados não estão mais no escopo de sincronização de senha.
* Em raras situações em que a topologia tem muitos controladores de domínio, a sincronização de senha não funciona.
* "Servidor-parado" ao importar do Conector do AD do Azure depois que o gerenciamento de dispositivo tiver sido habilitado no AD do Azure/Intune.
* Ingressar FSPs (Entidades de Segurança Externa) de vários domínios na mesma floresta causa um erro de ingresso ambíguo.

## <a name="104751202"></a>1.0.475.1202
Lançamento: dezembro de 2014

**Novos recursos:**

* Agora há suporte para a sincronização de senhas com filtragem baseada em atributo. Para obter mais informações, confira [Sincronização de senha com filtragem](active-directory-aadconnectsync-configure-filtering.md).
* O atributo msDS-ExternalDirectoryObjectID será gravado de volta no Active Directory. Esse recurso adiciona suporte a aplicativos do Office 365. Ele usa OAuth2 para acessar caixas de correio Locais e Online em uma Implantação Híbrida do Exchange.

**Problemas de atualização corrigidos:**

* Uma versão mais recente do que o assistente de logon está disponível no servidor.
* Um caminho de instalação personalizada foi usado para instalar o Azure AD Sync.
* Um critério de associação personalizado inválido bloqueará a atualização.

**Outras correções:**

* Modelos do Office Pro Plus corrigidos.
* Foram corrigidos os problemas de instalação causados por nomes de usuário que começam com um traço.
* Foi corrigida a perda da configuração sourceAnchor ao executar o assistente de instalação uma segunda vez.
* Rastreamento ETW fixo para a sincronização de senha corrigido.

## <a name="104701023"></a>1.0.470.1023
Lançamento: outubro de 2014

**Novos recursos:**

* Sincronização de senha de vários locais do Active Directory para o Azure AD.
* Interface do usuário de instalação localizada para todos os idiomas do Windows Server.

**Atualizando do AADSync 1.0 GA**

Se já tiver instalado o Azure AD Sync, há uma etapa adicional que você precisa realizar caso você já tenha alterado algumas das regras de sincronização de fábrica. Depois de atualizar para a versão 1.0.470.1023, a sincronização de regras que você modificou será duplicada. Para cada regra de sincronização modificada, faça o seguinte:

1.  Localize a regra de sincronização você modificou e anote as alterações.
* Exclua a regra de sincronização.
* Localize a nova regra de sincronização que é criada pelo Azure AD Sync e reaplique as alterações.

**Permissões para a conta do Active Directory**

A conta do Active Directory deve receber permissões adicionais para poder ler os hashes de senha do Active Directory. As permissões a serem concedidas são denominadas "Replicar Alterações de Diretório" e "Replicar Todas as Alterações de Diretório". Ambas as permissões são necessárias para ler os hashes de senha.

## <a name="104190911"></a>1.0.419.0911
Lançamento: setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).

