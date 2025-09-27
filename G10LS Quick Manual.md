# G10LS Quick Installation and Operation Manual 

Thank you very much for using the GPS tracker. This user manual will explain in detail how to operate this product. Please read it carefully before use so that you can use it correctly. Some functions and structures of the product are subject to change without notice, please take the real product as standard. The manufacturer is not legally liable for faults and omissions in the manual.

## 1. Product function parameters

### 1.1 Product function

The functions of this product are electronic fence, power off alarm, vibration alarm, displacement alarm, position query, track playback and cut off fuel and power.

### 1.2 Performance parameters

- GSM: 850/900/1800/1900MHz worldwide used Quad-band system;
- Wide operating voltage input range: 12-92VDC;
- Positioning time: average cold start≤32sec (outdoors), average hot start≤2sec (outdoors)
- Size of product: please take real product as standard;
- Location accuracy:≤30 meters;
- Operating temperature:-20℃ to +55℃

**IMPORTANT:This product only supports installation and operation within the voltage withstand range. Any consequences caused by illegal installation over the voltage withstand range are borne by users themselves.**



## 2. Product accessories and LED status

### 2.1 Product accessories

- **Standard:** GPS tracker/power lines/quick installation and operation manual/double-sided stickers
- **Optional:** microphone, SOS button and relay

### 2.2 Product’s LED working status

#### 2.2.1 Yellow LED (GSM signal status)

| LED status | Meanings |
| :--- | :--- |
| flashing | GSM initialization |
| Constant on | GPRS is on work/on line |
| Not shine | GSM is in sleep mode |

#### 2.2.2 Blue LED (GPS signal status)

| LED status | Meanings |
| :--- | :--- |
| flashing | GPS signal searching |
| Constant on | GPS positioning successfully |
| Not shine | GPS is in sleep mode |



## 3. Installation instructions

### 3.1 Preparation before installation

1.  To open the box to check whether the GPS tracker model is right or not, and to make sure accessories are complete, otherwise please contact your dealer;
2.  To choose a SIM card. The GPS tracker needs to insert a GSM SIM card. To make sure the missing corner of the SIM card is inward, and the chip of the card is facing USB port, and then push the SIM card into the card slot.

**IMPORTANT:**

*   (1) before installing or removing the SIM card, please cut off the power of GPS tracker;
*   (2) the SIM card used in GPS tracker must have GPRS function;

### 3.2 Installation

The GPS tracker is suggested to be installed in a hidden place, and it is recommended to be installed by a professional agency designated by the dealer. Please pay attention to the following items:

1.  Install the tacker in a hidden place, and make sure it is waterproof;
2.  Avoid the tracker placing close to signal source such as reversing radar, anti-theft device and other vehicle communication equipment;
3.  The tracker has built-in GSM antenna and GPRS antenna, and make sure the tacker’s GPS antenna side which is without sticker is facing up towards sky, and there is no any metal object cover the tracker.

**Recommended installation place:**

1.  shelter in the decoration frame below the front windshield of the car;
2.  shelter around the car front dashboard which is covered with non-metallic material;
3.  underneath the decoration board under rear windshield of the car;
4.  inside the car door or car central pillar;
5.  inside the electromobile/motorcycle dashboard or shelter under the back seat.



## 4. Product wiring diagram

- **Red line (anode)** connects to the positive pole of the battery 12V.
- **Black line (cathode)** connects to the negative pole of the battery.
- **Orange line (ACC)** connects to the vehicle's ACC switch.
- **Yellow line (cut off fuel and power)** connects to the 86 port of the oil pump relay and connects to the 85 port of the 12V relay.
- **White line (anode)** connects to the 87a port of the oil pump relay.
- **Oil pump** connects to the 30 port of the oil pump relay.

## 5. Wiring precautions

5.1 Please use the power lines provided by the factory. The red line is anode of power supply, and the black line is cathode of power supply. When wiring, the cathode of the power supply should be connected with ground or iron separately. Please do not wire the cathode with other grounding lines;

5.2 ACC line (orange line) is wired with ACC switch of cars. If users need ACC automatic anti-theft function, please be sure the ACC line is wired, and the tracker will determine to start up the fortification state or not depending on the status of ACC. The car will start up fortification status if ACC line is unconnected, and in this situation, it will trigger vibration alarm when there is vibration during car driving.

5.3 Control line (yellow line) which cut off feul and power is wired to thin yellow line of disconnector (optional).

**Steps to use product correctly: installation→boot→settings→registration**

- **Installation:** when installation, the GPS antenna should be facing the sky. Above the installation location must be a place that is not covered by electromagnetic wave absorption materials (such as metal, explosion-proof insulation film).
- **Boot:** insert the SIM card into the tacker correctly, wire lines according to product wiring diagram, and then turn on the power switch.
- **Registration:** ways of registration are different depending on different dealer’s platform, so please consult your dealer.



## 6. SMS command description and usage

When using a mobile phone to send the SMS code to the SIM card inserted in the GPS tracker, please use space bar as space punctuation. The comma in each of the following SMS commands is in English input format, and the letters are capitalized according to commands.

### 6.1 Center number settings (i.e. Car owner’s phone number)

- **Command format:** admin+password+space+phone number
- **For example:** admin123456 13512345678
- **Reply information:** admin ok

### 6.2 ACC anti-theft settings (The ACC line needs to be wired correctly):

- **6.2.1** When ACC is on work (turn on switch with car key), turn on ACC can automatic disarmed if user set fortification status; however turn on ACC cannot automatically disarmed if user set fortification status by SMS and platform.
- **6.2.2** When ACC is turned off (the car key switch is off), it will be in automatic fortification status after setting time.
- **6.2.3** Setting ACC automatic fortification on by SMS (default)
    - **Command:** ACCLOCK,123456,1
    - **Reply:** Successfully set up
- **6.2.4** Setting ACC automatic fortification off by SMS
    - **Command:** ACCLOCK,123456,0
    - **Reply:** Successfully set up
- **6.2.5** Setting automatic fortification time by SMS means automatically fortification after 5 minutes)
    - **Command:** ACCLT,123456,5

### 6.3 Vibration alarm setting: (this function can be realized only when the car owner’s phone number is registered and GPS is in fortification status，please refer to 6.1 and 6.2)

- **6.3.1** Vibration SMS alarm:
    - **Activating Command:** 125# (default)
    - **Inactivating Command:** 126#
    - **Reply:** OK
- **6.3.2** Vibration call alarm:
    - **Activating Command:** 122#
    - **Inactivating Command:** 121# (default)
    - **Reply:** OK

### 6.4 Upload time setting: (default time is 20s)

- **6.4.1** Upload time command: freq,123456,20

### 6.5 Main power off alarm setting:

- **6.5.1** Setting power off SMS alarm:
    - **Activating Command:** pwrsms123456,1 (default)
    - **Inactivating Command:** pwrsms123456,0
    - **Reply:** pwr sms alarm set ok
- **6.5.2** Setting power off call alarm:
    - **Activating Command:** pwrcall123456,1 (default)
    - **Inactivating Command:** pwrcall123456,0
    - **Reply:** pwr call alarm set ok

When the external power supply of the tracker is cut off, it will trigger a power off alarm (the platform will also receive a battery removed alarm), and a power off alarm message will be sent to the center number, and the center number will be dialed once.

### 6.6 Restore factory settings:

- **Command:** FORMAT
- **Reply:** restore the factory value successfully, please re-register the owner’s phone number

### 6.7 Tracker reboot:

- **Command:** CQ

### 6.8 Request information of tracker:

- **Command:** CXDZT

### 6.9 Setting APN:

- **APN setting command:** apn123456 cmnet
- **Reply:** apn ok
- **APN corresponding user name setting command:** apnuser123456 user
- **Reply:** apnuser ok
- **APN corresponding password setting command:** apnpasswd123456 password
- **Reply:** apnpasswd ok

## 7. Troubleshooting

7.1 If the tracker is installed for the first time, it cannot connect to the background server, and the background server show it is not online, please check installation of the tracker.

If user feel abnormal when operating the tracker, please refer to the following problems and solutions; if user still can not solve the problem, please contact the dealer.

| common problems | problem description | solutions |
| :--- | :--- | :--- |
| Poor signal reception | The tracker is used in poor reception areas such as near tall buildings or underground parking lots, which results radio waves cannot be effectively communicated | Using tracker in good signal reception places |
| Platform shows the tracker is not enabled when installation for the first time | Whether the main power line has been wired correctly | Do not wired to the vehicle’s internal control line |
| | SIM card is not installed | Check SIM card |
| | LED status | Check whether LED is flashing or constant on or not |
| | SIM card does not open GPRS function | Please contact the service provider to open GPRS function |
| Platform map shows GPS does not positioning | GPS does not positioning | Please go to open space for incorrect location |
| | Vehicle does not move after installing the tracker | Drive the vehicle on the road |
| | Whether ACC line is wired or not | Install the tracker and turn on ACC with car key |
| Platform shows main power is disconnected | Poor contact of power lines | Check whether power lines are wired correctly |
| Platform displays offline state | SIM card arrears or is canceled | Please check the SIM card status |
| | GPRS function | Please go to somewhere the signal is strong and try again |
| | Weak signal in the offline area | |