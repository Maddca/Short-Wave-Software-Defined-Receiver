# Short-Wave-Software-Defined-Receiver
The goal of this project has been to design a Software Defined Radio Receiver with [Taylor Smith](https://github.com/TaylorSmith28) for the Engineering Electronics II class.
## Design Objectives
For this project we were given the following design objectives by [Dr. Rob Frohne](https://github.com/frohro):

#### Objectives
 - Low noise figure
 - Good image rejection
 - Minimum discernible signal of less than 1uV
 - $25 Budget

For specifications we originally were going to have a narrow bandpass filter, but ended up switching to a relatively wider one in the later stages of the project.

#### Specifications
 - Frequency Range of 7-12MHz
 - Short Wave Radio
 - Uses a Tayloe Mixer
 - Uses a Multiple Feedback Lowpass Filter and Amplifier
 	- 25 V/V gain with Cutoff Frequency of more than 50KHz
 - Butterworth Bandpass Filter
 - Uses the Raspberry Pi Pico
 
 ## Overview
 The SDR receiver connects to an antenna and filters, mixes, and amplifies that signal to achieve the desired I and Q signals. The I and Q signals are then sent through an external sound card with a sample rate of 48kHz where software then handles the demodulation of the signal into audio. The mixer requires a 90 degree offset of two clocks operating at a frequency that can be controlled through software. In our design we use a Raspberry Pi Pico to control an si5351 to supply those two 90 degree offset clocks. There were some significant issues with this since the code supplied by professor Frohne didn't work when ported over to the Raspberry Pi Pico. In theory though the Raspberry Pi Pico interfaces with Quisk to tune to a certain frequency by adjusting the frequency of the si5351 clocks.
 
 ## Theory
<img width="870" alt="SDR Block Diagram" src="https://user-images.githubusercontent.com/103593959/172300478-5b9f30f3-213d-48fd-8808-7c275a4ef59f.png">

The Block Diagram above shows the basics of how the SDR works. We get an input signal from the antenna which is passed through the bandpass filter and sent to the Tayloe Mixer that uses a 1:4 demultiplexer and low pass filters and ampliers to create the I and Q quardrature signals. After the demuiltiplexer creates 4 signals each 90 degrees out of phase with each other those signals are combined together to create the I and Q signals to be sent to the Audio Jack.

## Schematic Version 6

### Bandpass Filter
<img width="892" alt="SDR BandPass Filter V6" src="https://user-images.githubusercontent.com/103593959/172314893-75881478-3dc4-4661-bd4f-f85d9c326cb0.png">
 Above is the bandpass filter made using the [LC Filter Design Tool](https://rf-tools.com/lc-filter/.). It is a 3rd order, conventional series first, Butterworth, bandpass filter that passes 7-12Mhz frequencies.
 
 ### Tayloe Mixer
 <img width="1231" alt="SDR Tayloe Mixer" src="https://user-images.githubusercontent.com/103593959/172303648-700520a2-89da-466e-b126-8586e74dde47.png">
The Tayloe Mixer above is comprised of the Demultiplexer stage which seperates the input signal into four signals all 90 degrees out of phase with each other. Then the signals that are 180 degrees out of phase with each other are combined in the following amplifier and low pass filter stage using opamps. The filter/amplifier here is a multiple feedback low pass filter and amplifier, in this case multiple refers to the filter being second order rather than single order.

### Voltage Smoother
<img width="604" alt="SDR Voltage Smoother" src="https://user-images.githubusercontent.com/103593959/172304110-ad263bd5-0f7e-4924-8e71-f7c88b45c8cd.png">
The voltage smoother above simply uses varying capacitor sizes to lower the noise coming from the USB.

### Oscillator
<img width="998" alt="SDR Oscillator" src="https://user-images.githubusercontent.com/103593959/172304171-e6c24725-75b7-4256-bf23-c743ff63e073.png">
The oscillator above is what outputs the clock signals necessary to run the Tayloe Mixer. It is controlled by the Raspberry Pi Pico on the SDA and SCL inputs.

### Raspberry Pi Pico
<img width="698" alt="SDR Raspberry Pi Pico" src="https://user-images.githubusercontent.com/103593959/172304244-dc54f33a-5759-4e20-bd7e-07ca2c132442.png">
Above is the part of the schematic dealing with the Raspberry Pi Pico. The main thing of interest here is to note the pull up resistors and the GPIO pin the SDA and SCL are connected to, since the pin needs to be I2C compatible, in this case pins 6 and 7 or GPIO 4 and 5 are I2C compatible.

## Simulations
Simulations were for the most part conducted in [LTSpice](https://www.analog.com/en/design-center/design-tools-and-calculators/ltspice-simulator.html).
### Bandpass Filter Simulations
<img width="1013" alt="SDR Bandpass Filter Sim Graph" src="https://user-images.githubusercontent.com/103593959/172318649-42233a15-3bbd-4986-8b04-fb9e14d2ecc6.png">
<img width="915" alt="SDR BandPass Filter LTSpice" src="https://user-images.githubusercontent.com/103593959/172316219-54d21552-6851-4861-8690-6f225e0667ba.png">
The Bandpass filter was designed to pass frequencies between 7 and 12 MHz, which is what the ac simulation shows.

### Lowpass Filter Simulations
<img width="1050" alt="SDR MFLPFA Simulation Graph" src="https://user-images.githubusercontent.com/103593959/172317607-272e52aa-086b-4b29-aa43-51809930be00.png">
<img width="874" alt="SDR MFLPFA LTSpice" src="https://user-images.githubusercontent.com/103593959/172317624-1e9dbeeb-1868-442b-8e47-05900adffd5f.png">

## Issues
The first major issue we encountered was not having functional code because of the port over to Raspberry Pi Pico. It took 2 days of troubleshooting to get the appropriate clock signal to display and thats still without the ability to change frequencies, which currently causes the program to hang. The second major issue was much simpler. When we went to test our bandpass filter we came to the realization that one of the leads on the handwound inductors couldn't make a good connection because there was still enamel on it. After several failed attempts to remove it we decided jump the bandpass filter, but much later discovered that shorting the bandpass filter with a wire caused one of the resistors responsible for the dc offset to be in parallel with the resistor in the signal generator. This caused us to lose our DC Offset which caused the SDR not to work. To resolve this issue we simply shorted the bandpass filter with a capacitor.
## Results
We ended up with a minimum discernable signal of approx 1uV and when we tried the antenna we listened to some Catholic sermon on sunday.
![Frequency Test](https://user-images.githubusercontent.com/103593959/172452457-a3950ff8-61dc-4eb1-8724-40cc2999b065.png)
