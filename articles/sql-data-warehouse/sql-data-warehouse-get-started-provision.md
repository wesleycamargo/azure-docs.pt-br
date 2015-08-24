<properties
   pageTitle="Introdução: provisionar um SQL Data Warehouse | Microsoft Azure"
   description="Provisione um SQL Data Warehouse seguindo essas etapas e diretrizes."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/23/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Introdução: provisionar um SQL Data Warehouse #

Este artigo é um guia acelerado para ajudá-lo a provisionar um SQL Data Warehouse no Azure. Ao seguir este guia, você executará essas tarefas:

1. Criar um novo banco de dados do SQL Data Warehouse
2. Configurar um novo servidor lógico
3. Defina uma regra de firewall do Azure para habilitar o acesso para cliente externo

## Avaliação gratuita do Azure ##
Você precisará ter uma assinatura do Azure para concluir as tarefas abaixo. Se você não tiver acesso a uma assinatura do Azure, resolva isso, pois é a primeira etapa!

Você pode obter acesso a uma [avaliação gratuita][] que permite que você experimente os serviços no Azure, incluindo o SQL Data Warehouse.


## Faça logon no portal do Azure ##

Depois que você tiver uma assinatura, poderá fazer logon para o [portal do Azure][]. Vá em frente e entre agora.

Na próxima série de etapas iremos rapidamente criar um novo servidor lógico e criar um novo banco de dados do SQL Data Warehouse.

## Localize o serviço SQL Data Warehouse

A primeira coisa que precisamos fazer é localizar o serviço do SQL Data Warehouse no Portal do Azure.

No canto inferior esquerdo do Portal do Azure está o novo botão. O novo botão é o ponto inicial para a criação de qualquer novo serviço no Azure.

- Agora, clique no botão novo.

### Dados + Armazenamento

Clicar no botão novo abriu todas as categorias de serviço no Azure. O SQL Data Warehouse encontra-se na categoria "Dados + Armazenamento".

- Clique em "Dados + Armazenamento" para analisar e ver os serviços oferecidos pelo Azure para essa categoria.

### SQL Data Warehouse

Como você pode ver, o Azure oferece vários mecanismos de dados e armazenamento. No entanto, este guia de introdução é para o SQL Data Warehouse.

- Vá em frente e selecione o SQL Data Warehouse.

## Configurar o SQL Data Warehouse

Para concluir o processo de provisionamento, simplesmente configure o SQL Data Warehouse.


### Nome do Banco de Dados

A primeira configuração é para nomear o banco de dados.



- Para esse início rápido, nomeie o banco de dados como "MySQLDW".


> [AZURE.NOTE]Quando você cria seu próprio banco de dados, certamente pode nomeá-como desejar. No entanto, ele precisa estar de acordo com os requisitos de nomenclatura básicos do Azure.

### Desempenho

A opção de desempenho é um requisito **importante**. O SQL Data Warehouse fornece sua capacidade escalonável por esse controle deslizante. Você pode aumentar ou diminuir o desempenho a qualquer momento, não apenas quando configurar o data warehouse. Quanto mais você desliza para a direita maior os recursos à sua disposição. Se esses recursos não são mais necessários, então você pode mover o controle deslizante; assim, você poupa nos custos. O SQL Data Warehouse permite alterar o perfil de desempenho sob demanda sem a necessidade de recriar o data warehouse ou mover dados.

- Use o controle deslizante agora para ver como as unidades de data warehouse aumentam à medida que você desliza para a direita e diminuem a medida que você volta para a esquerda.

- Antes de sair dessa etapa, não se esqueça de retornar o controle deslizante para a esquerda. Seu novo data warehouse é, portanto não é necessário muito. Salve os recursos para o restante de sua avaliação!

### Selecionar fonte

Esta opção oferece a opção de inicialização com um banco de dados vazio. Escolha o novo banco de dados como o ponto inicial.

> [AZURE.NOTE]Uma segunda opção também está disponível. Também é permitido criar o banco de dados de um ponto de restauração preexistente; uma opção de restauração.

### Servidor lógico

Seu novo banco de dados do SQL Data Warehouse encontra-se em um servidor lógico. O servidor lógico traz a consistência da configuração para vários bancos de dados e localiza o serviço para um data center do Azure.

As opções que precisam ser definidas são: 1. Nome do servidor 2. Nome do administrador de servidor 3. Senha 4. Local do data center 5. Permissão para os serviços do Azure acessar o servidor

Fique à vontade para definir esses valores como desejar. O Nome do servidor deve ser exclusivo. É uma boa ideia escolher um data center que está perto de você para reduzir a latência de rede. O SQL Data Warehouse também contém recursos avançados que aproveitam os outros serviços do Azure. Portanto, é uma boa ideia deixar a caixa de seleção habilitada para acesso de serviços do Azure.

> [AZURE.NOTE]O SQL Data Warehouse deve usar um Servidor V12. Certifique-se de que essa opção é definida como SIM. O servidor lógico também pode ser compartilhado por bancos de dados SQL do Azure e do SQL Data Warehouse. No entanto, ele deve ser um Servidor V12.

> [AZURE.NOTE]Registre o nome do servidor, o nome do administrador de servidor e a senha em algum lugar e proteja-os. Você precisará dessas informações para se conectar ao banco de dados do SQL Data Warehouse.

### Grupo de recursos
Grupos de recursos são contêineres, projetados para ajudá-lo a gerenciar uma coleção de recursos do Azure.

Para este guia rápido você pode deixar o Grupo de Recursos configurado em seus valores padrão.

Saiba mais sobre [grupos de recursos](../azure-portal/resource-group-portal.md).

### Assinatura
Um único usuário pode ter uma ou mais assinaturas do Azure. Se você tiver mais de uma assinatura associada ao seu logon, em seguida, pode escolher qual assinatura usar.

No entanto, para os fins deste guia, o padrão deve ser suficiente.

Vamos prosseguir e criar o SQL Data Warehouse!

## Criar seu data warehouse ##
Tudo que é necessário para a criação de seu data warehouse é clicar no botão Criar.

Parabéns! Você criou seu primeiro banco de dados do SQL Data Warehouse.

Você agora deve voltar para a página inicial do [Portal do Azure][]. Observe que seu banco de dados do SQL Data Warehouse foi adicionado à página


Neste momento, ninguém pode acessar o banco de dados do SQL Data Warehouse. Para manter tudo seguro por padrão, o banco de dados ainda não foi configurado para os clientes poderem acessá-lo.

Portanto, a última etapa no processo de provisionamento é configurar o serviço para acesso externo.

## Configurar o firewall do Azure ##

Para configurar o firewall do Azure pela primeira vez, execute as seguintes etapas:

1. Clique em Procurar no painel de navegação à esquerda

2. Escolha Servidores SQL

3. Selecione seu Servidor lógico do SQL

4. Escolha Configurações

5. Clique no firewall

6. Definir regra de firewall

    Há algumas coisas a fazer aqui. Elas são:-nomear sua regra de firewall - fornecer um intervalo de IP se você não tiver um endereço IP estático

    > [AZURE.NOTE]O intervalo de endereços IP do cliente que você precisa incluir é o endereço IP externo ou público. Para localizar seu endereço IP externo você pode usar um número de sites como <a href="http://www.whatismyip.com" target="\_blank">www.whatismyip.com</a>

7. Salvar sua regra de firewall


Agora que você configurou o firewall poderá fazer conexões da área de trabalho do Azure SQL Data Warehouse que você acabou de criar.

## Próximas etapas

Agora, o Serviço SQLDW foi provisionado com êxito para aprendermos a usá-lo.

As próximas etapas são, portanto, para saber como:

1. [Conectar e consultar][] o data warehouse.
2. Carregar [dados da amostra].

<!--Image references-->


<!-- Articles -->
[Conectar e consultar]: sql-data-warehouse-get-started-connect-query.md
[dados da amostra]: ./sql-data-warehouse-get-started-load-samples.md

<!--External links-->
[avaliação gratuita]: https://azure.microsoft.com/pt-br/pricing/free-trial/
[portal do Azure]: https://portal.azure.com/

<!---HONumber=August15_HO7-->