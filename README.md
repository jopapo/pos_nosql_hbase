# Atividade 2 - Pós DataScience - NoSQL - HBase

1. ativar o ambiente
```
$ docker-compose up hbase-server
```
use a flag -d se quiser que rode em segundo plano.

2. abrir a console para consultas ao hbase
```
$ docker-compose run hbase-server
```
ou:
```
$ docker-compose exec hbase-server hbase shell
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

2. Importe o arquivo via linha de comando: 
```
$ docker-compose exec hbase-server hbase shell /seed/italians.txt
Took 0.0205 seconds
```



## Exercício 2
 
1. Adicione mais 2 italianos mantendo adicionando informações como data de nascimento nas informações pessoais e um atributo de anos de experiência nas informações profissionais
```
hbase(main):002:0> put 'italians', '11', 'personal-data:name', 'João Paulo Poffo'
Took 0.0633 seconds
hbase(main):003:0> put 'italians', '11', 'personal-data:city', 'Blumenau'
Took 0.0043 seconds
hbase(main):004:0> put 'italians', '11', 'professional-data:role', 'Arquiteto de Software'
Took 0.0087 seconds
hbase(main):005:0> put 'italians', '11', 'professional-data:salary', '50.000'
Took 0.0074 seconds
hbase(main):008:0> put 'italians', '12', 'personal-data:name', 'Isaco Pandini'
Took 0.0060 seconds
hbase(main):009:0> put 'italians', '12', 'personal-data:city', 'Ascurra'
Took 0.0048 seconds
hbase(main):010:0> put 'italians', '12', 'professional-data:role', 'Agricultor'
Took 0.0071 seconds
hbase(main):011:0> put 'italians', '12', 'professional-data:salary', '100.000'
Took 0.0090 seconds
```

2. Adicione o controle de 5 versões na tabela de dados pessoais
```
hbase(main):003:0> alter 'italians', { NAME=>'personal-data', VERSIONS=>5 }
Updating all regions with the new schema...
1/1 regions updated.
Done.
Took 2.2117 seconds
```

3. Faça 5 alterações em um dos italianos
```
hbase(main):024:0> put 'italians', '11', 'personal-data:city', 'Gaspar'
Took 0.0064 seconds
hbase(main):025:0> put 'italians', '11', 'personal-data:city', 'Indaial'
Took 0.0048 seconds
hbase(main):026:0> put 'italians', '11', 'personal-data:city', 'Timbó'
Took 0.0058 seconds
hbase(main):027:0> put 'italians', '11', 'personal-data:city', 'Jaraguá'
Took 0.0038 seconds
hbase(main):028:0> put 'italians', '11', 'personal-data:city', 'Blumenau'
Took 0.0053 seconds
```

4. Com o operador get, verifique como o HBase armazenou o histórico
```
hbase(main):010:0> get 'italians', '11', { COLUMN=>'personal-data:city', VERSIONS=>5 }
COLUMN                    CELL
 personal-data:city       timestamp=1586299757579, value=Blumenau
 personal-data:city       timestamp=1586299745718, value=Jaragu\xC3\xA1
 personal-data:city       timestamp=1586299742790, value=Timb\xC3\xB3
 personal-data:city       timestamp=1586299739488, value=Indaial
 personal-data:city       timestamp=1586299735217, value=Gaspar
1 row(s)
Took 0.0430 seconds
```

5. Utilize o scan para mostrar apenas o nome e profissão dos italianos
```
hbase(main):020:0> scan 'italians', { COLUMNS => ['personal-data:name','professional-data:role']
}
ROW                       COLUMN+CELL
 1                        column=personal-data:name, timestamp=1586297227651, value=Paolo Sorrentino
 1                        column=professional-data:role, timestamp=1586297227713, value=Gestao Comercial
 10                       column=personal-data:name, timestamp=1586297227982, value=Giovanna Caputo
 10                       column=professional-data:role, timestamp=1586297227994, value=Comunicacao Institucional
 11                       column=personal-data:name, timestamp=1586298784137, value=Jo\xC3\xA3o Paulo Poffo
 11                       column=professional-data:role, timestamp=1586298743172, value=Arquiteto de Software
 12                       column=personal-data:name, timestamp=1586298791905, value=Isaco Pandini
 12                       column=professional-data:role, timestamp=1586298820518, value=Agricultor
 2                        column=personal-data:name, timestamp=1586297227724, value=Domenico Barbieri
 2                        column=professional-data:role, timestamp=1586297227737, value=Psicopedagogia
 3                        column=personal-data:name, timestamp=1586297227750, value=Maria Parisi
 3                        column=professional-data:role, timestamp=1586297227764, value=Optometria
 4                        column=personal-data:name, timestamp=1586297227786, value=Silvia Gallo
 4                        column=professional-data:role, timestamp=1586297227803, value=Engenharia Industrial Madeireira
 5                        column=personal-data:name, timestamp=1586297227824, value=Rosa Donati
 5                        column=professional-data:role, timestamp=1586297227843, value=Mecatronica Industrial
 6                        column=personal-data:name, timestamp=1586297227856, value=Simone Lombardo
 6                        column=professional-data:role, timestamp=1586297227875, value=Biotecnologia e Bioquimica
 7                        column=personal-data:name, timestamp=1586297227891, value=Barbara Ferretti
 7                        column=professional-data:role, timestamp=1586297227906, value=Libras
 8                        column=personal-data:name, timestamp=1586297227921, value=Simone Ferrara
 8                        column=professional-data:role, timestamp=1586297227940, value=Engenharia de Minas
 9                        column=personal-data:name, timestamp=1586297227958, value=Vincenzo Giordano
 9                        column=professional-data:role, timestamp=1586297227970, value=Marketing
12 row(s)
Took 0.0440 seconds
```

6. Apague os italianos com row id ímpar
```
hbase(main):025:0> deleteall 'italians', '3'
Took 0.0135 seconds
hbase(main):026:0> deleteall 'italians', '7'
Took 0.0050 seconds
```
_Mudei para excluir apenas 2 ímpares para não conflitar com os próximos exercícios._

7. Crie um contador de idade 55 para o italiano de row id 5
```
hbase(main):028:0> put 'italians', '5', 'personal-data:age', '00000055'
Took 0.0050 seconds
```

8. Incremente a idade do italiano em 1 
```
hbase(main):047:0> incr 'italians', '5', 'personal-data:age'
COUNTER VALUE = 3472328296227681590
Took 0.0058 seconds
hbase(main):050:0> get 'italians', '5'
COLUMN                    CELL
 personal-data:age        timestamp=1586300529129, value=00000056
 personal-data:city       timestamp=1586297227836, value=Palermo
 personal-data:name       timestamp=1586297227824, value=Rosa Donati
 professional-data:role   timestamp=1586297227843, value=Mecatronica Industrial
 professional-data:salary timestamp=1586297227850, value=7037
1 row(s)
Took 0.0232 seconds
```


## Exercício 3

Alterado o script de geração de dados automático [italian-people-generator.js](italian-people-generator.js) pra gerar uma saída padrão pro HBase. Ao executar conforme exemplo abaixo, ele gera um volume de dados [seed/italians-generated-data.txt](seed/italians-generated-data.txt).
```
$ node italian-people-generator.js > seed/italians-generated-data.txt
```

Importado o arquivo via:
```
$ docker-compose exec hbase-server hbase shell /seed/italians-generated-data.txt
```

PS: A parte abaixo não foi possível fazer por causa de dificuldades em executar o MapReduce com todas as questões de compatibilidade do HBase. Segundo o professor, essa parte é opcional e então foi deixada de fora.

* Utilizando o MapReduce, faça um relatório ou mais de um indicando as seguintes informações: 

1. Quantidade de gatos e cachorros na amostra 
2. Média de gatos e cachorros na população 
3. Quantidade de pais e mães  
4. Média de pais e mães  
5. Média de frutas e filmes por italiano 
6. Salário médio dos italianos  
