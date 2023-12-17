Tools to manage the DBs (switch, save & restore)

### Setup

To define and get the database you are working on, `setdb` and `getdb`
must be used

- `setdb` takes one argument and writes it in a file. By default, it
  also writes the DB name in the `.odoorc` file, but this line is
  actually not mandatory and can be removed
- `getdb` simply reads and returns the content of the file

### Save & Restore

Here are the two mains scripts of this repo:

**`savedb`:**
It stores the state of the current DB and its filestore. To do so, the
script simply copies the current DB into a new one
called `<current_db>__<savepoint name>`, where `<savepoint name>` is the
optional argument of the script. If not provided, the savepoint name is
defaulted to *SAVEPOINT*

**`restoredb`:**
It restores the current DB to a given state. The script takes one
argument: the savepoint name. If not provided, it will use the state
nammed *SAVEPOINT*.

### Flow example

I want to work on a DB called _1234567-15.0_:

```
setdb 1234567-15.0
```

I run Odoo, I create some products and set a specific configuration. I'm
going to follow some additional steps, but I want to create a backup
point first so that I can return to it later, and this state should be
called *config*:

```
savedb config
# This will copy the current DB to a new one called 1234567-15.0__config
```

I then go through several steps: I create a SO, confirm it, process the
delivery, create the invoice, etc. I'm about to post this invoice, but I
know that my bug appears right at that moment. In doubt, I first make
another backup, this time without worrying about the name:

```
savedb
# This will copy the current DB to a new one called 1234567-15.0__SAVEPOINT
```

I then post the invoice, try few things, change the code etc., and now I
would like to try again. I can simply restore my DB juste before the
invoice posting:

```
restoredb
# This will copy the DB called 1234567-15.0__SAVEPOINT to the current one
```

Suppose now I would even like to change the configuration, but it's a
bit complicated because the posted AML, some constraints, etc. I can
simply restore the first state I saved:

```
restoredb config
# This will copy the DB called 1234567-15.0__config to the current one
```

### Misc

This repository provides few other scripts. Some of them are used by the
above ones, some other are additional tools.

- `copydb` takes two arguments _X_ and _Y_, it copies _X_ on a new DB
  called _Y_. It is used by `savedb` and `restoredb`
- `killodoo` kills all Odoo processes. It is executed by `copydb`,
  otherwise it's not possible to process the copy.
- `ldb` lists all databases  (`-a` will also list all savepoints)
- `dropall`: for each argument _X_, it drops all databases that contain
  _X_ in their name
- `.bash_completion` allows the user to auto complete the arguments
  of `dropdb`, `setdb`, `copydb`, `restoredb` and `savedb` in the
  terminal
