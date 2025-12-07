```mermaid 
graph LR
    User([User / Client])
    
    %% --- FASTAPI SERVER ---
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
    
    %% --- AGENT SYSTEM ---
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
    
    %% Dark Theme Styling (High Contrast)
    classDef server fill:#0d47a1,stroke:#82b1ff,color:#ffffff,stroke-width:2px;
    classDef agent fill:#4a148c,stroke:#ce93d8,color:#ffffff,stroke-width:2px;
    classDef storage fill:#bf360c,stroke:#ffab91,color:#ffffff,stroke-width:2px;
    
    class MainEntry,RouterMenu,RouterOrder,RouterRAG,SvcMenu,SvcOrder,SvcRAG server;
    class Agent,LLM,ToolSearch,ToolCreate,ToolView,ToolConfirm agent;
    class DB,KB storage;

```
    class Agent,LLM,ToolSearch,ToolCreate,ToolView,ToolConfirm agent;
    class DB,KB db;
