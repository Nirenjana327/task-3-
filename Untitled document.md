* Connect RTL-SDR Dongle.  
* Installed GNUradio in ubuntu .  
* In the GNU radio, we need to make a flow graph. This flow graph Shows the Signal Path (i.e,Data Flow).

* OPTIONS

ID : variable name. Here it is RTL\_SDR\_FM  
Normal sample rate is slow so the sample rate is set to 2.4 Mega samples per second (2.4 e6).We first tunes the receiver to a known local radio station at 104.3 .To view the data,we use a  QT GUI Frequency sink. 

* The QT GUI Range →  is used initiate a slider for tuning Its named as tune. Its  value is set to 104.3 MHz.The tuning range is set from 88.7 MHz (start of the band) to 110.1 MHz (end of the band), with a step size of 0.1 MHz  
* RTL-SDR Source → receives raw RF samples.Then it converts the signal to digital form.

Frequency \- Which radio station to listen to  
Sample Rate \- How fast the dongle reads signal data ( The bandwidth is equal to the sample rate)  
When  frequency of the dongle changes, it  tunes to another station

* Low pass filter Block → removes unwanted frequencies and isolates the FM channel. To isolate the desired radio station, the center frequency is changed back to zero.It  then picks out frequencies around zero and get rid of adjacent stations at higher and negative frequencies.The relevant FM information is contained in a band about 200 kHz wide (100 kHz on each side of the center)

Cutoff Frequency \- it is  how much frequency we have to cut from the filtered frequency ( 100 E3 ie,100 kHz)  
Transition Width \- How sharply the filter cuts off unwanted parts ( 50 kHz)  
Decimation \- take nth sample of the frequency and discard the rest  
Decimation is set to 5, meaning only one in five samples is kept.  
The bandwidth of the Frequency sink must be manually updated to sample rate / 5 to reflect the new rate, as GNU Radio does not track this automatically.  
The resulting sample rate is one-fifth of the original  
Sample Rate \- its value should be same as in source block  
This block cleans the signal and removes extra noise and unwanted nearby channels.

* WBFM Receive Block →  it decodes the FM signal and extracts audio information. In FM signals information  is stored in small frequency changes of the radio wave.The goal is to turn the frequency changes in the waves into an audio tone (where lower signal frequencies correspond to low audio amplitude and high signal frequencies correspond to high audio amplitude)

Quadrature Rate \- Set to samp rate / 5 (which is 480 kHz)(its like the sample rate for amplitude)  
Audio Decimation \- Reduces data speed for audio 

* Audio Sink → plays the output sound .This block connects the signal to the computer sound card.The universal, highest quality audio rate from most sound cards is 48 kHz.  
* Time Sink → Shows the audio waveform. It reveals the frequency modulation showing places where the signal frequency is quite high or quite low.  
* Frequency sink → to show the frequency spectrum. The bandwidth is set to 48 kHz

