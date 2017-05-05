Você pode aplicar marcas para os recursos do Azure para organizá-los logicamente por categorias. Cada marca consiste em uma chave e um valor. Por exemplo, você pode aplicar a chave de "Ambiente" e o valor "Produção" a todos os recursos na produção. Sem essa marca, você pode ter dificuldade para identificar se um recurso é destinado para desenvolvimento, teste ou produção. No entanto, "Ambiente" e "Produção" são apenas exemplos. Você define as chaves e valores que façam mais sentido para organizar sua assinatura.

Após a aplicação de marcas, você pode recuperar todos os recursos em sua assinatura com essa chave de marca e o valor. Marcas que você possa recuperar recursos relacionados que residem em diferentes grupos de recursos. Esta abordagem será útil quando você precisar organizar os recursos de gerenciamento ou de cobrança.

As seguintes limitações se aplicam a marcas:

* Cada recurso ou grupo de recursos pode ter um máximo de 15 pares chave/valor de marca. Essa limitação se aplica somente a marcas aplicadas diretamente ao grupo de recursos ou recurso. Um grupo de recursos pode conter muitos recursos que possuem 15 pares de chave/valor de marca. 
* O nome da marca é limitado a 512 caracteres.
* O valor da marca é limitado a 256 caracteres. 
* Marcas aplicadas ao grupo de recursos não são herdadas pelos recursos desse grupo de recursos. 

Se você tiver mais de 15 valores que você precisa associar a um recurso, use uma cadeia de caracteres JSON para o valor da marca. A cadeia de caracteres JSON pode conter vários valores que são aplicados a uma única chave de marca. Um exemplo de atribuição de uma cadeia de caracteres JSON para a chave de marca é mostrado neste artigo.