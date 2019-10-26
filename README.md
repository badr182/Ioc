# Dependency Injection and Inversion of Control 


To understand the advantage of inversion of control and injection of dependency let's imagine we have class user and this class depends on class MysqlDatabase

What is exactly inversion of control ? 

Objects do not create other objects on which they rely to do their work 
Instead, they get the objects that they need from an outside source
( for example an XML configuration file )



```java
    /**
     * Buisness Layer Logic  
     */    
    public class User {
 
        MysqlDatabase database;
 
        public User( ){
            database = new MysqlDatabase();
        }
 
        public void add(String data){
            database.persist(data);
        }
 
    }
    
    /**
     * Database Access Layer
     */
    public class MysqlDatabase{
 
        public void persist(String data){
            System.out.println("Mysql has persisted:"+ data);
        }
 
    }
```

```java
  public class Ioc {

      public static void main( String[] args ){
          Ioc container = new Ioc();

          User user = container.new User() ;     
          user.add("This is some data");

      }
  }
```


For now, everything is good but if someone or some developer went to add another Database like Oracle or Postgres to user class, it's become so hard.

what we should do is inverse the control flow 
instead of user class handling the life cycle of database object,  I'm just gonna have someone or somebody else do it for me 

Let’s update user class and Ioc class

```java
   /**
    * Buisness Layer Logic  
    */
    public class User {
 
        MysqlDatabase database;
 
        public User( MysqlDatabase database ){
            this.database = database;
        }
 
        public void add(String data){
            database.persist(data);
        }
 
    }

```

Passing MysqlDatabase like parameter this concept is what we called  Dependency injection 

Dependency injection generally means passing a dependent object as a parameter to a method,
rather than having the method create the dependent object. 

What it means in practice is that the method does not have a direct dependency on particular implementation; any implementation that meets the requirements can be passed as a parameter

```java
  public class Ioc {

      public static void main( String[] args ){
          Ioc container = new Ioc();

          User user = container.new User( container.new MysqlDatabase()) ;     
          user.add("This is some data");

      }
  }

```
Now let's add a new Database Oracle 
```java
    /**
     * Database Access Layer
     */
    public class OraclDatabase {
 
        public void persist(String data){
            System.out.println("Oracl has persisted:"+ data);
        }
 
    }

```
In order to use OracLDatabase, we should pass an instance of OraclDatabase

```java
public class Ioc {
 
    public static void main( String[] args ){
        Ioc container = new Ioc();
    
        User user = container.new User( container.new OraclDatabase() ) ;     
        user.add("This is some data");
 
    }
}
```
by doing that  we have error because we don't support different databases in our class User 
and that is very problematic, to remedy this problem we are going to use dependency inversion  this is one of principal SOLID design pattern 
So dependency inversion says that we need to rely on abstraction rather than concrete implementation  

for that, we must create an interface Database
```java
   public interface Database{ 
   
        void persist( String data);        
        
   }

```
Now let’s implement interface Database in our class MysqlDatabase and OraclDatabese
And also inject this interface in User instead of MysalDatabse.
By doing this implementation we resolve the problem and without worry for the type of database that we will use 

```java
   /**
    * Buisness Layer Logic  
    */
    public class User {
 
        Database database;
 
        public User( Database database ){
            this.database = database;
        }
 
        public void add(String data){
            database.persist(data);
        }
 
    }

    
    /**
    * Database Access Layer
    */
    public class MysqlDatabase implements Database{
 
        public void persist(String data){
            System.out.println("Mysql has persisted:"+ data);
        }
 
    }
 
    /**
     * Database Access Layer
     */
    public class OraclDatabase implements Database {
 
        public void persist(String data){
            System.out.println("Oracl has persisted:"+ data);
        }
 
    }

```
