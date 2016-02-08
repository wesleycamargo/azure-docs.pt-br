<properties
   pageTitle="Azure AD Connect: histórico de versão | Microsoft Azure"
   description="Este tópico lista todas as versões do Azure AD Connect e Azure AD Sync"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="andkjell"/>

# Azure AD Connect: histórico de lançamento de versão

A equipe do Active Directory do Azure atualiza regularmente o Azure AD Connect com novos recursos e funcionalidades. Nem todas as adições são aplicáveis a todos os públicos.

Este artigo foi projetado para ajudá-lo a controlar as versões que foram lançadas e compreender se você precisa atualizar para a versão mais recente ou não.

Links relacionados:

- Para obter permissões necessárias para aplicar uma atualização, veja [contas e permissões](active-directory-aadconnect-accounts-permissions.md#upgrade)
- [Baixar o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

## 1\.0.9131.0
Lançamento: dezembro de 2015

**Problemas corrigidos:**

- A sincronização de senha pode não funcionar quando você alterar as senhas no AD DS, mas funciona quando você define a senha.
- Quando você tiver um servidor proxy, autenticação do AD do Azure pode falhar durante a instalação ou atualização na página de configuração.
- A atualização de uma versão anterior do Azure AD Connect com um SQL Server completo falhará se você não for SA no SQL.
- A atualização de uma versão anterior do Azure AD Connect com um SQL Server remoto mostrará o erro "Não é possível acessar o banco de dados SQL do ADSync".

## 1\.0.9125.0
Lançado: novembro de 2015

**Novos recursos:**

- Pode reconfigurar a relação de confiança entre o ADFS e o AD do Azure.
- Pode atualizar o esquema do Active Directory e gerar Regras de Sincronização.
- Pode desabilitar uma regra de sincronização.
- Pode definir “AuthoritativeNull” como um novo literal em uma Regra de Sincronização.

**Novos recursos de visualização:**

- [Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md).
- Suporte para sincronização de senha dos [Serviços de Domínio do AD do Azure](active-directory-get-started.md).

**Novo cenário com suporte:**

- Dá suporte a várias organizações local do Exchange. Veja [Implantações híbridas com várias florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx) para obter mais informações.

**Problemas corrigidos:**

- Problemas de sincronização de senha:
    - Um objeto movido de fora do escopo para dentro do escopo não terá sua senha sincronizada. Isto inclui a UO e a filtragem de atributo.
    - Selecionar uma nova UO para incluir na sincronização não exige uma sincronização de senha completa.
    - Quando um usuário desabilitado é habilitado, a senha não é sincronizada.
    - A fila de repetição de senha é infinita e o limite anterior de 5.000 objetos a ser retirado foi removido.
    - [Solução de problemas avançada](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Não é possível se conectar ao Active Directory com o nível funcional de floresta do Windows Server 2016.
- Não é possível alterar o grupo usado para a filtragem de grupo após a instalação inicial.
- Não criará mais um novo perfil do usuário no servidor do Azure AD Connect para cada usuário que faz uma alteração de senha com write-back de senha habilitada.
- Não é possível usar valores Inteiros Longos em escopos de Regras de Sincronização.
- A caixa de seleção “write-back de dispositivo” permanecerá desabilitada se houver controladores de domínio inacessíveis.

## 1\.0.8667.0
Lançamento: agosto de 2015

**Novos recursos:**

- O assistente de instalação do Azure AD Connect agora está localizado para todos os idiomas do Windows Server.
- Suporte adicionado para desbloqueio de contas ao usar o gerenciamento de senhas do AD do Azure.

**Problemas corrigidos:**

- O assistente de instalação do Azure AD Connect falha se outro usuário continuar a instalação em vez da pessoa que iniciou a instalação inicialmente.
- Se uma desinstalação anterior do Azure AD Connect falhar ao desinstalar o Azure AD Connect Sync corretamente, não será possível reinstalá-lo.
- Não é possível instalar o Azure AD Connect usando a Instalação expressa se o usuário não estiver no domínio raiz da floresta ou se uma versão diferente do inglês do Active Directory for usada.
- Se o FQDN da conta de usuário do Active Directory não puder ser resolvido, é mostrada a mensagem enganosa de erro "Falha ao confirmar o esquema".
- Se a conta usada no Active Directory Connector for alterada fora do assistente, o assistente falhará em execuções posteriores.
- O Azure AD Connect às vezes não pode instalar em um controlador de domínio.
- Não é possível habilitar e desabilitar o "Modo de preparo" se os atributos de extensão forem adicionados.
- O write-back de senha falha em algumas configurações devido a uma senha incorreta no Active Directory Connector.
- O DirSync não pode ser atualizado se dn for usado na filtragem de atributo.
- Uso excessivo de CPU ao usar a redefinição de senha.

**Recursos de visualização removidos:**

- O recurso de visualização [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback) foi temporariamente removido com base nos comentários de nossos clientes da visualização. Ele será adicionado novamente após solucionarmos as questões indicadas nos comentários fornecidos.

## 1\.0.8641.0
Lançamento: junho de 2015

**Versão inicial do Azure AD Connect.**

Nome alterado de Azure AD Sync para Azure AD Connect.

**Novos recursos:**

- Instalação das [configurações expressas](active-directory-aadconnect-get-started-express.md)
- É possível [configurar o ADFS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- É possível [atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Impedir exclusões acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Apresentação do [modo de preparo](active-directory-aadconnectsync-operations.md#staging-mode)

**Novos recursos de visualização:**

- [Write-back de usuário](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Write-back de grupo](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Write-back de dispositivo](active-directory-aadconnect-get-started-custom-device-writeback.md)
- [Extensões de diretório](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
Lançamento: maio de 2015

**Novo Requisito:**

- O Azure AD Sync agora requer a instalação do .Net framework versão 4.5.1.

**Problemas corrigidos:**

- O write-back de senha do AD do Azure está falhando com um erro de conectividade do barramento de serviço.

## 1\.0.491.0413
Lançamento: abril de 2015

**Problemas corrigidos e aperfeiçoamentos:**

- O Active Directory Connector não processa exclusões corretamente se a Lixeira estiver habilitada e se existirem vários domínios na floresta.
- O desempenho das operações de importação foi aprimorado para o Active Directory Connector do Azure.
- Quando um grupo excedeu o limite de associação (por padrão, o limite é definido como 50 mil objetos), o grupo foi excluído do Active Directory do Azure. O novo comportamento é que o grupo permanecerá, sendo gerado um erro e nenhuma alteração de associação será exportada.
- Não é possível provisionar um novo objeto se uma exclusão de preparo com o mesmo DN já estiver presente no espaço do conector.
- Alguns objetos são marcados para serem sincronizados durante uma sincronização delta, embora não haja nenhuma alteração no objeto de preparação.
- Forçar uma sincronização de senha também remove a lista preferencial do DC.
- CSExportAnalyzer tem problemas com alguns estados de objetos.

**Novos recursos:**

- Um ingresso agora pode se conectar ao tipo de objeto "ANY" na MV.

## 1\.0.485.0222
Lançamento: fevereiro de 2015

**Aperfeiçoamentos:**

- Desempenho aprimorado de importação.

**Problemas corrigidos:**

- A Sincronização de Senha honra o atributo cloudFiltered usado pela filtragem de atributo. Objetos filtrados não estarão no escopo de sincronização de senha.
- Em raras situações em que a topologia tem muitos controladores de domínio, a sincronização de senha não funciona.
- "Servidor-parado" ao importar do Conector do AD do Azure depois que o gerenciamento de dispositivo tiver sido habilitado no AD do Azure/Intune.
- Ingressar FSPs (Entidades de Segurança Externa) de vários domínios na mesma floresta causa um erro de ingresso ambíguo.

## 1\.0.475.1202
Lançamento: dezembro de 2014

**Novos recursos:**

- Agora há suporte para a sincronização de senha com a filtragem baseada em atributo. Para obter mais detalhes, veja [Sincronização de senha com filtragem](active-directory-aadconnectsync-configure-filtering.md).
- O atributo msDS-ExternalDirectoryObjectID é gravado para o AD. Isso adiciona suporte para aplicativos do Office 365 usando OAuth2 para acessar caixas de correio Online e Local em uma Implantação Híbrida do Exchange.

**Problemas de atualização corrigidos:**

- Uma versão mais recente do que o assistente de logon está disponível no servidor.
- Um caminho de instalação personalizada foi usado para instalar o Azure AD Sync.
- Um critério de associação personalizado inválido bloqueará a atualização.

**Outras correções:**

- Modelos do Office Pro Plus corrigidos.
- Foram corrigidos os problemas de instalação causados por nomes de usuário que começam com um traço.
- Foi corrigida a perda da configuração sourceAnchor ao executar o assistente de instalação uma segunda vez.
- Rastreamento ETW fixo para a sincronização de senha corrigido

## 1\.0.470.1023
Lançamento: outubro de 2014

**Novos recursos:**

- Sincronização de senha de vários AD local para o AD do Azure.
- Interface do usuário de instalação localizada para todos os idiomas do Windows Server.

**Atualizando do AADSync 1.0 GA**

Se já tiver instalado o Azure AD Sync, há uma etapa adicional que você precisa realizar caso você já tenha alterado algumas das regras de sincronização de fábrica. Depois de atualizar para a versão 1.0.470.1023, a sincronização de regras que você modificou será duplicada. Para cada Regra de Sincronização modificada, faça o seguinte:

- Localize a Regra de Sincronização você modificou e anote as alterações.
- Exclua a Regra de Sincronização.
- Localize a nova Regra de Sincronização criada pelo Azure AD Sync e aplique novamente as alterações.

**Permissões para a conta do AD**

A conta do AD deve ter permissões adicionais para poder ler os hashes de senha do AD. As permissões a serem concedidas são denominadas "Replicar Alterações de Diretório" e "Replicar Todas as Alterações de Diretório". Ambas as permissões são necessárias para ler os hashes de senha

## 1\.0.419.0911
Lançamento: setembro de 2014

**Versão inicial do Azure AD Sync.**

## Próximas etapas
Saiba mais sobre a [Integração de suas identidades locais com o Active Directory do Azure](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0128_2016-->