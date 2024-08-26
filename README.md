# Flowchart for `post_tree_promptlets_for_shop` Function

```mermaid
flowchart TD
    A(Start) --> B(Check config access)
    B -->|If fails| C(HTTP 403 Forbidden) 
    C --> D(Exit)
    B -->|Success| E(Parse JSON from request)
    E --> |If not a list| F(HTTP 400 Bad Request) 
    F --> G(Exit)
    E --> |Is list| H(Initialize empty list)
    
    H --> I(Loop through promptlet updates)
    I --> J(Check required params)
    J --> |If missing| K(HTTP 400 Bad Request)
    K --> L(Exit)
    J --> |Present| M(Add IDs to list)
    
    M --> N(Retrieve existing promptlets from DB)
    N --> O(Sort promptlets)
    O --> P(Loop through sorted updates)
    
    P --> |type == "update"| Q{If promptlet exists}
    Q --> |Exists| R(Update promptlet text)
    R --> S(Process submission)
    S --> P
    
    Q --> |Not found| T(HTTP 404 Not Found)
    T --> U(Exit)
    
    P --> |type == "create"| V(Create new promptlet)
    V --> W{If parent ID is present}
    W --> |Present| X(Find parent promptlet)
    X --> Y{If found}
    Y --> |Found| Z(Set parent ID and append to parent)
    Z --> AA(Add new promptlet to DB)
    
    Y --> |Not found| AA
    W --> |Not present| AA
    
    P --> |type == "delete"| AB{If promptlet exists}
    AB --> |Exists| AC(Disable promptlet)
    AC --> AD(Commit DB transaction)
    AD --> P
    AB --> |Not found| AD
    
    AD --> AE(Split scopes)
    AE --> AF(Loop through scopes)
    AF --> AG(Retrieve promptlet tree from DB)
    AG --> AH(Return promptlets as response)
    AH --> AI(End)
