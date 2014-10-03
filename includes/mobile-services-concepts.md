## <a name="what-is"></a>O que são os Serviços Móveis

Os Serviços Móveis são uma oferta de serviço do Azure desenvolvida para facilitar a criação de aplicativos móveis altamente funcionais usando o Azure. Os Serviços Móveis reúnem um conjunto de serviços do Azure que habilitam funcionalidades de back-end para seus aplicativos. Os Serviços Móveis fornecem os seguintes recursos do Azure para dar suporte a seus aplicativos:

-   Provisionamento e gerenciamento simples de tabelas para armazenar dados de aplicativo.
-   Integração com os serviços de notificação para enviar notificações por push para seu aplicativo.
-   Integração com provedores de identidade conhecidos para autenticação.
-   Controle granular para autorizar o acesso a tabelas.
-   Lógica de negócios personalizada no servidor.
-   Integração com outros serviços de nuvem.
-   Suporte à capacidade de dimensionar uma instância de serviço móvel.
-   Monitoramento e registro em log de serviços.

Os Serviços Móveis fornecem uma biblioteca de cliente para cada plataforma suportada, o que torna ainda mais fácil de desenvolver aplicativos em sua plataforma que consome Serviços Móveis.

## <a name="concepts"> </a>Conceitos

A seguir, encontram-se importantes recursos e conceitos dos Serviços Móveis:

<!--![1][1]-->

-   **Back-end do .NET:** esse tipo de serviço móvel é implementado como um projeto de API Web ASP.NET. O código de back-end é implementado como controladores WebAPI.
-   **Chave do aplicativo:** um valor exclusivo que é gerado pelos Serviços Móveis distribuído com o seu aplicativo e apresentado em solicitações geradas pelo cliente. Embora seja útil para limitar o acesso ao serviço móvel em clientes aleatórios, esta chave não é segura e não deve ser usada para autenticar os usuários de seu aplicativo.
-   **Token de autenticação:** o token de acesso que é gerado pelos Serviços Móveis depois da autenticação de um usuário.
-   **Provedor de identidade:** serviço externo, como o Facebook, Twitter, Google, Conta da Microsoft ou Active Directory do Azure, que é usado para autenticar os usuários do seu serviço móvel.
-   **Back-end do JavaScript:** esse tipo de serviço móvel é implementado como um serviço node.js. O código de back-end está gravado como JavaScript.
-   **Permissão:** o nível de autenticação mínimo necessário para invocar uma operação de tabela ou API personalizada.
-   **Notificação por push:** mensagem iniciada pelo serviço que é enviada para um usuário ou dispositivo registrado, pelo uso de Hubs de Notificação do Azure.
-   **Escala:** a capacidade de adicionar, por um custo adicional, mais poder de processamento, desempenho e armazenamento, conforme seu aplicativo se torna mais popular.
-   **Trabalho agendado:** código personalizado que é executado em um cronograma predeterminado ou sob demanda.
-   **Script de servidor:** código de lógica de negócios personalizado em um serviço móvel de back-end de JavaScript. Esse código é registrado em uma operação de tabela (ler, inserir, atualizar, excluir), em uma API personalizada ou em um trabalho agendado.
-   **Tabela:** os dados de usuário são armazenados em tabelas em um banco de dados SQL. Você pode criar essas tabelas no Portal de Gerenciamento.

<!-- Images. -->

