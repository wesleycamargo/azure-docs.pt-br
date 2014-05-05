<properties linkid="manage-services-how-to-scale-a-sqldb" urlDisplayName="How to scale" pageTitle="Como dimensionar um banco de dados SQL - Azure" metaKeywords="" description="Saiba mais sobre as opções para dimensionar o seu banco de dados SQL no Azure." metaCanonical="" services="sql-database" documentationCenter="" title="Como dimensionar uma solução de banco de dados SQL" authors="" solutions="" manager="" editor="" />





<h1 id="scale">Como dimensionar uma solução de Banco de Dados SQL</h1>


No Azure, a escalabilidade do banco de dados é sinônimo de dimensionamento, na qual uma carga de trabalho é redistribuída entre vários servidores de commodity em um data center. O dimensionamento é uma estratégia para lidar com problemas de desempenho ou capacidade de dados. Um banco de dados muito grande que esteja em uma trajetória de alto crescimento acabará exigindo uma estratégia de dimensionamento, seja ele acessado por alguns ou vários usuários.

O dimensionamento no Azure é mais facilmente obtido por meio da federação. A federação do Banco de Dados SQL baseia-se na fragmentação horizontal, na qual uma ou mais tabelas são divididas por linha e repartidas entre vários membros da federação. 

A federação não é a única resposta para cada problema de escalabilidade. Às vezes, as características dos seus requisitos de dados ou de aplicativo apontam para abordagens mais simples. A lista a seguir apresenta possíveis soluções em ordem de complexidade.

##Aumente o tamanho do banco de dados

Os bancos de dados são criados com um tamanho fixo sujeito a um máximo imposto por cada edição. Para a Web edition, você pode aumentar um banco de dados até um máximo de 5 gigabytes. Para a Business Edition, o tamanho máximo do banco de dados é de 150 gigabytes. A forma mais óbvia para aumentar a capacidade de dados é alterar a edição e o tamanho máximo:

     ALTER DATABASE school MODIFY (EDITION = 'Business', MAXSIZE=10GB);

##Usar vários bancos de dados e alocar usuários

Em cenários limitados, você pode criar cópias de um banco de dados e, em seguida, alocar logins e usuários em cada banco de dados. Antes que a federação fosse uma opção, essa era uma abordagem comum para redistribuir uma carga de trabalho. Essa abordagem é viável para bancos de dados usados a curto prazo e que depois são mesclados posteriormente em um banco de dados primário que é mantido no local e para soluções que oferecem dados somente leitura.

##Usar federações

As federações no Banco de Dados SQL são usadas para atingir uma maior escalabilidade e desempenho. Uma ou mais tabelas em um banco de dados são divididas por linha e repartidas em vários bancos de dados (membros da Federação). Esse tipo de repartição horizontal é conhecida como 'fragmentação'. Os principais cenários em que isso é útil são aqueles nos quais você precisa obter escala, desempenho ou para gerenciar a capacidade. 

As federações são suportadas no Business Edition. Para obter mais informações, consulte [Federações no Banco de Dados SQL][] e [Tutorial de Federações do Banco de Dados SQL - DBA][].

##Considere outras formas de armazenamento

Lembre-se de que o Azure oferece suporte a várias formas de armazenamento de dados, incluindo o armazenamento de tabelas e de blobs. Se você não precisar de recursos relacionais, o armazenamento de tabelas ou de blobs pode ser mais econômico. 

[Federações no Banco de dados SQL]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh597452.aspx
[Tutorial de Federações do Banco de Dados SQL – DBA]: http://msdn.microsoft.com/pt-br/library/windowsazure/hh778416.aspx

