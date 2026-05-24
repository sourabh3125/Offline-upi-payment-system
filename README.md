# Offline UPI Payment System

Send money without internet. Your phone encrypts the payment and passes it
through nearby phones via Bluetooth until one with connectivity uploads it
to the backend for settlement.

## The Problem
UPI fails with no internet. This system routes payments through a
peer-to-peer device mesh — strangers relay your encrypted packet without
being able to read it.

## How It Works
1. Sender encrypts payment (RSA + AES-256-GCM)
2. Packet gossips device-to-device via Bluetooth
3. First device with internet uploads to backend
4. Backend deduplicates, decrypts, and settles

## Key Engineering
- **Hybrid encryption** — AES-256-GCM for payload, RSA-OAEP wraps the key.
  One flipped bit = decryption fails.
- **Exactly-once settlement** — SHA-256 hash of ciphertext + atomic
  ConcurrentHashMap.putIfAbsent. Three bridges upload simultaneously,
  exactly one settles.
- **Replay protection** — signed timestamp inside encrypted payload.
  Can't modify without breaking the auth tag.

## Run Locally
```bash
git clone https://github.com/sourabh3125/Offline-upi-payment-system.git
cd Offline-upi-payment-system
./mvnw spring-boot:run
```
Open `http://localhost:2002`

## Stack
Java 17 · Spring Boot 3.3 · AES-256-GCM · RSA-OAEP · H2 · JPA
