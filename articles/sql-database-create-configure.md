<properties linkid="manage-services-how-to-configure-a-sqldb" urlDisplayName="How to configure" pageTitle="How to configure a SQL Database - Azure" metaKeywords="Azure creating SQL Server, Azure configuring SQL Server" description="Learn how to create and configure a logical server using SQL Server in Azure." metaCanonical="" services="sql-database" documentationCenter="" title="How to Create and Configure SQL Database" authors="Lori Clark," solutions="" manager="" editor="" />

<tags ms.service="sql-database" ms.workload="data-management" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="Lori Clark,"></tags>

# <span id="configLogical"></span></a>Como criar e configurar um Banco de Dados SQL

Neste tópico, você aprenderá passo a passo como criar e configurar um servidor lógico. No novo Portal de Gerenciamento do Azure (Visualização), os fluxos de trabalho revisados permitem que você crie um banco de dados primeiro e, em seguida, um servidor.

No entanto, neste tópico, você criará o servidor primeiro. Talvez você prefira essa abordagem se tiver bancos de dados do SQL Server existentes que deseja carregar.

## Sumário

-   [Como: Criar um servidor lógico][Como: Criar um servidor lógico]
-   [Como: Configurar o firewall para o servidor lógico][Como: Configurar o firewall para o servidor lógico]

## <span id="createLogical"></span></a>Como: Criar um servidor lógico

1.  Entre no [Portal de Gerenciamento][Portal de Gerenciamento].

2.  Clique em **Banco de Dados SQL** e depois clique em **SERVIDORES** na página inicial do Banco de Dados SQL.

3.  Clique em **Adicionar** na parte inferior da página.

4.  Nas Configurações do Servidor, digite um nome de administrador como uma palavra sem espaços.

    O Banco de Dados SQL usa a Autenticação do SQL por meio de uma conexão criptografada. Será criado um novo login de autenticação do SQL Server atribuído à função do servidor fixo do sysadmin usando o nome fornecido.

    O login não pode ser um endereço de e-mail, conta de usuário do Windows ou um ID do Windows Live. Declarações ou autenticação do Windows não são suportadas no Banco de Dados SQL.

5.  Forneça uma senha forte com mais de oito caracteres, usando uma combinação de letras maiúsculas e minúsculas e um número ou um símbolo.

6.  Escolha uma região. A Região determina a localização geográfica do servidor. As Regiões não podem ser facilmente alternadas, portanto, escolha uma que faça sentido para este servidor. Escolha um local mais próximo de você. Ao manter seu aplicativo do Azure e o banco de dados na mesma região, você economiza em custo de largura de banda de entrada e em latência de dados.

7.  Certifique-se de manter a opção **Permitir serviços** selecionada para que você possa se conectar ao banco de dados usando o Portal de Gerenciamento do Banco de Dados SQL, dos serviços de armazenamento e de outros serviços no Azure.

8.  Clique na marca de seleção localizada na parte inferior da página quando tiver concluído.

Observe que você não especificou um nome de servidor. O Banco de dados SQL gera automaticamente o nome do servidor para garantir que não existam entradas do DNS duplicadas. O nome do servidor é uma cadeia de 10 caracteres alfanuméricos. Você não pode alterar o nome do seu servidor de Banco de Dados SQL.

Na próxima etapa, você configurará o firewall para que as conexões de aplicativos em execução em sua rede tenham permissão de acesso.

## <span id="configFWLogical"></span></a>Como: Configurar o firewall para o servidor lógico

1.  No [Portal de Gerenciamento][Portal de Gerenciamento], clique em **Banco de Dados SQL**, clique em **Servidores** e depois clique no servidor que você acabou de criar.

2.  Clique em **Configurar**.

3.  Copie o endereço IP atual do cliente. Se você estiver se conectando a partir de uma rede, este é o endereço IP que seu roteador ou o servidor proxy está escutando. O Banco de Dados SQL detecta o endereço IP usado pela conexão atual para que você possa criar uma regra de firewall para aceitar solicitações de conexão nesse dispositivo.

4.  Cole o endereço IP no intervalo de início e fim. Mais tarde, se você encontrar erros de conexão indicando que o intervalo é muito estreito, você poderá editar essa regra para aumentar o intervalo.

    Se os computadores cliente usam endereços IP atribuídos dinamicamente, você deve especificar um intervalo que seja amplo o suficiente para incluir os endereços IP atribuídos aos computadores em sua rede. Inicie com um intervalo estreito e depois expanda-o apenas se for necessário.

5.  Digite um nome para a regra de firewall, como o nome de seu computador ou empresa.

6.  Clique na marca de seleção para salvar a regra.

7.  Clique em **Salvar** na parte inferior da página para concluir a etapa. Se você não vir a opção **Salvar**, atualize a página do navegador.

Agora você tem um servidor lógico e uma regra de firewall que permite conexões de entrada de seu endereço IP e um login de administrador. Na próxima etapa, você alternará para o computador local para concluir as etapas de configuração restantes.

**Observação:** O servidor lógico recém-criado é transitório e será hospedado dinamicamente em servidores físicos em um data center. Se você excluir o servidor, você deve saber antecipadamente que isto se trata de uma ação não recuperável. Certifique-se de fazer backup dos bancos de dados que você posteriormente carregar para o servidor.

  [Como: Criar um servidor lógico]: #createLogical
  [Como: Configurar o firewall para o servidor lógico]: #configFWLogical
  [Portal de Gerenciamento]: http://manage.windowsazure.com
