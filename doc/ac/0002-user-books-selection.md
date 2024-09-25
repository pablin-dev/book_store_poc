# User Selection Books Flow

## Acceptance criteria

### Empty Cart

**AR-01**: User is Logged in and has Empty Cart

* Preconditions:
  * Valid username and password
  * Empty cart
* Input: Valid login credentials
* Expected Result: The system displays an empty cart confirmation

Edge Cases:

* Invalid login credentials:
  * System displays error message indicating incorrect username or password.
  * User is not logged in.
* Cart is already full when user logs in:
  * System displays a "cart full" message, preventing user from logging in.

**CR-01**: Search for Books when Cart is Empty

* Preconditions: Valid username and password, empty cart
* Input: Empty cart, search query (books title, author, etc.)
* Expected Result: The system displays a list of relevant books

Edge Cases:

* Invalid search query:
  * System displays error message indicating invalid search query.
  * No books are displayed in the search results.
* Cart is filled when user searches for books:
  * System prevents searching for books until cart is empty.

**AR-02**: Add Book to Cart Successfully

* Preconditions: Valid username and password, empty cart, book selection
* Input: Valid login credentials, selected book
* Expected Result: The book is added to the cart successfully

Edge Cases:

* Duplicate book addition:
  * System prevents adding duplicate books to cart.
  * Error message is displayed indicating that the book is already in the cart.
* Book quantity exceeds maximum allowed:
  * System prevents adding more than the maximum allowed quantity of a book.
  * Error message is displayed indicating that the maximum quantity has been reached.

**FR-01**: Check if Cart is Full when Adding Book

* Preconditions: Valid username and password, cart is not full, book selection
* Input: Valid login credentials, selected book
* Expected Result: The system allows adding the book to the cart without errors

Edge Cases:

* Cart capacity exceeded:
  * System prevents adding more books to the cart.
  * Error message is displayed indicating that the cart is full.

**CR-02**: Search for Books when Cart is Not Empty

* Preconditions: Valid username and password, non-empty cart
* Input: Non-empty cart, search query (books title, author, etc.)
* Expected Result: The system displays a list of relevant books, considering the existing cart contents

Edge Cases:

* Invalid search query:
  * System displays error message indicating invalid search query.
  * No books are displayed in the search results.
* Cart is filled when user searches for books:
  * System prevents searching for books until cart is empty.

**FR-02**: Check if Cart is Full when Adding Book to Non-Empty Cart

* Preconditions: Valid username and password, non-empty cart, book selection
* Input: Valid login credentials, selected book
* Expected Result: The system checks if adding the book exceeds the maximum cart capacity without errors

Edge Cases:

* Cart capacity exceeded:
  * System prevents adding more books to the cart.
  * Error message is displayed indicating that the cart is full.

**AR-03**: Payment Generation Flow Initiated when Cart is Full

* Preconditions: Valid username and password, full cart
* Input: Valid login credentials
* Expected Result: The system initiates the payment generation flow

Edge Cases:

* Invalid payment information:
  * System displays error message indicating invalid payment information.
  * Payment generation flow is not initiated.
* Cart is empty when user tries to pay:
  * System prevents initiating the payment generation flow until cart is full.
