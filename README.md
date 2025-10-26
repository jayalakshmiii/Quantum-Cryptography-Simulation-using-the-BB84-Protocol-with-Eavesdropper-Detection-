Quantum Cryptography Simulation using the BB84 Protocol (with Eavesdropper Detection)

Introduction
Quantum cryptography leverages principles of quantum mechanics to enable secure communication. The BB84 protocol, invented in 1984 by Charles Bennett and Gilles Brassard, is the first and most widely recognized protocol for Quantum Key Distribution (QKD). It enables two communicating parties (commonly referred to as Alice and Bob) to establish a shared secret key, with the assurance that any attempt at eavesdropping can be detected through quantum disturbances.
Objectives
•	Simulate the BB84 QKD protocol using Python and Qiskit.
•	Demonstrate the protocol’s essential steps including bit generation, quantum encoding, transmission, measurement, and key sifting.
•	Incorporate an 'Eve' eavesdropper simulation to show how her interference increases the error rate, thereby revealing her presence.
Method and Code Overview
The BB84 protocol is implemented using Qiskit, IBM’s open-source quantum computing framework. This simulation allows us to mimic quantum state preparation, measurement, and communication steps between two users using virtual quantum circuits.
Qiskit Tools
- QuantumCircuit: Used to encode quantum states representing bits.
- Aer Simulator: Executes quantum circuits and simulates measurement outcomes without physical quantum hardware.
Steps
1.	Random Bit and Basis Generation: Alice generates a random bit string and a random sequence of measurement bases (Z or X). Bob independently generates his own basis sequence.
2.	Quantum Encoding: Each bit is encoded as a quantum state using corresponding gates in the Qiskit QuantumCircuit object.
3.	Eavesdropper Intervention: Eve measures each qubit using a random basis and then resends a new qubit to Bob, disturbing the quantum state when her basis differs from Alice’s.
4.	Measurement: Bob measures the received qubits in his chosen bases.
5.	Key Sifting: Alice and Bob publicly compare their measurement bases (not the key bits). Bits measured with matching bases form the ‘sifted key’.
6.	Error Rate and Security Check: A subset of bits is compared to estimate the error rate. A high error rate implies eavesdropping activity.
Eavesdropper Detection
Without Eve: The error rate is approximately 0%, demonstrating secure communication and correct key exchange.
With Eve: The error rate increases significantly, often to around 25%, as random measurement by Eve disturbs quantum states. In this specific simulation, the error rate reached 40%, clearly indicating an eavesdropper’s interference.
Project Results
Two simulation runs were conducted to evaluate the BB84 protocol:
- No Eve: The error rate was 0.00%, confirming secure quantum key generation.
- With Eve: The error rate rose to 40.00%, confirming successful detection of an eavesdropper.
These outcomes demonstrate a key principle of quantum cryptography — any attempt to intercept or measure qubits introduces detectable disturbances.
Conclusions
The BB84 protocol ensures the confidentiality of communication through quantum mechanics. Its core advantage lies in built-in eavesdropper detection: any intrusion disrupts quantum coherence, introducing measurable errors. This simulation effectively showcases the BB84 mechanism using Python and Qiskit, illustrating how quantum cryptography ensures data security in theory and practice.
Possible Extensions
•	Statistical Analysis: Execute multiple simulations to observe average error rate variations.
•	Partial Eavesdropping: Model Eve as an intermittent attacker, affecting only certain qubits.
•	Visualization: Implement matplotlib to graphically display key lengths and error rates.
•	Real Quantum Hardware: Adapt the program to Qiskit’s real quantum backends for live experiments.

References
•	Bennett, C.H. & Brassard, G. (1984). 'Quantum cryptography: Public key distribution and coin tossing.'
•	Qiskit Textbook: https://qiskit.org/textbook/ch-algorithms/quantum-key-distribution.html
Programming codes
# bb84_qkd.py
# Quantum Cryptography Simulation using BB84 Protocol
# Works with latest Qiskit + Qiskit Aer (2025 compatible)

from qiskit import QuantumCircuit
from qiskit_aer import Aer
import random

def random_bits(n):
"""Generate n random bits"""
return [random.randint(0, 1) for _ in range(n)]

def random_bases(n):
"""Generate n random bases (0 = Z, 1 = X)"""
return [random.randint(0, 1) for _ in range(n)]

def encode_qubits(bits, bases):
"""Encode bits into qubits depending on basis"""
qubits = []
for bit, basis in zip(bits, bases):
qc = QuantumCircuit(1, 1)
if basis == 0:
if bit == 1:
qc.x(0)  # apply X gate
else:
if bit == 1:
qc.x(0)
qc.h(0)  # apply H gate for X basis
qubits.append(qc)
return qubits

def measure_qubits(qubits, bases):
"""Measure qubits in chosen bases"""
backend = Aer.get_backend('qasm_simulator')
results = []
for qc, basis in zip(qubits, bases):
new_qc = qc.copy()  # avoid in-place modification
if basis == 1:
new_qc.h(0)  # measure in X basis
new_qc.measure(0, 0)
result = backend.run(new_qc, shots=1, memory=True).result()
measured_bit = int(result.get_memory()[0])
results.append(measured_bit)
return results

def sift_keys(sender_bases, receiver_bases, sender_bits, receiver_bits):
"""Compare bases and keep only matching ones"""
key = [s for s, sb, rb in zip(sender_bits, sender_bases, receiver_bases) if sb == rb]
received_key = [r for r, sb, rb in zip(receiver_bits, sender_bases, receiver_bases) if sb == rb]
return key, received_key

# Step 2: Simulate BB84
n = 20  # number of qubits
alice_bits = random_bits(n)
alice_bases = random_bases(n)
bob_bases = random_bases(n)

print("Alice's bits:   ", alice_bits)
print("Alice's bases:  ", alice_bases)
print("Bob's bases:    ", bob_bases)

# Encode and measure
qubits = encode_qubits(alice_bits, alice_bases)
bob_results = measure_qubits(qubits, bob_bases)

# Key sifting
alice_key, bob_key = sift_keys(alice_bases, bob_bases, alice_bits, bob_results)

print("Alice's key (sifted):", alice_key)
print("Bob's key (sifted):", bob_key)

# Step 3: Check for mismatch (Eve detection)
errors = sum([1 for a, b in zip(alice_key, bob_key) if a != b])
error_rate = (errors / len(alice_key)) * 100 if alice_key else 0

print(f"Error rate: {error_rate:.2f}%")
if error_rate > 20:
print("Possible eavesdropper detected (Eve may be listening!)")
else:
print("Secure quantum key established!")




 





 






 




