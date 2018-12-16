# Data Persistence



#### Data Persistence

the capability of retaining program state beyond a program's current life cycle, such as termination due to loss of power or computer shutdown.

can be achieved by saving program state in a non-volatile storage medium.

requires a conversion from in-memory program state to and from a serializable data format.

* Volatile Storage
  * Data is not retatined when powered off
  * Extremely fast but expensive
  * ex) RAM (memory)
* Non-Volatile Storage
  * Data is retained through a power cycle
  * Extremely slow and inexpensive
  * ex) Hard Drive (or SSD), File, Database



#### Data Serialization

* Saving
* Restoring



#### DBMS (Database Management System)

a software system that exposes an interface for interacting with a database while abstracting the complexities associated with data persistence.



#### Relational DB

\- using Structured Query Languages(SQL)

\- require to define schema before storing

\- represent data in tables and rows

\- hard to store rich data type

\- support atomic transaction

\- expensive to build and maintain



#### No-Relational DB (No-SQL)

\- using Object Querying

\- no schema

\- represend data as collections of JSON documents

\- able to store any type of data

\- does not support atomic transaction