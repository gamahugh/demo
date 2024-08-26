flowchart TD
    A(Start) --> B(check_config_access)
    B -->|If fails| C(HTTP 403 Forbidden) 
    C --> D(Exit)
    B -->|Success| E(Parse JSON from request)
    E --> |If not a list| F(HTTP 400 Bad Request) 
    F --> G(Exit)
    E --> |Is list| H(Initialize empty list: existing_promptlet_ids)
    
    H --> I(For each promptlet in promptlet_updates)
    I --> J(Check if "type" and "id" are present)
    J --> |If missing| K(HTTP 400 Bad Request)
    K --> L(Exit)
    J --> |Present| M(Add "id" and "parent_id" to existing_promptlet_ids)
    
    M --> N(Retrieve existing promptlets by IDs from DB)
    N --> O(Sort promptlet_updates)
    O --> P(Sort by: promptlets without parent_id first)
    
    P --> Q(For each promptlet in sorted promptlet_updates)
    Q --> |type == "update"| R{If promptlet exists}
    R --> |Exists| S(Update promptlet_text)
    S --> T(Process submission)
    T --> Q
    
    R --> |Not found| U(HTTP 404 Not Found)
    U --> V(Exit)
    
    Q --> |type == "create"| W(Create new Promptlet)
    W --> X{If parent_id is present}
    X --> |Present| Y(Find parent_promptlet)
    Y --> Z{If found}
    Z --> |Found| AA(Set new_promptlet's parent_id and append to parent’s children)
    AA --> AB(Add new_promptlet to DB and existing_promptlets_by_id)
    
    Z --> |Not found| AB
    X --> |Not present| AB
    
    Q --> |type == "delete"| AC{If promptlet exists}
    AC --> |Exists| AD(Disable promptlet (set enabled = False))
    AD --> AE(Commit DB transaction)
    AE --> Q
    AC --> |Not found| AE
    
    AE --> AF(Split scopes by comma into return_scopes)
    AF --> AG(For each scope in return_scopes)
    AG --> AH(Retrieve promptlet tree from DB for the scope)
    AH --> AI(Return all retrieved promptlets as the response)
    AI --> AJ(End)
