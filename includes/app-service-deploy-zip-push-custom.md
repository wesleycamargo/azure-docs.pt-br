## <a name="deployment-customization"></a>Personalização da implantação

O processo de implantação presume que o arquivo .zip em que você efetua push contém um aplicativo pronto para execução. Por padrão, nenhuma personalização é executada. Para habilitar os mesmos processos de compilação que você obtém com a integração contínua, adicione o seguinte para as configurações do aplicativo:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Quando você usar a implantação por push do .zip, essa configuração é **falsa** por padrão. O padrão é **verdadeiro** para implantações de integração contínua. Quando definido como **verdadeiras**, as configurações de implantação são usadas durante a implantação. Você pode configurar essas configurações como configurações de aplicativo ou em um arquivo de configuração de .deployment localizado na raiz do seu arquivo .zip. Para obter mais informações, consulte [Repositório e configurações relacionadas à implantação](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) na referência de implantação.