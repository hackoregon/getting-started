# Accessing the PostgeSQL DB on a virtual machine 

### This is a brief description of how to access the Postgres DB that's on your virtual machine(VM) inside Jupyter Notebook 

#### First we need to create a new postgres user so we can connect to the DB server using this user in a Jupyter Notebook

1. From your home terminal ssh into the virtual machine by entering the command below
  * ssh vagrant@localhost -p2222
  * when prompted enter password: 'ORturkeyeggs'
  
2. In VM terminal session switch to the postgres user using command below
  * sudo su postgres
  * when prompted enter password: 'ORturkeyeggs'
  
3. Open a psql session using command below
  * psql 
  * you should now see this prompt: 'postgres=#'
  
4. Inside psql session create a new superuser using command below
  * CREATE USER turkey WITH SUPERUSER PASSWORD 'ORturkeyeggs';
  
5. Close psql by entering the '\q' command in the psql session and hitting enter 

#### You now have the pieces needed to connect to the DB using Jupyter Notebook. Open a Jupyter Notebook by following the steps below.
1. Activate the Jupyter Notebook environment with the command below
  * activate-data-science
  * you should now see this prompt in your terminal session: '(data-science) vagrant@hackoregon-base'
  

2. Start the Jupyter notebook server
  * your prompt in the VM terminal should still look like this: '(data-science) hack@hackoregon-base'
  * if it doesn't enter this command: 'activate-data-science'
  * finally start notebook server with: 'jupyternb' 
  
3. Then using your web browser go to: http://localhost:7777/tree
  * you should now see the file structure of the VM inside a Jupyter notebook in your browser 

#### Creating a new notebook so you can start writing scripts and connect to the DB

1. Create a new notebook
  * Click the 'new' tab on the right side of the page 
  * select the '[conda env:data-science]' notebook option
  
2. You should now have a notebook open with one cell inside it. 

#### Add the snippet below to the notebook's cell to test the DB connection 

```python
import psycopg2
import pandas as pd

# enter the values below from your VM, the defaults are already set change if needed
DATABASE = 'postgres'
USER = 'turkey'
PASSWORD = 'ORturkeyeggs'

# makes connection to DB
conn = psycopg2.connect(database=DATABASE, user=USER, password=PASSWORD, host='localhost', port=5432)

# use pandas to query postgres and create a dataframe
pd.read_sql_query('SELECT * FROM information_schema.information_schema_catalog_name;', con=conn)
```

#### After adding the above code the the first cell in your notebook and running it you should see this output: 

|   | **catalog_name** |
|---|------------------|
| 0 | postgres         |

##### See the docs for psycopg2 here: http://initd.org/psycopg/docs/usage.html
