## 🎯 Objetivo(s)

- Entender o que é um ORM (qual problema resolve?).

- Refletir sobre as dificuldades de se manter um banco de dados atualizado em uma equipe de desenvolvimento x atualizar o banco de dados em produção.

###

[](https://hub.driven.com.br/computacao/modulo/87/topico/733/aula/146/compilado#bb96a25d08744ad7a6249241c63c9444 "📚 Compilado")📚 Compilado

- Nos últimos meses aprendemos como criar aplicações back-end completas usando o Node, Express e recentemente o TypeScript.

- Nesta aplicações, vimos duas possibilidades diferentes para trabalhar com a camada de persistência: uma tecnologia de banco de dados relacional (Postgres) e uma não relacional (Mongo). Em ambos os casos, usamos um driver para fazer a conexão entre a aplicação e o banco de dados.

- No caso do Postgres, optamos pelo driver `pg`. Com ele, fomos capazes de criar uma conexão com o banco de dados e então, por meio da sua API, escrever queries para serem executadas nele. Por exemplo:

```typescript
import pg from "pg";

const { Pool } = pg;

const connection = new Pool({
  host: "localhost",
  port: 5432,
  user: "postgres",
  password: "postgres",
  database: "users",
});

async function findAll() {
  const users = await connection.query(`SELECT * FROM users`);

  return users.rows;
}
```

exemplo de query para buscar os registros na tabela users

- Posteriormente vimos que podemos usar o poder do TypeScript para fazer a tipagem dessa estrutura para ter mais controle, em tempo de desenvolvimento, de como ela deve se comportar na aplicação, afinal, somente com o código abaixo, é muito difícil prever o que virá como resultado da função:

```typescript
import pg from "pg";

const { Pool } = pg;

const connection = new Pool({
  host: "localhost",
  port: 5432,
  user: "postgres",
  password: "postgres",
  database: "users",
});

type User = {
  id: number;
  name: string;
  email: string;
  password: string;
};

async function findAll() {
  const users = await connection.query<User>(`SELECT * FROM "users"`);

  return users.rows;
}
```

tipagem sendo aplicada junto ao pg com o recurso de generics do TypeScript

- E até aqui tudo bem. Essa abordagem funciona, desde que tenhamos os cuidados necessários, como por exemplo, prevenção de ataques do tipo SQL Injection. Mas (olha o “mas” aí de novo, rs) note que se começarmos a pensar nessa aplicação a médio-longo prazo, alguns problemas graves tendem a acontecer:

- Toda vez que a estrutura da tabela `users` mudar (devido a uma nova funcionalidade e/ou uma nova regra de negócio), precisaremos rever e atualizar as queries (CRUD) e os tipos criados com base nele. Dependendo do nível de complexidade da aplicação, isso pode começar a ficar bem custoso.
- Ao trocar, atualizar ou criar uma tabela, precisamos dar um jeito de atualizá-la em ambiente de desenvolvimento e/ou produção. Dependendo das mudanças e da quantidade de pessoas envolvidas, isso também se torna problemático. O projeto precisará de uma pasta de arquivos `.sql` só para conter esses scripts de migração (e possivelmente uma forma de automatizar sua execução).
- Bancos de dados relacionais apesar de usarem a linguagem SQL para realizar suas operações, possuem particularidades em cada implementação diferente. O Oracle não é igual ao Postgres que não é igual o MySQL. Se eventualmente a aplicação precisar ser implantada com um banco X e não Y, será necessário validar se a aplicação funciona. No pior dos casos, será necessário rever a implementação do código para gerar compatibilidade.
- A manutenção de queries em strings se torna mais difícil com o tempo, pois dependendo da query (envolvendo múltiplos joins, por exemplo), fica quase certo de que erraremos nomes de tabelas, campos, alias… etc.
- Não existe validação em tempo de compilação que nos impeça de cometer erros. Assim como fazemos com o JS sem TS, estamos propensos a cometer erros ao tentar acessar recursos que não podemos.

- Não seria mais fácil se existisse uma tecnologia capaz de nos ajudar com tudo isso? É pra isso mesmo que existem os ORMs!

- ORM vêm de _Object-Relational Mapping_ e significa Mapeamento Objeto Relacional. Em resumo, é uma tecnologia capaz de converter dados das tabelas em objetos na aplicação (JS/TS) e vice-versa.

- _“E por que isso parece interessante? Como ele resolve esses problemas apontados”_

- Porque tecnologias como ORM abstraem a camada de construção das queries nos oferecendo APIs que fazem isso “por debaixo dos panos”. Além disso, os ORMs são capazes de “encaixar” os dados vindo das tabelas em objetos do JavaScript/TypeScript, o muito mais fácil a sua manipulação na aplicação.

- Existem várias tecnologias de ORM disponíveis no mercado. No universo do JavaScript, os mais famosos são: [Sequelize](https://sequelize.org/), [TypeORM](https://typeorm.io/) e o [Prisma](https://www.prisma.io/). Cada uma delas tem um bom nível de maturidade e aplicabilidade no mercado. Todas funcionam muito bem dentro de suas respectivas propostas. Por ter alguns diferenciais interessantes e aparecer com frequência no mercado, durante a formação, estudaremos o Prisma.

- Neste minicurso, você aprenderá todo o essencial sobre o Prisma e como sua proposta diferenciada nos ajuda muito no desenvolvimento de aplicações evitando as armadilhas já citadas.

###

[](https://hub.driven.com.br/computacao/modulo/87/topico/733/aula/146/compilado#820a956c533f4320826ca9270d1c9ffd "✏️ Exercício: Experiência de evolução no banco")✏️ Exercício: Experiência de evolução no banco

📝 Enunciado

- Podemos conectar nossas aplicações aos bancos de dados utilizando drivers.

- Os drivers oferecem APIs que nos permitem escrever queries para serem executadas.

- No entanto, escrever queries “na mão” nem sempre é a melhor solução.

- Imagine que você está em uma equipe de desenvolvimento e precisa fazer a seguinte alteração na aplicação:

- O campo `createAt` deve ser inserido na tabela `posts`. Deve ser do tipo Date.
- A informação deve ser enviada no post no formato: `YYYY/MM/dd`. Ela não é obrigatória. Se não for informada, receber o valor `new Date()`.
- Altere o campo `body` da tabela posts para `content`.

- ➡️ Crie os SQL correspondentes imaginando que já existem dados em produção. Faça as alterações na queries, types e o que mais for necessário.

- Não é necessário rodar o arquivo .sql, você pode só criar/alterar a tabela pelo terminal ou pgadmin usando as queries que estão no arquivo.

🔗 Código para o exercício

[

enunciado\_\_experiencia-evolucao.zip

38.3KB

](https://file.notion.so/f/s/4950bd2a-5929-4600-9818-e1c16d5fddbd/enunciado__experiencia-evolucao.zip?id=7b5c6eea-d957-4adf-b15d-d240cc40680b&table=block&spaceId=f797e032-5eb2-4c9d-beb7-cd7181e19e47&expirationTimestamp=1695304800000&signature=tos_dJV67pgvsYcFj0arxbfJaUTLY0-07MkXZnZQ194)

###

[](https://hub.driven.com.br/computacao/modulo/87/topico/733/aula/146/compilado#4296c6d7471e4f20bb1dbd8e549c6f15 "🚀 Para ir além")🚀 Para ir além

- `🌐` Is Prisma an ORM? (Prisma)

[

Is Prisma an ORM? | What is an ORM?

Learn about how Prisma implements the Data Mapper ORM pattern and how it achieves the same goal as traditional ORMs without requiring you to map classes to tables as traditional ORMs do.

https://www.prisma.io/docs/concepts/overview/prisma-in-your-stack/is-prisma-an-orm

![Is Prisma an ORM? | What is an ORM?](https://www.prisma.io/docs/social/docs-social.png)

](https://www.prisma.io/docs/concepts/overview/prisma-in-your-stack/is-prisma-an-orm)

- `🌐` Top 11 Node.js ORMs, query builders & database libraries in 2022 (Prisma)

[

Best 11 ORMs for Node.js, Query Builders & Database Libraries in 2022

Choosing an ORM or query builder for your Node.js app can be daunting. We review the most popular query builders, ORMs, and other database libraries so you don't have to.

https://www.prisma.io/dataguide/database-tools/top-nodejs-orms-query-builders-and-database-libraries

![Best 11 ORMs for Node.js, Query Builders & Database Libraries in 2022](https://www.prisma.io/dataguide/social/node-orms-2022.png)

](https://www.prisma.io/dataguide/database-tools/top-nodejs-orms-query-builders-and-database-libraries)

- `🌐` What is an ORM (freeCodeCamp)

[

What is an ORM – The Meaning of Object Relational Mapping Database Tools

Object Relational Mapping (ORM) is a technique used in creating a "bridge" between object-oriented programs and, in most cases, relational databases \[/news/what-is-a-relational-database-rdbms-definition/\]. Put another way, you can see the ORM as the layer that connects object oriented programming \[/news/four-pillars-of-object-oriented-programming/\] (OOP) to relational databases. When interacting with a database using

![What is an ORM – The Meaning of Object Relational Mapping Database Tools](https://cdn.freecodecamp.org/universal/favicons/favicon.ico)

https://www.freecodecamp.org/news/what-is-an-orm-the-meaning-of-object-relational-mapping-database-tools/

![What is an ORM – The Meaning of Object Relational Mapping Database Tools](https://www.freecodecamp.org/news/content/images/2022/09/markus-winkler-gLdJnQFcIXE-unsplash.jpg)

](https://www.freecodecamp.org/news/what-is-an-orm-the-meaning-of-object-relational-mapping-database-tools/)
