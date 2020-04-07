# Atividade 2 - Pós DataScience - NoSQL - HBase

1. ativar o ambiente
```
# docker-compose up hbase-server
```

use a flag -d se quiser que rode em segundo plano.

2. abrir a console para consultas ao hbase
```
# docker exec -it pos_nosql_hbase_hbase-server_1 hbase shell
```

## Exercício 1

1. Crie a tabela com 2 famílias de colunas:  a. personal-data b. professional-data 
```
hbase(main):001:0> create_namespace 'italians'
Took 0.6322 seconds
hbase(main):002:0> create 'italians','personal-data','professional-data'
Created table italians
Took 0.8425 seconds
=> Hbase::Table - italians
```

2. Importe o arquivo via linha de comando 
```
# hbase shell /seed/italians.txt
Took 0.0205 seconds
```


## Exercício 2
 
1. Adicione mais 2 italianos mantendo adicionando informações como data de nascimento nas informações pessoais e um atributo de anos de experiência nas informações profissionais
2. Adicione o controle de 5 versões na tabela de dados pessoais
3. Faça 5 alterações em um dos italianos
4. Com o operador get, verifique como o HBase armazenou o histórico
5. Utilize o scan para mostrar apenas o nome e profissão dos italianos
6.  Apague os italianos com row id ímpar
7. Crie um contador de idade 55 para o italiano de row id 5
8. Incremente a idade do italiano em 1 
