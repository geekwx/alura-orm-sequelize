---
data: 23/03/2021
title: Aprendendo Sequelize
---


# SEQUELIZE

Sequelize é um orm para manipular a conexão entre o nodejs e o banco de dados sql. Facilitando e não sendo necessario utilizar os comandos sql tradicionais. 


#### Iniciando Sequelize

> **npx sequelize-cli init**

Isso irar criar as pastas na estrutura dele. 

* Migrations
* Models
* Seeders
* Config

Vc ainda pode startar as pasta isoladamente usando

> **npx sequelize-cli init:models**


#### arquivo de localização 

Para personalizar o sequelize vc pode cirar um arquivos de apontamento da localização das pasta anterioes, para isso vc criar um arquivo chamado **.sequelizerc**
e dentro dele 



```javascript

const path = require('path');

module.exports = {
  'config': path.resolve('./api', 'config/config.json'),
  'models-path': path.resolve('./api', 'models'),
  'seeders-path': path.resolve('./api', 'seeders'),
  'migrations-path': path.resolve('./api', 'migrations')
};


```


#### Iniciando uma tabela

> **npx sequelize-cli model:create --name Pessoas --attributes nome:string,ativo:boolean,email:string,role:string**

#### Fazendo as migrações 

> **npx sequelize-cli db:migrate**

#### Seeds

Server para popular seu banco e com isso auxilar nos teste. 

> **npx sequelize-cli seed:generate --name demo-pessoa**

e para Realmente popular

> **npx sequelize-cli db:seed:all**


### Chaves Estrangeiras

Para adicionar a chaves entrangeiras, precisa ir ate o arquivo no models e aii colocar o tipo de relacionamento que ele possui na parte associativa, pegando isso como exemplo:


```javascript

static associate(models) {
      Turmas.hasMany(models.Matriculas, {
        foreignKey : 'turma_id'
      })
      Turmas.belongsTo(models.Pessoas)
      Turmas.belongsTo(models.Niveis)
    }

```
E No migration precisa adicionar os posicionamento das chaves estrangeiras

```javascript

nivel_id: {
        allowNull: false,
        type: Sequelize.INTEGER,
        references: {model: 'Niveis', key: 'id'}
      }

```

Para rodas as migrações

> npx sequelize-cli db:migrate


### Desfazendo as operações

Rodou o comando de migração antes de fazer alguma alteração importante em algum modelo e agora as tabelas do banco não estão como você precisa? Bom, já comentamos que as migrações em ORM também servem para termos um tipo de “versionamento” (feito através do arquivo SequelizeMeta no seu banco) e poder voltarmos o banco a um estado anterior à última alteração.

Como fazer isso? Através dos comandos:

> **npx sequelize-cli db:migrate:undo**

Este comando vai desfazer somente a última migração feita, na ordem em que os arquivos são lidos e executados pelo Sequelize (de acordo com as datas e horários no nome dos arquivos). Se você tiver rodado 3 migrações - por exemplo, das tabelas Niveis, Turmas e Matriculas, o comando npx sequelize-cli db:migrate:undo vai desfazer apenas Matriculas.

Você pode rodar o mesmo comando novamente para ir desfazendo as migrações na ordem em que foram executadas, ou usar o comando:

> **db:migrate:undo --name [data-hora]-create-[nome-da-tabela].js**

Para desfazer uma migração específica. Nesse caso, lembre-se que só irá funcionar se não tiver nenhuma outra tabela relacionada a ela!
**Desfazendo seeds**

Os seeds servem para termos dados iniciais no banco, normalmente dados de exemplo e/ou para teste. Quando você quiser desfazer essa operação para limpar esses dados do banco, pode rodar o comando:

> **npx sequelize db:seed:undo**

Para desfazer o último seed feito.

> **npx sequelize-cli db:seed:undo --seed nome-do-arquivo**

Para desfazer seeds de uma tabela específica.

> **npx sequelize-cli db:seed:undo:all**

Para desfazer todos os seeds feitos.

Importante:

Ao contrário das migrações, não existe nenhum recurso de “versionamento” de seeds, só é possível incluir no banco e desfazer a operação (o que vai deletar os registros do banco).

Se você rodar o :undo em uma tabela e quiser mais tarde utilizar os seeds novamente na mesma tabela, os IDs deles serão outros. Por exemplo:

Rodamos o comando npx sequelize-cli db:seed:all e populamos a tabela:

| id | nome           | ativo     | email             | role      | createdAt           | updatedAt           |
|----|----------------|-----------|-------------------|-----------|---------------------|---------------------|
| 1  | Ana Souza      | 1         | ana@ana.com       | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 2  | Marcos Cintra  | 1         | marcos@marcos.com | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 3  | Felipe Cardoso | 1         | felipe@felipe.com | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 4  | Sandra Gomes   | 0         | sandra@sandra.com | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 5  | Paula Morais   | 1         | paula@paula.com   | docente   | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 6  | Sergio Lopes   | 1         | sergio@sergio.com | docente   | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |

Se rodarmos o comando npx sequelize-cli db:seed:undo:all para deletar esses registros e, em seguida, refazer os seed novamente com npx sequelize-cli db:seed:all, o resultado será o abaixo. Compare os números de id!

| id | nome           | ativo     | email             | role      | createdAt           | updatedAt           |
|----|----------------|-----------|-------------------|-----------|---------------------|---------------------|
| 7  | Ana Souza      | 1         | ana@ana.com       | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 8  | Marcos Cintra  | 1         | marcos@marcos.com | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 9  | Felipe Cardoso | 1         | felipe@felipe.com | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 10 | Sandra Gomes   | 0         | sandra@sandra.com | estudante | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 11 | Paula Morais   | 1         | paula@paula.com   | docente   | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |
| 12 | Sergio Lopes   | 1         | sergio@sergio.com | docente   | 2020-04-15 01:14:12 | 2020-04-15 01:14:12 |

Os registros terão novos IDs, pois uma vez deletado o ID nunca é reutilizado. Se você estiver migrando/seedando tabelas relacionadas, é sempre bom conferir os IDs de todas, do contrário o Sequelize vai lançar um erro de relação.

#### Adicionando outras tabelas como exemplo. 

> npx sequelize-cli model:create --name Niveis --attributes descr_nivel:string
> npx sequelize-cli model:create --name Turmas --attributes data_inicio:dateonly
> npx sequelize-cli model:create --name Matriculas --attributes status:string

#### Adicionando outras seeds para popular tabela

> npx sequelize-cli seed:generate --name demo-niveis
> npx sequelize-cli seed:generate --name demo-turmas 
> npx sequelize-cli seed:generate --name demo-matriculas


Populando tudo 
> **npx sequelize-cli db:seed:all**


##### Populando banco como exemplo

```sql
insert into Pessoas (nome, ativo, email, role, createdAt, updateAt) values ("Carla Gomes", 1, "carla@cala.com", "estudante", NOW(), NOW())

SELECT * FROM Pessoas


```