Aqui estão as restrições de uso e outros limites de serviço para o serviço Active Directory do Azure.

### Diretórios

Um único usuário só pode ser associado a um máximo de 20 diretórios do Active Directory do Azure. Esse limite pode ser contatado em qualquer um dos exemplos a seguir:

- Um único usuário cria 20 diretórios.
- Um único usuário é adicionado a 20 diretórios como um membro.
- Um único usuário cria 10 diretórios e posteriormente é adicionado por outros 10 diretórios diferentes.

### Objetos

- Não há nenhum limite para assinantes Azure Active Directory Premium ou Azure Active Directory Basic, Enterprise Mobility Suite, Office 365, Microsoft Intune ou qualquer outro serviço online da Microsoft que dependa do Active Directory do Azure para serviços de diretório.
- Um máximo de 500.000 objetos pode ser usado em um único diretório com a edição do Active Directory do Azure Gratuito.
- Um usuário não administrador pode criar até 250 objetos.

###Extensões de esquema

Atualmente, as entidades "User", "Group", "TenantDetail", "Device", "Application" e "ServicePrincipal" podem ser estendidas com atributos de valor único de tipo "cadeia de caracteres" ou "binário". Elas incluem as seguintes limitações:

- Extensões de tipo de cadeia de caracteres podem ter no máximo 256 caracteres.
- Extensões de tipo binário são limitadas a 256 bytes.
- É possível gravar 100 valores de extensão (entre todos os tipos e todos os aplicativos) em um único objeto.
- As extensões de esquema estão disponíveis apenas na versão de visualização 1.21 da Graph API. É preciso conceder acesso de gravação para poder registrar uma extensão.

### Aplicativos

Um máximo de 10 usuários podem ser proprietários de um único aplicativo.

### Grupos

- Um máximo de 10 usuários podem ser proprietários de um único grupo.
- Qualquer número de objetos podem ser membros de um único grupo no Active Directory do Azure.


> [AZURE.NOTE]
>
> Há um limite para o número de objetos que você pode sincronizar do seu Active Directory local ao Active Directory do Azure.
>
> - Se você estiver usando o DirSync, o limite será de 15 mil usuários.
> - Se você estiver usando o Azure AD Connect, o limite será de 50 mil usuários.

<br/>
### Painel de acesso

- Não há nenhum limite para o número de aplicativos que podem ser vistos por cada usuário final no painel de acesso para os assinantes do Azure AD Premium ou do Enterprise Mobility Suite.
- Um máximo de 10 blocos de aplicativo (exemplos: Box, Salesforce ou Dropbox) pode ser visto no painel de acesso para cada usuário final com as edições gratuita ou básica do Active Directory do Azure. Esse limite não se aplica às contas de administrador.

### Relatórios

Um máximo de 1.000 linhas podem ser exibidas ou baixadas em qualquer relatório. Dados adicionais serão truncados.

<!---HONumber=July15_HO3-->