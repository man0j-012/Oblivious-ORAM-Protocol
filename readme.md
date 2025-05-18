# Oblivious ORAM: Recursive & XOR‑Optimized Implementation

A modular Java reference implementation of Oblivious ORAM with recursive position maps, XOR‑based bandwidth savings, and secure client–server separation. This version integrates symmetric‑key encryption into the XOR path‑access optimization and has been validated on AWS S3‑backed storage.

---

## Key Capabilities

### Core ORAM Features

- **Recursive position map** for sub‑logarithmic client storage
- **XOR path compression** (encrypted) to cut online bandwidth
- **Reverse‑lexicographic eviction** with early bucket reshuffling
- Fully configurable parameters (tree depth, `Z`, recursion depth)
- Clean, extensible Java architecture with separate client and server modules
- Runs locally or on Amazon EC2; S3 support baked in

### Cloud‑Scale Performance Highlights

- **Path ORAM on AWS S3**: 4‑level (16‑leaf, `Z = 4`) tree hides all client access patterns
- **Integrity at scale**: Merkle‑tree fingerprinting ( *O* (log *n*) checks) validates data under concurrent loads
- **Stronger privacy**: Randomized leaf assignment + per‑access block reshuffling make read/write operations unlinkable to the cloud provider
- **33 % throughput boost**: Asynchronous S3 pipelining (AWS SDK + KMS) drops mean latency from 1.5 s to 1.0 s at 200 req/s

---

## Requirements

- Java 11 or later
- `guava-19.0.jar` (collections and in‑memory caching utilities)

## Project Structure

ObliviousORAM/
├── src/
│ └── com/
│ ├── server/ # Server‑side bucket‑tree handler
│ ├── client/ # Client entry point and logic
│ ├── oblivious/ # Core ORAM implementation
│ └── messages/ # Serializable request/response classes
├── config/
│ └── oram.properties # Z, depth, recursion, XOR flags
├── libs/
│ └── guava-19.0.jar
└── README.md

---

## Configuration

Edit `config/oram.properties` to match your deployment:

```properties
TREE_DEPTH=10
BUCKET_SIZE=4
SERVER_IP=your.ec2.ip.address
SERVER_PORT=9000
RECURSION_DEPTH=3
ENABLE_XOR=true
```

## Running the System

## Compile

javac -cp libs/guava-19.0.jar -d bin $(find src -name "\*.java")

## Start the server

java -cp bin:libs/guava-19.0.jar com.server.Server

## Run the client (in a new terminal)

java -cp bin:libs/guava-19.0.jar com.client.Client

Ensure the server is running before launching the client.

## Testing & Debugging

Log the current stash size after each access.

Prints the full eviction path and per‑bucket contents.

Verifies position‑map consistency across recursive layers.

Validates XOR compression and decryption integrity.

## Internal Design Notes

Bucket Metadata (com.oblivious)
BucketMetadata distinguishes real from dummy blocks using two arrays:

## Structure Indices Purpose

meta_buf 0 … REAL_BLOCK_COUNT‑1 Block IDs for real data
REAL_BLOCK_COUNT … REAL_BLOCK_COUNT+Z‑1 Offsets into bucket_data
offset Same layout Byte offsets for each entry

This split lets the client fetch only needed blocks on a path read while enabling secure reshuffles.

## References

Ren, Ling et al. “Constants Count: Practical Improvements to Oblivious RAM.” USENIX Security Symposium, 2015.

## Gratitude

Thanks to my UIC Class, Coursework, Prof.
