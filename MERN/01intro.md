# MERN Stack

- **M**ongo db
- **E**xpress
- **R**eact
- **N**ode.js

```mermaid
flowchart LR
    subgraph "Front-end(browser)"
    A[React App]
    end
    subgraph "Back-end(server)"
    B[Express/Node API]
    C[MongoDB]
    end

    A<-->|JSON|B
    B<-->C 
```
When we need to interact with the back-end (such as send an authentication request) we send it to the back-end <br>

We typically interact with a database (like MongoDB) to authenticate, sign-in, sign off etc.

This separation is required to not expose any sensititve information in the front-end (ie. React)

**Goal:** Workout Buddy Website 

