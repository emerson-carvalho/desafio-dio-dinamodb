# desafio-dio-dinamodb
Repositório destinado ao desafio DIO "Boas Práticas com o DynamoDB"

### Serviços utilizados:
  - Amazon DynamoDB
  - Amazon CLI para execução em linha de comando

### Comandos para execução do experimento:


- Criar uma tabela:

```
aws dynamodb create-table \
    --table-name Heroes \
    --attribute-definitions \
        AttributeName=Hero,AttributeType=S \
        AttributeName=Power,AttributeType=S \
    --key-schema \
        AttributeName=Hero,KeyType=HASH \
        AttributeName=Power,KeyType=RANGE \
    --provisioned-throughput \
        ReadCapacityUnits=10,WriteCapacityUnits=5
```

- Inserir um item:

```
aws dynamodb put-item \
    --table-name Heroes \
    --item file://hero.json \
```

- Inserir múltiplos itens:

```
aws dynamodb batch-write-item \
    --request-items file://heroes.json
```

- Criar um index global secundário baseado no nome do herói:

```
aws dynamodb update-table \
    --table-name Heroes \
    --attribute-definitions AttributeName=Hero,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Hero-index\",\"KeySchema\":[{\"AttributeName\":\"Hero\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no poder:

```
aws dynamodb update-table \
    --table-name Heroes \
    --attribute-definitions AttributeName=Power,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"Power-index\",\"KeySchema\":[{\"AttributeName\":\"Power\",\"KeyType\":\"HASH\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no nome do herói e no poder:

```
aws dynamodb update-table \
    --table-name Heroes \
    --attribute-definitions\
        AttributeName=Hero,AttributeType=S \
        AttributeName=Power,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"HeroPower-index\",\"KeySchema\":[{\"AttributeName\":\"Hero\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"Power\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Criar um index global secundário baseado no herói e no nome pessoal:

```
aws dynamodb update-table \
    --table-name Heroes \
    --attribute-definitions\
        AttributeName=Hero,AttributeType=S \
        AttributeName=PersonalName,AttributeType=S \
    --global-secondary-index-updates \
        "[{\"Create\":{\"IndexName\": \"HeroPersonalName-index\",\"KeySchema\":[{\"AttributeName\":\"Hero\",\"KeyType\":\"HASH\"}, {\"AttributeName\":\"PersonalName\",\"KeyType\":\"RANGE\"}], \
        \"ProvisionedThroughput\": {\"ReadCapacityUnits\": 10, \"WriteCapacityUnits\": 5      },\"Projection\":{\"ProjectionType\":\"ALL\"}}}]"
```

- Pesquisar por herói:

```
aws dynamodb query \
    --table-name Heroes \
    --key-condition-expression "Hero = :hero" \
    --expression-attribute-values  '{":hero":{"S":"Deku"}}'
```

- Pesquisar por herói e poder:

```
aws dynamodb query \
    --table-name Heroes \
    --key-condition-expression "Hero = :hero and Power = :power" \
    --expression-attribute-values file://keyconditions.json
```

- Pesquisa pelo index secundário baseado no herói:

```
aws dynamodb query \
    --table-name Heroes \
    --index-name Hero-index \
    --key-condition-expression "Hero = :hero" \
    --expression-attribute-values  '{":hero":{"S":"All Might"}}'
```

- Pesquisar pelo index secundário baseado no poder:

```
aws dynamodb query \
    --table-name Heroes \
	--index-name Power-index \
    --key-condition-expression "Power = :power" \
    --expression-attribute-values  '{":power":{"S":"One for All"}}'
```

- Pesquisa pelo index secundário baseado no herói e no poder:

```
aws dynamodb query \
    --table-name Heroes \
    --index-name HeroPower-index \
    --key-condition-expression "Hero = :v_hero and Power = :v_power" \
    --expression-attribute-values  '{":v_hero":{"S":"Shoto"},":v_power":{"S":"Meio Frio, Meio Quente"} }'
```

- Pesquisa pelo index secundário baseado no herói e no nome pessoal:

```
aws dynamodb query \
    --table-name Heroes \
    --index-name HeroPersonalName-index \
    --key-condition-expression "Hero = :v_hero and PersonalName = :v_name" \
    --expression-attribute-values  '{":v_hero":{"S":"Deku"},":v_name":{"S":"Izuku Midoriya"} }'
```
