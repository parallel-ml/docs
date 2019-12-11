# Verified Communication Problem Statement and Solution

## Secure and Verified Inter-vehicle Communication and Performance Considerations

# Vehicle-to-Vehicle (V2V) Communication and Existing Work

Is going to be implemented in many future cars, including those that are not self-driving. See [https://www.theverge.com/2016/12/13/13936342/wireless-vehicle-to-vehicle-communication-v2v-v2i-dot-nhtsa](https://www.theverge.com/2016/12/13/13936342/wireless-vehicle-to-vehicle-communication-v2v-v2i-dot-nhtsa)

## Potentials of V2V

Traffic lights could broadcast their state over the network.

Vehicles could broadcast their speeds.

# Problems with V2V

## Infrastructure

Currently, a separate protocol has been proposed for V2V, and not many cars support this protocol. In our experiments, we will be setting up a mesh WiFi network to communicate (as an emulation of this protocol). This may prove to be viable in the real world as well.

## Security

Security is the biggest concern with these efforts. If our system design has clear security flaws, then bad actors can abuse these flaws to do some massive harm.

Some key security concerns are listed below.

### 1. Impersonation and Falsification

A bad actor could impersonate another vehicle and report information that is incorrect, leading to accidents.

### 2. Lack of Verification

When you get a message, you should be able to verify which vehicle, in the physical world, that message comes from. Many existing systems do not implement this (?)

### 3. Duplication of Legitimate Messages

Due to the peer-to-peer nature of the network setup, nearby vehicles will receive all messages, even those that are not particularly interesting to them. Assuming that some underlying digital signing mechanism (using asymmetric encryption) is used, there is still a problem in cases where.

### 4. Privacy

Broadcasting information to vehicles near you has some clear privacy concerns.

# Our Solution: Verified and Encrypted Messaging Between Vehicles

Every vehicle (and other broadcasting entities in the street, such as traffic lights) has a public/private key pair. The vehicle broadcasts its public key using a QR code (or some other visual method of transmitting information). When a vehicle wants to transmit information to other vehicles nearby, it encrypts that message with its private key. Other vehicles can verify that the message is coming from the right vehicle by decrypting that message using the QR code for that vehicle. This way, you can ensure that the message is coming from the correct source. This solution solves problems (1) and (2).

## Time-Dependence

To prevent the duplication of signed messages, we can encode the current time into the sent packet. Then, clients will only trust messages within a few seconds of their transmission time.

## Acknowledgement Packets

The final security guarantee, added on top of the existing security measures, will be an end-to-end encrypted acknowledgment process. When a vehicle broadcasts some sort of update packet (e.g. speed update), other clients will receive this message and verify it using the vehicle's QR code. Once this verification is successful, clients can send a direct message to the source vehicle, establish an encrypted channel of communication with them, and ask them to verify that the packet received is legitimate and valid in the current time frame.

## Multiple "Hops"

In cases where vehicles do not have a direct line of sight to the QR codes of the subjects they want to verify, we implement a "hop" system. In this case, every vehicle that verifies some message. Imagine the following scenario:

In the scenario below, imagine if four cars are all in the same lane consecutively: A B C D (A is in front of B who is in front of C who is in front of D), and A wants to verifiably transmit some message to D.

1. Vehicle A sends a speed update to 200 mph.
2. Vehicle B, who has a direct line of sight with A's QR code, receives vehicle A's speed update packet and verifies this information by decrypting with vehicle A's public key.
3. Vehicle B then broadcasts that it has verified vehicle A's speed update to 200 mph.
4. Vehicle C receives vehicle A's original speed update packet but is unable to verify it.
5. Vehicle C receives vehicle B's verification packet and verifies its authenticity by decrypting with vehicle B's public key.
6. Vehicle C then broadcasts that it has verified vehicle B's verification of vehicle A's speed up.
7. Vehicle D receives vehicle A's original speed update packet but is unable to verify it.
8. Vehicle D receives vehicle C's verification packet of vehicle B, which pertains to the original speed up packet of vehicle A, and verifies its authenticity by decrypting with vehicle C's public key.
9. Vehicle D has now received the original information from vehicle A. Please note that the "trustability" of the message drops for every hop that the packet takes. Please see the next section for more details

### Trustability

As packets hop through vehicle to vehicle, we calculate a trustability factor for them. For the sake of simplicity, we define the "trustability" of a packet to be 1 / (# of hops that the packet takes). For example, the packet from the previous section took 3 hops from A to D, so it has a trustability factor of 1/3 or 33%.

A vehicle will only trust a packet if the net trustability factor is 100%. For example, if D receives 2 other verification messages about A's speed up, then it will trust the event. This may happen if other vehicles in other lanes also send verification messages.

### Scan a QR code every 500 ms

If we scan a QR code and validate it, then we cache for some time period. You also want to mark this into your localization.

We could expand the integration with the localization system and exploring the security risks of doing so.

If we get some message from a vehicle that we don't recognize (i.e. we haven't scanned the QR code for this message yet), then we wait for some time period before fully discarding the message.
