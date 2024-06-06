# laboratorio di reti 
## livello network
- `ctrl+m ` nuova macchina
- `ctrl+s` nuovo switch
- `ctrl+d` nuovo cavo dritto
    - per far comunicare dispositivi di __diverso__ livello
- `ctrl+c` nuovo cavo incrociato
    - per dispositivi dello __stesso__ livello

### configurazione di rete
`/etc/network/interfaces` oppure se si vuole usare + file indipendenti `/etc/network/interfaces.d/*`

`nmcli` sovrascrive le regole di `/etc/network/interfaces`

direttive interfaccia di rete

- `iface ethN inet static`
    - attiva un interfaccia definita in modo statico 
- `address `
- `netmask`
- `broadcast`
- `gateway`

esempio

``` bash

auto eth0
iface eth0 inet static
    address 192.168.1.1
```

__attenzione__ auto fa attivare al avvio dei sistemi le interfacce di rete

`ifdown eth0 && ifup eth0` per disattivare e ri attivare un interfaccia di rete `-a` le attiva tutte

### consultare la configurazione

- `ifconfig [-a] [name]`  __attenzione__ se l'interfaccia __non__ è attiva non verrà visualizata dal comando ma verrà visualizata con `-a`

+ `ip addr show dev [name]`

## verifica della configurazione

+ connessioni livello 2:
    - arping 
    - `arping -i eth0 192.168.1.254`
    - devono essere stesso dominio __broadcast__
+ connessioni livello 3
    +  ping

```bash 
ifdown eth0
editor /etc/network/interfaces  # modificatelo a piacimento
ifup eth0
```
`service networking restart`  per un restart


per testare se una configurazione è ottimale meglio usare configurazione temporanea

`ip addr {add, change, replace} dev <interfaccia>  <ip>` oppure 
`ifconfig <iface> <ip-address> [up]`

per attivare le interfaccie di rete sneza che vadano nei file di configurazione, due comandi:
- `ip link set dev <iface> {up,down}`
- `ifconfig <iface> {up,down}` 

rimuovere interfaccia di rete

- `ip addr del <address> dev <iface>`
- `ifconfig <iface> 0`

## livello ip 
## ip forwarding 
possibilità di accettare pacchetti e mandarli verso altre reti.
per abilitarla usare il comando temporaneo `sysctl -w net.ipv4.ip_forward=1`
per farlo in modo definitivo
`sysctl -p /etc/sysctl.conf` dopo aver messo il parametro `=1` nel file.


per veserele tabelle di routing `route -n` oppune `/sbin/route` o `ip route show`

3 diversi tipi di routing da aggiungere 
 
 1) routing tramite singolo host
    -  `route add <HOST> gw <gateway>`
    - se voglio xontattare `host` devo contattare `gateway`
2) routing per sottorete
    - `route add -net <net> netmask <netmask> gw <GW>`
    - per parlare con la rete `net` inoltrare alla macchina `gw`
3) default gateway
    + `route add default gw 192.168.1.254`
    + se nessuna delle regole precedenti funziona mandare al gatway
## VLAN

* access link (non sanno di far parte di una Vlan)

* Trunk link connette dispositivi che sanno di essere in una VLAN

### vlan trunk 
sul dispositivo si setta 
`ip link add link <eth fisica> <virtual interface> type vlan id <N vlan>`

su linux per rendere la configurazione permanente
```
auto <physif>.<N>
iface <physif>.<N> inet static
address <ip_address>
netmask <netmask>
gateway <ip_addr_gateway>
```

comandi su switch 
* `port`
* `vlan`&rarr; gestione vlan 
* `hash` &rarr; gestione hash table 

### vlan 
`vlan/create vlan_n` crea la vlan di valore vlan_n

`port/setvlan port_n vlan_n` &rarr; untagged quindi access link quindi accetta pacchetti non taggati

`vlan/addport vlan_n port_n` &rarr; tagged quindi trunk link 

### configuazione host
configurazione temporanea:
``` bash
ip link add link eth0 vlan30 type vlan id 30
ip address add 192.168.3.1/24 dev vlan30
ip link set dev vlan30 up
```
configurazione permanente:

```bash
auto eth0.30
iface eth0.30 inet static
    address 192.168.3.1
    netmask 255.255.255.0
```