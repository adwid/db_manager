Tools to manage the DBs (switch, save & restore)


### Base

All scripts are based on the idea that we are working on one DB at the time. To define and get that current DB's name, `setdb` and `getdb` must be used
- `setdb` will write the argument in a hidden file. It also writes the DB on the `.odoorc` file (but this line is not mandatory and can be removed)
- `getdb` simply reads and returns the content of the hidden file

### Save & Restore

Suppose an initialized DB with a specific configuration on it. I want to save its state and have the possibility to restore that state
- `savedb` will save the current DB (`getdb`) into a new one. Suppose _X_ is the name of the current DB:
  - If an argument _Y_ is given to `savedb`: the saved DB is called _X\_\_Y_
  - Otherwise: the saved DB is called _X\_\_SAVEPOINT_
- `restoredb` tries to find a saved state of the current DB (`getdb`) and restores it. Suppose _X_ is the name of the current DB:
  - If an argument _Y_ is given to `restoredb`: the script tries to find a saved state of _X_ called _Y_ (i.e., it searches for a DB called _X\_\_Y_). If such a state exists, it will restore it on _X_
  - Otherwise, it tries to find a saved state called _SAVEPOINT_ and does the same than above

/!\\ The filestore is never saved nor restored. In most cases, it is not useful. 

#### Flow example
I want to work on a DB called _1234567-15.0_:
```
setdb 1234567-15.0
```
I run Odoo, I create some products and set a specific configuration. I don't want to lose it:
```
savedb config
```
Then I do some stuff and I create a SO. 
I'm going to validate it but I would like to save the current state and I don't care about the name:
```
savedb
```
Again, I confirm the SO and do some stuff. Finally, I want to go back either before the SO creation or before its validation:
```
restoredb config
restoredb
```

### Misc
- `copydb` takes two arguments _X_ and _Y_ and copies _X_ on a new DB called _Y_. It is used by `savedb` and `restoredb`
- `killodoo` kills all Odoo processes. It is executed by `copydb`
- `ldb` lists all databases
- `dropall`: for each argument _X_, it drops all databases that contain _X_ in their name
- `.bash_completion` allows the user to auto complete the arguments of `setdb`, `savedb` and `restoredb` in his terminal
- `renamedb`: takes one argument X and will rename the current DB into X
