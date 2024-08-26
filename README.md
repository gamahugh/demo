# Flowchart for `post_tree_promptlets_for_shop` Function

```mermaid
flowchart TD
    A(Start) --> B(Check config access)
    B -->|Fails| C(HTTP 403 Forbidden) 
    C --> D(Exit)
    B -->|Succeeds| E(Parse JSON from request)
    E --> |Not a list| F(HTTP 400 Bad Request) 
    F --> G(Exit)
    E --> |Is a list| H(Initialize empty list)
    
    H --> I(Loop through promptlet updates)
    I --> J(Check required params)
    J --> |Missing params| K(HTTP 400 Bad Request)
    K --> L(Exit)
    J --> |Params present| M(Add IDs to list)
    
    M --> N(Retrieve existing promptlets from DB)
    N --> O(Sort promptlets)
    O --> P(Loop through sorted updates)
    
    P --> Q{Update promptlet?}
    Q --> |Yes| R(Update promptlet text)
    R --> S(Process submission)
    S --> P
    
    Q --> |No| T(Create promptlet?)
    T --> |Yes| U(Create new promptlet)
    U --> V{Parent ID present?}
    V --> |Yes| W(Find parent promptlet)
    W --> X{Parent found?}
    X --> |Yes| Y(Set parent ID and append to parent)
    Y --> Z(Add new promptlet to DB)
    
    X --> |No| Z
    V --> |No| Z
    
    T --> |No| AA(Delete promptlet?)
    AA --> |Yes| AB(Disable promptlet)
    AB --> AC(Commit DB transaction)
    AC --> P
    AA --> |No| AC
    
    AC --> AD(Split scopes)
    AD --> AE(Loop through scopes)
    AE --> AF(Retrieve promptlet tree from DB)
    AF --> AG(Return promptlets as response)
    AG --> AH(End)
