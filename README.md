# thc tesla-powerwall2-hack

TESLA PowerWall 2 Security Shenanigans 

24h ago Elon Musk announced to build the next [TESLA Gigafactory in Germany](https://techcrunch.com/2019/11/12/elon-musk-picks-berlin-for-teslas-europe-gigafactory/) somewhere near Berlin. Here at THC we consider Berlin to be our home turf. Welcome to Berlin, Mr. Musk.

Let's take a look at this TESLA Powerwall 2 and make sure the security is up to our standards.

This is a start. Take this and take it further.

**Introduction:**
The Tesla Powerwall 2 (PW) is a battery storage solution. It is often installed in combination with photovoltaic solar panels (PVs). The PW will store the PV generated power during daytime when the sun is shining and make the power available to the house when the sun is not shining. The PW can store up to 13.5kW of electric power and load/unload it at 5kW (7kW peak US / 5kW peak in the UK).

**Components:**
The Gateway (GW) consists of a Single Board Computer (SBC) and an energy meter (Neurio).

The SBC is custom built by WinSystems Inc and is called the G400.

The Neurio energy meter is the W1 module. It connects to the SBC via WiFi.

RS-485 is used to communicate between the SBC and the Powerwalls. Up to 10 Powerwalls can be connected to a single GW (daisy-chain).

The research is based on firmware 1.10.2.

The GW can  be configured to connect via Ethernet, GSM and Wifi to the Internet/Tesla-HQ. The Internet connection is used for firmware updates and for Tesla to fiddle with your PW. Tesla has unrestricted and remote management capability.

**PW-UI Management Interface**

The PW-GW’s management interface is accessible via WiFi. The WiFi network name is *TEG-<XYZ>* with XYZ being the last 3 digits of the PW’s serial number. TEG stands for ‘Tesla Energy Gateway’.

The password for the WiFi is the serial number. The serial number looks like this:

```
ST<YY><L>0001<XYZ>
```
YY is the built year and L is the revision number. I’ve seen revisions with letter D through to I. For example *ST17H0001789* would be a valid password for the Tesla PW on a WiFi network with SSID TEG-789.

The Management Interface (PW-UI) is accessible at http://192.168.91.1. The webpage will ask for a password which is again the serial number. The same management interface is accessible via the ethernet connection.

The password can not be changed. It is not possible to disable the WiFi network. I accessed mine from 50m away. It's broadcasting its SSID as well (yeha!).

**THC says: Fix it! No default passwords. Do not have the WiFi accessable unless during installation.**


**CF fun:**

The Tesla PW comes with two CT sensors. These are A/C current clamps that are installed around the LIVE wire of the incoming GRID line (house LIVE) and the LIVE wire of the SOLAR PV.

The Tesla PW uses these clamps to determine when to charge the PW from the Solar PV. E.g. the PW goes into charging mode when the Solar PV generate more electricity than the house uses.

The CT sensors have to be installed in the correct orientation or otherwise the GW sees an inverted (negative) reading. E.g. The GW thinks the house is exporting electricity rather than importing from the grid. Electricians do make mistakes during installation and the Tesla GW allows to *invert the reading in software*.

In the PW-UI Management Interface you will see a tick box called "Flip" next to the CT Clamp. This will INVERT the reading of the CT Sensor.

**CHARGE!!!**

Let's flip the reading of the GRID CT Clamp in the PW-UI. Lets assume the CT Clamp reading was +1kW that the house was taking from the grid. After flipping it reads -1kW (energy being EXPORTED to the grid). The PW immediately goes into chargning mode and starts charging the PW. This causes more power to be drawn from the grid. The CT Clamp immediately reports instead (remember, it's flipped) that now even more power is exported...and the PW ramps up charging...and draws even more power from the grid. All this happenes within microseconds.

There are other fun CT Clamp Flip Combinations. Another combination forces the PW to dump it's entire charge back into the grid.

The Problem:
1. Here in the UK we have night rate and day rate for electricty. Night rate is usually 300% cheaper and the PW is charged by night. Now an attacker can trick the PW to charge at day-rate and dump the load into the grid at night-rate, causing a financial loss to the consumer.
2. I can access quite a few PW's in my village. Does anyone know how the grid feels if all Tesla PW's start dumping their load back into the grid? I can also quickly change between CHARGING and DUMPING. It's really quick. We are talking sub-second switching between CHARGING and DUMPING. How does the grid feel about this oscilating behaviour and when will the sub-station wear out?
3. It is little understood that Tesla can do all this from their HQ. By this I mean any hacker or employee with the right access to Tesla HQ can put all PW's worldwide into CHARGING and DUMPING and oscilating between these two multiple times per second.  

**THC says: Err, do not cause any harm**


**Hacking Environment**

A list of some tips and tricks. Some parts are optional and not needed to hack the PW.

Part 1:
Place the PW on its own dedicated network behind a linux router. This will allow us to capture all network traffic between the PW and the Internet. 

Part 2:
Run the PW-UI Wizard and write down all values. Disable GSM to prevent any further firmware upgrade and to prevent Tesla from fiddling with your PW.

Part 3:
We use the web browser ‘Mozilla’. Go to “about:config" and delete the value in ‘network.http.accept-encoding’. This will make it easier to read captured network traffic between our browser and the PW:


Part 4:
In Mozilla open the Network Monitor by pressing ‘Cmd+Opt+E’ (Windows: Ctrl+Shift+E). This will allow us to inspect every request to the PW UI and read the response.




At Your Service,

The Hacker's Choice

