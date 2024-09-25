# User Login Flow

## Acceptance criteria

### Valid Login

**AR-01**: When a valid username and password are entered, the system displays the Books Selection Flow page.

* Preconditions: Valid username and password
* Input: Valid login credentials
* Expected Result: The Books Selection Flow page is displayed

**AR-02**: When a valid username and password are entered, the system does not display an error message or prompt to re-enter credentials.

* Preconditions: Valid username and password
* Input: Valid login credentials
* Expected Result: No error message or prompt to re-enter credentials is displayed

### Invalid Login

**AR-03**: When an invalid username is entered, the system prompts the user to re-enter their credentials.

* Preconditions: Invalid username
* Input: Invalid login credentials
* Expected Result: A prompt to re-enter credentials is displayed

**AR-04**: When an invalid password is entered, the system prompts the user to re-enter their credentials.

* Preconditions: Invalid password
* Input: Invalid login credentials
* Expected Result: A prompt to re-enter credentials is displayed

### System Behavior

**AR-05**: The system does not allow multiple concurrent login attempts with the same username and password.

* Preconditions: Valid username and password, multiple concurrent login attempts
* Input: Multiple concurrent login attempts
* Expected Result: Only one successful login attempt is allowed

**AR-06**: The system logs each login attempt and stores the data for auditing purposes.

* Preconditions: Valid or invalid username and password
* Input: Login credentials
* Expected Result: Login attempts are logged and stored

### Edge Cases

**AR-07**: When no username is entered, the system prompts the user to enter a valid username.

* Preconditions: No username entered
* Input: Empty login form
* Expected Result: A prompt to enter a valid username is displayed

**AR-08**: When an empty password is entered, the system prompts the user to enter a valid password.

* Preconditions: Empty password
* Input: Empty login form
* Expected Result: A prompt to enter a valid password is displayed
