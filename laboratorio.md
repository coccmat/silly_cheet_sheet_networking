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