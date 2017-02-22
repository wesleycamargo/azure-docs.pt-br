## <a name="incremental-and-complete-deployments"></a>Implantações incrementais e completas
Ao implantar os recursos, especifique que a implantação é uma atualização incremental ou uma atualização completa. Por padrão, o Gerenciador de Recursos trata as implantações como atualizações incrementais para o grupo de recursos. Com a implantação incremental, o Gerenciador de Recursos:

* **deixa inalterados** os recursos existentes no grupo de recursos, mas que não foram especificados no modelo
* **adiciona** os recursos especificados no modelo, mas que não existem no grupo de recursos 
* **não reprovisiona** os recursos existentes no grupo de recursos na mesma condição definida no modelo
* **reprovisiona** recursos existentes com configurações atualizadas no modelo

Com a implantação completa, o Gerenciador de Recursos:

* **exclui** os recursos existentes no grupo de recursos, mas que não foram especificados no modelo
* **adiciona** os recursos especificados no modelo, mas que não existem no grupo de recursos 
* **não reprovisiona** os recursos existentes no grupo de recursos na mesma condição definida no modelo
* **reprovisiona** recursos existentes com configurações atualizadas no modelo

Especifique o tipo de implantação por meio da propriedade **Mode** .



<!--HONumber=Nov16_HO3-->


