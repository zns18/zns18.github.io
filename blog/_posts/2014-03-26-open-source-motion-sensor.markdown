---
layout: post
title: A cheaper, open-source, motion sensor
date: 2014-03-26
comments: true
archive: false
tags: electronics, motion sensor, open-source
blog: true
description: Complete instructions on how to developer, build, and implement a motion sensor that works just as well but costs less than anything you can buy!
...

[I bemused a
lot](http://rpiai.wordpress.com/2014/03/24/developing-a-motion-sensing-network-trials-and-errors/)
about building a motion sensor, but now I can actually post something
useful. Everything I am about to tell you about can be found at [my
github page](https://github.com/schollz/motion_sensor_diy), including
the Eagle files and Arduino scripts. Here is the finished product (still
thinking about making an enclosure):

![DIY Motion
Sensor](/assets/images/diy-sensor.jpg)

Motion sensor + RF transmitter
==============================

**Components (order from eBay to get really cheap)**

-   [PIR
    sensor](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=6&cad=rja&uact=8&ved=0CDkQFjAF&url=http%3A%2F%2Fwww.mpja.com%2Fdownload%2F31227sc.pdf&ei=8FgzU868GsHLsQSTnYKIDg&usg=AFQjCNELdcZfKHaZhM4NlzFmhzu2_fOHwg&sig2=eFrgkllWo_47kU5vjeJpJA&bvm=bv.63738703,d.cWc),
    on
    [Amazon](http://www.amazon.com/HC-SR501-Sensor-Module-Pyroelectric-Infrared/dp/B007XQRKD4)
    or
    [Ebay](http://www.ebay.com/itm/10pcs-HC-SR501-Adjust-IR-Pyroelectric-Infrared-PIR-Motion-Sensor-Detector-Module-/131028677440?pt=LH_DefaultDomain_0&hash=item1e81eaf340)
-   [PT2262 encoder](http://www.100y.com.tw/pdf_file/PT2262-IRS.PDF)
-   [ATtiny85](http://www.atmel.com/images/atmel-2586-avr-8-bit-microcontroller-attiny25-attiny45-attiny85_datasheet.pdf)
-   [434 MHz RF
    transmitter](http://www.amazon.com/433Mhz-transmitter-receiver-your-Experiment/dp/B00AMB3NCY)
-   Several 500-1k Ohm resistors
-   LED (optional)

The motion sensor is simply a PIR sensor connected to a 434MHz RF
transmitter via an encoder. The encoder uses four bits, in the form of
resistors tied to HIGH (+VCC) or LOW (GND). That’s enough for 12
sensors. The ATtiny85 is programmed via an Arduino which allows for
devising a smart way of sending the RF data. It can be programmed to
send the output as randomized timed sequences in order to reduce the
conflicts of having other motion sensors on the same band. I also used
some [code from Alex for running the ATtiny at low
power](http://www.insidegadgets.com/2011/02/05/reduce-attiny-power-consumption-by-sleeping-with-the-watchdog-timer/),
so it is battery capable:

```
#include &lt;avr/sleep.h&gt; //Needed for sleep_mode
#include &lt;avr/wdt.h&gt; //Needed to enable/disable watch dog timer

int pinLed = 0;
int irSensor = 2; // (pin 7 on chip)

void setup(){
    pinMode(pinLed,OUTPUT);
    pinMode(irSensor,INPUT);
        //Power down various bits of hardware to lower power usage
    set_sleep_mode(SLEEP_MODE_PWR_DOWN); //Power down everything, wake up from WDT
    sleep_enable();
}

void loop(){
    ADCSRA &amp;= ~(1&lt;&lt;ADEN); //Disable ADC, saves ~230uA
    setup_watchdog(5); //Setup watchdog to go off after 1sec
    sleep_mode(); //Go to sleep! Wake up 1sec later and check water

    //Check for water
    ADCSRA |= (1&lt;&lt;ADEN); //Enable ADC
        int val = digitalRead(irSensor);
        while (val == HIGH) {
                digitalWrite(pinLed, HIGH);   // turn the LED on (HIGH is the voltage level)
            delay(500);               // wait for a second
            val = digitalRead(irSensor);
        }
            digitalWrite(pinLed, LOW);    // turn the LED off by making the voltage LOW
}

//This runs each time the watch dog wakes us up from sleep
ISR(WDT_vect) {
    //Don't do anything. This is just here so that we wake up.
}

//Sets the watchdog timer to wake us up, but not reset
//0=16ms, 1=32ms, 2=64ms, 3=128ms, 4=250ms, 5=500ms
//6=1sec, 7=2sec, 8=4sec, 9=8sec
//From: http://interface.khm.de/index.php/lab/experiments/sleep_watchdog_battery/
void setup_watchdog(int timerPrescaler) {

    if (timerPrescaler &gt; 9 ) timerPrescaler = 9; //Limit incoming amount to legal settings

    byte bb = timerPrescaler &amp; 7;
    if (timerPrescaler &gt; 7) bb |= (1&lt;&lt;5); //Set the special 5th bit if necessary

    //This order of commands is important and cannot be combined
    MCUSR &amp;= ~(1&lt;&lt;WDRF); //Clear the watch dog reset
    WDTCR |= (1&lt;&lt;WDCE) | (1&lt;&lt;WDE); //Set WD_change enable, set WD enable
    WDTCR = bb; //Set new watchdog timeout value
    WDTCR |= _BV(WDIE); //Set the interrupt enable, this will keep unit from resetting after each int
}
```

Here is the schematic for the main motion sensor module:

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/rf_tx_schematic.jpg" | prepend: site.url }}" alt="Sensor schematic" class="img-rounded img-responsive" width="100%">
    </p>
</div>


And the resulting board schematic (after lots of trials to eliminate
air-wires):

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/rf_tx_board.jpg" | prepend: site.url }}" alt="Main board" class="img-rounded img-responsive" width="100%">
    </p>
</div>

To make the board, I followed the instructions from
[blondihacks](http://quinndunki.com/blondihacks/?p=835) to the tee. The
only difference is that I used a etching solution of 4% HCl and 1% H2O2
which etched in about 10min (the vinegar was too slow for me). Here is
the finished version [after some trial and
error](http://i.imgur.com/ER5aDOn.jpg).

<div class="col-sm-12">
    <p align="center">
<img src="{{ "/assets/images/finished-board.jpg" | prepend: site.url }}" alt="Finished board" class="img-rounded img-responsive">
    </p>
</div>


RF Receiver
===========

**Components**

-   Arduino
-   PT2272 encoder
-   434 MHz RF receiver

This circuit is even simpler. Connect the RF receiver output to the DIN
of the PT2272. Then put each output pin (the four bits) into the Arduino
pins 3-6. It’s best to connect the VT (Viable Transmission indicator)
pin of the PT2272 to the Pin 2 of the Arduino so that you can attach an
interrupt on it. This way whenever VT goes high, the Arduino will read
the other four pins and use that to determine which sensor is which (I
leave this to you, though). The basic code for this is very simple:

```
int ledpin = 13;
volatile int state = LOW;

void setup()
{
    pinMode(pin, OUTPUT);
    attachInterrupt(0, blink, CHANGE); //pin 2
    Serial.begin(9600);
}

void loop()
{
    digitalWrite(ledpin, state);
}

void blink()
{
    int a = digitalRead(3);
    int b = digitalRead(4);
    int c = digitalRead(5);
    int d = digitalRead(6);
    Serial.println(String(a) + &quot; &quot; + String(b) + &quot; &quot; + String(c) + &quot; &quot; + String(d) + &quot; &quot;);
    if (a+b+c+d&gt;0){
        state = true;
    } else{
        state = false;
    }
}
```
