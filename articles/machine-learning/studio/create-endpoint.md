---
Título: Criando pontos de extremidade de serviço Web – titleSuffix: Descrição do Azure Machine Learning Studio: Criando pontos de extremidade de serviço Web no Azure Machine Learning. Cada ponto de extremidade no serviço Web é tratado, limitado e gerenciado de forma independente.
serviços: machine-learning ms.service: machine-learning ms.component: studio ms.topic: artigo

autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 04/10/2016
---
# <a name="creating-endpoints-for-deployed-azure-machine-learning-studio-web-services"></a>Criando pontos de extremidade para serviços Web implantados do Azure Machine Learning Studio
> [!NOTE]
>  Este tópico descreve técnicas aplicáveis a um serviço Web do Machine Learning **clássico**.
> 
> 

Quando você cria serviços Web que você vende antecipadamente aos seus clientes, você precisa fornecer modelos qualificados para cada cliente que ainda esteja vinculado ao experimento a partir do qual o serviço Web foi criado. Além disso, todas as atualizações do experimento devem ser aplicadas de forma seletiva a um ponto de extremidade sem substituir as personalizações.

Para isso, o Azure Machine Learning Studio permite que você crie vários pontos de extremidade para um serviço da Web implantado. Cada ponto de extremidade no serviço Web é tratado, limitado e gerenciado de forma independente. Cada ponto de extremidade é uma única URL e chave de autorização que você pode distribuir aos seus clientes.



## <a name="adding-endpoints-to-a-web-service"></a>Adicionando pontos de extremidade a um serviço Web
Há duas maneiras de adicionar um ponto de extremidade a um serviço Web.

* Programaticamente
* Por meio do portal de serviços Web do Azure Machine Learning

Quando o ponto de extremidade é criado, você pode consumi-lo por meio de APIs síncronas, APIs de lote e planilhas do Excel. Além de adicionar pontos de extremidade com essa interface do usuário, você também pode usar as APIs de gerenciamento do ponto de extremidade programaticamente para adicionar pontos de extremidade.

> [!NOTE]
> Caso você tenha adicionado mais pontos de extremidade ao serviço Web, você não poderá excluir o ponto de extremidade padrão.
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>Adicionando um ponto de extremidade programaticamente
Você pode adicionar um ponto de extremidade ao serviço Web programaticamente usando o código de exemplo [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>Adicionando um ponto de extremidade usando o portal de serviços Web do Azure Machine Learning
1. No Estúdio de Machine Learning, clique em Serviços Web na coluna de navegação à esquerda.
2. Na parte inferior do painel do serviço Web, clique em **Gerenciar pontos de extremidade**. O portal de serviços Web de Azure Machine Learning abre a página de pontos de extremidade do serviço Web.
3. Clique em **Novo**.
4. Digite um nome e uma descrição para o novo ponto de extremidade. Os nomes dos pontos de extremidade devem ter 24 caracteres ou menos e devem ser compostos de letras minúsculas ou números. Selecione o nível de log e se os dados de exemplo estão habilitados. Para obter mais informações sobre Registro em Log, veja [Habilitar registro em log para serviços Web de Machine Learning](web-services-logging.md).

## <a name="next-steps"></a>Próximas etapas
[Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md).

