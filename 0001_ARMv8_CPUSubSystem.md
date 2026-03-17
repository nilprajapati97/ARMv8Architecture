# SoC Architecture – Visual Layout (Closer to Image)

```mermaid
flowchart LR

%% ================= CPU =================
subgraph CPU["CPU Subsystem (big.LITTLE)"]
    direction TB
    subgraph A7["A7 Cluster"]
        A7C1["A7"]
        A7C2["A7"]
        A7C3["A7"]
        A7C4["A7"]
        A7L2["L2 Cache"]
    end

    subgraph A15["A15 Cluster"]
        A15C1["A15"]
        A15C2["A15"]
        A15L2["L2 Cache"]
    end

    CCI["Coherent Interconnect"]
    A7 --> A7L2 --> CCI
    A15 --> A15L2 --> CCI
end

%% ================= TOP BUS =================
NOC["FlexNoC Top Level Interconnect"]

CPU --> NOC

%% ================= TOP ROW =================
subgraph TOP["Design-Specific Subsystems"]
    direction LR

    GPU["GPU (3D Graphics)"]

    subgraph DSP["DSP Subsystem"]
        DSPIP["IP Blocks"]
        DSPINT["FlexWay"]
        DSPIP --> DSPINT
    end

    subgraph APP["Application IP"]
        APPIP["IP Blocks"]
        APPINT["FlexWay"]
        APPIP --> APPINT
    end

    MULTI["Multimedia (AES / 2D / MPEG)"]
end

GPU --> NOC
DSPINT --> NOC
APPINT --> NOC
MULTI --> NOC

%% ================= BOTTOM ROW =================
subgraph BOTTOM["System & I/O"]
    direction LR

    subgraph MEM["Memory"]
        MS["Scheduler"]
        MC["Controller"]
        DDR["DDR"]
        MS --> MC --> DDR
    end

    subgraph WIRED["Wired I/O"]
        USB["USB"]
        PCIE["PCIe"]
        ETH["Ethernet"]
    end

    subgraph WIRELESS["Wireless"]
        WIFI["WiFi"]
        GSM["GSM"]
        LTE["LTE"]
    end

    subgraph SEC["Security"]
        CRYPTO["Crypto FW"]
        RSA["RSA Engine"]
    end

    subgraph IO["I/O Peripherals"]
        HDMI["HDMI"]
        MIPI["MIPI"]
        DISP["Display"]
        PMU["PMU"]
        JTAG["JTAG"]
    end
end

MEM --> NOC
WIRED --> NOC
WIRELESS --> NOC
SEC --> NOC
IO --> NOC

%% ================= RIGHT SIDE =================
EXT["InterChip Links"]
NOC --> EXT
