---
title: 'DIY Crystal AM Radio Design - Part2: Circuit Implementation'
date: 2016-11-12 00:00:00 Z
tags:
- Projects
- Electrical
layout: post
comments: true
---

<p><a href="http://c4coffee.ca/2016/10/30/diy-am-radio.html">Continuing from the previous post</a>, this post is going to describe the implementation of the actual radio.</p>

## Reference Circuits    

![]({{site.urlt}}/img/2016-11-12.png)

This circuit is an example of a basic crystal radio circuit. <b>L1</b> and <b>C1</b> is the tuning circuit that is a band pass filter. The <b>diode</b> following is the peak detector that allows us to convert the received radio signals to audio.   

<!--more-->     

## Final Circuit Design   

Our final design (Fig 2.A) included extension capabilities for different audio sources. This is to ensure that the end user is not limited to the rare and uncommon piezoelectric speakers and crystal earphones used in the testing process.    

![]({{site.urlt}}/img/2016-11-12-1.png)   

*Fig 2.A.1 - Final Circuit Including Extensions*  


The strategy used for final implementation surrounded picking from a variety of different designs used for each function block (Labeled in Fig.2.A.1) and using the best performing combination. The amplification circuit was developed first as it was hard to test the quality of our tuning circuit without proper amplification of the AF signal produced. After we confirmed that our first amplification stage was sufficient for testing the tuning circuit, we focused on the design of our transformer within the tuning circuit for best RF results.

![]({{site.urlt}}/img/2016-11-12-2.png)   

*Fig 2.A.2- Main Function Blocks Labeled*   


Below (Fig 2.A.2) is our intermediate testing setup for most of the tuning circuit design process. The main improvements made from the first amplification stage was the switch from a crystal earphone to a piezoelectric speakers. We made this change to reduce the discomfort from the fluctuation and noise introduced in the circuit tuning process.

![]({{site.urlt}}/img/2016-11-12-3.png)    

*Fig 2.A.3 - Intermediate Testing Circuit*   


From the labeled figure below, we can see the main components to the intermediate testing circuit. The peak detector and 2 stage audio amplification circuit was constant through the whole circuit tuning process. The tuning circuit displayed below is the final setup, not the preliminary design. The reason why a second audio amplifier was added was to ensure that there was enough gain for both the crystal earphones and piezoelectric speakers.   

![]({{site.urlt}}/img/2016-11-12-4.png)   

*Fig 2.A.4 - Intermediate Testing Circuit Labeled*   



## Antenna Design   

The main antenna that was used in the research process was the rooftop antenna provided to us within the labs. This antenna was constructed by simply extending a long 22 AWG wire along the side of the building and across the rooftop.   

Though there were antennas provided in the lab, to address the portability aspects of our radio, we made sure to construct and test our own antennas after we had created our intermediate testing circuit.   

Two test antennas were constructed with 7 loop of 22 gauge conductor wire, with the difference being solid core v. threaded wire core. Performance was tested by comparing the audio output performance with the radio frequency tuned to CBC Radio One (890 AM). The reason CBC was selected for testing was because we were expecting limited results from our own antennas due to the faraday shielding nature of our building. With CBC being the strongest signal that we can receive, we would have a better chance of having observable results.   

The experiment was conducted based off of the hypothesis that the stranded wire would produce better results than the solid core wire due to losses from skin effect. However, the result was that we were unable to hear any signals being picked up using the stranded wire. Due to the qualitative nature of our test, we could not confirm the reason of the poor performance of the threaded wire. A quantitative method was proposed by measuring the magnitude of received signals using the spectrum analyzer to confirm performance. However, due to the short time frame and limits in available apparatus, this experiment was not carried forward. The rest of the experiment was carried forward by using the provided roof antennas and ¼ wavelength antennas.


![]({{site.urlt}}/img/2016-11-12-5.jpg)   

*Fig 2.B.1 - Threaded 22AWG Loop Antenna (Left) Solid 22AWG Loop Antenna (Right)*


## RF Amplifier  

The RF amplifier is the real design challenge in this project. After looking through various designs, I decided that the most efficient design was to use a 2 in 1 tuning circuit + amplifier. Since we were dealing with radio signals, a transformer design with the correct resonance inductance would be the perfect performance enhancer.   

An active RF amplifier was considered in the design process as it was one of two possible amplification stages we could implement in terms of a general radio design. However, through research it was noticed that most AM radios do not implement active RF amplification circuits due to the time and monetary cost trade off to actual performance increase. Therefore we did not carry forward in designing and implementing an RF amplifier.    

It is important to note, that during our conversations with our project advisor, we learned that commonly MOSFETS were used for RF Amplification circuits due to their fast switch speed (In respect to BJTs) for output at linear operation. In comparison, BJTs do not operate as efficiently for RF processes due to power loss in current draw, which can be easily affected by external factors including temperature and large signal fluctuations.
Voltage doublers and clampers were also considered in the design process. However from the peak detector performance, we decided that it was not necessary for good performance.     

Our final decision for addressing RF amplification was to use a step-up transformer. A passive process was selected in hopes that in times where the system lacks power, a minimum signal can be heard with high impedance audio devices. Further, a transformer being magnetically coupled will be able to ensure that only the AC components of our received radio signal is being fed into the rest of our circuit.   

![]({{site.urlt}}/img/2016-11-12-6.jpg)   

*Fig 2.C.1 - Tuning circuit transformer that acts as amplifier*


The transformer also acts as part of the tuning circuit. The functionality and tuning applications for the toroidal transformer will be discussed below in the tuning circuit section.  


## Tuning Circuit


The tuning circuit design is a tank circuit that would act as a band pass filter for the frequencies we wish to receive. Being provided with a 450pF and 900pF variable capacitor, it was decided that the fastest approach to a working tuning circuit would be to build a non variable inductor and have the radio tuned exclusively by the variable capacitors.   

To approach with inductor design, we had to start by figuring out the inductor size we needed for filtering the correct frequencies. The inductor values were calculated by calculating the necessary inductor value from the mid point capacitance on the capacitors (Roughly 225pF and 450pF respectively). The relationship used was:  

$$	 f  = 12 LC $$

Further, we had to decide between the type of inductor core to use in our fabrications processes. Though through research we learned that radio users prefer air coils due to lack of core losses. However, it was also noted during research that by connecting the antenna directly to a ferrite core inductor, we can create what’s known as a loopstick antenna. Loopstick antenna’s though are not very efficient, they are well suited for receiving within the AM frequency bandwidth (500kHz - 1600kHz). Thus our inductor designs through the project surrounded various types of ferrite cores that were available for purchase.


![]({{site.urlt}}/img/2016-11-12-7.png)   

*Fig 2.D.1 - Different Iterations of Tuning Design*

In Fig 2.D.1 there are three different circuit designs that we put into consideration from the beginning. On the left there is a parallel LC circuit with both a variable capacitor and a variable inductor connected to the antenna. The benefit of this circuit is that it provides us with the ability to not only select channels with the inductor (giving us a higher Q factor) but also allow us to do fine adjustments with the capacitor if needed. Our approach to fabricating the variable inductor was to create a toroidal inductor and had one surface sanded. By moving a conducting brush or wire across the sanded surface, we would be able to adjust the inductance to our needs. This design can be seen in Fig 2.H. However due to the limitations in our crafting skills and ferrite cores to pick from, we were unable to produce a variable inductor with satisfactory results.  

In the middle, there is a parallel LC circuit with a variable capacitor and a set value inductor measured at around 2.5mH. This design was used for a large portion of design in the intermediate circuit during the development of further audio amplification techniques. The toroidal inductor can also be seen in Fig 2.D.2 alongside other inductor types we fabricated for testing.    


![]({{site.urlt}}/img/2016-11-12-8.jpg)    

*Fig 2.D.2 - Various Types of Toroidal Ferrite Inductors  (Left) Variable Toroidal Inductor (Right - Top) Toroidal Transformer (Right - Bottom) 2.5mH Toroidal Inductor*


Last, there is the toroidal transformer design that we ended up implementing in our final circuit. The fabrication process was identical to previous toroidal inductors we made with the only difference being that there was an extra secondary winding on the toroid. It was very important to ensure that the two windings were looped in the same direction so the flux would add up properly giving us the best results. The model shown in Fig 2.H is one of the prototypes we used during the testing processes, however it did not give us the best results, so we opted for the transformer seen in Fig 2.F.

It is important to note that, to ensure there was the least amount of resistance in the circuit as possible, our team opted for thicker wires than the ones the lab provided. (22AWG replaced the 26AWG magnet wire).   

##  Detector

Out of the various types of peak detectors, we decided to use a simple diode structure to carry forward the detector task. However other possibilities were considered in the design process and will be discussed below.

To our understanding, the best performance can be obtained by using a superheterodyne receiver. Below is our theoretical circuit that we would use to carry out the process.

![]({{site.urlt}}/img/2016-11-12-E1.png)    

Fig 2.E.1 - Superheterodyne Receiver Radio Circuit with Mixer Demodulation

![]({{site.urlt}}/img/2016-11-12-E2.png)

Fig 2.E.2 - Superheterodyne Receiver Radio Circuit with Peak Detector


Due to the lack of time we did not carry forward in prototyping this circuit as it requires us to tap our own transformers specifically for constructing the mixer. Further, the results we were getting from a simple diode peak detector was sufficient in producing clear audio signals. It is important to note that the 1N34 Germanium diode was selected specifically due to the low voltage drop of 0.2V as opposed to 0.7V of a normal rectifier. The resistor and capacitor values were selected so our sampling time frequency is enough to follow the input RF signal.

![]({{site.urlt}}/img/2016-11-12-E3.png)

Fig 2.E.3 - 1N34 Peak Detector

![]({{site.urlt}}/img/2016-11-12-E4.jpg)

Fig 2.E.4 - Voltage Drop across detector with 1N34 Germanium diode

![]({{site.urlt}}/img/2016-11-12-E5.jpg)

Fig 2.E.5 -Voltage Drop across detector with 1N270 Germanium diode

![]({{site.urlt}}/img/2016-11-12-E6.jpg)

Fig 2.E.6 - Voltage Drop across detector with IN4739A zenner diode

![]({{site.urlt}}/img/2016-11-12-E7.jpg)

Fig 2.E.7 - Voltage Drop across detector with 1N5711 SCHOTTKY diode


As you can observe from Fig.2.E.4 and Fig.2.E.5, 1N34 and 1N270 Germanium diode have a low forward bias voltage drop of 0.2V, as opposed to 0.7V of a normal rectifier. In contrast, 1N5711 SCHOTTKY diode and 1N4739 diode have a higher voltage drop of 0.3V. Since the peak value of the peak detector is not totally exact to the true peak value of the input because of the inherent built-in voltage drop, we want a diode that has as lowest voltage drop as possible. We want the capacitor can output a DC voltage equal to the peak value of the detector, and as a consequence, it would make sense to select to use Germanium diode like 1N34 and 1N270 because of its low inherent built-in voltage drop. On the other hand, our crystal radio receiver usually produces around 0.1 volt. The Germanium can start to conduct at very low voltages like 0.1 volt.


## Audio amplifier


As discussed earlier, we have implemented a variety of audio amplifiers for different purposes. In this section, we will provide information about all audio amplifiers we have worked with and their specific applications.

Below (Fig 2.F.1) is a diagram of our first stage audio amplification circuit. The design consisted of a simple collector feedback biased amplification circuit. The 10uF capacitor was selected as a bypass capacitor to prevent any noise that is present, or generated in the circuit. We will later realize that a capacitor with a higher frequency response was needed. The 1uF capacitors in the circuit act as coupling capacitors to ensure that the only signals carried forward are the AC signals.

![]({{site.urlt}}/img/2016-11-12-F1.png)

*Fig 2.F.1 - First Stage Audio Amplification Circuit*

By plotting the frequency response of the first stage audio amplification circuit shown in Fig 3.F.2 we can observe that the maximum gain is -9.96dB. With the 3-dB frequencies occurring between 3.950kHz and 513kHz.

![]({{site.urlt}}/img/2016-11-12-F2.png)

*Fig 2.F.2 - Magnitude Bode Plot of First Stage Audio Amplification*

It can be observed that the max gain covers the higher end of the human hearing spectrum (20-20kHz) but lowers in the 1kHz to 4kHz region which is the region humans are most sensitive in. Putting the simulation data aside, practical use of the circuit was indeed hard operate due to the low gain coming from the crystal earphones.  Therefore we decided that another amplification stage was needed.


The second amplification was a simple cascade of the same transistor setup with a potentiometer in place of the collector resistor. This will allow us to adjust the amount of gain by  changing the bias voltage at the collector terminal. The implemented circuit can be seen below (Fig 2.F.3):

![]({{site.urlt}}/img/2016-11-12-F3.png)

*Fig 2.F.3 - Circuit with Second Amplification Stage*

The frequency response of the circuit was evaluated again after the introduction of the second amplification circuit.

![]({{site.urlt}}/img/2016-11-12-F4.png)

*Fig 2.F.4 - Magnitude Bode Plot of Circuit with Second Amplification Stage*

From the bode plot it can be observed that the frequency was not adjusted much compared to the first amplification circuit. Though we could have adjusted the coupling capacitor values to shift the 3dB point towards the low frequency end to better cover the human hearing range, the practical results from using the radio showed that the frequency response was enough.  
It should also be noted that the max gain is at -4.720dB, which is 5dB higher than the first stage amplification circuit on its own.   
At this point we were satisfied with the circuit’s performance using a crystal earphone and piezoelectric speaker. However, both of these audio devices are not easily obtainable, so we further worked on developing other circuits that can connect to commercially available audio devices.
Below is a transistor amplification circuit for low impedance ear phones.  


![]({{site.urlt}}/img/2016-11-12-F5.png)

*Fig 2.F.5 - Low Impedance Earphone Amplifier*

Below is a speaker amplifier made using an LM358 audio amplifier. We followed the recommended circuit designed for 200dB gain by the manufacturer and had great results. The only downside is that the speaker draws too much current and will most definitely be a viable option in emergencies.

![]({{site.urlt}}/img/2016-11-12-F6.png)

*Fig 2.F.6 - Low Impedance Speaker Amplifier*

---
Excerpt from final design report for fulfillment of requirements for ELEC 391, June 2016-11

---
Part 1 can be found at: [http://c4coffee.ca/2016/10/30/diy-am-radio.html](http://c4coffee.ca/2016/10/30/diy-am-radio.html)
