#Accessing the PostgeSQL DB on a virtual machine 

###This is a brief description of how to access the Postgres DB that's on your virtual machine(VM) inside Jupyter Notebook 

####First we need to create a new postgres user so we can connect to the DB server using this user in a Jupyter Notebook

1. From your home terminal ssh into the virtual machine by entering the command below
  * ssh hack@localhost -p2222
  * when prompted enter password: 'ORturkeyeggs'
  
2. In VM terminal session switch to the postgres user using command below
  * sudo su postgres
  * when prompted enter password: 'ORturkeyeggs'
  
3. Open a psql session using command below
  * psql 
  * you should now see this prompt: 'postgres=#'
  
4. Inside psql session create a new super user using command below
  * CREATE USER turkey WITH SUPERUSER PASSWORD 'ORturkeyeggs';
  
5. Close psql by entering the '\q' command in the psql session and hitting enter 

#### Now that you have a new superuser created we need to activate the Jupyter Notebook virtual environment and install the Python psycopg2 package that lets us make connections to the DB

1. Activate the Jupyter Notebook environment with the command below
  * activate-data-science
  * you should now see this prompt in your terminal session: '(data-science) hack@hackoregon-base'
  
2. Use pip to install psycopg2 using the following command
  * pip install psycopg2
  
#### You now have the pieces needed to connect to the DB using Jupyter Notebook. Open a Jupyter Notebook by following the steps below.

1. Start the Jupyter notebook server
  * your prompt in the VM terminal should still look like this: '(data-science) hack@hackoregon-base'
  * if it doesn't enter this command: 'activate-data-science'
  * finally enter 'jupyter notebook --ip 0.0.0.0 --no-browser' to start the notebook server
  
2. Then using your web browser go to: http://localhost:7777/tree
  * you should now see the file structure of the VM inside a Jupyter notebook in your browser 

#### Creating a new notebook so you can start writing scripts and connect to the DB

1. Create a new notebook
  * Click the 'new' tab on the right side of the page 
  * select the '[conda env:data-science]' notebook option
  
2. You should now have a notebook open with one cell inside it. 

#### Add the snippet below to the notebook's cell to test the DB connection 


    import psycopg2
    
    # enter the values below from your VM, the defaults are already set change if needed
    DATABASE = 'postgres'
    USER = 'turkey'
    PASSWORD = 'ORturkeyeggs'
    
    # makes connection to DB
    conn = psycopg2.connect(database=DATABASE, user=USER, password=PASSWORD, host='localhost', port=5432)
    
    # makes cursor that lets you make queries to the DB
    cur = conn.cursor()

    def connection_test():
        sql_state = "SELECT * FROM information_schema.information_schema_catalog_name;"
        cur.execute(sql_state)

        data = cur.fetchall()
        print(data)

    connection_test()

    cur.close()

#### After adding the above code the the first cell in your notebook and running it you should see this output: '[('postgres',)]'

##### See the docs for psycopg2 here: http://initd.org/psycopg/docs/usage.html
