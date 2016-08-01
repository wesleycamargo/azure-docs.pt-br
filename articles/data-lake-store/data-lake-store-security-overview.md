<properties 
   pageTitle="Visão geral da segurança no Repositório Data Lake | Microsoft Azure" 
   description="Entender como o Repositório Azure Data Lake é um repositório big data seguro" 
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
   ms.date="07/18/2016"
   ms.author="nitinme"/>

# Segurança no Armazenamento do Azure Data Lake

Muitas empresas estão aproveitando a análise Big Data para obter ideias comerciais para tomar decisões inteligentes. Essas organizações podem ter um ambiente regulamentado e complexo, com um número crescente de usuários diferentes. Portanto, é fundamental que as empresas verifiquem se os dados críticos de negócios estão armazenados com segurança e com o nível certo de acesso concedido aos usuários relevantes. O Repositório Azure Data Lake foi criado com esses requisitos de segurança em mente. Neste artigo, você aprenderá sobre os recursos de segurança do ADLS (Repositório Azure Data Lake), como:

* Autenticação
* Autorização
* Isolamento da rede
* Proteção de dados
* Auditoria

 
## Autenticação e gerenciamento das identidades

Autenticação é o processo pelo qual os usuários comprovem sua identidade ao interagirem com o Repositório Data Lake ou quaisquer serviços que conectam o Repositório Data Lake. Para o Gerenciamento das identidades e a autenticação, o Repositório Data Lake usa o [Azure Active Directory ](../active-directory/active-directory-whatis.md) (AAD), uma solução na nuvem do gerenciamento de identidades e acessos completo que simplifica o gerenciamento de usuários e grupos.

Atualmente, toda assinatura do Azure pode ser associada um Azure Active Directory. Somente os usuários e as identidades de serviço definidos nesse diretório podem acessar seu Repositório Data Lake usando o Portal do Azure, as ferramentas da linha de comando ou os aplicativos cliente criados usando o SDK do Repositório Data Lake. As principais vantagens de usar o Azure Active Directory como um mecanismo de controle de acesso centralizado são:

* Ele simplifica o gerenciamento do ciclo de vida das identidades. A identidade de um usuário ou um serviço (identidade principal do serviço) pode ser criada rapidamente e revogada simplesmente excluindo ou desabilitando a conta no diretório.

* Ele oferece suporte à [autenticação multifator](../multi-factor-authentication/multi-factor-authentication.md), que fornece uma camada adicional de segurança para os logons do usuário e as transações.

* Ele permite que os usuários se autentiquem a partir de qualquer cliente usando o protocolo padrão aberto, como OAuth e OpenID.

* Ele permite a federação com serviços de diretório da empresa e provedores de identidade da nuvem.

## Autenticação e controle de acessos

Assim que um usuário é autenticado pelo AAD para acessar o Repositório Azure Data Lake, os controles de autorização acessam as permissões para o Repositório Data Lake. O Data Lake Store separa autorização das atividades relacionadas à conta e relacionadas a dados da maneira que se segue.

* RBAC ([Controle de acesso baseado em função](../active-directory/role-based-access-control-what-is.md)) fornecido pelo Azure para gerenciamento de contas
* ACL POSIX para acessar dados no repositório.

### Usando o RBAC para o gerenciamento da conta

Há quatro funções básicas definidas por padrão. Elas permitem operações diferentes em uma conta do Repositório Data Lake através do portal, cmdlets do PowerShell e APIs REST. As funções **Proprietário** e **Colaborador** permitem várias funções de administração na conta. Para os usuários que interagem somente com dados, você pode adicioná-los à função **Leitor**.

![Funções RBAC](./media/data-lake-store-security-overview/rbac-roles.png "Funções RBAC")

Observe que embora a finalidade de atribuir essas funções seja para o gerenciamento da conta, dependendo de uma função específica, também pode ter implicação nas permissões do acesso a dados. Para as operações que o usuário pode executar no sistema de arquivos, você precisará usar as Listas de Controle de Acesso (ACLs). Abaixo está um resumo dos direitos de gerenciamento e dos direitos de acesso a dados para essas funções.

| Funções | Direitos de gerenciamento | Direitos de acesso a dados | Explicação |
|--------------------------|---------------------------------|--------------------|-------------|
| Nenhuma função atribuída | Nenhum | Controlado pela ACL | Em tais casos, os usuários não podem usar o Portal do Azure nem os Cmdlets do Azure PowerShell para percorrer o Repositório Data Lake. Esses usuários terão que confiar exclusivamente nas ferramentas da linha de comando. |
| Proprietário | Todos | Todos | O Proprietário é um superusuário, assim, a função Proprietário permite gerenciar tudo e ter acesso total aos dados | 
| Leitor | Somente leitura | Controlado pela ACL | O Leitor pode ver tudo sobre o gerenciamento da conta, como qual usuário é atribuído a qual função, mas não é possível fazer alterações. |
| Colaborador | Tudo, exceto adicionar e remover as funções. | Controlado pela ACL | O Colaborador pode gerenciar outros aspectos de uma conta, como criar/gerenciar alertas, implantar etc. Um Colaborador não pode adicionar nem remover as funções. |
| Administrador de acesso do usuário | Adicionar e remover funções | Controlado pela ACL | Administrador de acesso do usuário permite gerenciar o acesso do usuário às contas. |

Para obter instruções, confira [Atribuir usuários ou grupos de segurança às contas do Azure Data Lake Store.](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### Usando ACLs para as operações nos sistemas de arquivos

O Azure Data Lake Store é um sistema de arquivos hierárquico, como o HDFS, compatível com [ACLs POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists), que permite ler (r), gravar (w) e executar (x) os direitos de acesso aos recursos concedidos para o proprietário, grupo proprietário e outros usuários/grupos. Na Visualização Pública do Repositório Data Lake (versão atual), as ACLs estão habilitadas somente na pasta-raiz, que significa que as ACLs aplicadas na pasta-raiz também são aplicáveis a todas as pastas/arquivos filhos também. Nas futuras versões, você poderá definir as ACLs em qualquer arquivo ou pasta.

É recomendável definir as ACLs para muitos usuários usando [grupos de segurança](../active-directory/active-directory-accessmanagement-manage-groups.md). Agrupe os usuários em um grupo de segurança, em seguida, atribua as ACLs do arquivo e da pasta a esse grupo de segurança. Isso é útil ao fornecer um acesso personalizado, pois há um limite no qual você pode adicionar apenas um máximo de nove entradas como parte do acesso personalizado. Confira [Atribuir usuários ou grupo de segurança como ACLs ao sistema de arquivos do Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions) para obter mais informações sobre como proteger os dados armazenados no Data Lake Store usando grupos de segurança do AAD.

![Listar acesso padrão e personalizado](./media/data-lake-store-security-overview/adl.acl.2.png "Listar acesso padrão e personalizado")

## Isolamento da rede

O Repositório Azure Data Lake permite bloquear ainda mais o acesso ao seu repositório de dados no nível da rede. Você pode habilitar o firewall e definir um intervalo de endereços IP para seus clientes confiáveis. Uma vez habilitado, somente os clientes com os endereços IP no intervalo definido poderão conectar o repositório.

![Configurações do firewall e acesso IP](./media/data-lake-store-security-overview/firewall-ip-access.png "Configurações do firewall e endereço IP")

## Proteção de dados

As organizações querem garantir que seus dados corporativos críticos sejam protegidos durante seu ciclo de vida. Para os dados em trânsito, o Repositório Data Lake usa o padrão da indústria TLS (protocolo Transport Layer Security) para proteger os dados entre o cliente e a Segurança Data Lake.

A proteção dos dados para os dados em repouso também estará disponível nas futuras versões.

## Logs de auditoria e diagnóstico

Você pode usar os logs de auditoria ou diagnóstico dependendo de estar procurando logs para as atividades relacionadas ao gerenciamento ou atividades relacionadas aos dados.

*  As atividades relacionadas ao gerenciamento usam as APIs do Azure Resource Manager e são exibidas no Portal do Azure por meio dos logs de auditoria.
*  As atividades relacionadas aos dados usam as APIs WebHDFS e são exibidas no Portal do Azure por meio dos logs de diagnóstico.

### Logs de auditoria

Para cumprir as normas, as organizações podem exigir trilhas de auditoria adequadas, caso precisem aprofundar-se em um incidente. O Repositório Data Lake tem uma auditoria e monitoramento internos onde registra todas as atividades de gerenciamento da conta.

Para as trilhas de auditoria do gerenciamento da conta, você pode exibir e escolher as colunas de interesse do log e também exportar os logs de auditoria para o repositório do Azure.

![Logs de auditoria](./media/data-lake-store-security-overview/audit-logs.png "Logs de auditoria")

### Logs de diagnóstico

Você pode habilitar as trilhas de auditoria de acesso aos dados no portal do Azure (**Configurações de Diagnóstico**) e fornecer uma conta de armazenamento de Blobs do Azure onde os logs serão armazenados.

![Logs de diagnóstico](./media/data-lake-store-security-overview/diagnostic-logs.png "Logs de diagnóstico")

Depois de habilitar as configurações de diagnóstico, você poderá observar os logs na guia **Logs de Diagnóstico**.

## Resumo

Os clientes corporativos exigem uma plataforma de nuvem da análise de dados que seja segura e fácil de usar. O Repositório Azure Data Lake foi projetado para endereçar esses requisitos com o gerenciamento da identidade e a autenticação através da integração do Azure Active Direction, das ACLs baseadas na autorização, isolamento da rede, criptografia do dados em trânsito e repouso (no futuro) e auditoria.

Se você quiser ver os novos recursos incluídos no Data Lake Store, envie seus comentários para o [Fórum Uservoice](https://feedback.azure.com/forums/327234-data-lake).

## Consulte também

- [Visão geral do repositório Azure Data Lake](data-lake-store-overview.md)
- [Introdução ao Repositório Data Lake](data-lake-store-get-started-portal.md)
- [Proteger dados no Repositório Data Lake](data-lake-store-secure-data.md)

<!---HONumber=AcomDC_0720_2016-->