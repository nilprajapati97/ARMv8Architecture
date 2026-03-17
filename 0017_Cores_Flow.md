# ARM CPUSS

## Overview
- Octa-core architecture  
- Big.LITTLE architecture  

## Core Types
- Cortex-A57 → High performance applications  
- Cortex-A53 → Power saving, low performance applications  

## Core Configuration
- Flexible mix of high-performance and low-performance cores  
- Example configuration:
  - 1 × Cortex-A57  
  - 7 × Cortex-A53  

## Core Roles
- One core acts as the **primary core**  
- Remaining 7 cores act as **secondary cores**  

### Boot Behavior
- Primary core boots first on reset  
- Primary core is responsible for booting the remaining cores  
