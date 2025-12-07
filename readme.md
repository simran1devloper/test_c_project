```mermaid 
graph TD
User([User / Client])
    
    subgraph "FastAPI Server"
        direction TB
        MainEntry["Main Entry\n(Backend/api/main.py)"]
        
        subgraph "Routers"
            RouterMenu["/menu"]
            RouterOrder["/order"]
            RouterRAG["/rag"]
        end
        
        subgraph "Services"
            SvcMenu["Menu Svc"]
            SvcOrder["Order Svc"]
            SvcRAG["RAG Svc"]
        end
        
        DB[(SQLite)]
        KB[(Chroma DB)]
    end
    
    subgraph "Agent System"
        direction TB
        Agent["Food Agent"]
        LLM["LLM (gpt-4o)"]
        
        subgraph "Tools"
            ToolSearch["search_menu"]
            ToolCreate["create_order"]
            ToolView["view_order"]
            ToolConfirm["confirm_order"]
        end
    end

    %% Main Request Flow
    User -->|HTTP| MainEntry
    
    MainEntry --> RouterMenu
    MainEntry --> RouterOrder
    MainEntry --> RouterRAG
    
    RouterMenu --> SvcMenu
    RouterOrder --> SvcOrder
    RouterRAG --> SvcRAG
    
    SvcMenu --> DB
    SvcOrder --> DB
    SvcRAG --> KB
    
    %% Agent Interaction
    RouterRAG -->|"/query"| Agent
    Agent <-->|Context| KB
    Agent <-->|Prompt| LLM
    
    Agent --> ToolSearch
    Agent --> ToolCreate
    Agent --> ToolView
    Agent --> ToolConfirm
    
    %% Loopback (Dotted Lines)
    ToolSearch -.->|GET /menu/search| MainEntry
    ToolCreate -.->|POST /order/create| MainEntry
    ToolView -.->|GET /order/{id}| MainEntry
    ToolConfirm -.->|POST /order/{id}| MainEntry
    
    %% Styling
    classDef server fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef agent fill:#f3e5f5,stroke:#4a148c,stroke-width:2px;
    classDef storage fill:#fff3e0,stroke:#e65100,stroke-width:2px;
    
    class MainEntry,RouterMenu,RouterOrder,RouterRAG,SvcMenu,SvcOrder,SvcRAG server;
    class Agent,LLM,ToolSearch,ToolCreate,ToolView,ToolConfirm agent;
    class DB,KB storage;
```
    class Agent,LLM,ToolSearch,ToolCreate,ToolView,ToolConfirm agent;
    class DB,KB db;
