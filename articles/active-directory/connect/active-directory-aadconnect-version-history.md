---
title: "Azure AD Connect: Histórico de liberação de versão | Microsoft Docs"
description: "Este tópico lista todas as versões do Azure AD Connect e do Azure AD Sync"
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
ms.date: 12/07/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: bddd581b5164b03ccba75370cd89a7831101cf37
ms.openlocfilehash: c7abb31a222a5e817dc7b4f73c9cf54ce4d26255


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: histórico de lançamento de versão
A equipe do Active Directory do Azure atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi projetado para ajudá-lo a controlar as versões que foram lançadas e compreender se você precisa atualizar para a versão mais recente ou não.

Esta é a lista de tópicos relacionados:


Tópico |  
--------- | --------- |
Etapas para atualizar do Azure AD Connect | Métodos diferentes para [atualizar de uma versão anterior para a versão mais recente](active-directory-aadconnect-upgrade-previous-version.md) do Azure AD Connect.
Permissões necessárias | Para obter permissões necessárias para aplicar uma atualização, veja [contas e permissões](./active-directory-aadconnect-accounts-permissions.md#upgrade)
Baixar| [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="113710"></a>1.1.371.0
Lançamento: dezembro de 2016

**Problema corrigido:**

* A instalação ou atualização do Azure AD Connect falhará se a porta 9090 não estiver aberta para a conexão de saída.

## <a name="113700"></a>1.1.370.0
Lançamento: dezembro de 2016

**Novos recursos:**

* Autenticação de Passagem (Visualização).


**Problemas conhecidos:**

* A porta 9090 deve estar aberta para a saída para que a instalação seja concluída.

>[!NOTE]
>Este build não estará disponível para os clientes por meio do recurso Atualização Automática do Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Lançado: novembro de 2016

**Problemas corrigidos:**

* Algumas vezes, instalar o Azure Connect AD falha porque não é possível criar uma conta de serviço local cuja senha satisfaça o nível de complexidade especificado pela política de senha da organização.
* Corrigido um problema em que as regras de associação não são avaliadas novamente quando um objeto no espaço do conector simultaneamente fica fora do escopo de uma regra de junção e tornam-se em escopo para outro. Isso pode ocorrer se você tiver duas ou mais regras de junção cujas condições de junção são mutuamente exclusivas.
* Corrigido um problema em que regras de sincronização de entrada (do AD do Azure) que não contêm regras de união não são processadas se elas tiverem valores de precedência inferiores daqueles que contém regras de associação.

**Aperfeiçoamentos:**

* Adicionado suporte para instalar o Azure AD Connect no Windows Server 2016 padrão ou melhor.
* Foi adicionado suporte para usar o SQL Server 2016 como banco de dados remoto para o Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Lançamento: agosto de 2016

**Problemas corrigidos:**

* Não ocorrerão alterações no intervalo de sincronização até após a conclusão do próximo ciclo de sincronização.
* O assistente do Azure AD Connect não aceita uma conta do Azure AD cujo nome de usuário começa com um sublinhado (\_).
* Falha do Assistente do Azure AD Connect ao autenticar a conta do Azure AD fornecida se a senha da conta contém muitos caracteres especiais. A mensagem de erro "Não foi possível validar as credenciais. Ocorreu um erro inesperado.” é retornado.
* Desinstalar o servidor de preparo desabilita a sincronização de senha no locatário do Azure AD e causa a falha da sincronização de senha com o servidor ativo.
* A sincronização de senha falha em casos incomuns quando não há hash de senha armazenado no usuário.
* Quando o servidor do Azure AD Connect está habilitado para o modo de preparo, o write-back de senha não é temporariamente desabilitado.
* O assistente do Azure AD Connect não mostra a sincronização de senha real e a configuração de write-back de senha quando o servidor está no modo de preparo. Ele sempre os mostra como desabilitados.
* As alterações de configuração na sincronização de senha e no write-back de senha não são mantidas pelo Assistente do Azure AD Connect quando o servidor está no modo de preparo.

**Aperfeiçoamentos:**

* Cmdlet Start-ADSyncSyncCycle atualizado para indicar se pode iniciar um novo ciclo de sincronização com êxito ou não.
* Cmdlet Stop-ADSyncSyncCycle adicionado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Cmdlet Stop-ADSyncScheduler atualizado para encerrar o ciclo de sincronização e a operação que estão atualmente em andamento.
* Ao configurar [Extensões de Diretório](active-directory-aadconnectsync-feature-directory-extensions.md) no Assistente do Azure AD Connect, o atributo do AD do tipo "Cadeia de caracteres Teletex" pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Lançamento: junho de 2016

**Problemas corrigidos e aperfeiçoamentos:**

* O Azure AD Connect agora pode ser instalado em um servidor compatível com FIPS.
  * Para sincronização de senha, consulte [Sincronização de Senha e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
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
* Uma regra de sincronização desabilitada não poderá mais reabilitar objetos e atributos incluídos na atualização ou na atualização de esquema de diretório.

## <a name="111300"></a>1.1.130.0
Lançamento: abril de 2016

**Novos recursos:**

* Suporte adicionado para atributos com valores múltiplos para [Extensões do Directory](active-directory-aadconnectsync-feature-directory-extensions.md).
* Suporte adicionado para mais variações de configuração de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) serem consideradas qualificadas para atualização.
* Adicionados alguns cmdlets para o [agendador personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Lançamento: março de 2016

**Problemas corrigidos:**

* Garantia que a instalação expressa não pode ser usada no Windows Server 2008 (pré-R2), já que a sincronização de senha não tem suporte neste sistema operacional.
* A atualização do DirSync com uma configuração de filtro personalizado não funcionou conforme o esperado.
* Ao atualizar para uma versão mais recente e se não houver alterações na configuração, a importação/sincronização completa não deverá ser agendada.

## <a name="111100"></a>1.1.110.0
Lançamento: fevereiro de 2016

**Problemas corrigidos:**

* A atualização de versões anteriores não funcionará se a instalação não estiver na pasta padrão **C:\Arquivos de Programas**.
* Se você instalar e desmarcar **Iniciar o processo de sincronização...** no final do assistente de instalação, executar novamente o assistente de instalação não habilitará o agendador.
* O agendador não funcionará como esperado em servidores em que o formato de data/hora não seja US-en. Ele também bloqueará `Get-ADSyncScheduler` para retornar as horas corretas.
* Se você instalou uma versão anterior do Azure AD Connect com o ADFS como a opção de entrada e atualização, não será possível executar o assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Lançamento: fevereiro de 2016

**Novos recursos:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) para clientes de configurações Expressas.
* Suporte para o administrador global usando MFA e PIM no assistente de instalação.
  * Se estiver usando MFA, você precisará permitir que o proxy permita o tráfego para https://secure.aadcdn.microsoftonline-p.com.
  * Você precisa adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites confiáveis para o MFA funcionar corretamente.
* Permita alterar o método de logon do usuário após a instalação inicial.
* Permita [Filtragem de Domínio e UO](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no assistente de instalação. Isso também permite conectar-se às florestas em que nem todos os domínios estão disponíveis.
* [Agendador](active-directory-aadconnectsync-feature-scheduler.md) é interno ao mecanismo de sincronização.

**Recursos promovidos da visualização para GA:**

* [Write-back de dispositivo](active-directory-aadconnect-feature-device-writeback.md).
* [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).

**Novos recursos de visualização:**

* O novo intervalo de ciclo de sincronização padrão é de 30 minutos. Ele costumava ser de 3 horas para todas as versões anteriores. Adiciona suporte para alterar o comportamento do [agendador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas corrigidos:**

* A página Verificar domínios DNS nem sempre reconheceu os domínios.
* Solicita credenciais de administrador de domínio ao configurar o ADFS.
* As contas locais do AD não são reconhecidas pelo assistente de instalação se estiverem localizadas em um domínio com uma árvore DNS diferente do domínio raiz.

## <a name="1091310"></a>1.0.9131.0
Lançamento: dezembro de 2015

**Problemas corrigidos:**

* A sincronização de senha pode não funcionar quando você alterar as senhas no AD DS, mas funciona quando você define a senha.
* Quando você tiver um servidor proxy, autenticação do AD do Azure pode falhar durante a instalação ou atualização na página de configuração.
* A atualização de uma versão anterior do Azure AD Connect com um SQL Server completo falhará se você não for SA no SQL.
* A atualização de uma versão anterior do Azure AD Connect com um SQL Server remoto mostrará o erro "Não é possível acessar o banco de dados SQL do ADSync".

## <a name="1091250"></a>1.0.9125.0
Lançado: novembro de 2015

**Novos recursos:**

* Pode reconfigurar a relação de confiança entre o ADFS e o AD do Azure.
* Pode atualizar o esquema do Active Directory e gerar Regras de Sincronização.
* Pode desabilitar uma regra de sincronização.
* Pode definir “AuthoritativeNull” como um novo literal em uma Regra de Sincronização.

**Novos recursos de visualização:**

* [Azure AD Connect Health para sincronização](../connect-health/active-directory-aadconnect-health-sync.md).
* Suporte para sincronização de senha dos [Serviços de Domínio do AD do Azure](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Novo cenário com suporte:**

* Dá suporte a várias organizações local do Exchange. Veja [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx) para obter mais informações.

**Problemas corrigidos:**

* Problemas de sincronização de senha:
  * Um objeto movido de fora do escopo para dentro do escopo não terá sua senha sincronizada. Isto inclui a UO e a filtragem de atributo.
  * Selecionar uma nova UO para incluir na sincronização não exige uma sincronização de senha completa.
  * Quando um usuário desabilitado é habilitado, a senha não é sincronizada.
  * A fila de repetição de senha é infinita e o limite anterior de 5.000 objetos a ser retirado foi removido.
  * [Solução de problemas avançada](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization).
* Não é possível se conectar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
* Não é possível alterar o grupo usado para a filtragem de grupo após a instalação inicial.
* Não criará mais um novo perfil do usuário no servidor do Azure AD Connect para cada usuário que faz uma alteração de senha com write-back de senha habilitada.
* Não é possível usar valores Inteiros Longos em escopos de Regras de Sincronização.
* A caixa de seleção “write-back de dispositivo” permanecerá desabilitada se houver controladores de domínio inacessíveis.

## <a name="1086670"></a>1.0.8667.0
Lançamento: agosto de 2015

**Novos recursos:**

* O assistente de instalação do Azure AD Connect agora está localizado para todos os idiomas do Windows Server.
* Suporte adicionado para desbloqueio de contas ao usar o gerenciamento de senhas do AD do Azure.

**Problemas corrigidos:**

* O assistente de instalação do Azure AD Connect falha se outro usuário continuar a instalação em vez da pessoa que iniciou a instalação inicialmente.
* Se uma desinstalação anterior do Azure AD Connect falhar ao desinstalar o Azure AD Connect Sync corretamente, não será possível reinstalá-lo.
* Não é possível instalar o Azure AD Connect usando a Instalação expressa se o usuário não estiver no domínio raiz da floresta ou se uma versão diferente do inglês do Active Directory for usada.
* Se o FQDN da conta de usuário do Active Directory não puder ser resolvido, é mostrada a mensagem enganosa de erro "Falha ao confirmar o esquema".
* Se a conta usada no Active Directory Connector for alterada fora do assistente, o assistente falhará em execuções posteriores.
* O Azure AD Connect às vezes não pode instalar em um controlador de domínio.
* Não é possível habilitar e desabilitar o "Modo de preparo" se os atributos de extensão forem adicionados.
* O write-back de senha falha em algumas configurações devido a uma senha incorreta no Active Directory Connector.
* O DirSync não pode ser atualizado se dn for usado na filtragem de atributo.
* Uso excessivo de CPU ao usar a redefinição de senha.

**Recursos de visualização removidos:**

* O recurso de visualização [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback) foi temporariamente removido com base nos comentários de nossos clientes da visualização. Ele será adicionado novamente após solucionarmos as questões indicadas nos comentários fornecidos.

## <a name="1086410"></a>1.0.8641.0
Lançamento: junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de Azure AD Sync para Azure AD Connect.

**Novos recursos:**

* Instalação das [configurações expressas](active-directory-aadconnect-get-started-express.md)
* É possível [configurar o ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
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

* O Azure AD Sync agora requer a instalação do .Net framework versão 4.5.1.

**Problemas corrigidos:**

* O write-back de senha do AD do Azure está falhando com um erro de conectividade do barramento de serviço.

## <a name="104910413"></a>1.0.491.0413
Lançamento: abril de 2015

**Problemas corrigidos e aperfeiçoamentos:**

* O Active Directory Connector não processa exclusões corretamente se a Lixeira estiver habilitada e se existirem vários domínios na floresta.
* O desempenho das operações de importação foi aprimorado para o Active Directory Connector do Azure.
* Quando um grupo excedeu o limite de associação (por padrão, o limite é definido como 50 mil objetos), o grupo foi excluído do Active Directory do Azure. O novo comportamento é que o grupo permanecerá, sendo gerado um erro e nenhuma alteração de associação será exportada.
* Não é possível provisionar um novo objeto se uma exclusão de preparo com o mesmo DN já estiver presente no espaço do conector.
* Alguns objetos são marcados para serem sincronizados durante uma sincronização delta, embora não haja nenhuma alteração no objeto de preparação.
* Forçar uma sincronização de senha também remove a lista preferencial do DC.
* CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novos recursos:**

* Um ingresso agora pode se conectar ao tipo de objeto "ANY" na MV.

## <a name="104850222"></a>1.0.485.0222
Lançamento: fevereiro de 2015

**Aperfeiçoamentos:**

* Desempenho aprimorado de importação.

**Problemas corrigidos:**

* A Sincronização de Senha honra o atributo cloudFiltered usado pela filtragem de atributo. Objetos filtrados não estarão no escopo de sincronização de senha.
* Em raras situações em que a topologia tem muitos controladores de domínio, a sincronização de senha não funciona.
* "Servidor-parado" ao importar do Conector do AD do Azure depois que o gerenciamento de dispositivo tiver sido habilitado no AD do Azure/Intune.
* Ingressar FSPs (Entidades de Segurança Externa) de vários domínios na mesma floresta causa um erro de ingresso ambíguo.

## <a name="104751202"></a>1.0.475.1202
Lançamento: dezembro de 2014

**Novos recursos:**

* Agora há suporte para a sincronização de senha com a filtragem baseada em atributo. Para obter mais detalhes, veja [Sincronização de senha com filtragem](active-directory-aadconnectsync-configure-filtering.md).
* O atributo msDS-ExternalDirectoryObjectID é gravado para o AD. Isso adiciona suporte para aplicativos do Office 365 usando OAuth2 para acessar caixas de correio Online e Local em uma Implantação Híbrida do Exchange.

**Problemas de atualização corrigidos:**

* Uma versão mais recente do que o assistente de logon está disponível no servidor.
* Um caminho de instalação personalizada foi usado para instalar o Azure AD Sync.
* Um critério de associação personalizado inválido bloqueará a atualização.

**Outras correções:**

* Modelos do Office Pro Plus corrigidos.
* Foram corrigidos os problemas de instalação causados por nomes de usuário que começam com um traço.
* Foi corrigida a perda da configuração sourceAnchor ao executar o assistente de instalação uma segunda vez.
* Rastreamento ETW fixo para a sincronização de senha corrigido

## <a name="104701023"></a>1.0.470.1023
Lançamento: outubro de 2014

**Novos recursos:**

* Sincronização de senha de vários AD local para o Azure AD.
* Interface do usuário de instalação localizada para todos os idiomas do Windows Server.

**Atualizando do AADSync 1.0 GA**

Se já tiver instalado o Azure AD Sync, há uma etapa adicional que você precisa realizar caso você já tenha alterado algumas das regras de sincronização de fábrica. Depois de atualizar para a versão 1.0.470.1023, a sincronização de regras que você modificou será duplicada. Para cada Regra de Sincronização modificada, faça o seguinte:

* Localize a Regra de Sincronização você modificou e anote as alterações.
* Exclua a Regra de Sincronização.
* Localize a nova Regra de Sincronização criada pelo Azure AD Sync e aplique novamente as alterações.

**Permissões para a conta do AD**

A conta do AD deve ter permissões adicionais para poder ler os hashes de senha do AD. As permissões a serem concedidas são denominadas "Replicar Alterações de Diretório" e "Replicar Todas as Alterações de Diretório". Ambas as permissões são necessárias para ler os hashes de senha

## <a name="104190911"></a>1.0.419.0911
Lançamento: setembro de 2014

**Versão inicial do Azure AD Sync.**

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).




<!--HONumber=Dec16_HO3-->


