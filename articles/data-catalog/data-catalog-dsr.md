---
title: "Fontes de dados com suporte do Catálogo de Dados do Azure | Microsoft Docs"
description: "Especificação das fontes de dados com suporte no momento."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: jstevens
editor: 
tags: 
ms.assetid: fd4345ca-2ed8-4c5e-9c4b-f954be2fc9f9
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 03/14/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: d68ed62022834c9417d820a7a64e30b47f71a1bd
ms.contentlocale: pt-br
ms.lasthandoff: 03/31/2017


---

# <a name="azure-data-catalog-supported-data-sources"></a>Fontes de dados com suporte do Catálogo de Dados do Azure

Você pode publicar os metadados usando uma API pública, uma ferramenta de registro com um clique ou inserindo manualmente as informações de forma direta no portal da Web do Catálogo de Dados. A grade a seguir resume todas as fontes com suporte hoje do catálogo e os recursos de publicação para cada uma.  Também estão listadas as ferramentas de dados externas que cada fonte pode inicializar a partir de nossa experiência "aberta" do portal. A segunda grade tem uma especificação mais técnica de cada propriedade de conexão das fontes de dados.


## <a name="list-of-supported-data-sources"></a>Lista das fontes de dados com suporte

<table>

    <tr>
       <td><b>Objeto da Fonte de Dados</b></td>
       <td><b>API</b></td>
       <td><b>Entrada Manual</b></td>
       <td><b>Ferramenta de Registro</b></td>
       <td><b>Ferramentas Abertas</b></td>
       <td><b>Observações</b></td>
    </tr>

    <tr>
      <td>Diretório de Armazenamento do Azure Data Lake</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do Repositório Azure Data Lake</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Blob de Armazenamento do Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório de Armazenamento do Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela de armazenamento do Azure</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Diretório do HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do HDFS</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição de Hive</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Modo de Exibição do MySQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do Oracle Database</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do Oracle Database</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Outros (Ativo Genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SQL Data Warehouse</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensão do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Medida do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório do SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Navegador</font></td>
      <td><font size=2>Somente os servidores de modo nativo. Não há suporte para o modo do SharePoint.</font></td>
    </tr>

    <tr>
      <td>Tabela do SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SQL Server</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do Teradata</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SAP Hana</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela Db2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do Db2</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do Sistema de Arquivos</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Diretório do FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do FTP</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Relatório do HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Ponto de extremidade do HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Arquivo do HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Conjunto de entidades do Odata</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Função do Odata</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela do PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do PostgreSQL</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição do SAP Hana</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objeto do SalesForce</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Lista do SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>
  
    <tr>
      <td>Coleção do Azure DocumentDB</td>
      <td>✓</td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabela ODBC Genérica</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓ </td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Exibição ODBC Genérica</td>
      <td>✓ </td>
      <td>✓ </td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Se você precisar de suporte para outras fontes, envie uma solicitação de recurso usando o [Fórum do Catálogo de Dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br>
<br>
## <a name="data-source-reference-specification"></a>Especificação de referência da fonte de dados
> [!NOTE]
> A coluna "Estrutura de DSL" na tabela a seguir lista apenas as propriedades de conexão do recipiente de propriedades "address" que são usadas pelo Catálogo de Dados do Azure. Ou seja, o recipiente de propriedades "address" pode conter outras propriedades de conexão da fonte de dados que o Catálogo de Dados do Azure persiste, mas não usa.
<table>
    <tr>
       <td><b>Tipo de Fonte</b></td>
       <td><b>Tipo de Ativo</b></td>
       <td><b>Tipos de Objeto</b></td>
       <td><b>Estrutura DSR<b></td>
    </tr>
    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Contêiner</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Repositório Azure Data Lake</td>
      <td>Tabela</td>
      <td>Diretório, arquivo</td>
      <td>
        <font size=2> protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>
        <font size=2> protocolo: azure-blobs <br>autenticação: {azure-access-key} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contêiner </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Blog, diretório</td>
      <td>
        <font size=2> protocolo: azure-blobs <br>autenticação: {azure-access-key} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; contêiner <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nome </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Contêiner</td>
      <td>Contêiner</td>
      <td>
        <font size=2> protocolo: azure-tables <br>autenticação: {azure-access-key} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta </font>
      </td>
    </tr>
    <tr>
      <td>Armazenamento do Azure</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2> protocolo: azure-tables <br>autenticação: {azure-access-key} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; domínio <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; conta <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; nome </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contêiner</td>
      <td>Cluster Virtual</td>
      <td>
        <font size=2> protocolo: cosmos <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabela</td>
      <td>Fluxo, conjunto de fluxo, exibição</td>
      <td>
        <font size=2> protocolo: cosmos <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Relatório</td>
      <td>Relatório, painel</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: db2 <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: db2 <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de Arquivos</td>
      <td>Tabela</td>
      <td>Arquivo</td>
      <td>
        <font size=2> protocolo: file <br>autenticação: {nenhuma, básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; caminho </font>
      </td>
    </tr>
    <tr>
      <td>FTP</td>
      <td>Tabela</td>
      <td>Diretório, arquivo</td>
      <td>
        <font size=2> protocolo: ftp <br>autenticação: {nenhuma, básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Contêiner</td>
      <td>HDInsight</td>
      <td>
        <font size=2> protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de Arquivos Distribuído Hadoop</td>
      <td>Tabela</td>
      <td>Diretório, arquivo</td>
      <td>
        <font size=2> protocolo: webhdfs <br>autenticação: {básica, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: hive <br>autenticação: {hdinsight, básica, nome de usuário, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: hive <br>autenticação: {hdinsight, básica, nome de usuário, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>connectionProperties: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Relatório</td>
      <td>Relatório, painel</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>HTTP</td>
      <td>Tabela</td>
      <td>Ponto de Extremidade, arquivo</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: mysql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: mysql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>Contêiner</td>
      <td>Contêiner da entidade</td>
      <td>
        <font size=2> protocolo: odata <br>autenticação: {nenhuma, básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Odata</td>
      <td>Tabela</td>
      <td>Conjunto de entidades, função</td>
      <td>
        <font size=2> protocolo: odata <br>autenticação: {nenhuma, básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; recurso </font>
      </td>
    </tr>
    <tr>
      <td>Banco de dados Oracle</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: oracle <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Banco de dados Oracle</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: oracle <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: postgresql <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: postgresql <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contêiner</td>
      <td>Site</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Relatório</td>
      <td>Relatório, painel</td>
      <td>
        <font size=2> protocolo: http <br>autenticação: {nenhuma, básica, windows, oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Power Query</td>
      <td>Tabela</td>
      <td>Mashup de Dados</td>
      <td>Protocolo 
        <font size=2>: power-query <br>autenticação: {oauth} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabela</td>
      <td>Objeto</td>
      <td>
        <font size=2> protocolo: salesforce-com <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; loginServer <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; classe <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; itemName </font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: sap-hana-sql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor </font>
      </td>
    </tr>
    <tr>
      <td>Hana SAP</td>
      <td>Tabela</td>
      <td>Visualizar</td>
      <td>
        <font size=2> protocolo: sap-hana-sql <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabela</td>
      <td>Listar</td>
      <td>
        <font size=2> protocolo: sharepoint-list <br>autenticação: {básica, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Command</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>TableValuedFunction</td>
      <td>Função com valor de tabela</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>SQL Data Warehouse</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Command</td>
      <td>Procedimento armazenado</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>TableValuedFunction</td>
      <td>Função com valor de tabela</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: tds <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Medida} </font>
      </td>
    </tr>
    <tr>
      <td>Multidimensional do SQL Server Analysis Services</td>
      <td>Tabela</td>
      <td>Dimensão</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Dimensão} </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Medida</td>
      <td>Medida</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Medida} </font>
      </td>
    </tr>
    <tr>
      <td>Tabela do SQL Server Analysis Services</td>
      <td>Tabela</td>
      <td>Tabela</td>
      <td>
        <font size=2> protocolo: analysis-services <br>autenticação: {windows, básica, anônima, nenhuma} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objectType: {Tabela} </font>
      </td>
    </tr>
    <tr>
      <td>Serviços de Relatório do SQL Server</td>
      <td>Contêiner</td>
      <td>Servidor</td>
      <td>
        <font size=2> protocolo: reporting-services <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Serviços de Relatório do SQL Server</td>
      <td>Relatório</td>
      <td>Relatório</td>
      <td>
        <font size=2> protocolo: reporting-services <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; caminho <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocolo: teradata <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>
        <font size=2> protocolo: teradata <br>autenticação: {protocolo, windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; servidor <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Serviços de Dados Mestre do SQL Server</td>
      <td>Contêiner</td>
      <td>Modelo</td>
      <td>
        <font size="2"> protocolo: mssql-mds <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão </font>
      </td>
    </tr>
    <tr>
      <td>Serviços de Dados Mestre do SQL Server</td>
      <td>Tabela</td>
      <td>Entidade</td>
      <td>
        <font size="2"> protocolo: mssql-mds <br>autenticação: {windows} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; modelo <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; versão <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; entidade </font>
      </td>
    </tr>
    <tr>
      <td>Banco de Dados de Documentos do Azure</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>
        <font size=2> protocol: document-db <br>autenticação: {azure-access-key} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>Banco de Dados de Documentos do Azure</td>
      <td>Coleção</td>
      <td>Coleção</td>
      <td>
        <font size=2> protocol: document-db <br>autenticação: {azure-access-key} <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; url <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>Coleção &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </font>
      </td>
    </tr>
    <tr>
      <td>ODBC Genérico</td>
      <td>Contêiner</td>
      <td>Banco de dados</td>
      <td>Protocolo 
        <font size=2>: odbc <br>autenticação: {básica, windows} <br>endereço: <br>Opções &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados </font>
      </td>
    </tr>
    <tr>
      <td>ODBC Genérico</td>
      <td>Tabela</td>
      <td>Tabela, exibição</td>
      <td>Protocolo 
        <font size=2>: odbc <br>autenticação: {básica, windows} <br>endereço: <br>Opções &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; banco de dados <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; objeto <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Outro (Nenhum dos anteriores)</td>
      <td>\*</td>
      <td>\*</td>
      <td>
        <font size=2> protocolo: generic-asset <br>endereço: <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; assetId </font>
      </td>
    </tr>
</table>

