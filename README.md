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
<img width="985" alt="SDR BandPass Filter" src="https://user-images.githubusercontent.com/103593959/172302826-40fc8595-4909-4c58-8f03-eb0a1fa90c3c.png">
 Above is the bandpass filter made using the [LC Filter Design Tool](https://rf-tools.com/lc-filter/.). It is a 3rd order, conventional series first, Butterworth, bandpass filter that passes 7-12Mhz frequencies.
 
 ### Tayloe Mixer
 <img width="1231" alt="SDR Tayloe Mixer" src="https://user-images.githubusercontent.com/103593959/172303648-700520a2-89da-466e-b126-8586e74dde47.png">


### Voltage Smoother
<img width="604" alt="SDR Voltage Smoother" src="https://user-images.githubusercontent.com/103593959/172304110-ad263bd5-0f7e-4924-8e71-f7c88b45c8cd.png">


### Oscillator
<img width="998" alt="SDR Oscillator" src="https://user-images.githubusercontent.com/103593959/172304171-e6c24725-75b7-4256-bf23-c743ff63e073.png">


### Raspberry Pi Pico
<img width="698" alt="SDR Raspberry Pi Pico" src="https://user-images.githubusercontent.com/103593959/172304244-dc54f33a-5759-4e20-bd7e-07ca2c132442.png">


