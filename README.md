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
This is a parameterized edge connector for PCIe which is configurable from x1 to x16 lanes. The connector supports the PCIe protocol which is defiend in JSL (https://docs.jitx.com/reference/jsl/protocols/pcie.html?h=). The user is expected to instantiate this connector with an argument defining the PCIe lane width. Options for the lane width are:
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
