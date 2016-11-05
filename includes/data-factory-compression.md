### <a name="compression-support"></a>Suporte à compactação
O processamento de grandes conjuntos de dados pode causar afunilamentos de E/S e de rede. Portanto, os dados compactados em repositórios não apenas aceleram a transferência de dados pela rede e economizam espaço em disco, mas também oferecem aprimoramentos consideráveis de desempenho no processamento de Big Data. No momento, a compactação tem suporte para repositórios de dados baseados em arquivo, por exemplo, Blob do Azure ou o Sistema de Arquivos Local.  

> [!NOTE]
> Não há suporte para configurações de compactação de dados no **AvroFormat**, **OrcFormat** ou **ParquetFormat**. 
> 
> 

Para especificar a compactação de um conjunto de dados, use a propriedade **compactação** no conjunto de dados JSON, como no exemplo a seguir:   

    {  
        "name": "AzureBlobDataSet",  
        "properties": {  
            "availability": {  
                "frequency": "Day",  
                "interval": 1  
            },  
            "type": "AzureBlob",  
            "linkedServiceName": "StorageLinkedService",  
            "typeProperties": {  
                "fileName": "pagecounts.csv.gz",  
                "folderPath": "compression/file/",  
                "compression": {  
                    "type": "GZip",  
                    "level": "Optimal"  
                }  
            }  
        }  
    }  

A seção **compactação** tem duas propriedades:  

* **Tipo:** o codec de compactação, que pode ser **GZIP**, **Deflate** ou **BZIP2**.  
* **Nível:** a taxa de compactação, que pode ser **Ideal** ou **Mais rápida**. 
  
  * **Mais rápida:** a operação de compactação deve ser concluída o mais rápido possível, mesmo se o arquivo resultante não for compactado da maneira ideal. 
  * **Ideal**: a operação de compactação deve ser concluída da maneira ideal, mesmo se a operação demorar mais tempo para ser concluída. 
    
    Para saber mais, veja o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) . 

Vamos supor que o exemplo de conjunto de dados acima seja usado como a saída de uma atividade de cópia. A atividade de cópia compacta os dados de saída com o codec GZIP usando a melhor taxa e, em seguida, grava os dados compactados em um arquivo chamado pagecounts.csv.gz no Armazenamento de Blobs do Azure.   

Quando você especifica a propriedade de compactação em um conjunto de dados de entrada JSON, o pipeline pode ler os dados compactados da origem e, quando você especifica a propriedade em um conjunto de dados de saída JSON, a atividade de cópia pode gravar dados compactados no destino. Aqui estão alguns exemplos de cenários: 

* Ler dados compactados em GZIP de um blob do Azure, descompactá-los e gravar os dados resultantes em um banco de dados do SQL Azure. Nesse caso, você define o conjunto de dados de entrada do Blob do Azure com a propriedade de compactação JSON. 
* Ler dados de um arquivo de texto sem formatação do Sistema de arquivos local, compactá-los usando o formato GZip e gravar os dados compactados em um blob do Azure. Nesse caso, você define um conjunto de dados do Blob do Azure com a propriedade de compactação JSON.  
* Ler dados compactados em GZIP de um blob do Azure, descompactá-los, compactá-los usando BZIP2 e gravar os dados de resultado em um blob do Azure. Nesse caso, você define o conjunto de dados de entrada do Blob do Azure com o tipo de compactação definido como GZIP, e o conjunto de dados de saída com o tipo de compactação definido como BZIP2.   

<!--HONumber=Oct16_HO2-->


