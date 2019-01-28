# postgres-differ :mag_right:
[![forthebadge](https://forthebadge.com/images/badges/built-with-love.svg)]() [![forthebadge](https://forthebadge.com/images/badges/makes-people-smile.svg)]()

Node.js module for easy synchronization of postgres tables with models (json-schemas). Migration alternative

## Features

  - Easy to use [model scheme structure](#model-scheme-structure)
  - Logging

## Installation

*\* postgres-differ requires [Node.js](https://nodejs.org/) v8+*
 
```bash
npm i pg-differ
```

## Documentation

  - [Usage example](#usage-example)
  - [Constructor params](#constructor-params)
  - [Model scheme structure](#model-scheme-structure)
    - [*indexes*](#indexes)
    - [*columns*](#columns)
    - [*foreignKey* params](#foreignkey-params)
    - [*forceIndexes*](#forceindexes)

## Usage example

#### *[example *.schema.json](schema-example.json)*

```javascript
const Differ = require('pg-differ')
const path = require('path')

 const differ = new Differ({
    dbConfig: {},
    schemaFolder: path.resolve(__dirname, 'schemas'), // or/and use 'define' method to add model,
    logging: true,
    logger: function(message){},
    placeholders: {
      schema: 'schema_name'
    }
 })
 
 differ.define({
     table: '${schema}.table_name',
     indexes: [
       {
            type: 'foreignKey',
            columns: ['id'],
            references: {
                table: 'reference_table_name',
                columns: ['id']
            }
       }
     ],
     columns: [
       {
            name: 'id',
            type: 'bigint',
            nullable: false,
            primaryKey: true
       },
     ]
 })
 
 differ.sync()
```

## Constructor params

| Option | Type | Default | Required | Description |
| ------ | ------ | ------ | ------ | ------ |
| **dbConfig** | Object | null | Yes | Connection configuration object for [node-postgres](https://node-postgres.com/features/connecting#programmatic) |
| **logging** | Boolean | `false` | No | Option to enable logging in the console (or output a message to the arguments of the `ottions.logger` function) |
| **schemaFolder** | String | null | No | Path to the folder with `* .schema.json` files for automatic model definitions. Equivalent to function calls `differ.define ({... schemaObject})`  |
| **logger** | Function | `console.info` | No | Callback of the format `function (message) {}` for displaying a message about changes | 
| **placeholders** | Object | `null` | No | An object with names and their values to replace placeholders in `schemaFolder` | 

## Model scheme structure
*\* parameters of the `differ.define` method or the` * .schema.json` file structure for `options.schemaFolder`*

| Option | Type | Default | Required | Description |
| ------ | ------ | ------ | ------ | ------ |
| **table** | String | `null` | Yes | The name of the format table is `'schema_name.table_name'` or `' table_name'` |
| **indexes** | Array[Object] | `null` | No | Array of objects with parameters of table indexes |
| **columns** | Array[Object] | `null` | Yes | Array of objects with table column parameters |
| **forceIndexes** | Array[String] | `null` | No | [`index`&#124;`foreignKey`&#124;`unique`] | 

### indexes

| Option | Type | Default | Required | Description |
| ------ | ------ | ------ | ------ | ------ |
| **type** | String | `null` | Yes | `index`&#124;`foreignKey`&#124;`primaryKey`&#124;`unique` |
| **columns** | Array[String] | `null` | Yes | List of column names |
| [**foreignKey params**](#foreignkey-params) |  |  | No | Parameter list for `type: 'foreignKey'` |

### columns

| Option | Type | Default | Required | Description |
| ------ | ------ | ------ | ------ | ------ |
| **name** | String | `null` | Yes | Column name |
| **type** | String | `null` | Yes | Type name (with alias support) |
| **nullable** | Boolean | `true` | No | In the case of `nullable === false`, it will set the constraint ` NOT NULL` |
| **force** | Boolean | `false` | No | Deleting column values in case of impossible conversion of values to a new type |
| **primaryKey** | Boolean | `false` | No |  | 
| **unique** | Boolean | `false` | No |  | 
| **foreignKey** | Boolean | `false` | No |  | 
| [**foreignKey params**](#foreignkey-params) |  |  | No | Parameter list for `foreignKey: true` |

### foreignKey params

| Option | Type | Default | Required | Description |
| ------ | ------ | ------ | ------ | ------ |
| **match** | String | `'SIMPLE'` | No | An object with required parameters for the `type: 'foreign Key'` index |
| **onDelete** | String | `'NO ACTION'` | No | `CASCADE`&#124;`RESTRICT`&#124;`NO ACTION` |  
| **onUpdate** | String | `'NO ACTION'` | No | `CASCADE`&#124;`RESTRICT`&#124;`NO ACTION` | 
| **references** | Object | `null` | Yes | Object with foreign table parameters |  
| references.**table** | String | `null` | Yes | Foreign table name |   
| references.**columns** | Array[String] | `null` | Yes | Foreign table column names |  

### forceIndexes

`forceIndexes` - this is an array with a list of types [`index` |` foreignKey` | `unique`], which are deleted from the database if they are not defined in the model schema

## License
postgres-differ is open source software [licensed as MIT](LICENSE).