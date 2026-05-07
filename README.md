# UCB-Based Adaptive Frequency Hopping using GNU Radio and USRP

This project implements an adaptive wireless communication system using GNU Radio and USRP, where the operating frequency is selected dynamically using a UCB (Upper Confidence Bound) multi-armed bandit algorithm.

Unlike a fixed-frequency SDR link, this system continuously observes channel conditions, estimates interference, computes a reward metric, and automatically switches to a better frequency when the current channel becomes poor. This makes the project unique as it combines SDR communication, online learning, and real-time control in a closed-loop architecture.

## Project idea

In many wireless systems, communication performance degrades when interference appears on the current channel. A fixed transmitter-receiver pair cannot react intelligently unless it is manually reconfigured.

This project solves that problem by building a receiver-assisted intelligent hopping framework. The receiver monitors the incoming signal, estimates how good the current channel is, and runs a UCB-based channel selection algorithm. Based on the measured reward, the receiver selects the next best frequency and sends the updated frequency to the transmitter through a UDP control link.

As a result, the transmitter and receiver can move across multiple candidate frequencies and maintain communication on better channels.

## Why this project is unique

- It is not just frequency hopping; it is **adaptive frequency hopping** based on online reward feedback.
- It uses a **UCB multi-armed bandit algorithm** instead of fixed hopping patterns.
- The receiver performs **real-time interference-aware decision making**.
- The selected frequency is sent back to the transmitter through a **UDP-based control path**.
- The system includes **dynamic interference simulation**, **logging**, and **live spectrum/audio monitoring**.

## System architecture

The project is divided into two parts:

### 1. Transmitter side
The transmitter:
- Reads an audio signal from a WAV file
- Performs NBFM modulation
- Upsamples the signal to the USRP sample rate
- Transmits through USRP
- Receives frequency update commands through UDP
- Changes its RF center frequency according to the command sent by the receiver

### 2. Receiver side
The receiver:
- Captures the RF signal using USRP
- Monitors the received spectrum
- Demodulates the received NBFM audio
- Estimates reward from the received signal quality
- Runs the UCB-based adaptive hopping algorithm
- Sends updated frequency commands to the transmitter over UDP
- Logs frequency, reward, SNR, BER estimate, and received power

## Candidate frequencies

The adaptive hopping logic operates over the following channels:

- 2.412 GHz
- 2.437 GHz
- 2.462 GHz
- 2.472 GHz
- 2.480 GHz

These frequencies are treated as bandit arms, and the algorithm learns which frequency gives better performance under interference.

## Receiver-side intelligent blocks

### Enhanced reward calculator
The reward calculator evaluates the received signal and generates a reward between 0 and 1.
It uses:
- estimated SNR
- interference metric
- signal power stability

A higher reward indicates a cleaner and more stable channel.

### Adaptive UCB hopper
This block is the core intelligence of the system.
It:
- initializes channel exploration
- collects enough samples on each frequency
- computes UCB values
- balances exploration and exploitation
- decides whether to stay on the current channel or switch

This allows the system to learn which frequency is currently better in a changing interference environment.

### Dynamic interference simulator
To test the adaptive behavior, the receiver side includes a dynamic interference simulator that changes the interference pattern over time. This is useful for validating whether the UCB-based hopper actually responds correctly to channel degradation.

### Smart logger
The logger stores:
- time
- frequency
- estimated SNR
- estimated BER
- received power
- reward

This helps in offline analysis and performance evaluation.

## Transmitter-side control mechanism

The transmitter receives frequency update messages over UDP. These messages are converted into center-frequency commands and applied to the USRP sink. This enables the transmitter to follow the decision taken by the receiver.

In this way, the receiver acts as the decision-making node, while the transmitter follows the selected hopping frequency.

## Files

- `rx_side.grc` - Receiver flowgraph with adaptive UCB hopping, reward calculation, interference simulation, and logging
- `tx_side-2.grc` - Transmitter flowgraph with NBFM transmission and UDP-based frequency control

## Main features

- Real-time SDR communication using USRP
- Adaptive frequency hopping
- UCB-based online channel selection
- UDP-based transmitter control
- Dynamic interference simulation
- Live spectrum visualization
- Audio recovery at the receiver
- Channel quality logging for analysis

## Tools and hardware

- GNU Radio
- USRP
- Python embedded blocks
- UDP socket communication
- Linux

## Working principle

1. The transmitter sends an NBFM-modulated audio signal.
2. The receiver captures the signal and evaluates the current channel.
3. A reward is calculated from signal quality indicators.
4. The UCB algorithm decides whether to remain on the current frequency or switch.
5. The selected frequency is sent to the transmitter via UDP.
6. The transmitter updates its center frequency.
7. The system continues adapting as interference changes over time.

## Applications

This type of system is useful for:
- cognitive radio experimentation
- interference-aware wireless links
- adaptive SDR communication
- reinforcement learning / bandit-based radio control
- dynamic spectrum access research

## Notes

This project is an experimental implementation of intelligent adaptive communication using SDR. It demonstrates how learning-based decision methods can be integrated with practical GNU Radio and USRP flowgraphs for real-time spectrum adaptation.
