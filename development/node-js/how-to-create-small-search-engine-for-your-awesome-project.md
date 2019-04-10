# How to create small search engine for your awesome project

### How to create small search engine for your awesome project?

What do you think about Google or DuckDuckGo?   
Yes, I love it too, as millions of people.

But, if you create new project, you will need strong and very smart search engine?  
No, you will need very small and fast search engine!

Okay let's google it and what will we get?  
Finally you see ElasticSearch, awesome search engine for full text.

Suppose you create project on Nodejs + PostgreSQL and your database have 10,000 items. ElasticSearch to cope with this task.

But, you need copy your database, because ElasticSearch should generate its “search graph” and we have 2 “databases”, I dont like it, because we need more free space in my hard drive.

Can PostgreSQL help us for created custom search engine? Yes, he can.

Okay, to help you understand, i have programming language JavaScript, because i like him, but PostgreSQL main database in 2017. Very many people use NodeJS and PostgreSQL in your small and big projects.

### Maybe start programming ?

Yeah, go to start and create new folder for our project.

Naming: **sm-fs-search-engine** \(small fast search engine\).

And we need init npm and install this packages.

```text
{
  "name": "sm-fs-search-engine",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "MIT",
  "dependencies": {
    "body-parser": "^1.18.2",
    "express": "^4.16.2",
    "pg": "^7.4.0"
  }
}
```

Next, we create file `server.js` and write this:

```text
const express       = require('express');
const app       = express();
const bodyParser    = require('body-parser');

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

const port = process.env.PORT || 3000;

app.get('/search', function(req, res) {
    console.log('SERVER UP!');
})

app.listen(port);
console.log(new Date() + ' => start server :' + port);
```

And go to check this, `node server.js`, hmmm very nice work!

Okay, next we need set setting in PostgreSQL, go to open `pgsql` and create database.

```text
CREATE DATABASE universities;
```

Nice, we have our database. Next, create table `university`.

```text
CREATE TABLE university (id SERIAL PRIMARY KEY, name VARCHAR, description VARCHAR);
```

And now, we need university. I added the universities itself.

Now the most important thing, add another column, her name fts.

```text
ALTER TABLE university ADD COLUMN fts tsvector;
```

And update this.

```text
UPDATE university SET fts=to_tsvector('english', coalesce(name,'') || ' ' || coalesce(description,''));
```

Finish step for database, this create search index.

```text
CREATE INDEX textsearch_idx ON university USING GIN (fts);
```

Awww right, now, we can start writing REST API for search engine.

### Create API for search engine

If you notice we set the pack `pg`, special for him we need create folder `lib`and create file db.js, this file current for connection on our database.

```text
const pg = require('pg');

var config = {
  user: 'postgres',  
  database: 'universities',  
  password: 'qwe123', 
  host: 'localhost', 
  port: 5432, 
  max: 100, // max number of clients in the pool 
  idleTimeoutMillis: 30000, // how long a client is allowed to remain idle before being closed 
};

const pool = new pg.Pool(config);

pool.on('error', function (err, client) {
  console.error('idle client error', err.message, err.stack);
});

module.exports.query = function (text, values, callback) {
  console.log('query:', text, values);
  return pool.query(text, values, callback);
};

module.exports.connect = function (callback) {
  return pool.connect(callback);
};
```

This file we include if we use query for database.

Okay, create folder `functions` this folder have all functions we need.

```text
const pool = require('../lib/db');

function search(search_string, func) {
     pool.query(
        "SELECT * FROM university WHERE fts @@ to_tsquery('english',$1)", 
        [search_string],
        function(err, result) {  
            if(err) {
                func([])
            } else {
                func(result.rows)
            }
        }
    );
}

module.exports = search
```

And update server.js

```text
const express       = require('express');
const app           = express();
const bodyParser    = require('body-parser');

const search        = require('./functions/search.js');

app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());

const port = process.env.PORT || 3000;

app.get('/search', function(req, res) {
    if (typeof req.query.text !== 'undefined') {
        search(req.query.text, function(data_items) {
            res.send({
                response : {
                    items : data_items
                }
            })
        })
    } else {
        res.send({error : '[100] Not search params text in query.'})
    }
})

app.listen(port);
console.log(new Date() + ' => start server :' + port);
```

And go to check this, open your browser and write:

[**http://localhost:3000/search?text=Institute**](http://localhost:3000/search?text=Institute)

Example response:

```text

{
   "response":{
      "items":[
         {
            "id":1,
            "name":"Massachusetts Institute of Technology (MIT)",
            "description":"Founded in 1861, Massachusetts Institute of Technology (M.I.T.) is the best university in the world according to the QS World University Rankings 2016-2017, a title it has regularly claimed over the last few years. Rated 5+ QS stars, it’s achieved the maximum score across all categories, including research, employability, teaching, facilities, internationalization, innovation, specialist criteria and inclusiveness. MIT is also the best university in the world for a number of subjects including, but not limited to, architecture, linguistics, computer science and information systems, engineering and technology, chemistry, mathematics, economics and econometrics. Based in Cambridge, MIT’s labs and classroom continue to deliver pioneering research into the world’s most pressing issues, such as cancer therapy, identity politics, global engagement, energy, public service and industry.",
            "fts":"'-2017':29 '1861':8 '2016':28 '5':42 'accord':21 'achiev':47 'across':51 'also':67 'architectur':84 'base':99 'best':16,69 'cambridg':101 'cancer':121 'categori':53 'chemistri':94 'claim':35 'classroom':106 'comput':86 'continu':107 'criteria':62 'deliv':109 'econom':96 'econometr':98 'employ':56 'energi':127 'engag':126 'engin':91 'facil':58 'found':6 'global':125 'ident':123 'includ':54,79 'inclus':64 'industri':131 'inform':89 'innov':60 'institut':2,10 'internation':59 'issu':118 'lab':104 'last':38 'limit':82 'linguist':85 'm.i.t':13 'massachusett':1,9 'mathemat':95 'maximum':49 'mit':5,65,102 'number':76 'pioneer':110 'polit':124 'press':117 'public':128 'qs':24,43 'rank':27 'rate':41 'regular':34 'research':55,111 'scienc':87 'score':50 'servic':129 'specialist':61 'star':44 'subject':78 'system':90 'teach':57 'technolog':4,12,93 'therapi':122 'titl':31 'univers':17,26,70 'world':20,25,73,114 'year':40"
         }
            ...
      ]
   }
}


```

Its work!

And we created small and fast engine!

[Full code](https://github.com/Gaserd/sm-fs-earch-engine)

How to make it even better ?

We can write support response page for university, like this [**http://localhost:3000/search?text=Institute&page=2**](http://localhost:3000/search?text=Institute&page=2), this will increase the upload speed results.

If you have troubles, write me [twitter](https://twitter.com/gaserd123) or mail \([gaserd123@gmail.com](mailto:gaserd123@gmail.com)\).

Thanks for read and may the force be with you!

