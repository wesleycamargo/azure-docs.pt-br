<properties
   pageTitle="Sincronização do Azure AD Connect: conector do PowerShell | Microsoft Azure"
   description="Este artigo descreve como configurar o conector Windows PowerShell da Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/24/2016"
   ms.author="andkjell"/>

# Referência técnica do Windows PowerShell Connector

Este artigo descreve o conector do Windows PowerShell. O artigo se aplica aos seguintes produtos:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   É necessário usar o hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2 o conector está disponível para download do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## Visão geral do conector do PowerShell

O conector do PowerShell permite a você integrar o serviço de sincronização a sistemas externos que oferecem APIs (interfaces de programação de aplicativo) com base no Windows PowerShell. O conector fornece uma ponte entre os recursos da estrutura do ECMA2 (agente de gerenciamento de conectividade extensível 2) baseada em chamada e o Windows PowerShell. Para obter mais informações sobre a estrutura do ECMA, confira [Extensible Connectivity 2.2 Management Agent Reference](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### Pré-requisitos

Para usar o conector, verifique se você tem os seguintes itens no servidor de sincronização, além de qualquer hotfix mencionado acima:

- Microsoft .NET 4.5.2 Framework ou posterior
- Windows PowerShell 2.0, 3.0 ou 4.0

A política de execução no servidor do Serviço de Sincronização deve ser configurada para permitir que o conector execute scripts do Windows PowerShell. A menos que os scripts que o conector executará sejam digitalmente assinados, configure a política de execução usando este comando:

`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## Criar um novo conector

Para criar um conector Windows PowerShell no serviço de sincronização, você deve fornecer uma série de scripts do Windows PowerShell que execute as etapas solicitadas pelo serviço de sincronização. Os scripts que você deverá implementar variarão de acordo com a fonte de dados com a qual você vai se conectar e da funcionalidade que vai exigir. Esta seção descreve cada um dos scripts que podem ser implementados e quando eles são exigidos.

O conector Windows PowerShell foi desenvolvido para armazenar cada um dos scripts dentro do banco de dados do Serviço de Sincronização. Embora seja possível executar scripts que são armazenados no sistema de arquivos, é muito mais fácil inserir o corpo de cada script diretamente na configuração do conector

Para criar um conector PowerShell, em **Serviço de Sincronização**, selecione **Agente de Gerenciamento** e **Criar**. Selecione o conector **PowerShell (Microsoft)**.

![Criar o conector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### Conectividade

Em seguida, você pode fornecer parâmetros de configuração para se conectar a um sistema remoto. Esses parâmetros serão armazenados com segurança pelo Serviço de Sincronização e disponibilizados para os scripts do Windows PowerShell quando o conector for executado.

![Conectividade](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

É possível configurar os seguintes parâmetros de conectividade:

**Conectividade**

Parâmetro | Valor Padrão | Finalidade
--- | --- | ---
Servidor | <Blank> | Nome do servidor ao qual o conector deve se conectar.
Domínio | <Blank> | Domínio da credencial a ser armazenado para uso quando o conector é executado.
Usuário | <Blank> | Nome de usuário da credencial a ser armazenado para uso quando o conector é executado.
Senha | <Blank> | Senha da credencial a ser armazenada para uso quando o conector é executado.
Representar Conta do Conector | Falso | Quando verdadeiro, o serviço de sincronização executará os scripts do Windows PowerShell no contexto das credenciais fornecidas acima. Quando possível, é recomendável que o parâmetro $Credentials passado para cada script seja usado no lugar da representação. Para obter mais informações sobre permissões adicionais que são necessárias para usar esse parâmetro, confira Configuração adicional para representação.
Carregar o Perfil de Usuário ao Representar | Falso | Instrui o Windows a carregar o perfil do usuário das credenciais do conector durante a representação. Se o usuário a ser representado tiver um perfil móvel, o conector não carregará o perfil móvel. Para obter mais informações sobre permissões adicionais que são necessárias para usar esse parâmetro, confira Configuração adicional para representação.
Tipo de Logon ao Representar | Nenhum | Tipo de logon durante representação. Para obter mais informações, veja a documentação [dwLogonType][dw].
Somente Scripts Assinados | Falso | Se verdadeiro, o conector Windows PowerShell confirmará que cada script tem uma assinatura digital válida. Se falso, verifique se a política de execução do Windows PowerShell do servidor do Serviço de Sincronização é RemoteSigned ou Unrestricted.

**Módulo comum**

O conector permite ao administrador armazenar um módulo compartilhado do Windows PowerShell na configuração. Quando o conector executa um script, o módulo do Windows PowerShell é extraído para o sistema de arquivos para que ele possa ser importado por cada script.

Para os scripts Importação, Exportação e Sincronização de Senha, o módulo comum é extraído para a pasta MAData do conector. Para os scripts Descoberta de Esquema, Validação, Hierarquia e Partição, o módulo comum é extraído para a pasta %TEMP%. Em ambos os casos, o script Módulo Comum é denominado de acordo com a configuração Nome de Script do Módulo Comum.

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 da pasta MAData, use a seguinte instrução: `Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 da pasta %TEMP%, use a seguinte instrução: `Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validação de parâmetro**

O Script de Validação é um script opcional do Windows PowerShell que pode ser usado para garantir que os parâmetros de configuração do conector fornecidos pelo administrador sejam válidos. Validar as credenciais de conexão e servidor, bem como os parâmetros de conectividade são usos comuns do script de validação. O script de validação é chamado depois que as seguintes guias e caixas de diálogo são modificadas:

- Conectividade
- Parâmetros Globais
- Configuração de Partição

O script de validação recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameterPage | [ConfigParameterPage][cpp] | A guia ou caixa de diálogo de configuração que disparou a solicitação de validação.
ConfigParameters | [KeyedCollection][keyk] [string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.

O script de validação deve retornar um único objeto ParameterValidationResult para o pipeline.

**Descoberta de Esquema**

O script Descoberta de Esquema é obrigatório. Esse script retorna os tipos de objeto e atributos, bem como restrições de atributo que o Serviço de Sincronização usará ao configurar regras de fluxo de atributo. O script Descoberta de Esquema é executado durante a criação do conector e será populado pelo esquema do conector e subsequentemente pela função Atualizar Esquema no Gerenciador do Serviço de Sincronização.

O script de descoberta de esquema recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk] [string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.

O script deve retornar um único objeto [Schema][schema] para o pipeline. O objeto Schema é composto por objetos [SchemaType][schemaT] que representam tipos de objeto (por ex., usuários, grupos, etc.). O objeto SchemaType mantém um conjunto de objetos [SchemaAttribute][schemaA] que representa os atributos (por ex., nome fornecido, sobrenome, endereço postal, etc.) do tipo.

**Parâmetros adicionais**

Além das definições de configuração padrão abordadas até agora, você pode definir configurações personalizadas adicionais que sejam específicas à instância do conector. Esses parâmetros podem ser especificados nos níveis de conector, partição ou etapa de execução e acessados no script relevante do Windows PowerShell. As definições de configuração personalizadas podem ser armazenadas no banco de dados do Serviço de Sincronização como texto sem formatação ou podem ser criptografadas. O Serviço de Sincronização criptografa e descriptografa automaticamente as definições de configuração seguras quando necessário.

Para especificar as definições de configuração personalizadas, separe o nome de cada parâmetro com uma vírgula (,).

Para acessar as definições de configuração personalizadas em um script, você deve colocar o sufixo sublinhado (\_) no nome e o escopo do parâmetro (Global, Partição ou RunStep). Por exemplo, para acessar o parâmetro Global FileName, use este trecho de código: `$ConfigurationParameters["FileName_Global"].Value`

### Funcionalidades

A guia Recursos do Designer do Agente de Gerenciamento define o comportamento e a funcionalidade do conector. As seleções feitas nessa guia não podem ser modificadas depois que o conector tiver sido criado. A tabela a seguir lista cada uma das configurações do recurso.

![Funcionalidades](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

Recurso | Descrição |
--- | --- |
[Estilo de Nome Diferenciado][dnstyle] | Indica se o conector oferecerá suporte a nomes diferenciados e, em caso positivo, à qual estilo.
[Tipo de Exportação][exportT] | Determina os tipos de objeto que são apresentados para o script Exportar. <li>AttributeReplace – inclui o conjunto completo de valores para um atributo com vários valores quando o atributo muda.</li><li>AttributeUpdate – inclui apenas os deltas para um atributo com vários valores quando o atributo muda.</li><li>MultivaluedReferenceAttributeUpdate - inclui um conjunto completo de valores de atributos com vários valores de não referência e apenas deltas para atributos de referência com vários valores.</li><li>ObjectReplace – inclui todos os atributos de um objeto quando algum atributo muda</li>
[Normalização de Dados][DataNorm] | Instrui o Serviço de Sincronização a normalizar os atributos de âncora antes que eles sejam fornecidos aos scripts.
[Confirmação do Objeto][oconf] | Define o comportamento de importação pendente no Serviço de Sincronização. <li>Normal – comportamento padrão que espera que todas as alterações exportadas sejam confirmadas via importação</li><li>NoDeleteConfirmation – quando um objeto é excluído, não há nenhuma importação pendente gerada.</li><li>NoAddAndDeleteConfirmation – quando um objeto é criado ou excluído, não há nenhuma importação pendente gerada.</li>
Usar DN como âncora | Se o Estilo de Nome Diferenciado for definido como LDAP, o atributo de âncora do espaço do conector também será o nome diferenciado.
Operações Simultâneas de Vários Conectores | Quando marcada, vários conectores do Windows PowerShell podem ser executados simultaneamente.
Partições | Quando marcada, o conector oferece suporte a várias partições e à descoberta de partição.
Hierarquia | Quando marcada, o conector oferece suporte a uma estrutura hierárquica de estilo LDAP.
Habilitar Importação | Quando marcada, o conector importará dados por meio de scripts de importação.
Habilitar Importação Delta | Quando marcada, o conector pode solicitar deltas de scripts de importação.
Habilitar Exportação | Quando marcada, o conector exportará dados por meio de scripts de exportação.
Habilitar Exportação Completa | Quando marcada, os scripts de exportação oferecem suporte à exportação do espaço inteiro do conector. Para usar essa opção, Habilitar Exportação também deve ser marcada.
Nenhum Valor de Referência na Primeira Passagem de Exportação | Quando marcada, os atributos de referência são exportados em uma segunda passagem de exportação.
Habilitar Renomeação do Objeto | Quando marcada, os nomes diferenciados podem ser modificados.
Excluir-Adicionar como Substituição | Quando marcada, as operações excluir-adicionar são exportadas como uma única substituição.
Habilitar Operações de Senha | Quando marcada, há suporte para scripts de sincronização de senha.
Habilitar Exportar Senha na Primeira Passagem | Quando marcada, as senhas definidas durante o provisionamento são exportadas quando o objeto é criado.

### Parâmetros Globais

A guia Parâmetros Globais no Designer do Agente de Gerenciamento permite ao administrador configurar cada script do Windows PowerShell que será executado pelo conector, bem como os valores globais para definições de configuração personalizadas na guia Conectividade.

**Descoberta de partição**

Uma partição é um namespace separado dentro de um esquema compartilhado. Por exemplo, no Active Directory, cada domínio é uma partição em uma floresta. Uma partição é o agrupamento lógico de operações de importação e exportação. Importação e Exportação têm partição como um contexto e todas as operações devem ocorrer nesse contexto. As partições supostamente representam uma hierarquia no LDAP. O nome diferenciado de uma partição é usado na importação para verificar se todos os objetos retornados estão dentro do escopo de uma partição. O nome diferenciado da partição também é usado durante o provisionamento do metaverso para o espaço do conector, de modo a determinar à qual partição um objeto deve ser associado durante a exportação.

O script de descoberta de partição recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.

O script deve retornar um único objeto [Partition][part] ou uma Lista[T] de objetos Partition para o pipeline.

**Descoberta de hierarquia**

O script de descoberta de hierarquia é usado apenas quando o recurso Estilo de Nome Diferenciado é LDAP. O script é usado para permitir aos administradores navegar e selecionar em um conjunto de contêineres que serão considerados dentro ou fora do escopo para operações de importação e exportação. O script deve fornecer somente uma lista de nós que sejam filhos diretos do nó raiz fornecido para o script.

O script de descoberta de hierarquia recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
ParentNode | [HierarchyNode][hn] | O nó raiz da hierarquia sob a qual o script deve retornar os filhos diretos.

O script deve retornar um único objeto filho HierarchyNode ou uma Lista[T] de objetos filho HierarchyNode para o pipeline.

#### Importar

Os conectores que oferecem suporte às operações de importação devem implementar três scripts.

**Iniciar Importação**

O script de início de importação é executado no começo de uma etapa de execução de importação. Durante esta etapa, você pode estabelecer uma conexão com sistemas de origem e realizar quaisquer etapas preparatórias antes da importação de dados do sistema conectado.

O script de início de importação recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de execução de importação (delta ou completa), partição, hierarquia, marca d'água e tamanho esperado da página.
Tipos | [Esquema][schema] | Esquema para o espaço do conector que será importado.

O script deve retornar um único objeto [OpenImportConnectionResults][oicres] para o pipeline. O código de exemplo abaixo demonstra como retornar um objeto OpenImportConnectionResults para o pipeline:

`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importar dados**

O script de importação de dados é chamado pelo conector até que o script indique que não há mais dados a serem importados e o serviço de sincronização não precise solicitar importações completas de objeto durante uma importação delta. O conector Windows PowerShell tem um tamanho de página de 9.999 objetos. Se o script for retornar mais de 9.999 objetos para importação, você deverá oferecer suporte à paginação. O conector expõe uma propriedade dados personalizada que pode ser usada para armazenar uma marca d’água, de modo que toda vez que o script de importação de dados for chamado, o script retome a importação de objetos onde ela foi interrompida.

O script de importação de dados recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
GetImportEntriesRunStep | [ImportRunStep][irs] | Contém a marca d'água (CustomData) que pode ser usada durante importações paginadas e importações delta.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de execução de importação (delta ou completa), partição, hierarquia, marca d'água e tamanho esperado da página.
Tipos | [Esquema][schema] | Esquema para o espaço do conector que será importado.

O script de importação de dados deve gravar um objeto List[[CSEntryChange][csec]] no pipeline. Essa coleção é composta por atributos CSEntryChange que representam cada objeto que está sendo importado. Durante uma importação completa, essa coleção deve ter um conjunto completo de objetos CSEntryChange com todos os atributos para cada objeto individual. Durante uma importação delta, o objeto CSEntryChange deve conter os deltas de nível de atributo para cada objeto a ser importado ou uma representação completa dos objetos que foram alterados (modo Substituir).

**Finalizar Importação**

Na conclusão da execução da importação, o script Finalizar Importação será executado. Esse script deve executar as tarefas de limpeza necessárias (por exemplo, fechar conexões com sistemas, responder a falhas, etc.).

O script de finalização de importação recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
OpenImportConnectionRunStep | [OpenImportConnectionRunStep][oicrs] | Informa o script sobre o tipo de execução de importação (delta ou completa), partição, hierarquia, marca d'água e tamanho esperado da página.
CloseImportConnectionRunStep | [CloseImportConnectionRunStep][cecrs] | Informa o script sobre o motivo da finalização da importação.

O script deve retornar um único objeto [CloseImportConnectionResults][cicres] para o pipeline. O código de exemplo abaixo demonstra como retornar um objeto CloseImportConnectionResults para o pipeline: `Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### Exportação

Assim como a arquitetura de importação do conector, os conectores que oferecem suporte à exportação devem implementar três scripts.

**Iniciar Exportação**

O script de início de exportação é executado no começo de uma etapa de execução de exportação. Durante esta etapa, você pode estabelecer uma conexão com sistemas de origem e realizar quaisquer etapas preparatórias antes da exportação de dados do sistema conectado.

O script de início de exportação recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de execução de exportação (delta ou completa), partição, hierarquia e tamanho esperado da página.
Tipos | [Esquema][schema] | Esquema para o espaço do conector que será exportado.

O script não deve retornar saídas para o pipeline.

**Exportar Dados**

O Serviço de Sincronização chamará o script Exportar Dados quantas vezes forem necessárias para processar todas as exportações pendentes. Independentemente de o espaço do conector ter ou não mais exportações pendentes do que o tamanho da página do conector, da presença dos atributos de referência, ou senhas, o script de exportação de dados pode ser chamado várias vezes e, possivelmente, várias vezes para o mesmo objeto.

O script de exportação de dados recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
CSEntries | IList[CSEntryChange][csec] | Lista de todos os objetos do espaço do conector com exportações pendentes a serem processadas durante essa etapa.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de execução de exportação (delta ou completa), partição, hierarquia e tamanho esperado da página.
Tipos | [Esquema][schema] | Esquema para o espaço do conector que será exportado.

O script de exportação de dados deve retornar um objeto [PutExportEntriesResults][peeres] para o pipeline. Esse objeto não precisa incluir informações de resultado para cada conector exportado, a menos que ocorra um erro ou uma alteração no atributo de âncora.

O código de exemplo abaixo demonstra como retornar um objeto PutExportEntriesResults para o pipeline: `Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Finalizar Exportação**

Na conclusão da execução da exportação, o script Finalizar Exportação será executado. Esse script deve executar as tarefas de limpeza necessárias (por exemplo, fechar conexões com sistemas, responder a falhas, etc.).

O script de finalização de exportação recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
OpenExportConnectionRunStep | [OpenExportConnectionRunStep][oecrs] | Informa o script sobre o tipo de execução de exportação (delta ou completa), partição, hierarquia e tamanho esperado da página.
CloseExportConnectionRunStep | [CloseExportConnectionRunStep][cecrs] | Informa o script sobre o motivo da finalização da exportação.

O script não deve retornar saídas para o pipeline.

#### Sincronização de senha

Os conectores do Windows PowerShell podem ser usados como um destino para alterações/redefinições de senha.

O script de senha recebe os seguintes parâmetros do conector:

Nome | Tipo de Dados | Descrição
--- | --- | ---
ConfigParameters | [KeyedCollection][keyk][string, [ConfigParameter][cp]] | Tabela de parâmetros de configuração para o conector.
Credencial | [PSCredential][pscred] | Contém as credenciais inseridas pelo administrador na guia Conectividade.
Partição | [Partition][part] | Partição de diretório em que CSEntry está.
CSEntry | [CSEntry][cse] | Entrada do espaço do conector para o objeto que recebeu uma alteração ou redefinição de senha.
OperationType | Cadeia de caracteres | Indica se a operação é uma redefinição (**SetPassword**) ou uma alteração (**ChangePassword**).
PasswordOptions | [PasswordOptions][pwdopt] | Sinalizadores que especificam o comportamento de redefinição de senha pretendido. Esse parâmetro estará disponível somente se OperationType for **SetPassword**.
OldPassword | Cadeia de caracteres | Populado com a senha antiga do objeto para alterações de senha. Esse parâmetro estará disponível somente se OperationType for **ChangePassword**.
NewPassword | Cadeia de caracteres | Populado com a nova senha do objeto que o script deve definir.

Não é comum que o script de senha retorne algum resultado para o pipeline do Windows PowerShell. Se ocorrer um erro no script de senha, o script deverá lançar uma das seguintes exceções para informar o Serviço de Sincronização sobre o problema:

- [PasswordPolicyViolationException][pwdex1] – lançada se a senha não atender à política de senha no sistema conectado.
- [PasswordIllFormedException][pwdex2] – lançada se a senha não for aceitável para o sistema conectado.
- [PasswordExtension][pwdex3] – lançada para todos os outros erros no script de senha.

## Conectores de exemplo

Para obter uma visão geral completa dos conectores de exemplo disponíveis, confira [Windows PowerShell Connector Sample Connector Collection][samp].

## Outras observações

### Configuração adicional para representação

Conceda ao usuário que será representado as seguintes permissões no servidor do Serviço de Sincronização:

Acesso de leitura para as seguintes chaves do registro:

- HKEY\_USERS\\[SynchronizationServiceServiceAccountSID]\\Software\\Microsoft\\PowerShell
- HKEY\_USERS\\[SynchronizationServiceServiceAccountSID]\\Environment

Para determinar o SID (Identificador de Segurança) da conta de serviço do Serviço de Sincronização, execute os seguintes comandos do PowerShell:

```
$account = New-Object System.Security.Principal.NTAccount "<domain><username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Acesso de leitura para as seguintes pastas do sistema de arquivos:

- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\Extensions
- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\ExtensionsCache
- %ProgramFiles%\\Microsoft Forefront Identity Manager\\2010\\Synchronization Service\\MaData<ConnectorName>

Substitua o nome do conector Windows PowerShell para o espaço reservado <ConnectorName>.

## Solucionar problemas

-	Para saber mais sobre como habilitar o registro em log para solucionar problemas do conector, confira [How to Enable ETW Tracing for Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291

<!---HONumber=AcomDC_0601_2016-->