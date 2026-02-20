# 🔌 Mapeo Completo de Cableado Físico — Práctica 1, Grupo 34

> **#Grupo = 7** (3+4=7). Todos los cálculos ya están hechos con este valor.

---

## 📐 Resumen de la Topología

| Elemento | Cantidad |
|----------|----------|
| Routers 2901 | 4 (Router0–Router3) |
| Switches 2960-24TT | 24 (Switch0–Switch23) |
| PCs | 8 (PC0–PC7) |
| Laptops | 2 (Laptop0, Laptop1) |
| **Total de cables** | **53** |

### Puertos disponibles por dispositivo

| Dispositivo | Puertos |
|------------|---------|
| Router 2901 | Gi0/0, Gi0/1 |
| Switch 2960-24TT | Fa0/1–Fa0/24, Gi0/1, Gi0/2 |
| PC / Laptop | FastEthernet0 |

---

## 🔗 1. Conexiones Router ↔ Router

| # | Origen | Puerto | ← Cable →| Puerto | Destino | Red | Protocolo |
|---|--------|--------|-----------|--------|---------|-----|-----------|
| 1 | Router0 | **Gi0/0** | Cross-Over | **Gi0/0** | Router1 | 10.10.10.0/24 | EIGRP |
| 2 | Router1 | **Gi0/1** | Cross-Over | **Gi0/0** | Router2 | 10.10.9.0/24 | RIP |
| 3 | Router2 | **Gi0/1** | Cross-Over | **Gi0/0** | Router3 | 10.10.8.0/24 | OSPF |

### IPs de enlaces entre routers

| Dispositivo | Interfaz | IP | Protocolo |
|------------|----------|-----|-----------|
| Router0 | Gi0/0 | 10.10.10.1/24 | EIGRP |
| Router1 | Gi0/0 | 10.10.10.2/24 | EIGRP |
| Router1 | Gi0/1 | 10.10.9.1/24 | RIP |
| Router2 | Gi0/0 | 10.10.9.2/24 | RIP |
| Router2 | Gi0/1 | 10.10.8.1/24 | OSPF |
| Router3 | Gi0/0 | 10.10.8.2/24 | OSPF |

---

## 🔗 2. Conexiones Router ↔ Switch (Trunk)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Modo |
|---|--------|--------|-----------|--------|---------|------|
| 4 | Router0 | **Gi0/1** | Straight-Through | **Gi0/1** | Switch0 | Trunk |
| 5 | Router3 | **Gi0/1** | Straight-Through | **Gi0/1** | Switch12 | Trunk |

> Router0 Gi0/1 → sub-interfaces para VLANs 17, 27, 37
> Router3 Gi0/1 → sub-interfaces para VLANs 67, 77, 87

---

## 🔗 3. EDIFICIO IZQUIERDO — Switch ↔ Switch (19 cables)

### Capa 1 → Capa 2 (SW0 baja a SW1, SW2, SW3)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 6 | Switch0 | **Fa0/1** | Straight-Through | **Gi0/1** | Switch1 | Trunk |
| 7 | Switch0 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch2 | Trunk |
| 8 | Switch0 | **Fa0/3** | Straight-Through | **Gi0/1** | Switch3 | Trunk |

### Capa 2 → Capa 3 (SW1, SW2, SW3 bajan a SW4, SW5, SW6)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 9 | Switch1 | **Fa0/1** | Straight-Through | **Gi0/1** | Switch4 | Trunk |
| 10 | Switch1 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch5 | Trunk ⚡ |
| 11 | Switch2 | **Fa0/1** | Straight-Through | **Gi0/2** | Switch5 | Trunk ⚡ |
| 12 | Switch3 | **Fa0/1** | Straight-Through | **Fa0/1** | Switch5 | Trunk ⚡ |
| 13 | Switch3 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch6 | Trunk |

> ⚡ = SW1, SW2 y SW3 **todos** bajan a SW5, creando rutas redundantes (PVST gestiona los loops).

### Cross-Links Capa 3 (SW4 ↔ SW5 ↔ SW6)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 14 | Switch4 | **Fa0/1** | Straight-Through | **Fa0/2** | Switch5 | Trunk 🔄 |
| 15 | Switch5 | **Fa0/3** | Straight-Through | **Fa0/1** | Switch6 | Trunk 🔄 |

> 🔄 = Cross-links horizontales entre switches de la misma capa. PVST decidirá cuáles bloquear.

### Capa 3 → Capa 4 Access (bajada a SW7–SW11)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 16 | Switch4 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch7 | Trunk |
| 17 | Switch4 | **Fa0/3** | Straight-Through | **Gi0/1** | Switch8 | Trunk |
| 18 | Switch5 | **Fa0/4** | Straight-Through | **Gi0/2** | Switch7 | Trunk ⚡ |
| 19 | Switch5 | **Fa0/5** | Straight-Through | **Gi0/2** | Switch8 | Trunk ⚡ |
| 20 | Switch5 | **Fa0/6** | Straight-Through | **Gi0/1** | Switch9 | Trunk |
| 21 | Switch5 | **Fa0/7** | Straight-Through | **Gi0/1** | Switch10 | Trunk |
| 22 | Switch5 | **Fa0/8** | Straight-Through | **Gi0/1** | Switch11 | Trunk |
| 23 | Switch6 | **Fa0/2** | Straight-Through | **Gi0/2** | Switch10 | Trunk ⚡ |
| 24 | Switch6 | **Fa0/3** | Straight-Through | **Gi0/2** | Switch11 | Trunk ⚡ |

> ⚡ = Doble uplink: SW7 y SW8 reciben de SW4 y SW5. SW10 y SW11 reciben de SW5 y SW6. PVST gestiona la redundancia.

---

## 🔗 4. EDIFICIO DERECHO — Switch ↔ Switch (19 cables)

### Capa 1 → Capa 2 (SW12 baja a SW13, SW14, SW15)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 25 | Switch12 | **Fa0/1** | Straight-Through | **Gi0/1** | Switch13 | Trunk |
| 26 | Switch12 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch14 | Trunk |
| 27 | Switch12 | **Fa0/3** | Straight-Through | **Gi0/1** | Switch15 | Trunk |

### Capa 2 → Capa 3 (SW13, SW14, SW15 bajan a SW16, SW17, SW18)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 28 | Switch13 | **Fa0/1** | Straight-Through | **Gi0/1** | Switch16 | Trunk |
| 29 | Switch13 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch17 | Trunk ⚡ |
| 30 | Switch14 | **Fa0/1** | Straight-Through | **Gi0/2** | Switch17 | Trunk ⚡ |
| 31 | Switch15 | **Fa0/1** | Straight-Through | **Fa0/1** | Switch17 | Trunk ⚡ |
| 32 | Switch15 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch18 | Trunk |

> ⚡ = SW13, SW14 y SW15 **todos** bajan a SW17 (hub central del lado derecho). Rapid PVST gestiona los loops.

### Cross-Links Capa 3 (SW16 ↔ SW17 ↔ SW18)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 33 | Switch16 | **Fa0/1** | Straight-Through | **Fa0/2** | Switch17 | Trunk 🔄 |
| 34 | Switch17 | **Fa0/3** | Straight-Through | **Fa0/1** | Switch18 | Trunk 🔄 |

> 🔄 = Cross-links horizontales. Rapid PVST decidirá cuáles bloquear.

### Capa 3 → Capa 4 Access (bajada a SW19–SW23)

| # | Origen | Puerto | ← Cable → | Puerto | Destino | Tipo |
|---|--------|--------|-----------|--------|---------|------|
| 35 | Switch16 | **Fa0/2** | Straight-Through | **Gi0/1** | Switch19 | Trunk |
| 36 | Switch16 | **Fa0/3** | Straight-Through | **Gi0/1** | Switch20 | Trunk |
| 37 | Switch17 | **Fa0/4** | Straight-Through | **Gi0/2** | Switch19 | Trunk ⚡ |
| 38 | Switch17 | **Fa0/5** | Straight-Through | **Gi0/2** | Switch20 | Trunk ⚡ |
| 39 | Switch17 | **Fa0/6** | Straight-Through | **Gi0/1** | Switch21 | Trunk |
| 40 | Switch17 | **Fa0/7** | Straight-Through | **Gi0/1** | Switch22 | Trunk |
| 41 | Switch17 | **Fa0/8** | Straight-Through | **Gi0/1** | Switch23 | Trunk |
| 42 | Switch18 | **Fa0/2** | Straight-Through | **Gi0/2** | Switch22 | Trunk ⚡ |
| 43 | Switch18 | **Fa0/3** | Straight-Through | **Gi0/2** | Switch23 | Trunk ⚡ |

> ⚡ = Doble uplink: SW19 y SW20 reciben de SW16 y SW17. SW22 y SW23 reciben de SW17 y SW18. SW21 solo recibe de SW17.

---

## 🔗 5. Conexiones Switch → Host (Access — 10 cables)

### Edificio Izquierdo

| # | Switch | Puerto | ← Cable → | Host | Puerto Host | VLAN | Nombre |
|---|--------|--------|-----------|------|-------------|------|--------|
| 44 | Switch7 | **Fa0/1** | Straight-Through | PC0 | FastEthernet0 | **17** | Primaria 🟢 |
| 45 | Switch8 | **Fa0/1** | Straight-Through | PC1 | FastEthernet0 | **27** | Básicos 🔒🩷 |
| 46 | Switch9 | **Fa0/1** | Straight-Through | Laptop0 | FastEthernet0 | **27** | Básicos 🔒🩷 |
| 47 | Switch10 | **Fa0/1** | Straight-Through | PC2 | FastEthernet0 | **37** | Bachillerato 🟠 |
| 48 | Switch11 | **Fa0/1** | Straight-Through | PC3 | FastEthernet0 | **37** | Bachillerato 🟠 |

### Edificio Derecho

| # | Switch | Puerto | ← Cable → | Host | Puerto Host | VLAN | Nombre |
|---|--------|--------|-----------|------|-------------|------|--------|
| 49 | Switch19 | **Fa0/1** | Straight-Through | PC4 | FastEthernet0 | **77** | Básicos 🔒🩷 |
| 50 | Switch20 | **Fa0/1** | Straight-Through | PC5 | FastEthernet0 | **67** | Primaria 🟢 |
| 51 | Switch21 | **Fa0/1** | Straight-Through | Laptop1 | FastEthernet0 | **67** | Primaria 🟢 |
| 52 | Switch22 | **Fa0/1** | Straight-Through | PC6 | FastEthernet0 | **87** | Bachillerato 🟠 |
| 53 | Switch23 | **Fa0/1** | Straight-Through | PC7 | FastEthernet0 | **87** | Bachillerato 🟠 |

> 🔒 = Requiere **Port-Security** (máx 1 MAC, violation shutdown). Solo VLANs "Básicos" (27 y 77).

---

## 🌐 6. Direccionamiento IP de Hosts

### Edificio Izquierdo (Gateway = Router0 sub-interfaces Gi0/1)

| Host | VLAN | IP | Máscara | Gateway |
|------|------|----|---------|---------|
| PC0 | 17 Primaria | 192.178.17.2 | 255.255.255.0 | 192.178.17.1 |
| PC1 | 27 Básicos | 192.178.27.2 | 255.255.255.0 | 192.178.27.1 |
| Laptop0 | 27 Básicos | 192.178.27.3 | 255.255.255.0 | 192.178.27.1 |
| PC2 | 37 Bachillerato | 192.178.37.2 | 255.255.255.0 | 192.178.37.1 |
| PC3 | 37 Bachillerato | 192.178.37.3 | 255.255.255.0 | 192.178.37.1 |

### Edificio Derecho (Gateway = Router3 sub-interfaces Gi0/1)

| Host | VLAN | IP | Máscara | Gateway |
|------|------|----|---------|---------|
| PC4 | 77 Básicos | 192.178.77.2 | 255.255.255.0 | 192.178.77.1 |
| PC5 | 67 Primaria | 192.178.67.2 | 255.255.255.0 | 192.178.67.1 |
| Laptop1 | 67 Primaria | 192.178.67.3 | 255.255.255.0 | 192.178.67.1 |
| PC6 | 87 Bachillerato | 192.178.87.2 | 255.255.255.0 | 192.178.87.1 |
| PC7 | 87 Bachillerato | 192.178.87.3 | 255.255.255.0 | 192.178.87.1 |

---

## 🏷️ 7. Sub-interfaces Router-on-a-Stick

### Router0 — Gi0/1 (Edificio Izquierdo)

| Sub-interfaz | VLAN | IP | Encapsulación |
|-------------|------|----|---------------|
| Gi0/1.17 | 17 | 192.178.17.1/24 | dot1Q 17 |
| Gi0/1.27 | 27 | 192.178.27.1/24 | dot1Q 27 |
| Gi0/1.37 | 37 | 192.178.37.1/24 | dot1Q 37 |

### Router3 — Gi0/1 (Edificio Derecho)

| Sub-interfaz | VLAN | IP | Encapsulación |
|-------------|------|----|---------------|
| Gi0/1.67 | 67 | 192.178.67.1/24 | dot1Q 67 |
| Gi0/1.77 | 77 | 192.178.77.1/24 | dot1Q 77 |
| Gi0/1.87 | 87 | 192.178.87.1/24 | dot1Q 87 |

---

## 🔧 8. VTP y Hostnames

### Configuración VTP

| Switch | Rol VTP | Dominio | Password secreta |
|--------|---------|---------|-----------------|
| **Switch0** | **Server** | G7 | redes2grupo7 |
| Switch1–Switch11 | Client | G7 | — |
| **Switch12** | **Server** | G7 | redes2grupo7 |
| Switch13–Switch23 | Client | G7 | — |

> VTP no cruza routers. Se necesita un server por edificio.

### Hostnames

| Switch | Hostname | | Switch | Hostname |
|--------|----------|-|--------|----------|
| Switch0 | SW1_G7 | | Switch12 | SW13_G7 |
| Switch1 | SW2_G7 | | Switch13 | SW14_G7 |
| Switch2 | SW3_G7 | | Switch14 | SW15_G7 |
| Switch3 | SW4_G7 | | Switch15 | SW16_G7 |
| Switch4 | SW5_G7 | | Switch16 | SW17_G7 |
| Switch5 | SW6_G7 | | Switch17 | SW18_G7 |
| Switch6 | SW7_G7 | | Switch18 | SW19_G7 |
| Switch7 | SW8_G7 | | Switch19 | SW20_G7 |
| Switch8 | SW9_G7 | | Switch20 | SW21_G7 |
| Switch9 | SW10_G7 | | Switch21 | SW22_G7 |
| Switch10 | SW11_G7 | | Switch22 | SW23_G7 |
| Switch11 | SW12_G7 | | Switch23 | SW24_G7 |

---

## 🗺️ 9. Resumen de Puertos Usados por Switch

### Switch0 (4 puertos usados)
```
Gi0/1 ← Router0 Gi0/1 (trunk)
Fa0/1 → SW1 Gi0/1 (trunk)
Fa0/2 → SW2 Gi0/1 (trunk)
Fa0/3 → SW3 Gi0/1 (trunk)
```

### Switch1 (3 puertos)
```
Gi0/1 ← SW0 Fa0/1 (trunk)
Fa0/1 → SW4 Gi0/1 (trunk)
Fa0/2 → SW5 Gi0/1 (trunk)
```

### Switch2 (2 puertos)
```
Gi0/1 ← SW0 Fa0/2 (trunk)
Fa0/1 → SW5 Gi0/2 (trunk)
```

### Switch3 (3 puertos)
```
Gi0/1 ← SW0 Fa0/3 (trunk)
Fa0/1 → SW5 Fa0/1 (trunk)
Fa0/2 → SW6 Gi0/1 (trunk)
```

### Switch4 (4 puertos)
```
Gi0/1 ← SW1 Fa0/1 (trunk)
Fa0/1 → SW5 Fa0/2 (trunk) 🔄 cross-link
Fa0/2 → SW7 Gi0/1 (trunk)
Fa0/3 → SW8 Gi0/1 (trunk)
```

### Switch5 — HUB CENTRAL IZQUIERDO (10 puertos)
```
Gi0/1 ← SW1 Fa0/2 (trunk)
Gi0/2 ← SW2 Fa0/1 (trunk)
Fa0/1 ← SW3 Fa0/1 (trunk)
Fa0/2 ← SW4 Fa0/1 (trunk) 🔄 cross-link
Fa0/3 → SW6 Fa0/1 (trunk) 🔄 cross-link
Fa0/4 → SW7 Gi0/2 (trunk) ⚡ dual-uplink
Fa0/5 → SW8 Gi0/2 (trunk) ⚡ dual-uplink
Fa0/6 → SW9 Gi0/1 (trunk)
Fa0/7 → SW10 Gi0/1 (trunk)
Fa0/8 → SW11 Gi0/1 (trunk)
```

### Switch6 (4 puertos)
```
Gi0/1 ← SW3 Fa0/2 (trunk)
Fa0/1 ← SW5 Fa0/3 (trunk) 🔄 cross-link
Fa0/2 → SW10 Gi0/2 (trunk) ⚡ dual-uplink
Fa0/3 → SW11 Gi0/2 (trunk) ⚡ dual-uplink
```

### Switch7 (3 puertos)
```
Gi0/1 ← SW4 Fa0/2 (trunk)
Gi0/2 ← SW5 Fa0/4 (trunk) ⚡ dual-uplink
Fa0/1 → PC0 Fa0 (access VLAN 17)
```

### Switch8 (3 puertos)
```
Gi0/1 ← SW4 Fa0/3 (trunk)
Gi0/2 ← SW5 Fa0/5 (trunk) ⚡ dual-uplink
Fa0/1 → PC1 Fa0 (access VLAN 27) 🔒
```

### Switch9 (2 puertos)
```
Gi0/1 ← SW5 Fa0/6 (trunk)
Fa0/1 → Laptop0 Fa0 (access VLAN 27) 🔒
```

### Switch10 (3 puertos)
```
Gi0/1 ← SW5 Fa0/7 (trunk)
Gi0/2 ← SW6 Fa0/2 (trunk) ⚡ dual-uplink
Fa0/1 → PC2 Fa0 (access VLAN 37)
```

### Switch11 (3 puertos)
```
Gi0/1 ← SW5 Fa0/8 (trunk)
Gi0/2 ← SW6 Fa0/3 (trunk) ⚡ dual-uplink
Fa0/1 → PC3 Fa0 (access VLAN 37)
```

---

### Switch12 (4 puertos)
```
Gi0/1 ← Router3 Gi0/1 (trunk)
Fa0/1 → SW13 Gi0/1 (trunk)
Fa0/2 → SW14 Gi0/1 (trunk)
Fa0/3 → SW15 Gi0/1 (trunk)
```

### Switch13 (3 puertos)
```
Gi0/1 ← SW12 Fa0/1 (trunk)
Fa0/1 → SW16 Gi0/1 (trunk)
Fa0/2 → SW17 Gi0/1 (trunk)
```

### Switch14 (2 puertos)
```
Gi0/1 ← SW12 Fa0/2 (trunk)
Fa0/1 → SW17 Gi0/2 (trunk)
```

### Switch15 (3 puertos)
```
Gi0/1 ← SW12 Fa0/3 (trunk)
Fa0/1 → SW17 Fa0/1 (trunk)
Fa0/2 → SW18 Gi0/1 (trunk)
```

### Switch16 (4 puertos)
```
Gi0/1 ← SW13 Fa0/1 (trunk)
Fa0/1 → SW17 Fa0/2 (trunk) 🔄 cross-link
Fa0/2 → SW19 Gi0/1 (trunk)
Fa0/3 → SW20 Gi0/1 (trunk)
```

### Switch17 — HUB CENTRAL DERECHO (9 puertos)
```
Gi0/1 ← SW13 Fa0/2 (trunk)
Gi0/2 ← SW14 Fa0/1 (trunk)
Fa0/1 ← SW15 Fa0/1 (trunk)
Fa0/2 ← SW16 Fa0/1 (trunk) 🔄 cross-link
Fa0/3 → SW18 Fa0/1 (trunk) 🔄 cross-link
Fa0/4 → SW19 Gi0/2 (trunk) ⚡ dual-uplink
Fa0/5 → SW20 Gi0/2 (trunk) ⚡ dual-uplink
Fa0/6 → SW21 Gi0/1 (trunk)
Fa0/7 → SW22 Gi0/1 (trunk)
Fa0/8 → SW23 Gi0/1 (trunk)
```

### Switch18 (4 puertos)
```
Gi0/1 ← SW15 Fa0/2 (trunk)
Fa0/1 ← SW17 Fa0/3 (trunk) 🔄 cross-link
Fa0/2 → SW22 Gi0/2 (trunk) ⚡ dual-uplink
Fa0/3 → SW23 Gi0/2 (trunk) ⚡ dual-uplink
```

### Switch19 (3 puertos)
```
Gi0/1 ← SW16 Fa0/2 (trunk)
Gi0/2 ← SW17 Fa0/4 (trunk) ⚡ dual-uplink
Fa0/1 → PC4 Fa0 (access VLAN 77) 🔒
```

### Switch20 (3 puertos)
```
Gi0/1 ← SW16 Fa0/3 (trunk)
Gi0/2 ← SW17 Fa0/5 (trunk) ⚡ dual-uplink
Fa0/1 → PC5 Fa0 (access VLAN 67)
```

### Switch21 (2 puertos)
```
Gi0/1 ← SW17 Fa0/6 (trunk)
Fa0/1 → Laptop1 Fa0 (access VLAN 67)
```

### Switch22 (3 puertos)
```
Gi0/1 ← SW17 Fa0/7 (trunk)
Gi0/2 ← SW18 Fa0/2 (trunk) ⚡ dual-uplink
Fa0/1 → PC6 Fa0 (access VLAN 87)
```

### Switch23 (3 puertos)
```
Gi0/1 ← SW17 Fa0/8 (trunk)
Gi0/2 ← SW18 Fa0/3 (trunk) ⚡ dual-uplink
Fa0/1 → PC7 Fa0 (access VLAN 87)
```

---

## 📊 10. Conteo Total de Cables

| Tipo de Conexión | Cantidad |
|-----------------|----------|
| Router ↔ Router | 3 |
| Router ↔ Switch | 2 |
| Switch ↔ Switch (Edificio Izquierdo) | 19 |
| Switch ↔ Switch (Edificio Derecho) | 19 |
| Switch ↔ Host | 10 |
| **TOTAL** | **53 cables** |

### Leyenda
- 🔄 = Cross-link horizontal (redundancia STP entre switches de la misma capa)
- ⚡ = Dual-uplink (access switch con 2 caminos hacia arriba — STP gestiona redundancia)
- 🔒 = Requiere Port-Security (VLAN Básicos: máx 1 MAC, violation shutdown)
