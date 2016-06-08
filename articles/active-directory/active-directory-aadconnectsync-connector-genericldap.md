<properties
   pageTitle="Sincronização do Azure AD Connect: conector LDAP genérico | Microsoft Azure"
   description="Este artigo descreve como configurar o Conector LDAP genérico da Microsoft."
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

# Referência técnica do Conector LDAP genérico

Este artigo descreve o Conector LDAP genérico. O artigo se aplica aos seguintes produtos:

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   É necessário usar o hotfix 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2 o conector está disponível para download do [Centro de Download da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Ao fazer referência ao IETF RFCs, este documento usa o formato (RFC [Número de RFC]/[seção no documento RFC]), por exemplo, (RFC 4512/4.3). Saiba mais em http://tools.ietf.org/html/rfc4500 (substitua 4500 pelo número RFC correto).

## Visão geral do Conector LDAP genérico

O Conector LDAP genérico permite que você integre o serviço de sincronização com o servidor LDAP v3.

Determinados elementos de operações e esquema, como aqueles necessários para executar a importação delta, não são especificados noas IETF RFCs. Para essas operações, apenas os diretórios LDAP especificados explicitamente recebem suporte.

Partindo de um ponto de vista detalhado, os seguintes recursos têm suporte na versão atual do conector:

Recurso | Suporte
--- | --- |
Fonte de dados conectada | O Conector recebe suporte com todos os servidores LDAP v3 (compatíveis com RFC 4510). Ele foi testado com as seguintes opções: <li>AD LDS (Microsoft Active Directory Lightweight Directory Services)</li><li>AD GC (Catálogo Global do Microsoft Active Directory)</li><li>389 Directory Server</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Open DJ</li><li>Open DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (antiga Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>**Diretórios conhecidos sem suporte:** <li>AD DS (Serviços de Domínio do Microsoft Active Directory) [Em seu lugar, use o Conector do Active Directory interno]</li><li>OID (Oracle Internet Directory)</li>
Cenários | <li>Gerenciamento de ciclo de vida do objeto</li><li>Gerenciamento de grupos</li><li>Gerenciamento de senhas</li>
Operações |As seguintes operações têm suporte em todos os diretórios LDAP: <li>Importação total</li><li>Exportação</li>As seguintes operações têm suporte apenas em diretórios especificados:<li>Importação delta</li><li>Definir senha, Alterar senha</li>
Esquema | <li>O esquema é detectado do esquema LDAP (RFC3673 e RFC4512/4.2)</li><li>Suporta classes estruturais, classes auxiliares e classe de objeto extensibleObject (RFC4512/4.3)</li>

### Suporte de gerenciamento de importação delta e de senha

Diretórios com suporte para gerenciamento de importação delta e de senha:

- Microsoft Active Directory Lightweight Directory Services (AD LDS)
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição de senha
- Catálogo Global do Microsoft Active Directory (AD GC)
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição de senha
- Directory Server 389
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
- Apache Directory Server
    - Não oferece suporte para importação delta, pois esse diretório não tem um log de alteração persistente
    - Oferece suporte para definição de senha
- IBM Tivoli DS
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
- Isode Directory
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
- Novell eDirectory e NetIQ eDirectory
    - Oferece suporte para operações de adição, atualização e renomeação para importação delta
    - Não oferece suporte para operações de exclusão para importação delta
    - Oferece suporte para definição e alteração de senha
- Open DJ
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
- Open DS
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
- Open LDAP (openldap.org)
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição de senha
    - Não oferece suporte para alteração de senha
- Oracle (antiga Sun) Directory Server Enterprise Edition
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
- RadiantOne Virtual Directory Server (VDS)
    - Deve usar a versão 7.1.1 ou superior
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha
-  Sun One Directory Server
    - Oferece suporte para todas as operações de importação delta
    - Oferece suporte para definição e alteração de senha

### Pré-requisitos

Para usar o conector, verifique se você tem os seguintes itens no servidor de sincronização, além de qualquer hotfix mencionado acima:

- Microsoft .NET 4.5.2 Framework ou posterior

### Como detectar o servidor LDAP

O Conector depende de diversas técnicas para detectar e identificar o servidor LDAP. O Conector usa Root DSE para localizar o nome e a versão do fornecedor e inspeciona o esquema para localizar objetos exclusivos e atributos presentes em determinados servidores LDAP. Esses dados, se forem encontrados, serão usados para preencher previamente as opções de configuração no Conector.

### Permissões da fonte de dados conectada

Para executar operações de importação e exportação nos objetos no diretório conectado, a conta do conector deve ter permissões suficientes. O conector precisará de permissões de gravação para conseguir exportar, e de permissões de leitura para importar. Configuração das permissões é executada dentro das experiências de gerenciamento da própria pasta de destino.

### Portas e protocolos

O conector usará o número de porta especificado na configuração, cujo padrão é 389 para LDAP e 636 para LDAP.

Para LDAPS, você deve usar SSL 3.0 ou TLS. Não há suporte para SSL 2.0 e não é possível ativá-lo.

### Recursos e controles necessários

Os seguintes controles/recursos de LDAP devem estar disponíveis no servidor LDAP para que o conector funcione corretamente:

- 1\.3.6.1.4.1.4203.1.5.3 Filtros Verdadeiro/Falso

Frequentemente, o filtro Verdadeiro/Falso não é indicado como compatível com diretórios LDAP e pode aparecer na **Página Global** em **Recursos Obrigatórios Não Encontrados**. Ele é usado para criar filtros **OU** em consultas LDAP, por exemplo, ao importar vários tipos de objeto. Se você puder importar mais de um tipo de objeto, seu servidor LDAP oferecerá suporte a isso.

Se você usar um diretório no qual um identificador exclusivo é a âncora, o seguinte também deverá estar disponível (confira a seção [Configurar âncoras](#configure-anchors) posteriormente neste artigo para saber mais):

- 1\.3.6.1.4.1.4203.1.5.1 Todos os atributos operacionais

Se o diretório tiver mais objetos do que uma chamada para o diretório consegue comportar, recomendamos a paginação. Para a paginação funcionar, uma das seguintes opções será necessária:

**Opção 1:**

- 1\.2.840.113556.1.4.319 pagedResultsControl

**Opção 2:**

- 2\.16.840.1.113730.3.4.9 VLVControl
- 1\.2.840.113556.1.4.473 SortControl

Se as duas opções estiverem habilitadas na configuração do conector, pagedResultsControl será usada.

- 1\.2.840.113556.1.4.417 ShowDeletedControl

ShowDeletedControl só é usado com o método de importação delta USNChanged para conseguir ver objetos excluídos.

O conector tentará detectar se as opções estão presentes no servidor. Se não for possível detectar as opções, um aviso será apresentado na página Global das propriedades do conector. Nem todos os servidores LDAP apresentarão todos os controles/recursos para os quais oferecem suporte e, mesmo que esse aviso esteja presente, o conector poderá funcionar sem problemas.

### Importação delta

A importação delta só estará disponível quando um diretório de suporte tiver sido detectado. Os métodos a seguir são usados no momento:

- LDAP Accesslog. Confira [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- LDAP Changelog. Confira [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- TimeStamp. Para Novell/NetIQ eDirectory, o Conector usará a última data/hora para obter objetos criados e atualizados. Novell/NetIQ eDirectory não fornece um meio equivalente de recuperar objetos excluídos. Essa opção também pode ser usada se nenhum outro método de importação delta estiver ativo no servidor LDAP. Essa opção não permitirá a importação de objetos excluídos.
- USNChanged. Confira: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### Sem suporte

Não há suporte para os seguintes recursos LDAP:

- Referências de LDAP entre servidores (RFC 4511/4.1.10)

## Criar um novo conector

Para criar um conector LDAP genérico, em **Serviço de Sincronização** selecione **Agente de Gerenciamento** e **Criar**. Escolha o Conector **LDAP Genérico (Microsoft)**.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### Conectividade

Na página Conectividade, especifique as informações de Host, Porta e Associação. Dependendo da Associação selecionada, outras informações poderão ser fornecidas nas seções a seguir.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- A configuração de Tempo Limite de Conexão é usada apenas para a primeira conexão com o servidor durante a detecção do esquema.
- Se a Associação for Anônima, nenhum nome de usuário/senha ou certificado será usado.
- Para outras associações, insira informações em nome de usuário/senha ou escolha um certificado.
- Se você estiver usando o Kerberos para autenticar, forneça também o Realm/Domínio do usuário.

A caixa de texto **aliases de atributo** é usada para atributos definidos no esquema com a sintaxe RFC4522. Não é possível detectá-los durante a detecção de esquema, e o Conector precisa de ajuda para identificá-los. Por exemplo, é necessário inserir o seguinte na caixa de aliases de atributo a fim de identificar corretamente o atributo userCertificate como um atributo binário:

`userCertificate;binary`

Veja a seguir um exemplo de como isso seria:

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Marque a caixa de seleção **incluir atributos operacionais no esquema** para incluir também os atributos criados pelo servidor. Entre esses atributos estão, por exemplo, quando o objeto foi criado e hora da última atualização.

Escolha **incluir atributos extensíveis no esquema** se forem usados objetos extensíveis (RFC4512/4.3). Além disso, habilitar essa opção permitirá que cada atributo seja usado em todos os objetos. A seleção dessa opção tornará o esquema muito grande. Portanto, a menos que o diretório conectado esteja usando esse recurso, a recomendação é manter a opção desmarcada.

### Parâmetros Globais

Na página Parâmetros Globais, configure o DN para o log de alterações delta e os recursos de LDAP adicionais. A página será preenchida previamente com as informações fornecidas pelo servidor LDAP.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

A seção superior mostra informações fornecidas pelo próprio servidor, como o nome do servidor. O Conector também verificará se os controles obrigatórios estão presentes no Roots DSE. Se não estiverem listados, um aviso será apresentado. Alguns diretórios LDAP não listarão todos os recursos em Root DSE, e é possível que o Conector funcione sem problemas mesmo que exista um aviso.

As caixas de seleção dos **controles com suporte** controlam o comportamento de determinadas operações:

- Com a opção de exclusão de árvore marcada, uma hierarquia será excluída com uma chamada LDAP. Com a opção de exclusão de árvore desmarcada, o conector realizará uma exclusão recursiva, se for necessário.
- Com a opção de resultados paginados marcada, o conector realizará importações paginadas com o tamanho especificado nas etapas de execução.
- VLVControl e SortControl são alternativas ao pagedResultsControl para leitura de dados do diretório LDAP.
- Se todas as três opções (pagedResultsControl, VLVControl e SortControl) estiverem desmarcadas, o Conector importará todos os objetos em uma única operação, que poderá falhar se o diretório for grande.
- ShowDeletedControl é usado somente quando o método de Importação delta é USNChanged.

O DN de log de alterações é o contexto de nomenclatura usado pelo log de alterações delta, por exemplo, **cn=changelog**. Você precisa especificar esse valor para poder executar a importação delta.

Veja a seguir uma lista de DNs de log de alteração padrão:

Diretório | Log de alterações delta
--- | ---
Microsoft AD LDS e AD GC | Detectado automaticamente. USNChanged.
Apache Directory Server | Não disponível.
Directory 389 | Log de alterações. Valor padrão a ser usado: **cn=changelog**
IBM Tivoli DS | Log de alterações. Valor padrão a ser usado: **cn=changelog**
Isode Directory | Log de alterações. Valor padrão a ser usado: **cn=changelog**
Novell/NetIQ eDirectory | Não disponível. TimeStamp. O Conector usará a data/hora da última atualização para obter os registros adicionados e atualizados.
Open DJ/DS | Log de alterações. Valor padrão a ser usado: **cn=changelog**
Open LDAP | Log de acesso. Valor padrão a ser usado: **cn=accesslog**
Oracle DSEE | Log de alterações. Valor padrão a ser usado: **cn=changelog**
RadiantOne VDS | Diretório virtual. Depende do diretório conectado ao VDS.
Sun One Directory Server | Log de alterações. Valor padrão a ser usado: **cn=changelog**

O atributo de senha é o nome do atributo que o Conector deve usar para definir a senha nas operações de alteração e definição de senha. Isso está definido por padrão como **userPassword**, mas pode ser alterado se for necessário para um determinado sistema LDAP.

Na lista de partições adicionais, é possível adicionar outros namespaces não detectados automaticamente. Isso pode ser usado, por exemplo, se vários servidores formarem um cluster lógico para importação simultânea. Assim como o Active Directory pode ter vários domínios em uma floresta, mas todos compartilharem um esquema, o mesmo pode ser simulado inserindo os namespaces adicionais nessa caixa. Cada namespace pode importar de servidores diferentes e ainda ser configurada na página Configurar Partições e Hierarquias. Use Ctrl + Enter para obter uma nova linha.

### Configurar a hierarquia de provisionamento

Esta página é usada para mapear o componente de DN, por exemplo, OU, para o tipo de objeto que deve ser provisionado, por exemplo, organizationalUnit.

![Hierarquia de provisionamento](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Ao configurar a hierarquia de provisionamento, você pode configurar o Conector para criar automaticamente uma estrutura quando for necessário. Por exemplo, se houver um namespace dc=contoso,dc=com e um novo objeto cn=Joe, ou=Seattle, c=US, dc=contoso, dc=com será provisionado, e o Conector poderá criar um novo objeto do tipo país para EUA e um organizationalUnit para Seattle, caso ainda não estejam presentes no diretório.

### Configurar partições e hierarquias

Na página de partições e hierarquias, escolha todos os namespaces com objetos que você planeja importar e exportar.

![Partições](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Para cada namespace também é possível definir as configurações de conectividade que poderiam substituir os valores especificados na tela Conectividade. Se esses valores forem deixados com seus valores padrão em branco, as informações na Conectividade serão usadas.

Também é possível selecionar quais contêineres e UOs o Conector deve importar e exportar.

### Configurar Âncoras

Essa página sempre têm um valor pré-configurado e não pode ser alterada. Se o fornecedor e a versão do servidor tiverem sido identificados, será possível preencher isso com um atributo imutável, por exemplo, o GUID de um objeto. Se não tiver sido detectado ou não tiver um atributo imutável conhecido, o conector usará o dn (nome distinto) como a âncora.

![âncoras](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Veja a seguir uma lista de servidores LDAP e a âncora usada:

Diretório | Atributo de âncora
--- | ---
Microsoft AD LDS e AD GC | objectGUID
Directory Server 389 | dn
Apache Directory | dn
IBM Tivoli DS | dn
Isode Directory | dn
Novell/NetIQ eDirectory | GUID
Open DJ/DS | dn
Open LDAP | dn
Oracle ODSEE | dn
RadiantOne VDS | dn
Sun One Directory Server | dn

## Outras observações

Esta seção fornece informações sobre aspectos específicos a esse Conector, ou que por outros motivos sejam importante conhecer.

### Importação delta

A marca d'água delta em Open LDAP é data/hora UTC. Por esse motivo, os relógios entre o Serviço de Sincronização FIM e o Open LDAP devem ser sincronizados. Caso contrário, algumas entradas no log de alteração delta podem ser omitidas.

Para Novell eDirectory a importação delta não detectará quaisquer exclusões de objeto. Por esse motivo, é necessário executar periodicamente uma importação completa para localizar todos os objetos excluídos.

Para diretórios com um log de alteração delta com base na data/hora, é altamente recomendável executar periodicamente uma importação completa para localizar diferenças entre o servidor LDAP e o que está atualmente no espaço do conector.

## Solucionar problemas

-	Para saber mais sobre como habilitar o registro em log para solucionar problemas do conector, confira [How to Enable ETW Tracing for Connectors](http://go.microsoft.com/fwlink/?LinkId=335731).

<!---HONumber=AcomDC_0525_2016-->