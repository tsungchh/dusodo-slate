# Chapter 11 System

## Separate constructing a system from using it ##

```java
public Service getService() { 
  if (service == null)
    service = new MyServiceImpl(...); // Good enough default for most cases? return service;
}

```

- Don't mix constructing with runtime execuation logic
- Lazy initialization is convenient, but no!
- Should have a global, consistent strategy for resolving dependencies

### Solutions ###

- Dependency Injection
  * DI container initiate object using factory pattern
  * DI container inject dependency using class setter(Inversion of Control)
- Decouple runtime from implementation of construction


## Separation of Concerns ##

> EJB2 example (Enterprise Java Bean)

```java
package com.example.banking; 
import java.util.Collections; 
import javax.ejb.*;
public abstract class Bank implements javax.ejb.EntityBean { // Business logic...
  public abstract String getStreetAddr1();
  public abstract String getStreetAddr2();
  public abstract String getCity();
  public abstract String getState();
  public abstract String getZipCode();
  public abstract void setStreetAddr1(String);
  public abstract void setStreetAddr2(String);
  public abstract void setCity(String city);
  public abstract void setState(String state);
  public abstract void setZipCode(String zip);
  public abstract Collection getAccounts();
  public abstract void setAccounts(Collection accounts); 
  public void addAccount(AccountDTO accountDTO) {
    InitialContext context = new InitialContext();
    AccountHomeLocal accountHome = context.lookup("AccountHomeLocal"); AccountLocal account = accountHome.create(accountDTO);
    Collection accounts = getAccounts();
    accounts.add(account);
  }
  // EJB container logic
  public abstract void setId(Integer id);
  public abstract Integer getId();
  public Integer ejbCreate(Integer id) { ... }
  public void ejbPostCreate(Integer id) { ... }
  // The rest had to be implemented but were usually empty: public void setEntityContext(EntityContext ctx) {} public void unsetEntityContext() {}
  public void ejbActivate() {}
  public void ejbPassivate() {}
  public void ejbLoad() {}
  public void ejbStore() {}
  public void ejbRemove() {}
}
```
- Class should have only one responsibility (Single Responsibility Principle)
- Example: tight coupling of bean class with business logic
  * Hard to test
  * Undermine OO concept


## Cross-cutting Concerns ##

> Proxy Pattern

```java
// Bank.java (suppressing package names...) 
import java.utils.*;

// The abstraction of a bank. 
public interface Bank {
  Collection<Account> getAccounts();
  void setAccounts(Collection<Account> accounts); 
}

// BankImpl.java 
import java.utils.*;

// The “Plain Old Java Object” (POJO) implementing the abstraction. 
public class BankImpl implements Bank {
  private List<Account> accounts;
  public Collection<Account> getAccounts() { 
    return accounts;
  }

  public void setAccounts(Collection<Account> accounts) {
    this.accounts = new ArrayList<Account>(); 
    for (Account account: accounts) {
      this.accounts.add(account); 
    }
  } 
}


// BankProxyHandler.java 
import java.lang.reflect.*; 
import java.util.*;

// “InvocationHandler” required by the proxy API.
public class BankProxyHandler implements InvocationHandler {
  private Bank bank;
  public BankHandler (Bank bank) { this.bank = bank;
}

// Method defined in InvocationHandler
public Object invoke(Object proxy, Method method, Object[] args)
throws Throwable {
  String methodName = method.getName(); 
  if (methodName.equals("getAccounts")) {
    bank.setAccounts(getAccountsFromDatabase());
    return bank.getAccounts();
  } else if (methodName.equals("setAccounts")) {
    bank.setAccounts((Collection<Account>) args[0]); setAccountsToDatabase(bank.getAccounts());
    return null;
  } else {
    ... }
  }
  // Lots of details here
  protected Collection<Account> getAccountsFromDatabase() { ... }
  protected void setAccountsToDatabase(Collection<Account> accounts) { ... }
}

// Somewhere else...
Bank bank = (Bank) Proxy.newProxyInstance( 
  Bank.class.getClassLoader(),
  new Class[] { Bank.class },
  new BankProxyHandler(new BankImpl()));

```

- How can we share common works(logging, transaction, etc.) in different codebases?
- Use AOP(aspect-oriented programming) via Proxy Pattern
  * Proxy class execute target class's method using decarator way
  * Put \*cross-cutting concerns\* inside proxy class

##How EJB improves using AOP #

> EJB final version

```java
package com.example.banking.model; 
import javax.persistence.*; 
import java.util.ArrayList; 
import java.util.Collection;

@Entity
@Table(name = "BANKS")
public class Bank implements java.io.Serializable {
  @Id @GeneratedValue(strategy=GenerationType.AUTO) 
  private int id;

  @Embeddable // An object “inlined” in Bank’s DB row 
  public class Address {
    protected String streetAddr1; 
    protected String streetAddr2; 
    protected String city; 
    protected String state; 
    protected String zipCode;
  }


  @Embedded
  private Address address;

  @OneToMany(cascade = CascadeType.ALL, fetch = FetchType.EAGER, 
             mappedBy="bank")

  private Collection<Account> accounts = new ArrayList<Account>();
  
  public int getId() { 
    return id;
  }
  
  public void setId(int id) { 
    this.id = id;
  }
  
  public void addAccount(Account account) { 
    account.setBank(this); 
    accounts.add(account);
  }

  public Collection<Account> getAccounts() { 
    return accounts;
  }
  
  public void setAccounts(Collection<Account> accounts) { 
    this.accounts = accounts;
  } 
}

```

-  Removing mixing logic(db and business related) 
-  Using AOP to move cross-cutting concerns to top level(config/annotation)
-  Keep target class focus on logic, so easy to test!



## Test Drive the System Architecture ##

- Let's each component focus on their jobs(business logic, caching, security virtualization etc..)
- Different component integrated together with Aspect-oriented programming
- Easy to test, easy to evolve


## Domain Specific Language(DSL) ##

- todo 