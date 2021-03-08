# My CV

## First Name, Last Name:
 Pavel Yrosh
 
***
## Contact Info:
**Phone:** +375336898879  

**Telegram:** @ZeddikusZZ
 
**Email:** epiccinemabattles@gmail.com, py@bel.biz  
 
**GitHub:** ErricoMallatesta  
**GitLab:** Zeddikus97  
 
***
## Summary:
 My goal is to realize myself as a programmer. Like to learn new languages and opportunities, and solve unusual problems.   
 I want to recall the basic skills and the basis of the language again, because during the current projects I forgot a little. This will give me the opportunity to be more independent in choosing a job and implementing projects.

***
## Skills:
 * JS, React/Redux, Node.js(Express.js), JQuery
 * Docker
 * Salesforce.com, Apex, VisualForce
 * PHP, Wordpress, Laravel
 * SQL, PostgresSQL, MS SQL Server
 * Git  
 * Windows, Linux as OS
 * basic knowledges of C#(ASP.NET), Python 

***
## Code examples:
```javascript
const environment = process.env.ENVIRONMENT || 'development'
const config_knex = require('../../../../../../knexfile.js')[environment];
const knex = require('knex')(config_knex);



function increaseDepthCounterGoingUp(table_list, table_name, additional_depth){
  if (table_list[table_name]===undefined) {
    table_list[table_name] = { name:table_name, depth: additional_depth }
  }
  else if (table_list[table_name]['relations']===undefined) {
    table_list[table_name]['depth'] = Math.max(table_list[table_name]['depth'], additional_depth);
  }
  else {
    table_list[table_name]['depth'] = Math.max(table_list[table_name]['depth'], additional_depth);
    table_list[table_name]['relations'].forEach(element => {
      table_list=increaseDepthCounterGoingUp(table_list, element, additional_depth+1)
    });
  }
  return table_list
}

function increaseDepthCounterGoingDown(tables_list, table_name, accum, depth){
  for (let low_relationship of Object.keys(tables_list)){
    if (tables_list[low_relationship]['relations']!==undefined){
      if (tables_list[low_relationship]['relations'].includes(table_name)) {
        accum[`${table['tablename']}`] = accum[`${table['tablename']}`]!==undefined
        ? Math.max(tables_list[table_name]['depth'], depth)
        : depth;
        accum=increaseDepthCounterGoingDown(tables_list, low_relationship, accum, depth+1);
      }
    }
  }
  return accum;
}

async function getInfo() {
  const result_tables = await knex.raw(`SELECT tablename FROM pg_catalog.pg_tables WHERE schemaname='public'`);
  const tables_seeds = {};
  let tables_list = {};
  console.log(result_tables.rows);
  for await (table of result_tables.rows.reverse()) {
    if(!table['tablename'].includes('-')){
      let result_seed = await knex.raw(`SELECT * FROM ${table['tablename']}`);
      let result_relations = await knex.raw(`SELECT COALESCE(array_agg(ccu.table_name::TEXT), ARRAY[]::text[]) as rels
      FROM information_schema.table_constraints AS tc 
      JOIN information_schema.constraint_column_usage AS ccu
        ON ccu.constraint_name = tc.constraint_name
        AND ccu.table_schema = tc.table_schema
      WHERE tc.constraint_type = 'FOREIGN KEY' AND tc.table_name='${table['tablename']}';`);
      let relations=result_relations['rows'][0]['rels'];
      let seed=result_seed['rows'];
      tables_list[`${table['tablename']}`] = tables_list[`${table['tablename']}`]===undefined
      ? { name:table['tablename'], seeds: seed, depth: 0, relations: relations}
      : { name:table['tablename'], seeds: seed, depth: tables_list[`${table['tablename']}`].depth, relations: relations}
      let accum = increaseDepthCounterGoingDown(tables_list, table['tablename'], {}, 1);
      if(!(Object.entries(accum).length === 0 && accum.constructor === Object)){
        for (let name of Object.keys(accum)){
          tables_list[name]['depth']=Math.max(tables_list[name]['depth'], accum[name])
        }
      }
      for (let high_relationship of relations){
        if (tables_list[high_relationship]===undefined) tables_list[high_relationship] = { name:high_relationship, depth: 0 }
        else tables_list=increaseDepthCounterGoingUp(tables_list, high_relationship, tables_list[`${table['tablename']}`]['depth']+1);
      }

    }
  }
  let tables_order = Object.keys(tables_list).sort((a,b) => (tables_list[a].depth > tables_list[b].depth) ? 1 : -1)
  console.log(tables_list);
  tables_seeds['tables_order']=tables_order;
  for (table in tables_list){
    tables_seeds[table]=tables_list[table].seeds; 
  }
  //console.log(tables_seeds);
  return tables_seeds;
}


async function writeInFile(json) {
  const fs = require('fs');
  fs.writeFile(
    'seeds/json_data/data.json',
    JSON.stringify(json),
    function(err) {
      console.log(err);
      //if (err) throw err;
      console.log('complete');
      process.exit();
    },
  )
}

(async() => {
  const table_data = await getInfo();
  //console.log(table_data);
  await writeInFile(table_data);  
  //console.log(table_data['core_store']);
})()

```
***
## Experience: 
 Projects on SENLA courses  
 Working ad a programer on IMAGURU own project  
 GRSU-lab (see above)

***
## Education:
 University education as a math-programmer  
 SENLA Salesforce courses with certificate

*** 
## English: 
 Probably B1. Have good reading and writing skills. Can read library documentation.
