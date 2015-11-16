<properties 
                pageTitle="Noções básicas sobre o acesso aos recursos do Azure." 
                description="Este tópico explica os conceitos sobre como usar os administradores de assinatura para controlar o acesso aos recursos no portal do Azure completo." 
                services="active-directory" 
                documentationCenter="" 
                authors="markusvi" 
                manager="stevenpo" 
                editor=""/>

<tags 
                ms.service="active-directory" 
                ms.workload="identity" 
                ms.tgt_pltfrm="na" 
                ms.devlang="na" 
                ms.topic="article" 
                ms.date="10/30/2015" 
                ms.author="markusvi"/>


# Noções básicas sobre o acesso aos recursos do Azure


> [AZURE.NOTE]Este tópico explica os conceitos sobre como usar os administradores de assinatura para controlar o acesso aos recursos no portal do Azure completo. Como alternativa, o portal de visualização do Azure fornece [O controle de acesso baseado em funções](role-based-access-control-configure.md) para que os recursos do Azure possam ser gerenciados com mais precisão.

Em outubro de 2013, o Portal de Gerenciamento do Azure e o Gerenciamento de Serviço de APIs foram integrados com o Active Directory do Azure para definir as bases visando melhorar a experiência do usuário em gerenciar o acesso aos recursos do Azure. O Active Directory do Azure já fornece ótimos recursos como o gerenciamento de usuários, sincronização de diretórios local, multi-factor authentication e controle de acesso do aplicativo. Naturalmente, eles também devem ser disponibilizados para gerenciar os recursos do Azure globalmente.

O controle de acesso no Azure parte de uma perspectiva de cobrança. O proprietário de uma conta do Azure, acessada pela visitação ao [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions), é o Administrador da Conta (AA). As assinaturas são um contêiner para cobrança, mas também atuam como um limite de segurança: cada assinatura tem um Administrador de Serviços (SA) que pode adicionar, remover e modificar recursos do Azure nessa assinatura usando o [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/). O SA padrão de uma nova assinatura é o AA, mas o AA pode alterar o SA no Centro de contas do Azure.
 
<br><br>![Contas do Azure][1]

As assinaturas também têm uma associação com um diretório. O diretório define um conjunto de usuários. Estes podem ser usuários do trabalho ou da escola que criou o diretório ou podem ser usuários externos (ou seja, Contas da Microsoft). As assinaturas são acessíveis por um subconjunto desses usuários do diretório que foram atribuídos como um serviço SA (Administrador) ou CA (Coadministrador); a única exceção é que, por motivos de herança, Contas da Microsoft (anteriormente chamadas de Windows Live ID) podem ser atribuídas como SA ou CA sem estarem presentes no diretório.

<br><br>![Controle de acesso no Azure][2]

 
A funcionalidade no Portal de gerenciamento do Azure permite que os SAs que se conectaram usando uma Conta da Microsoft para alterar o diretório que uma assinatura está associada usando o comando **Editar Diretório** na página **Assinaturas** nas **Configurações**. Observe que esta operação tem implicações no controle de acesso da assinatura.



> [AZURE.NOTE]O comando **Editar Diretório** no Portal de Gerenciamento do Azure não está disponível para os usuários que se conectaram usando uma conta corporativa ou de estudante, pois essas contas podem se conectar apenas ao diretório ao qual pertencem.

<br><br>![Fluxo de Logon de Usuário Simples][3]

No caso mais simples, uma organização (como por exemplo, Contoso) fará a cobrança e o controle de acesso no mesmo conjunto de assinaturas. Ou seja, o diretório está associado às assinaturas pertencentes a uma única conta do Azure. Após o logon bem-sucedido no portal de Gerenciamento do Azure, os usuários veem duas coleções de recursos (representadas em laranja na ilustração anterior):


- Diretórios onde sua conta de usuário existe (originados ou adicionados como uma entidade externa). Observe que o diretório usado para logon não é relevante para esse cálculo, portanto os diretórios serão sempre mostrados independentemente de onde você fez logon.

- Recursos que fazem parte das assinaturas que estão associados com o diretório usado para fazer logon E que o usuário pode acessar (seja um SA ou CA).


<br><br>![Usuário com Várias Assinaturas e Diretórios][4]


Os usuários com assinaturas em vários diretórios têm a capacidade de alternar o contexto atual do Portal de Gerenciamento do Azure usando o filtro de assinatura. Internamente, isso resulta em um logon separado para um diretório diferente, mas isso é feito diretamente usando logon único (SSO).

Operações como mover recursos entre as assinaturas podem ser mais difíceis como resultado desta visão única de diretório de assinaturas. Para executar a transferência de recursos, talvez seja necessário primeiro usar o comando **Editar Diretório** na página de assinaturas em **Configurações** para associar as assinaturas ao mesmo diretório.



<!--Image references-->
[1]: ./media/active-directory-understanding-resource-access/IC707931.png
[2]: ./media/active-directory-understanding-resource-access/IC707932.png
[3]: ./media/active-directory-understanding-resource-access/IC707933.png
[4]: ./media/active-directory-understanding-resource-access/IC707934.png

<!---HONumber=Nov15_HO2-->