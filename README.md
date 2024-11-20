# Installation

In slm.toml add:
```
connectors = { git = "JITx-Inc/connectors", version = "0.4.0" }
```

# USB C Connector
## USB-C Connector Module
This is a Molex 2012670005, wrapped in a module to map it to a standard `usb-c-connector` bundle.
```
inst usb-c : connectors/components/USB/USBTypeC/device
```
### Ports
```
conn : usb-c-connector
```
## USB 2.0 High-Speed Interface
This is a basic USB2 480mbps capable USB-C interface. It doesn't include the SuperSpeed connections, but does provide the necessary features to support a simple debug interface, like to an FTDI.
Circuit Includes:
1.  USB-C connector
2.  Pull-downs for CC1 and CC2
3.  ESD protection diodes for the USB2 data bus.
4.  Shield termination
```
inst usb-if : connectors/components/USB/USBTypeC/USBC-HighSpeed-Iface(res-query, cap-query)
```
### Ports
```
USB : usb-data
VDD-USB : power
```
### Parameters
- R-query: Resistor query parameters - default is `ResistorQuery()`.
- C-query:  Capacitor query parameters - default is `CapacitorQuery()`.

# Weidmuller terminal block
This is a parametric component modeling the [Weidmuller LSF-SMT family](https://catalog.weidmueller.com/procat/Group.jsp;jsessionid=2D3E17129178EB5048158C0C39D6D6A1?groupId=(%22group21472460020482%22)&page=Group) of terminal blocks. 
```
inst terminal-block : connectors/components/LSF-SMT/component(num-poles = 3)
```
### Parameters
- num-poles : Int - the number of poles in the terminal block
