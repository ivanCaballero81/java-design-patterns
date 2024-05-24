---
title: Table Module
category: Data access
language: en
tag:
    - Data access
    - Encapsulation
    - Persistence
---

## Also known as

* Record Set

## Intent

Encapsulates database table data access logic in a single module, allowing for easy retrieval and manipulation of data without exposing the database structure.

## Explanation

Real-world example

> Imagine a library where all books are stored in a large database. The Table Module design pattern can be compared to a librarian who manages a specific section of the library, such as the fiction section. This librarian is responsible for all tasks related to that section: adding new books, updating book information, removing old books, and providing information about the books when requested. Just like the Table Module pattern encapsulates all the database access logic for a particular table, the librarian handles all operations related to the fiction section without exposing the complexities of how books are cataloged, stored, and managed to the library users. This makes it easier for the library users to interact with the fiction section, as they can rely on the librarian to efficiently manage and retrieve books without needing to understand the underlying details.

In plain words

> The Table Module pattern centralizes and encapsulates database access logic for a specific table, simplifying data retrieval and manipulation while hiding database complexities.

**Programmatic Example**

In the user system example, the domain logic for user login and registration needs to be managed. By using the Table Module pattern, we can create an instance of the `UserTableModule` class to encapsulate and handle all business logic associated with the rows in the user table.

Here is the basic `User` entity.

```java
@Setter
@Getter
@ToString
@EqualsAndHashCode
@AllArgsConstructor
public class User {
  private int id;
  private String username;
  private String password;
}
```

Here is the `UserTableModule` class.

```java
public class UserTableModule {
  private final DataSource dataSource;
  private Connection connection = null;
  private ResultSet resultSet = null;
  private PreparedStatement preparedStatement = null;

  public UserTableModule(final DataSource userDataSource) {
    this.dataSource = userDataSource;
  }
  
  public int login(final String username, final String password) throws SQLException {
  		// Method implementation
  }

  public int registerUser(final User user) throws SQLException {
  		// Method implementation
  }
}
```

In the class `App`, we use an instance of the `UserTableModule` to handle user login and registration.

```java
// Create data source and create the user table.
final var dataSource = createDataSource();
createSchema(dataSource);
userTableModule = new UserTableModule(dataSource);

//Initialize two users.
var user1 = new User(1, "123456", "123456");
var user2 = new User(2, "test", "password");

//Login and register using the instance of userTableModule.
userTableModule.registerUser(user1);
userTableModule.login(user1.getUsername(), user1.getPassword());
userTableModule.login(user2.getUsername(), user2.getPassword());
userTableModule.registerUser(user2);
userTableModule.login(user2.getUsername(), user2.getPassword());

deleteSchema(dataSource);
```

The program output:

```java
12:22:13.095 [main] INFO com.iluwatar.tablemodule.UserTableModule - Register successfully!
12:22:13.117 [main] INFO com.iluwatar.tablemodule.UserTableModule - Login successfully!
12:22:13.128 [main] INFO com.iluwatar.tablemodule.UserTableModule - Fail to login!
12:22:13.136 [main] INFO com.iluwatar.tablemodule.UserTableModule - Register successfully!
12:22:13.144 [main] INFO com.iluwatar.tablemodule.UserTableModule - Login successfully!
```

In this example, the `UserTableModule` class is responsible for encapsulating all interactions with the users table in the database. This includes the logic for logging in and registering users. The User class represents the user entity with attributes such as `id`, `username`, and `password`.

1. **Initialization**: The `UserTableModule` is initialized with a `DataSource` object which is used to establish a connection to the database.
2. **User Registration**: The `registerUser` method in `UserTableModule` handles the logic for registering a new user into the database.
3. **User Login**: The `login` method manages the logic for authenticating a user based on their username and password.
4. **Application Flow**: The `App` class demonstrates how to use the `UserTableModule` to register and log in users. The data source is created, the schema is set up, and users are registered and logged in with appropriate feedback.

This example shows how the Table Module pattern centralizes database operations for the `users` table, making the application more modular and easier to maintain.

## Class diagram

![Table Module](./etc/table-module.urm.png "Table Module")

## Applicability

* Use when you need to manage data access logic for a database table in a centralized module.
* Ideal for applications that interact heavily with database tables and require encapsulation of database queries.
* Suitable for systems where the database schema may evolve over time, as the changes can be managed within the table module.

## Known Uses

* In enterprise applications where multiple modules need to interact with the same database tables.
* Web applications that require CRUD operations on database tables.
* Java-based ORM frameworks such as Hibernate or JPA utilize similar concepts for managing data access.

## Consequences

Benefits:

* Centralizes and encapsulates data access logic, leading to easier maintenance.
* Reduces code duplication by providing a single point of interaction for database tables.
* Enhances code readability and reduces the risk of SQL injection attacks by encapsulating query logic.

Trade-offs:

* May lead to a large module if the table has many operations, potentially reducing readability.
* Can become a bottleneck if not properly optimized, especially in high-load scenarios.

## Related Patterns

* Active Record: Unlike Table Module, Active Record combines data access and domain logic in the same class.
* [Data Access Object (DAO)](https://java-design-patterns.com/patterns/dao/): Provides an abstract interface to some type of database or other persistence mechanism, often used alongside Table Module to separate low-level data access operations from high-level business logic.
* [Data Mapper](https://java-design-patterns.com/patterns/data-mapper/): Separates the in-memory objects from the database, unlike Table Module which directly maps database tables.
* [Domain Model](https://java-design-patterns.com/patterns/domain-model/): Represents the domain logic and behavior, often used in conjunction with Table Module to handle complex business rules and data interactions.
* [Repository](https://java-design-patterns.com/patterns/repository/): Abstracts the data layer, allowing more complex queries, whereas Table Module is usually simpler and table-centric.
* [Transaction Script](https://java-design-patterns.com/patterns/transaction-script/): Organizes business logic by procedures where each procedure handles a single request from the presentation layer, contrasting with the Table Module's data-centric approach.

## Credits

* [Core J2EE Patterns: Best Practices and Design Strategies](https://amzn.to/4cAbDap)
* [Java Persistence with Hibernate](https://amzn.to/44tP1ox)
* [Patterns of Enterprise Application Architecture](https://amzn.to/3WfKBPR)
* [Architecture patterns: Domain model and friends - Inviqa](https://inviqa.com/blog/architecture-patterns-domain-model-and-friends)
* [Table Module Pattern](http://wiki3.cosc.canterbury.ac.nz/index.php/Table_module_pattern)