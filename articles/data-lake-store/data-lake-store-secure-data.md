<properties 
   pageTitle="Protegendo os dados armazenados no repositório Azure Data Lake | Microsoft Azure" 
   description="Aprenda a proteger os dados no repositório Azure Data Lake usando grupos e listas de controle de acesso" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/22/2016"
   ms.author="nitinme"/>

# Protegendo os dados armazenados no repositório Azure Data Lake

A proteção de dados no repositório Azure Data Lake é uma abordagem de três etapas.

1. Comece criando grupos de segurança no Active Directory do Azure (AAD). Esses grupos de segurança são usados para implementar o controle de acesso baseado em função (RBAC) no Portal do Azure. Para saber mais, consulte [Controle de acesso baseado em função no Microsoft Azure](../active-directory/role-based-access-control-configure.md).

2. Atribua os grupos de segurança do AAD à conta do repositório Azure Data Lake. Isso controla o acesso à conta do repositório Data Lake a partir das operações de portal e de gerenciamento do portal ou das APIs.

3. Atribua os grupos de segurança do AAD como listas de controle de acesso (ACLs) no sistema de arquivos do repositório Data Lake.

4. Além disso, você também pode definir um intervalo de endereços IP para clientes que podem acessar os dados no armazenamento do Data Lake.

Este artigo fornece instruções sobre como usar o Portal do Azure para realizar as tarefas acima. Para obter informações detalhadas sobre como Data Lake Store implementa a segurança no nível de conta e dados, consulte [Segurança no Armazenamento do Azure Data Lake](data-lake-store-security-overview.md).

## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Uma conta do repositório Azure Data Lake**. Para obter instruções sobre como criar uma, consulte [Introdução ao repositório Azure Data Lake](data-lake-store-get-started-portal.md)

## Você aprende rapidamente com vídeos?

[Assista a este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre como proteger os dados armazenados no Repositório Data Lake.

## Criar grupos de segurança no Active Directory do Azure

Para obter instruções sobre como criar grupos de segurança do AAD e como adicionar usuários ao grupo, consulte [Gerenciar grupos de segurança no Active Directory do Azure](../active-directory/active-directory-accessmanagement-manage-groups.md).

## Atribua usuários ou grupos de segurança às contas do repositório Azure Data Lake.

Ao atribuir usuários ou grupos de segurança às contas do repositório Azure Data Lake, você controla o acesso às operações de gerenciamento na conta usando o Portal do Azure e as APIs do Gerenciador de Recursos do Azure.

1. Abra uma conta do repositório Azure Data Lake. No painel esquerdo, clique em **Procurar**, clique em **Repositório Data Lake** e, na folha do repositório Data Lake, clique no nome de conta a qual você deseja atribuir um usuário ou grupo de segurança.

2. Na folha de sua conta do Repositório Data Lake, clique no ícone de usuário.

	![Atribuir grupo de segurança à conta do repositório Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir grupo de segurança à conta do repositório Azure Data Lake")

3. A folha **Usuário** lista por padrão o grupo **Administradores de assinatura** como proprietário.

	![Adicionar usuários e funções](./media/data-lake-store-secure-data/adl.add.group.roles.png "Adicionar usuários e funções")
 
	Há duas maneiras de adicionar um grupo e atribuir funções relevantes.

	* Adicione um usuário/grupo à conta e atribuir uma função ou
	* Adicione uma função e atribua usuários/grupos à função.

	Nesta seção, examinaremos a primeira abordagem, adicionando um grupo e atribuindo funções. Você pode executar etapas semelhantes para selecionar primeiro uma função e atribuir grupos a essa função.
	
4. Na folha **Usuários**, clique em **Adicionar** para abrir a folha **Adicionar acesso**. Na folha **Adicionar acesso**, clique em **Selecionar uma função** e selecione uma função para o usuário/grupo.

	 ![Adicionar uma função ao usuário](./media/data-lake-store-secure-data/adl.add.user.1.png "Adicionar uma função ao usuário")

	As funções **Proprietário** e **Colaborador** fornecem acesso a várias funções de administração da conta do Data Lake. Adicione os usuários que interagirão com dados no Data Lake à função **Leitor**. O escopo dessas funções é limitado às operações de gerenciamento relacionadas à conta do repositório Azure Data Lake.

	Para as operações de dados, as permissões do sistema de arquivos individual definem o que os usuários podem fazer. Portanto, um usuário com uma função de Leitor pode exibir somente as configurações administrativas associadas à conta, mas possivelmente pode ler e gravar dados com base nas permissões de sistema de arquivo atribuídas a ele. As permissões do sistema de arquivos do repositório Data Lake são descritas em [Atribuir grupo de segurança como ACLs ao sistema de arquivos do repositório Azure Data Lake](#filepermissions).



5. Na folha **Adicionar acesso**, clique em **Adicionar usuários** para abrir a folha **Adicionar usuários**. Nesta folha, procure o grupo de segurança criado anteriormente no Active Directory do Azure. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique em **Selecionar**.

	![Adicionar um grupo de segurança](./media/data-lake-store-secure-data/adl.add.user.2.png "Adicionar um grupo de segurança")

	Se você quiser adicionar um grupo/usuário que não esteja listado, convide-o usando o ícone **Convidar** e especificando o endereço de email para o usuário/grupo.

6. Clique em **OK**. O grupo de segurança deve ter sido adicionado, como exibido abaixo.

	![Grupo de segurança adicionado](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança adicionado")

7. Agora, o usuário/grupo de segurança tem acesso à conta do repositório Azure Data Lake. Se você quiser fornecer acesso a usuários específicos, adicione-os ao grupo de segurança. Da mesma forma, se você quiser revogar o acesso de um usuário, remova-o do grupo de segurança. Também é possível atribuir vários grupos de segurança a uma conta.

## <a name="filepermissions"></a>Atribuir usuários ou grupo de segurança como ACLs ao sistema de arquivos do repositório Azure Data Lake

Ao atribuir usuários/grupos de segurança ao sistema de arquivos do Azure Data Lake, defina o controle de acesso nos dados armazenados no repositório Azure Data Lake.

>[AZURE.NOTE] Na versão atual, você só pode definir ACLs no nó raiz da conta do Repositório Data Lake. Além disso, somente os usuários com a função de proprietário atribuído podem adicionar/modificar ACLs.

1. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.

	![Criar diretórios na conta do repositório Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar diretórios na conta do Data Lake")

2. Na folha **Gerenciador de Dados**, clique na raiz de sua conta e, na folha de sua conta, clique no ícone **Acesso**.

	![Definir ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de arquivos do Data Lake")

3. A folha **Acesso** lista o acesso padrão e o acesso personalizado já atribuídos à raiz. Clique no ícone **Adicionar** para adicionar as ACLs de nível personalizado.

	![Listar acesso padrão e personalizado](./media/data-lake-store-secure-data/adl.acl.2.png "Listar acesso padrão e personalizado")

	* **Acesso padrão** é o acesso ao estilo UNIX, no qual você especifica leitura, gravação e execução (rwx) a três classes de usuário distintas: proprietário, grupo e outros.
	* O **acesso personalizado** corresponde às ACLs POSIX e permite a definição de permissões para usuários ou grupos nomeados específicos, e não apenas ao proprietário do arquivo ou grupo.
	
	Para saber mais, consulte [ACLs HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists).

4. Clique no ícone **Adicionar** para abrir a folha **Adicionar Acesso Personalizado**. Nessa folha, clique em **Selecionar Usuário ou Grupo** e na folha **Selecionar Usuário ou Grupo**, procure o grupo de segurança que você criou anteriormente no Active Directory do Azure. Se houver muitos grupos para sua pesquisa, use a caixa de texto na parte superior para filtrar pelo nome do grupo. Clique no grupo que você deseja adicionar e clique em **Selecionar**.

	![Adicionar um grupo](./media/data-lake-store-secure-data/adl.acl.3.png "Adicionar um grupo")

5. Clique em **Selecionar Permissões**, selecione as permissões que você deseja atribuir a esse grupo e clique em **OK**.

	![Atribuir permissões ao grupo](./media/data-lake-store-secure-data/adl.acl.4.png "Atribuir permissões ao grupo")

	As permissões podem ser compreendidas da seguinte maneira:

	* **Leitura** – Se essa permissão for definida em um diretório, ela fornecerá a capacidade de ler os nomes dos arquivos no diretório.
	* **Gravação** – Se essa permissão for definida em um diretório, ela fornecerá a capacidade de modificar as entradas no diretório, como criar um arquivo, excluir um arquivo ou renomear um arquivo.
	* **Execução** - Se essa permissão for definida em um diretório, ela fornece a capacidade de acessar o conteúdo do arquivo no diretório. Além disso, também fornecerá acesso aos metadados do arquivo, se o nome do arquivo for conhecido. No entanto, essa permissão não permite listar os arquivos no diretório, a menos que a permissão **Leitura** também seja definida.

	>[AZURE.NOTE] A permissão de **Leitura + Execução** é necessária para a enumeração de diretórios e costuma ser necessária ao fornecer a um usuário ou grupo o acesso somente leitura aos dados.


6. Na folha **Adicionar Acesso Personalizado**, clique em **OK**. O grupo recém-adicionado, com as permissões associadas, estará listado na folha **Acesso**.

	![Atribuir permissões ao grupo](./media/data-lake-store-secure-data/adl.acl.5.png "Atribuir permissões ao grupo")

	> [AZURE.IMPORTANT] Na versão atual, você pode ter apenas 9 entradas em **Acesso Personalizado**. Se você quiser adicionar mais de nove usuários, crie grupos de segurança, adicione os usuários aos grupos, forneça acesso a esses grupos de segurança para a conta do repositório Data Lake.

7. Se for necessário, também é possível modificar as permissões de acesso depois de adicionar o grupo. Marque ou desmarque a caixa de seleção de cada tipo de permissão (Leitura, Gravação, Execução) com base em seu desejo de remover ou atribuir essa permissão ao grupo de segurança. Clique em **Salvar** para salvar as alterações ou em **Descartar** para desfazer as alterações.

## Definir o intervalo de endereços IP para acesso a dados

O Repositório Azure Data Lake permite bloquear ainda mais o acesso ao seu repositório de dados no nível da rede. Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Uma vez habilitado, somente os clientes com os endereços IP no intervalo definido poderão conectar o repositório.

![Configurações do firewall e acesso IP](./media/data-lake-store-secure-data/firewall-ip-access.png "Configurações do firewall e endereço IP")

## Remova grupos de segurança de uma conta do repositório Azure Data Lake.

Ao remover grupos de segurança das contas do Repositório do Azure Data Lake, você está apenas alterando o acesso às operações de gerenciamento na conta usando o Portal do Azure e as APIs do Gerenciador de Recursos do Azure.

1. Na folha de sua conta do Repositório Data Lake, clique no ícone de usuário.

	![Atribuir grupo de segurança à conta do Azure Data Lake](./media/data-lake-store-secure-data/adl.select.user.icon.png "Atribuir grupo de segurança à conta do Azure Data Lake")

2. Na folha **Usuários**, clique no grupo de segurança que você deseja remover.

	![Grupo de segurança para remoção](./media/data-lake-store-secure-data/adl.add.user.3.png "Grupo de segurança para remoção")

3. Na folha do grupo de segurança, clique em **Remover**.

	![Grupo de segurança removido](./media/data-lake-store-secure-data/adl.remove.group.png "Grupo de segurança removido")

## Remover ACLs do grupo de segurança do sistema de arquivos do repositório Azure Data Lake

Quando você remove as ACLs de grupos de segurança do sistema de arquivos do repositório Azure Data Lake, você está alterando o acesso aos dados no repositório Data Lake.

1. Na folha de sua conta do Repositório Data Lake, clique em **Gerenciador de Dados**.

	![Criar diretórios na conta do Data Lake](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Criar diretórios na conta do Data Lake")

2. Na folha **Gerenciador de Dados**, clique na raiz de sua conta e, na folha de sua conta, clique no ícone **Acesso**.

	![Definir ACLs no sistema de arquivos do Data Lake](./media/data-lake-store-secure-data/adl.acl.1.png "Definir ACLs no sistema de arquivos do Data Lake")

3. Na folha **Acesso**, na seção **Acesso Personalizado**, clique no grupo de segurança que você deseja remover. Na folha **Acesso Personalizado**, clique em **Remover** e, em seguida, clique em **OK**.

	![Atribuir permissões ao grupo](./media/data-lake-store-secure-data/adl.remove.acl.png "Atribuir permissões ao grupo")


## Consulte também

- [Visão geral do repositório Azure Data Lake](data-lake-store-overview.md)
- [Copiar dados de Blobs do Armazenamento do Azure para o Repositório Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Usar a Análise Data Lake do Azure com o Repositório Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usar o Azure HDInsight com o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Introdução ao Repositório Data Lake usando o PowerShell](data-lake-store-get-started-powershell.md)
- [Introdução ao Repositório Data Lake usando o SDK do .NET](data-lake-store-get-started-net-sdk.md)

<!---HONumber=AcomDC_0629_2016-->