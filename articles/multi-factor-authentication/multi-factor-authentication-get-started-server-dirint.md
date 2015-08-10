<properties 
	pageTitle="Integração de diretórios entre o Azure Multi-Factor Authentication autenticação multifator do Azure e o Active Directory" 
	description="Esta é a página de autenticação multifator do Azure que descreve como integrar o servidor Azure Multi-Factor Authentication ao Active Directory para sincronizar os diretórios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="billmath"/>

# Integração de diretórios entre o Azure MFA Server e o Active Directory

A seção Integração de Diretórios permite que você configure o servidor para se integrar com o Active Directory ou com outro diretório LDAP. Ele permite que você configure atributos para corresponder ao esquema de diretório e configurar a sincronização automática de usuários.

## Configurações
Por padrão, o Servidor do Azure Multi-Factor Authentication é configurado para importar ou sincronizar usuários do Active Directory. A guia permite substituir o comportamento padrão e associar a um diretório LDAP diferente, a um diretório ADAM ou a um controlador de domínio específico do Active Directory. Ele também permite o uso da autenticação LDAP para proxy LDAP ou associação LDAP como um destino RADIUS, pré-autenticação para autenticação IIS ou autenticação principal para o portal do usuário. A tabela a seguir descreve as configurações individuais.

![Configurações](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Recurso | Descrição |
| ------- | ----------- |
| Usar o Active Directory | Selecione a opção Usar o Active Directory para usar o Active Directory na importação e na sincronização. Esta é a configuração padrão. <br>Observação: o computador deve estar associado a um domínio e você precisa estar conectado com uma conta de domínio para que a integração do Active Directory funcione corretamente. |
| Incluir domínios confiáveis | Marque a caixa de seleção incluir domínios confiáveis para que o agente tente se conectar a domínios confiáveis pelo domínio atual, a outro domínio na floresta ou a domínios envolvidos em uma relação de confiança de floresta. Quando não estiver importando ou sincronizando usuários de qualquer um dos domínios confiáveis, desmarque a caixa de seleção para melhorar o desempenho. O padrão é verificado. |
| Usar configuração LDAP específica | Selecione a opção Usar LDAP para usar as configurações de LDAP especificadas na importação e na sincronização. Observação: quando a opção Usar LDAP é selecionada, a interface do usuário altera referências do Active Directory para LDAP. |
| Botão Editar | O botão Editar permite que as configurações atuais de LDAP sejam modificadas. |
| Usar consultas de escopo de atributo | Indica se as consultas de escopo de atributo devem ser usadas. As consultas de escopo de atributo permitem que as pesquisas eficientes de diretórios qualificando os registros com base no atributo de outro registro. O servidor Azure Multi-Factor Authentication usa as consultas de escopo de atributo para consultar eficientemente os usuários que são membros de um grupo de segurança. <br>Observação: há alguns casos em que há suporte para consultas de escopo de atributo, mas elas não devem ser usadas. Por exemplo, o Active Directory pode ter problemas com consultas de escopo de atributo quando um grupo de segurança contiver membros de mais de um domínio. Nesse caso, a caixa de seleção deve ser desmarcada. |

A tabela a seguir descreve as definições de configuração de LDAP.

| Recurso | Descrição |
| ------- | ----------- |
| Servidor | Insira o nome do host ou o endereço IP do servidor que está executando o diretório LDAP. Um servidor de backup também pode ser especificado separado por ponto-e-vírgula. <br>Observação: quando o tipo de associação é SSL, geralmente é necessário um nome de host totalmente qualificado. |
| DN base | Insira o nome diferenciado do objeto de diretório base do qual todas as consultas de diretório serão iniciadas. Por exemplo, d=abc,dc=com. |
| Tipo de associação – Consultas | Selecione o tipo de associação apropriado a ser usado na associação do diretório LDAP à pesquisa. Isso é usado para importações, sincronização e resolução de nome de usuário. <br><br>Anônimo: uma ligação anônima será executada. O DN de Associação e a Senha de Associação não serão usados. Isso só funcionará se o diretório LDAP permitir a associação anônima e as permissões autorizarem a consulta dos registros e atributos adequados. <br><br>Simples: Associar DN e Associar Senha serão passados como texto sem formatação para associar ao diretório LDAP. Isso deve ser usado apenas para testes, a fim de verificar se o servidor pode ser acessado e se a conta de associação tem o acesso apropriado. É recomendável que o SSL seja usado após o certificado apropriado ter sido instalado. <br><br>SSL: Associar DN e Associar Senha serão criptografados usando SSL para associar ao diretório LDAP. Isso requer que um certificado em que o diretório LDAP confie seja instalado localmente. <br><br>Windows: Associar Nome de Usuário e Associar Senha serão usados para se conectar com segurança a um controlador de domínio do Active Directory ou diretório ADAM. Se Associar Nome de Usuário for deixado em branco, a conta do usuário conectado será usada na associação. |
| Tipo de associação – Autenticações | Selecione o tipo de associação apropriado para usar ao executar a autenticação de associação LDAP. Veja as descrições de tipo de associação em Tipo de associação: consultas. Por exemplo, isso permite que a associação anônima seja utilizada para consultas enquanto associação SSL seja usada para proteger as autenticações de associação LDAP. |
| Associar DN de ligação ou nome de usuário | Digite o nome distinto do registro do usuário para a conta a ser usada ao associar ao diretório LDAP.<br><br>O nome distinto de associação somente é usado quando o tipo de associação é simples ou SSL. <br><br>Insira o nome de usuário da conta do Windows para usar ao associar ao diretório LDAP quando o tipo de associação for Windows. Se for deixado em branco, a conta do usuário conectado será usada na associação. |
| Senha de associação | Insira a senha de associação para o DN ou nome de usuário de associação que está sendo usado para associação ao diretório LDAP. Para configurar a senha do Serviço AdSync do Servidor Multi-Factor Auth, a sincronização deve ser habilitada e o serviço deve estar em execução no computador local. A senha será salva em Nomes de Usuário e Senhas Armazenados no Windows na conta em que o Serviço AdSync do Servidor Azure Multi-Factor Authentication estiver em execução. A senha também será salva na conta em que a interface de usuário do servidor Multi-Factor Auth estiver em execução e na conta em que o serviço do servidor Azure Multi-Factor Authentication estiver em execução. <br><br>Observação: uma vez que a senha é armazenada somente em Nomes de Usuário e Senhas Armazenados do Windows do servidor local, essa etapa precisará ser executada em cada Servidor Multi-Factor Auth que precise de acesso à senha. |
| Limite de tamanho de consulta | Especifique o limite de tamanho para o número máximo de usuários que uma pesquisa de diretório retornará. Esse limite deve corresponder à configuração no diretório LDAP. Em pesquisas grandes, em que a paginação não tem suporte, a importação e a sincronização tentarão recuperar os usuários em lotes. Se o limite de tamanho especificado aqui for maior que o limite configurado no diretório LDAP, alguns usuários poderão ser perdidos. |
| Botão de teste | Clique no botão Testar para testar a associação ao servidor LDAP. <br><br>Observação:a opção Usar LDAP não precisa ser selecionada para testar a associação. Isso permite que a associação seja testada antes de usar a configuração de LDAP. |

## Filtros
Filtros permitem que você defina os critérios para qualificar os registros ao executar uma pesquisa de diretório. Definindo o filtro, você pode definir o escopo de objetos que você deseja sincronizar.

![Filtros](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

O Azure Multi-Factor Authentication tem três opções a seguir.

- **Filtro de contêiner**: especifique os critérios de filtro usados para qualificar os registros de contêiner ao executar uma pesquisa de diretório. Para o Active Directory e o ADAM, (| ( objectClass=organizationalUnit)(objectClass=container)) geralmente é usado. Para outros diretórios LDAP, os critérios de filtro que qualificam cada tipo de objeto de contêiner devem ser usados dependendo do esquema de diretório. <br>Observação: se for deixado em branco, ((objectClass=organizationalUnit)(objectClass=container)) será usado por padrão.

- **Filtro de grupo de segurança**: especifique os critérios de filtro usados para qualificar os registros do grupo de segurança ao executar uma pesquisa de diretório. Para o Active Directory e o ADAM, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) geralmente é usado. Para outros diretórios LDAP, os critérios de filtro que qualificam cada tipo de objeto do grupo de segurança devem ser usados dependendo do esquema de diretório. <br>Observação: se for deixado em branco, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) será usado por padrão.

- **Filtro de usuário**: especifique os critérios de filtro usados para qualificar os registros de usuário ao executar uma pesquisa de diretório. Para o Active Directory e o ADAM, (& (objectClass=user)(objectCategory=person)) geralmente é usado. Para outros diretórios LDAP, (objectClass = inetOrgPerson) ou algo semelhante deve ser usado dependendo do esquema de diretório. <br>Observação: se for deixado em branco, (&(objectCategory=person)(objectClass=user)) será usado por padrão.

## Atributos
Os atributos podem ser personalizados conforme necessário para um diretório específico. Isso permite que você adicione atributos personalizados e ajuste a sincronização somente para os atributos de que você precisa. O valor para cada campo de atributo deve ser o nome do atributo conforme definido no esquema de diretório. Use a tabela abaixo para obter informações adicionais.

![Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Recurso | Descrição |
| ------- | ----------- |
| Identificador exclusivo | Digite o nome do atributo que serve como o identificador exclusivo do contêiner, do grupo de segurança e dos registros de usuário. No Active Directory, isso geralmente é objectGUID. Em outras implementações LDAP, pode ser entryUUID ou algo semelhante. O padrão é objectGUID. |
| ... Botões (selecionar atributo) | Cada campo de atributo tem um botão “..."ao lado dele que exibirá o diálogo Select Attribute, permitindo que um atributo seja selecionado em uma lista. <br><br>Diálogo Selecionar Atributo.<br><br>Observação: os atributos podem ser inseridos manualmente e não precisa coincidir com um atributo na lista de atributos. |
| Tipo de identificador exclusivo | Selecione o tipo de atributo de identificador exclusivo. No Active Directory, o atributo objectGUID é do tipo GUID. Em outras implementações LDAP, ele pode ser do tipo cadeia de caracteres ou matriz de bytes ASCII. O padrão é GUID. <br><br>Observação: é importante definir isso corretamente, já que os itens de sincronização são referenciados por seu identificador exclusivo e o tipo de identificador exclusivo é usado para localizar diretamente o objeto no diretório. Definir isso como cadeia de caracteres quando o diretório realmente armazena o valor como uma matriz de bytes de caracteres ASCII impedirá que a sincronização funcione corretamente. |
| Nome distinto | Digite o nome do atributo que contém o nome distinto para cada registro. No Active Directory, isso geralmente é distinguishedName. Em outras implementações LDAP, pode ser entryDN ou algo semelhante. O padrão é distinguishedName. <br><br>Observação: se um atributo contendo apenas o nome distinto não existir, o atributo adspath poderá ser usado. A parte do caminho "LDAP: / /<server>/" será removida automaticamente, deixando apenas o nome distinto do objeto. |
| Nome do contêiner | Insira o nome do atributo que contém o nome em um registro de contêiner. O valor desse atributo será exibido na hierarquia do contêiner ao importar do Active Directory ou adicionar itens de sincronização. O padrão é o nome. <br><br>Observação: se contêineres diferentes usarem atributos diferentes para seus nomes, vários atributos de nome de contêiner poderão ser especificados separados por ponto e vírgula. O primeiro atributo de nome de contêiner encontrado em um objeto de contêiner será ser usado para exibir seu nome. |
| Nome do grupo de segurança | Insira o nome do atributo que contém o nome em um registro de grupo de segurança. O valor desse atributo será exibido na lista Grupo de segurança ao importar do Active Directory ou adicionar itens de sincronização. O padrão é o nome. |
| Usuários | Os seguintes atributos são usados para procurar, exibir, importar e sincronizar informações de usuário do diretório. |
| Nome de Usuário | Digite o nome do atributo que contém o nome de usuário em um registro de usuário. O valor deste atributo será usado como o nome de usuário do servidor Multi-Factor Auth. Um segundo atributo pode ser especificado como um backup do primeiro. O segundo atributo somente será usado se o primeiro atributo não contiver um valor para o usuário. Os padrões são userPrincipalName e sAMAccountName. |
| Nome | Insira o nome do atributo que contém o nome em um registro de usuário. O padrão é givenName. |
| Sobrenome | Insira o nome do atributo que contém o sobrenome em um registro de usuário. O padrão é sn. |
| Endereço de email | Insira o nome do atributo que contém o endereço de email em um registro de usuário. O endereço de email será usado para enviar boas-vindas e emails de atualização para o usuário. O padrão é email. |
| Grupo de usuários | Insira o nome do atributo que contém o grupo de usuários em um registro de usuário. O grupo de usuários pode ser usado para filtrar usuários no agente e em relatórios no Portal de Gerenciamento do servidor Multi-Factor Authentication. |
| Descrição | Insira o nome do atributo que contém a descrição em um registro de usuário. A descrição é usada somente para pesquisa. O padrão é descrição. |
| Idioma da chamada de voz | Insira o nome do atributo que contém o nome curto do idioma a ser usado para chamadas de voz para o usuário. |
| Idioma de texto SMS | Insira o nome do atributo que contém o nome curto do idioma a ser usado para mensagens de texto SMS para o usuário. |
| Idioma do aplicativo Telefone | Insira o nome do atributo que contém o nome curto do idioma a ser usado para mensagens de texto do aplicativo Telefone do usuário. |
| Idioma do token OATH | Insira o nome do atributo que contém o nome curto do idioma a ser usado para mensagens de texto de token OATH para o usuário. |
| Telefones | Os seguintes atributos são usados para importar ou sincronizar os números de telefone do usuário. Se um nome de atributo não for especificado para o tipo de telefone, o tipo de telefone não estará disponível quando importar do Active Directory ou adicionar itens de sincronização. |
| Negócios | Insira o nome do atributo que contém o número de telefone comercial em um registro de usuário. O padrão é telephoneNumber. |
| Home | Insira o nome do atributo que contém o número de telefone residencial em um registro de usuário. O padrão é homePhone. |
| Pager | Digite o nome do atributo que contém o número de pager em um registro de usuário. O padrão é pager. |
| Móvel | Insira o nome do atributo que contém o número de telefone celular em um registro de usuário. O padrão é celular. |
| Fax | Insira o nome do atributo que contém o número de fax em um registro de usuário. O padrão é facsimileTelephoneNumber. |
| Telefone IP | Insira o nome do atributo que contém o número de telefone IP em um registro de usuário. O padrão é ipPhone. |
| Personalizado | Insira o nome do atributo que contém um número de telefone personalizado em |
| | um registro de usuário. O padrão é em branco. |
| Extensão | Insira o nome do atributo que contém o ramal do número de telefone em um registro de usuário. O valor do campo extensão será usado como a extensão somente para o número de telefone principal. O padrão é em branco. <br><br>Observação: se o atributo extensão não for especificado, as extensões podem ser incluídas como parte do atributo telefone. A extensão deve ser precedida por um 'x' para que ele possa ser analisado. Por exemplo, 3123-4567 x890 resultaria em 3123-4567 como o número de telefone e 890 como a extensão. |
| Botão Restaurar Padrões | Clique no botão Restaurar Padrões para retornar todos os atributos de volta ao seu valor padrão. Os padrões devem funcionar corretamente com o esquema do Active Directory ou do ADAM normal. |

Para editar atributos, basta clicar no botão Editar na guia Atributos. Isso abrirá uma janela que permite que você edite os atributos.

![Editar Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## Sincronização
A sincronização mantém o banco de dados do usuário do Azure Multi-Factor sincronizado com os usuários no Active Directory ou outro diretório LDAP (Lightweight Directory Access Protocol). O processo é semelhante à importação manual de usuários do Active Directory, mas faz sondagens para processar alterações do grupo de segurança e de usuário do Active Directory. Ele também possibilita a desativação ou remoção de usuários removidos de um contêiner ou grupo de segurança e remoção de usuários excluídos do Active Directory.

O serviço ADSync do Multi-Factor Auth é um serviço do Windows que executa a sondagem periódica do Active Directory. Isso não é deve ser confundido com o Azure AD Sync ou o Azure AD Connect. O Multi-Factor ADSync, embora criado sobre uma base de código semelhante, é específico para o servidor Azure Multi-Factor Authentication. Ele é instalado em um estado Parado e iniciado pelo serviço do servidor Multi-Factor Auth quando configurado para executar. Se você tiver uma configuração de vários servidores Multi-Factor Auth Server, o ADSync do Multi-Factor Auth somente pode ser executado em um único servidor.

O serviço ADSync do multi-Factor Auth usa a extensão do servidor DirSync LDAP fornecida pela Microsoft para sondar alterações com eficiência. O chamador de controle DirSync deve ter os direitos "directory get changes” e o controle estendido DS-Replication-Get-Changes. Por padrão, esses direitos são atribuídos às contas de administrador e LocalSystem nos controladores de domínio. Por padrão, o serviço AdSync do Multi-Factor Auth está configurado para ser executado como LocalSystem. Portanto, é mais simples de executar o serviço em um controlador de domínio. O serviço pode ser executado como uma conta com menos permissões se for configurado para sempre executar uma sincronização completa. Isso é menos eficiente, mas requer menos privilégios de conta.

Se configurado para usar LDAP e o diretório LDAP suportar o controle DirSync, a sondagem de alterações do grupo de segurança e de usuário funcionam da mesma forma como acontece com o Active Directory. Se o diretório LDAP não der suporte para o controle DirSync, uma sincronização completa será executada durante cada ciclo.

![Sincronização](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Use a tabela abaixo para obter informações adicionais sobre cada uma das configurações individuais na guia Sincronização.

| Recurso | Descrição |
| ------- | ----------- |
| Habilitar a sincronização com o Active Directory | Quando marcado, o serviço do servidor Multi-Factor Auth será iniciado para sondar o Active Directory quanto a alterações. <br><br>Observação: pelo menos um Item de sincronização deve ser adicionado e Sincronizar agora deve ser executado antes que o serviço do servidor Multi-Factor Auth inicie o processamento das alterações. |
| Sincronizar cada | Especifique o intervalo de tempo que o serviço de servidor Multi-Factor Authentication aguardará entre a sondagem e o processamento de alterações. <br><br>Observação: o intervalo especificado é o tempo entre o início de cada ciclo. Se o tempo de processamento das alterações exceder o intervalo, o serviço fará a sondagem novamente imediatamente. |
| Remover os usuários não está mais no Active Directory | Quando marcado, o serviço do servidor Multi-Factor Auth processa marcas de exclusão de usuários excluídos do Active Directory e removerá o usuário do servidor Multi-Factor Auth associado. |
| Sempre executar uma sincronização completa | Quando marcado, o serviço do servidor Multi-Factor Auth sempre executará uma sincronização completa. Quando desmarcado, o serviço do servidor Multi-Factor Auth Server executará uma sincronização incremental consultando apenas os usuários que foram alterados. O padrão é desmarcado. <br><br>Observação: quando desmarcada, a sincronização incremental só pode ser executada quando o diretório suporta o controle DirSync e a conta que está sendo usada para associar ao diretório tem as permissões apropriadas para executar consultas incrementais de DirSync. Se a conta não tiver as permissões apropriadas ou vários domínios estiverem envolvidos na sincronização, recomendamos que execute uma sincronização completa. |
| Exigir a aprovação do administrador quando mais de X usuários forem desabilitados ou removidos | Os itens de sincronização podem ser configurados para desabilitar ou remover os usuários que não são mais membros de contêiner ou grupo de segurança do item. Como salvaguarda, a aprovação de administrador pode ser exigida quando o número de usuários para desabilitar ou remover exceder um limite. Quando marcada, a opção de aprovação será exigida para o limite especificado. O padrão é 5 e o intervalo é de 1 a 999. <br><br>A aprovação é facilitada com o envio inicial de uma notificação por email aos administradores. A notificação de email fornece instruções para revisar e aprovar a desabilitação e a remoção de usuários. Quando a interface de usuário do servidor Multi-Factor Authentication for iniciada, ela irá solicitar aprovação. |

O botão **Sincronizar Agora** permite que você execute uma sincronização completa dos itens de sincronização especificados. Uma sincronização completa é necessária sempre que itens de sincronização são adicionados, modificados, removidos ou reordenados. Também é necessária antes que o serviço AdSync do Multi-Factor Auth fique operacional, pois ele define o ponto de partida a partir do qual o serviço irá sondar alterações incrementais. Se houver alterações aos itens de sincronização e uma sincronização completa não tiver sido feita, você será solicitado a Sincronizar agora ao navegar para outra seção ou ao fechar a interface do usuário.

O botão **Remover** permite que o administrador exclua um ou mais itens de sincronização da lista de item de sincronização do servidor Multi-Factor Auth.

>[AZURE.WARNING]Depois que um registro de item de sincronização tiver sido removido, ele não pode ser recuperado. Você precisará adicionar o registro de item de sincronização novamente se tiver sido excluído por engano.

Os itens de sincronização foram removidos do servidor Multi-Factor Auth. O serviço do servidor Multi-Factor Auth não processará mais os itens de sincronização.

Os botões Mover para cima e Mover para baixo permitem que o administrador altere a ordem dos itens de sincronização. A ordem é importante, pois um mesmo usuário pode ser membro de mais de um item de sincronização (por exemplo, um contêiner e um grupo de segurança). As configurações aplicadas ao usuário durante a sincronização virão do primeiro item de sincronização na lista a qual o usuário estiver associado. Portanto, os itens de sincronização devem ser colocados em ordem de prioridade.

>[AZURE.TIP]Uma sincronização completa deve ser executada após a remoção de itens de sincronização. Uma sincronização completa deve ser executada após a ordenação dos itens de sincronização. Clique no botão Sincronizar Agora para executar uma sincronização completa.

## Servidores de autenticação multifator
Os servidores adicionais Multi-Factor Auth podem ser configurados para servir como um backup proxy RADIUS, proxy LDAP ou para autenticação IIS. A configuração da sincronização será compartilhada entre todos os agentes. No entanto, apenas um desses agentes pode ter a execução do serviço de servidor Multi-Factor Auth. Essa guia permite que você selecione o servidor Multi-Factor Auth que deve ser habilitado para sincronização.

![Servidores Multi-Factor Auth](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)

<!---HONumber=July15_HO5-->