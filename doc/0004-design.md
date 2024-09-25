# 3. correctness and resiliency

Date: 2024-08-13

## Status

Accepted

## Context

Consider a **book store** in a shopping mall. The **customer** selects the **books** from racks to purchase and put them into a shopping **Cart**. The customer brings the **Cart** with the selected **books** to cashier. The cashier scans each item with checkout **system** to prepare an **order**. The cashier **requests to customer for payment**. The customer gives **credit card** to cashier. The **verifier and checkout system scans the card**. The **verifier accepts the card and payment is accepted**. **Customer signs the credit card** slip. The **purchased books are handed over to customer**.

## Main Goal

- Create an online shopping that allow clients to search, select and purchase books.
- The system architecture should be **compositional distributed system**
- Service oriented architecture (SOA) (Microservices)

### Sub Goals

- The system should be distributed, secure and scalable.
- Availability "five nines", fault-tolerant

## Design

### UML Structural diagrams

- Architecture
- Component

#### UML Behavioral diagrams
  
##### Information Flow (security)

```mermaid
%%{init: {'theme':'neutral'}}%%
flowchart LR
    subgraph User
        A(User)
    end

    subgraph Customer
        B(Customer)
    end

    subgraph Credit Card
        C(Credit Card)
    end

    subgraph Book
        D(Book)
    end

    subgraph Order
        E(Order)
    end

    A --> B
    B --> C
    B --> D
    B --> E
    C --> E
    D --> E
```

##### Use Case

##### Activity

##### State Machine

##### Class Diagram

```mermaid
%%{init: {'theme':'neutral'}}%%
classDiagram
    Customer "1" --> "1..*" Card: add/remove/get/modify
    Customer "1" --> "1..*" Cart: Order books
    Customer "1" --> "1..*" CustomerStore: login/register
    Cart --> BookStore : Get books
    BookStore "1" --> "1..*" Book: Book Repository
    Cart --> Order: Generate
    Order --> Payment: Generate

    class CustomerStore{
        List~Customer~ customers
        +get_customer(customerId): Customer
        +add_customer(Customer): customerId
        +remove_customer(customerId): bool
        +modify_customer(customerId, Customer): Customer
    }

    class BookStore {
        List~int~ books
        +add_book(Book): bookId
        +remove_book(bookId): bool
        +modify_book(bookId, Book): Book
        +search_by_author(): List~Book~
        +search_by_title(): Book
    }

    class PaymentService {
        List~Payment~ payments
        +get_payment(paymentId): Payment
        +add_payment(Payment): paymentId
        +remove_book(paymentId): bool
        +modify_book(paymentId, Payment): Payment
        +search_by_customer(customerId): List~Payment~
    }

    class Customer {
        int id
        string name
        string address
        secret email
        secret password
        option(Cart) cart
        +login()
        +logout()
    }

    class Card {
        int id
        int CustomerId
        string currency
        secret card_number
        secret expire_date
        secret cvc
        +add_card(customerId): cardId
        +get_card(cardId): Card
        +remove_card(cardId): bool
        +modify_card(cardId, Card): Card
    }

    class Book {
        int id
        string title
        string author
        double price
        int quantity
        +get_title(int id)
        +get_author(int id)
        +get_price(int id)
        +get_quantity(int id)
    }

    class Cart{
        List~int~ books
        +add_book(bookId): Book
        +remove_book(bookId): bool
        +calculate_total_price(): double
        +generate_order(): bool
    }

    class Order{
        int id
        int customerId
        List~int~ books
        date order_date
        double total_amount
        +new_order(customerId): bool
        +add_book(bookId): Book
        +cancel_order(): bool
        +get_order_date(): date
        +get_total_amount(): double
    }

    class Payment{
        int id
        int orderId
        double amount
        date paymentDate
        +process_payment(order_id): bool
    }
```

##### Book Persistence

##### Payment Persistence

```mermaid
%%{init: {'theme':'neutral'}}%%
classDiagram
    PaymentPort "1..*" --> "1" PaymentStore: add/remove/get/modify
    class PaymentPort{
        int id
        int orderId
        double amount
        date paymentDate
        +process_payment(order_id): bool
    }

    class PaymentStore {
        List~Payment~ payments
        +get_payment(paymentId): Payment
        +add_payment(Payment): paymentId
        +remove_book(paymentId): bool
        +modify_book(paymentId, Payment): Payment
        +search_by_customer(customerId): List~Payment~
    }
```

##### Sequence

```mermaid
%%{init: {'theme':'forest'}}%%
sequenceDiagram
    actor Customer
    actor System
    actor PaymentService
    Customer-->>System: login()
    System-->> CustomerStore: identify
    CustomerStore-->>System: Customer
    System-->>Customer: login OK
    Customer-->>System: search_by_author() 
    System-->>Store: search_by_author()
    Store-->>System: List<Book>
    System-->>Customer: List<Book>
    Customer-->>System: new_cart()
    System-->>Cart: new_cart()
    System-->>Customer: Cart
    loop
        Customer-->>System: add_book()
        System-->>Cart: add_book(book_id)
        System-->>Customer: Ok/Fail
    end
        Customer-->>System: get_total_payment()
        System-->>Order: get_total_amount()
        System-->>Customer: total_amount
        Customer-->>System: Create Order
        System-->>Order: new_order()
        Order-->>System: orderId
        System-->>Cart: Delete
        System-->>Customer: Order created
    alt payment
        Customer-->>System: execute payment
        System-->>Payment: process_payment(order_id)
        System-->>PaymentService: check and pay
        PaymentService-->>System: Ok/Fail
        System-->>Customer: Ok/Fail
    else cancel_payment
        Customer-->>System: cancel_payment()
        System-->>Payment: cancel_payment()
        System-->>Order: cancel_order()
        System-->> Customer: Order cancelled
    end
```

##### Communication

```mermaid
---
title: Register System into a PaymentSystem
---
sequenceDiagram
    box Yellow System & SystemClient
        actor System
        actor SystemClient
    end
    box LightBlue PaymentServer & PaymentService
        actor PaymentServer
        actor PaymentService
    end

    System-->>SystemClient: register(System)
    critical Establish a connection to the PaymentService
        SystemClient-->>PaymentServer: TLS RPC/Rest Message(System, register)
        PaymentServer-->>PaymentService: process_register(System)
        PaymentService-->>PaymentServer: result(bool, tx_id, system_id)
        PaymentServer-->>SystemClient: TLS RPC/Rest Message(Result(bool, tx_id, system_id))
        SystemClient-->>System: result(bool, tx_id, system_id)
        System-->>System: register_payment_service(system_id)
    option Network Timeout
        SystemClient-->>PaymentServer: TLS RPC/Rest Message(System, register)
        SystemClient-->>System: timeout
        System-->>System: register_timeout_payment_service(system_id)
        System-->>System: Log Error
    end
```

```mermaid
---
title: Process Customer Payment on PaymentSystem
---
sequenceDiagram
    actor System
    actor SystemClient
    actor PaymentServer
    actor PaymentService

    System-->>SystemClient: process_payment(Payment)
    critical Establish a connection to the PaymentService
        SystemClient-->>PaymentServer: RPC/Rest Message(system_id, Payment, process_payment)
        PaymentServer-->>PaymentService: process_payment(Payment)
        PaymentService-->>PaymentServer: result(bool, tx_id)
        PaymentServer-->>SystemClient: RPC/Rest Message(Result(bool, tx_id))
        SystemClient-->>System: result(bool, tx_id)
    option Credentials rejected
        SystemClient-->>PaymentServer: TLS RPC/Rest Message(System, register)
        PaymentServer-->>SystemClient: Credential Rejected
        System-->>System: Log Error
    option Network Timeout
        SystemClient-->>PaymentServer: TLS RPC/Rest Message(System, register)
        SystemClient-->>System: timeout
        System-->>System: register_timeout_payment_service(system_id)
        System-->>System: Log Error
    end
```

```mermaid
flowchart LR
    Start -- Place Order --> B[Order Processing]
    B --> C[Accounts]
    B --> D[Dispatch]
    B --> E[Order History]
    D --> F[Order Dispatched]
```

### Prototyping

- Implementation
- Unit Tests
- Integration Tests

#### Login

authentication executable specification (analyses)

![authentication behavior](image-8.png)

Design

- could be a web login
- or fingerprint login
- or face recognition login

```plantuml
@startsalt
title Login
header Google
footer Smart Software Solutions

{+
  Login    | "UserName   "
  Password | "****     "
  [Cancel] | [  OK   ]
}
@endsalt
```

## Decision

The change that we're proposing or have agreed to implement.

## Consequences

What becomes easier or more difficult to do and any risks introduced by the change that will need to be mitigated.
