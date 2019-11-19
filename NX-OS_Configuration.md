# NX-OS Telemetry Configuration

### Event-based

> Needs to be configured with sample-interval 0

#### Interface Discovery / Up|Down State
```
  sensor-group 1
    path sys/intf depth unbounded query-condition query-target=subtree&query-target-filter=or(deleted(),created())
  sensor-group 2
    path sys/intf depth unbounded filter-condition or(and(updated(ethpmPhysIf.operSt),eq(ethpmPhysIf.operSt,"down")),and(updated(ethpmPhysIf.operSt),eq(ethpmPhysIf.operSt,"up")),and(updated(ethpmLbRtdIf.operSt),eq(ethpmLbRtdIf.operSt,"down")),and(updated(ethpmLbRtdIf.operSt),eq(ethpmLbRtdIf.operSt,"up")),and(updated(ethpmEncRtdIf.operSt),eq(ethpmEncRtdIf.operSt,"down")),and(updated(ethpmEncRtdIf.operSt),eq(ethpmEncRtdIf.operSt,"up")))
```

#### Failure of fan, dim, flash, sprom, and lc

```
  sensor-group 5
    path sys/ch depth unbounded filter-condition or(and(updated(eqptFan.operSt),ne(eqptFan.operSt,"ok")),ne(eqptDimm.operSt,"ok"),ne(eqptFlash.operSt,"ok"),ne(eqptSpromSup.operSt,"ok"),ne(eqptSpromLc.operSt,"ok"))
```

#### Discovery of anything in sys/ch (chassis related)

```
  sensor-group 6
    path sys/ch depth unbounded query-condition query-target=subtree&query-target-filter=or(deleted(),created())
```

### Routing/Forwarding

#### BGP

> RR

> Have trouble to scale on real fabric with 60k routes. Needs chunking to be activated on NX-OS.

> NB: using 9.2(3) there is a bug limiting the output of routes sent over Telemetry using BGP ephemeral to 7k

```
  sensor-group 10
    data-source NX-API
    path "show bgp ipv4 unicast vrf all”
```
Workaround to just count the prefixes.
```
  sensor-group 11
    data-source NX-API
    path "show bgp l2vpn evpn summary"
  sensor-group 12
    data-source NX-API
    path "show bgp ipv4 unicast summary"
```
Using DME Ephemeral, we need to speficy VRF.
```
  sensor-group 8
    path sys/bgp/inst/dom-VRF1/af-ipv4-ucast depth unbounded query-condition rsp-foreign-subtree=ephemeral
  sensor-group 9
    path sys/bgp/inst/dom-VRF2/af-ipv4-ucast depth unbounded query-condition rsp-foreign-subtree=ephemeral
  sensor-group 10
    path sys/bgp/inst/dom-VRF3/af-ipv4-ucast depth unbounded query-condition rsp-foreign-subtree=ephemeral
```
But retrieving all VRF works for L2VPN EVPN AF
```
    path sys/bgp/inst/dom-default/af-l2vpn-evpn depth 1 query-condition rsp-foreign-subtree=ephemeral
```

#### OSPF/ISIS

> All
```
  sensor-group 1
    path sys/ospf depth 4
  sensor-group 2
    data-source NX-API
    path "show isis adjacency"
```

#### LLDP

### VXLAN EVPN

#### VNI and NVE Peers

> Leaves
```
  sensor-group 5
    path sys/eps/epId-1/peers depth 2
    path sys/eps/epId-1/nws depth 2
```

### CPU/Mem/Temperature

> All
```
  sensor-group 1
    path sys/ch/supslot-1/sup depth 1
    path sys/procsys depth 1
    path sys/ch depth 4
  subscription X
    dst-grp Y
    snsr-grp 1 sample-interval 5000
```

### Interfaces/MAC/VLAN

> Leaves and RR (Super spine)

> DME interface counters updates every 36 seconds, lower sample-interval needs to use NX-API.
```
  sensor-group 2
    data-source NX-API
    path "show interface counters"
```
VLAN and MAC
```
  sensor-group 3
    path sys/bd depth 2
    path sys/mac/table depth 1
```