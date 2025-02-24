# Installation

In slm.toml add:
```
connectors = { git = "JITx-Inc/connectors", version = "0.4.2" }
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
inst usb-if : connectors/components/USB/USBTypeC/USBC-HighSpeed-Iface()
```
### Ports
```
USB : usb-data
VDD-USB : power
```
### Parameters
- R-query: Resistor query parameters - default is `get-default-resistor-query()`.
- C-query:  Capacitor query parameters - default is `get-default-capacitor-query()`.

# Weidmuller terminal block
This is a parametric component modeling the [Weidmuller LSF-SMT family](https://catalog.weidmueller.com/procat/Group.jsp;jsessionid=2D3E17129178EB5048158C0C39D6D6A1?groupId=(%22group21472460020482%22)&page=Group) of terminal blocks.
```
inst terminal-block : connectors/components/LSF-SMT/component(num-poles = 3)
```
### Ports
```
p : pin[num-poles]
```
### Parameters
- num-poles : Int - the number of poles in the terminal block

# RJ45 Ethernet jack
## RJ45 Module
This is a Pulse Electronics [JD0-0001NL](https://productfinder.pulseelectronics.com/api/open/part-attachments/datasheet/JD0-0001NL), wrapped in a module to map it to standard ports for MDI, and the LED, shield and tap pins. The user is expected to use a `require` statement to extract the 1000Base-T or 100Base-T interface for their application.
```
inst ethernet-jack : connectors/components/JD0-0001NL/connector
```
### Ports
```
  port CT       ; Device-Side Center Tap
  port ISO-CT   ; Isolated Cable-Side Center Tap
  port SHIELD
  ; Status LEDs
  port LED-G : polarized-ca
  port LED-Y : polarized-ca
```
Supports:
```
MDI-100Base-TX
MDI-1000Base-TX
```

# PCIe Edge Connector
## Edge-Conn-PCIe Component
This is a parameterized edge connector for PCIe which is configurable from x1 to x16 lanes. The connector supports the PCIe protocol which is defined in JSL (https://docs.jitx.com/reference/jsl/protocols/pcie.html?h=). The user is expected to instantiate this connector with an argument defining the PCIe lane width. Options for the lane width are:
```
PCIe-x1
PCIe-x4
PCIe-x8
PCIe-x16
```
```
inst pcie-edge : connectors/components/PCIe/Edge-Conn-PCIe(PCIe-x16) ; for a x16 lane connector
```
### Ports
```
  port pcie : pcie(width, PCIe-PRSNT#) ; PCIe port with the specified width and presence detect
  port smbus : std-smbus() ; standard SMBus port
  port jtag : jtag ; standard JTAG port (`TRST` is supported via the reset port)
  port reset : reset ; reset port
```

### Power Pins
```
  p+12V
  p+3_3V
  p3_3Vaux
  GND
```

# M.2 Edge Connector
## M2-2280-ADAPTER Module
This is a parameterized edge connector for a 2280 M.2 edge connector which is configurable with support for B and/or M type connectors (at present). The connector supports the PCIe (https://docs.jitx.com/reference/jsl/protocols/pcie.html) or SATA (https://docs.jitx.com/reference/jsl/protocols/sata.html) protocol. The user is expected to instantiate this connector with an argument defining the connector type. Options for the type are (including the no key option first):
```
[]
[M2-B-TYPE]
[M2-M-TYPE]
[M2-B-TYPE M2-M-TYPE]
```
```
inst m2-2280-adapter : connectors/components/M2-2280-ADAPTER/module([M2-B-TYPE])
```
### Ports
```
  port vdd3v3 : power
```
This port definition enables connectivity to 3.3V power as defined by the interface standard.
## Supported Protocols
Once instantiated, the connector will be available with the appropriate protocol enabled for the user to access.
To enable the use of a specific protocol, the user must instantiate the connector with the appropriate type (for instance, instantiating a B-type connector allows either `SATA` or `pcie(2)`) to be used. Note that the SATA protocol and pcie(x) protocols are mutually exclusive. The following protocols are supported:
### Supports
```
  pcie(2)
  pcie(4)
  SATA
```
In order to use one of these protocols, the user should utilize a `require` statement like one of the following (all are mutually exclusive):
```
  require sata : SATA from m2-2280-adapter
  require pcie : pcie(2) from m2-2280-adapter
  require pcie : pcie(4) from m2-2280-adapter
```
## Board Outline
The M.2 form factor supported in this library is the 2280 form factor (22.0 mm x 80.0 mm). The geometric outline of the connector with key positions is defined in the following variables:
```
M2-default-board-shape
M2-board-shape-B
M2-board-shape-M
M2-board-shape-B-M
```
The user can use these variables to create a custom board outline for their application.
```
; define the board with both a B and M key
val board-shape = M2-board-shape-B-M
set-board(an-example-board(board-shape))
```
Note that there is a grounded mounting hole at the center top of the board as well. Use the PTH mounting hole from the mechanical repo (https://github.com/JITx-Inc/mechanical) to create this mounting hole.

```
  inst G1 : pth-mounting-hole(
    PlatedHole(
      hole = Circle(1.750),
      copper = Circle(2.750)
      mask = 0.2,
    ))
  place(G1) at loc(0.0, 80.0) on Top
  net (G1.p[1] GND)
```

