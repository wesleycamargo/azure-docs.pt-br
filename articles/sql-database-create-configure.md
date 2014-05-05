<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="Como configurar" pageTitle="Como configurar um Banco de Dados SQL – Azure" metaKeywords="Criação do SQL Server no Azure, Configuração do SQL Server no Azure" description="Saiba como criar e configurar um servidor lógico usando o SQL Server no Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Como criar e configurar um Banco de Dados SQL" authors="" solutions="" manager="" editor="" />





<h1><a id="configLogical"></a>Como criar e configurar um Banco de Dados SQL</h1>

Neste tópico, você aprenderá passo a passo como criar e configurar um servidor lógico. No novo Portal de Gerenciamento do Azure (Visualização), os fluxos de trabalho revisados permitem que você crie um banco de dados primeiro e, em seguida, um servidor. 

No entanto, neste tópico, você criará o servidor primeiro. Talvez você prefira essa abordagem se tiver bancos de dados do SQL Server existentes que deseja carregar.

##Sumário##
* [Como criar um servidor lógico](#createLogical)
* [Como configurar o firewall para o servidor lógico](#configFWLogical)

<h2><a id="createLogical"></a>Como criar um servidor lógico</h2>

1. Faça logon [Portal de Gerenciamento](http://manage.windowsazure.com).

2. Clique em **Banco de Dados SQL** e depois clique em **SERVIDORES** na página inicial do Banco de Dados SQL.

4. Clique em **Adicionar** na parte inferior da página. 

5. Nas Configurações do Servidor, digite um nome de administrador como uma palavra sem espaços. 

	O Banco de Dados SQL usa a Autenticação do SQL por meio de uma conexão criptografada. Será criado um novo login de autenticação do SQL Server atribuído à função do servidor fixo do sysadmin usando o nome fornecido. 

	O login não pode ser um endereço de e-mail, conta de usuário do Windows ou um ID do Windows Live. Declarações ou autenticação do Windows não são suportadas no Banco de Dados SQL.

6. Forneça uma senha forte que tenha mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

7. Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

8. Certifique-se de manter a opção **Permitir serviços** selecionada para que você possa se conectar ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, dos serviços de armazenamento e de outros serviços no Azure. 

9. Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

Observe que você não especificou um nome de servidor. O Banco de dados SQL gera automaticamente o nome do servidor para garantir que não existam entradas do DNS duplicadas. O nome do servidor é uma cadeia de 10 caracteres alfanuméricos. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Na próxima etapa, você configurará o firewall para que as conexões de aplicativos em execução em sua rede tenham permissão de acesso.

<h2><a id="configFWLogical"></a>Como configurar o firewall para o servidor lógico</h2>

1. No [Portal de Gerenciamento](http://manage.windowsazure.com), clique em **Banco de Dados SQL**, clique em **Servidores** e depois clique no servidor que você acabou de criar.

2. Clique em **Configurar**. 

3. Copie o endereço IP atual do cliente. Se você estiver se conectando a partir de uma rede, este é o endereço IP que seu roteador ou o servidor proxy está escutando. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual para que você possa criar uma regra de firewall para aceitar solicitações de conexão nesse dispositivo. 

4. Cole o endereço IP no intervalo de início e fim. Mais tarde, se você encontrar erros de conexão indicando que o intervalo é muito estreito, você poderá editar essa regra para aumentar o intervalo.

	Se os computadores cliente usam endereços IP atribuídos dinamicamente, você deve especificar um intervalo que seja amplo o suficiente para incluir os endereços IP atribuídos aos computadores em sua rede. Inicie com um intervalo estreito e depois expanda-o apenas se for necessário.

5. Digite um nome para a regra de firewall, como o nome de seu computador ou empresa.

6. Clique na marca de seleção para salvar a regra.

7. Clique em **Salvar** na parte inferior da página para concluir a etapa. Se você não vir a opção **Salvar**, atualize a página do navegador.

Agora você tem um servidor lógico e uma regra de firewall que permite conexões de entrada de seu endereço IP e um login de administrador. Na próxima etapa, você alternará para o computador local para concluir as etapas de configuração restantes.

**Nota:** O servidor lógico recém-criado é transitório e será hospedado dinamicamente em servidores físicos em um data center. Se você excluir o servidor, você deve saber antecipadamente que isto se trata de uma ação não recuperável. Certifique-se de fazer backup dos bancos de dados que você posteriormente carregar para o servidor. 

