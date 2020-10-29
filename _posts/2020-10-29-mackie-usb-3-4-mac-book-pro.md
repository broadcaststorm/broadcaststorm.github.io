---
layout: post
title:  "Where are Channels 3-4 from Mackie ProFX10v3 mixer on Mac Book Pro???"
categories: mackie sound usb macbookpro
---
On the [Mackie ProFXv3 10 channel](https://mackie.com/products/profxv3-professional-effects-mixers-usb)
mixer, you have 2x4 USB I/O available: 2 output channels and 4 input
channels. On a Mac Book Pro, those channels are natively available
without a custom driver. Finding the input channels 3-4, which "should"
appear as "speaker" outputs on the Mac, is not quite as straightforward
as one might think.

## Mackie ProFX10v3 10-Channel Mixer

To read all about my new setup and the details on the Mackie ProFX10v3 mixer,
you'll want to read my post on the
[Pandemic Audio Upgrade]({% post_url 2020-10-26-pandemic-audio-upgrade %}).

The relevant feature for today of the Mackie mixer board is the USB 2x4 I/O
monitoring capability.  This 2x4 nomenclature refers to the number of channels
**sent** (2) and the number of channels **received** (4), from the perspective
of the mixer.

From the perspective of the Digital Audio Workstation (DAW), also known as
your computer, you'll receive 2 channels of input characterized as a microphone
and you'll send 4 channels of output as speaker options.  On a Windows DAW,
you'll need a driver to make all this happen but support for the mixer is
available natively on Mac OS X.

## Hello computer?

With the USB connection in place and the mixer powered on, the situation on the
Mac Book Pro looks promising.  The "microphone" (channels 1-2 sent) is easily
identifiable:

![MacBook Pro microphone options](/images/mackie-microphone-image.png){: .align-center}

The "speaker" options (channels 1-2, 3-4) however are a bit more vague:

![MacBook Pro speaker options](/images/mackie-speaker-image.png){: .align-center}

If I expect two pairs of channels, why do I only have one option? Shouldn't
I have "ProFX 1-2" and "ProFX 3-4"? Since it is only "ProFX", which two channels
are they because channels 1-2 can only be sent to the control room and headphone
outputs. If you want audio mixed with your other channels (via mixer channels 9/10),
your audio must arrive on USB channels 3-4.

It doesn't take long