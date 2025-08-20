## Updates 20 Aug 2025
- Updated SendRecv_main.cpp to filter out `","custom_emoji":fals` when doing requests to /v2/chat/users/me/messages

## Updates 13 Aug 2025
- Updated SendRecv_main.cpp to be proxy aware
- Updated ZoomAPI.py to not restrict only for the 4 permissions

## Detected by AV?
- AV detects the EXE?
- Use https://github.com/TheWover/donut to convert to shellcode, then inject via other ways.
- donut.exe -i <C2.exe> -f 7 -o C2.bin
- -f 7 to output to C#

## Getting Started
There's the [Quickstart]([https://github.com/0xEr3bus/ShadowForgeC2/wiki/](https://github.com/benlee105/ShadowForgeC2Mod/wiki/Instructions)) Guide to setting up and running everything.

## About ShadowForge C2
ShadowForge C2 is an API-centric Proof of Concept, similar to other C2s. The implant works with HTTP/v2 and TLS connecting over Zoom. The approach taken by this C2 is built upon an implementation strategy that leverages the capabilities of the Zoom Messaging Channel. The implant, residing within the compromised systems, establishes a connection to a designated Zoom Messaging Channel, serving as a secure and discreet communication medium. The domain used has a valid certificate, `api.zoom.us.` 

## Purpose?
ShadowForge is not a complete C2, and its purpose is to serve a basic understanding for beginners of how this communication works from a team server to the compromised implant. The short purpose is to learn the methodology and steps used during the communication. The C2 doesn't provide any advanced obfuscation, i.e., it's not a ready-to-use C2; This aims to provide a short introduction to using a different secure communication. To Summarize, the project is intended for other red teamers and security researchers to learn.

## How this works
* Implant
    * A C++-compiled implant, when executed, sends the very first call back to a Zoom Chat Channel, hardcoded in the executable.
    * Every 10 seconds, the implant checks for any updated command to execute; if the command is in this format `command: <COMMAND>,` the implant executes the command.
    * Once the implant executes the commands, it updates the message with `result: <OUTPUT>.`
    * The communication is done over TLS with HTTP/2, used library is `wininet.h`
    * There are quite a few downsides and limitations so far; the most important ones are that the API token used to Send/Receive messages lasts 60 mins from the time generated.  

* Team Server
    * Every 12 seconds, a thread on the team server checks if the message is updated with the result; if this message is updated, it just prints the output.
    * The shell commands are received base64 encoded, as the Zoom Message's format is terrible. The team server decodes and prints correctly.
    * Keeping this in mind, the Zoom Channels are being Man-In-The-Middle for the Command & Control Communication.
    * The Implant and Team server is not connected directly; that's been said, the probability of crashing the implant is significantly less.
