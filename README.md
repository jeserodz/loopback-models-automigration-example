#LoopBack Models Auto-migration Example
### Summary
LoopBack is a highly-extensible, open-source Node.js framework that enables you to:
 - Create dynamic end-to-end REST APIs with little or no coding.
 - Access data from major relational and NoSQL databases, SOAP and REST APIs.
 - Incorporate model relationships and access controls for complex APIs.

When a fresh database is created, the models in a LoopBack application are not present by default in the database schema. In addition, every change on the models properties are not automatically updated on the database schema neither. The ability to automatically migrate models to a relational database is called **auto-migration**.

This example demonstrate how to perform auto-migration of models to a MySQL datasource.

###Instructions
> **Requirements:** You will need to have Node.js and MySQL installed on your machine.

1. Clone this repository to your local machine: `git clone https://github.com/jeserodz/loopback-models-automigration-example.git`
2. Move into the project: `cd loopback-models-automigration-example`
3. Install dependencies: `npm install`
4. Create a database in your localhost MySQL server named **loopback-models-automigration-example**
5. You can run this example by running: `node .` or `npm run start`
6. You should get these messages on the console: 

>Models found: [ 'Task' ]
Cheking if table for model Task is created and up-to-date in DB...
Difference found! Auto-migrating model Task...
Auto-migrated model Task successfully.


###How auto-migration is performed
This operation is taking place in the `server/boot/models-automigration.js` boot script.

```
module.exports = function (app) {
    'use strict'
    var mysql = app.dataSources.mysql;
    
    console.log('-- Models found:', Object.keys(app.models));

    for (var model in app.models) {
        console.log("Cheking if table for model " + model + " is created and up-to-date in DB...");
        mysql.isActual(model, function (err, actual) {
            if (actual) {
                console.log("Model " + model + " is up-to-date. No auto-migrated.");
            } else {
                console.log('Difference found! Auto-migrating model ' + model + '...');
                mysql.autoupdate(model, function () {
                    console.log("Auto-migrated model " + model + " successfully.");
                });
            }
        });
    };

};
```