# ARMv8 CPU Subsystem – Mermaid Diagram

```mermaid
flowchart TB
    subgraph CPU["CPU Subsystem"]

        subgraph A7["A7 Cluster (Efficiency Cores)"]
            A7C0["A7 Core 0"]
            A7C1["A7 Core 1"]
            A7C2["A7 Core 2"]
            A7C3["A7 Core 3"]
            A7L2["Shared L2 Cache"]
            A7C0 --> A7L2
            A7C1 --> A7L2
            A7C2 --> A7L2
            A7C3 --> A7L2
        end

        subgraph A15["A15 Cluster (Performance Cores)"]
            A15C0["A15 Core 0"]
            A15C1["A15 Core 1"]
            A15C2["A15 Core 2"]
            A15C3["A15 Core 3"]
            A15L2["Shared L2 Cache"]
            A15C0 --> A15L2
            A15C1 --> A15L2
            A15C2 --> A15L2
            A15C3 --> A15L2
        end

    end

    CCI["Coherent Interconnect (CCI)"]

    MEM["System Memory (RAM / I-O)"]

    A7L2 --> CCI
    A15L2 --> CCI
    CCI --> MEM
