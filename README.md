# DynamoDB Projeto da Dio
O Amazon DynamoDB, banco de dados NoSQL (Não Relacional) altamente escalável e gerenciado pela AWS (Amazon Web Services). Ele é projetado para armazenar e processar grandes volumes de dados com baixa latência e alta disponibilidade, tornando-se uma ótima escolha para aplicações de alta performance e escalabilidade.

## O Projeto
    O projeto consiste em persistir os dados de um artista (Beyoncé) que possui vários álbuns e os álbuns possuem várias músicas.

## Comandos
* [Criar uma tabela](#✅-criar-uma-tabela)
* [Inserir um item](#✅-inserir-um-item)
* [Inserir múltiplos itens](#✅-inserir-múltiplos-itens)
* [Criar um index global secundário baeado no título do álbum](#✅-criar-um-index-global-secundário-baeado-no-título-do-álbum)
* [Criar um index global secundário baseado no nome do artista e no título do álbum](#✅-criar-um-index-global-secundário-baseado-no-nome-do-artista-e-no-título-do-álbum)
* [Criar um index global secundário baseado no título da música e no ano](#✅-criar-um-index-global-secundário-baseado-no-título-da-música-e-no-ano)
* [Pesquisar item por artista](#✅-pesquisar-item-por-artista)
* [Pesquisar item por artista e título da música](#✅-pesquisar-item-por-artista-e-título-da-música)
* [Pesquisa pelo index secundário baseado no título do álbum](#✅-pesquisa-pelo-index-secundário-baseado-no-título-do-álbum)
* [Pesquisa pelo index secundário baseado no nome do artista e no título do álbum](#✅-pesquisa-pelo-index-secundário-baseado-no-nome-do-artista-e-no-título-do-álbum)
* [Pesquisa pelo index secundário baseado no título da música e no ano](#✅-pesquisa-pelo-index-secundário-baseado-no-título-da-música-e-no-ano)

### Serviço utilizado
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando




## <p align="center"> Comandos utilizados durante a apresentação do projeto para execução do experimento:

### <p align="center">✅ Criar uma tabela
```
aws dynamodb create-table \
    --table-name Music \
    --attribute-definitions \
        AttributeName=Artist,AttributeType=S \
        AttributeName=SongTitle,AttributeType=S \
    --key-schema \
        AttributeName=Artist,KeyType=HASH \
        AttributeName=SongTitle,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```
### <p align="center">✅ Inserir um item
```
aws dynamodb put-item \
    --table-name Music \
    --item file://itemmusic.json \
```
### <p align="center">✅ Inserir múltiplos itens
```
aws dynamodb batch-write-item \
    --request-items file://batchmusic.json
```
### <p align="center">✅ Criar um index global secundário baeado no título do álbum
```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"AlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
### <p align="center">✅ Criar um index global secundário baseado no nome do artista e no título do álbum
```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=Artist,AttributeType=S \
        AttributeName=AlbumTitle,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"ArtistAlbumTitle-index\",\"KeySchema\":[{\"AttributeName\":\"Artist\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"AlbumTitle\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
### <p align="center">✅ Criar um index global secundário baseado no título da música e no ano
```
aws dynamodb update-table \
    --table-name Music \
    --attribute-definitions\
        AttributeName=SongTitle,AttributeType=S \
        AttributeName=SongYear,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"SongTitleYear-index\",\"KeySchema\":[{\"AttributeName\":\"SongTitle\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"SongYear\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```
### <p align="center">✅ Pesquisar item por artista
```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist" \
    --expression-attribute-values  '{":artist":{"S":"Beyoncé"}}'
```
### <p align="center">✅ Pesquisar item por artista e título da música
```
aws dynamodb query \
    --table-name Music \
    --key-condition-expression "Artist = :artist and SongTitle = :title" \
    --expression-attribute-values file://keyconditions.json
```
### <p align="center">✅ Pesquisa pelo index secundário baseado no título do álbum
```
aws dynamodb query \
    --table-name Music \
    --index-name AlbumTitle-index \
    --key-condition-expression "AlbumTitle = :name" \
    --expression-attribute-values  '{":name":{"S":"CUFF IT"}}'
```
### <p align="center">✅ Pesquisa pelo index secundário baseado no nome do artista e no título do álbum
```
aws dynamodb query \
    --table-name Music \
    --index-name ArtistAlbumTitle-index \
    --key-condition-expression "Artist = :v_artist and AlbumTitle = :v_title" \
    --expression-attribute-values  '{":v_artist":{"S":"Beyoncé"},":v_title":{"S":"Formation"} }'
```
### <p align="center">✅ Pesquisa pelo index secundário baseado no título da música e no ano
```
aws dynamodb query \
    --table-name Music \
    --index-name SongTitleYear-index \
    --key-condition-expression "SongTitle = :v_song and SongYear = :v_year" \
    --expression-attribute-values  '{":v_song":{"S":"Drunk in love"},":v_year":{"S":"2013"} }'
```

