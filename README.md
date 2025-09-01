# Implementing-Hybrid-R53-and-on-premise-DNS
In this demonstration I will integrate the DNS platforms of AWS and a linux based, simulated on-premises environment using Route53 inbound and outbound endpoints.

The demo consists of 4 stages, each implementing additional components of the architecture
```
    Stage 1 - Provision and Verify
    Stage 2 - Connectivity
    Stage 3 - Inbound R53 Endpoint (Onprem => AWS)
    Stage 4 - Outbound R53 Endpoint (AWS => Onprem)
```

## Architecture

<img width="1079" height="566" alt="image" src="https://github.com/user-attachments/assets/aac5778f-0e70-4301-92d4-b00568410fbd" />
