## Atomic Bomberman v1.0

### New Functionality

Frameskip (originally 4) is used to check whether to animate the next frame of the sprites.

The new code modifies the frameskip value by reading and writing to the Options.ini a "frameskip=4" line so
that the user can configure the frameskip to make the app seem to run slower. (Higher value = slower)

User must run the app once and exit the app for the frameskip line to be written to Options.ini.

Alternatively, the user can add the line in Options.ini manually.

### Disassembly of New Code (OllyDbg)

```
--- Read Frameskip --------------------------------------------------------------------------------------------

0040660D   . 0F85 3D180500        JNZ BM.00457E50				; jmp to new code to read settings from ini file

00457EF0     frameskip,0x00										; string "frameskip" to read from ini file

00457E50   > BA F07E4500          MOV EDX,BM.00457EF0			; "frameskip"
00457E55   . 8D85 F4FEFFFF        LEA EAX,DWORD PTR SS:[EBP-10C]; line in ini file to compare
00457E5B   . E8 979FFFFF          CALL BM.00451DF7				; stricmp function
00457E60   . 85C0                 TEST EAX,EAX
00457E62   .^0F85 4EE8FAFF        JNZ BM.004066B6				; jmp to next line in ini file is not 0
00457E68   . 8B45 F8              MOV EAX,DWORD PTR SS:[EBP-8]	; convert ini value to integer
00457E6B   . E8 5198FFFF          CALL BM.004516C1				; atoi function
00457E70   . 3C 01                CMP AL,1						; check if value is between 1 to 16
00457E72   . 7C 11                JL SHORT BM.00457E85
00457E74   . 3C 10                CMP AL,10
00457E76   . 7F 0D                JG SHORT BM.00457E85
00457E78   . C1E0 02              SHL EAX,2						; multiply value by 4
00457E7B   > A3 50604A00          MOV DWORD PTR DS:[4A6050],EAX	; mov into frameskip variable
00457E80   .^E9 5EE8FAFF          JMP BM.004066E3
00457E85   > 33C0                 XOR EAX,EAX					; if value is invalid, set frameskip to the
00457E87   . B0 04                MOV AL,4						; original 4
00457E89   .^EB F0                JMP SHORT BM.00457E7B

--- Compare Frameskip ----------------------------------------------------------------------------------------

0042A2C5     E9 C3DB0200          JMP BM.00457E8D				; jmp to new code to check against new 
0042A2CA     90                   NOP							; frameskip
0042A2CB     90                   NOP

00457E8D     50                   PUSH EAX
00457E8E     33C0                 XOR EAX,EAX
00457E90     A0 50604A00          MOV AL,BYTE PTR DS:[4A6050]	; mov frameskip into al
00457E95     3C 00                CMP AL,0			
00457E97     75 02                JNZ SHORT BM.00457E9B		
00457E99     B0 04                MOV AL,4						; if al = 0 (First Time), mov 4 into al
00457E9B     8405 94494600        TEST BYTE PTR DS:[464994],AL	; compare current frame with frameskip
00457EA1     58                   POP EAX
00457EA2    ^E9 2524FDFF          JMP BM.0042A2CC				; jmp to original code

--- Write Frameskip ------------------------------------------------------------------------------------------

00406017   . E9 8D1E0500          JMP BM.00457EA9				; jmp to new code to save frameskip settings
0040601C     90                   NOP
0040601D     90                   NOP

00457EA9   > 68 107F4500          PUSH BM.00457F10				; Valid frameskip values from 1 to 16
00457EAE   . FF75 FC              PUSH DWORD PTR SS:[EBP-4]		; file handle
00457EB1   . E8 889CFFFF          CALL BM.00451B3E				; write to ini comment
00457EB6   . 83C4 08              ADD ESP,8						; restore the stack
00457EB9   . A1 50604A00          MOV EAX,DWORD PTR DS:[4A6050]	; mov frameskip variable in eax
00457EBE   . 85C0                 TEST EAX,EAX			
00457EC0   . 75 02                JNZ SHORT BM.00457EC4
00457EC2   . B0 10                MOV AL,10						; if eax = 0, mov 16 into eax
00457EC4   > C1E8 02              SHR EAX,2						; div by 4
00457EC7   . 50                   PUSH EAX						; push eax (frameskip variable)
00457EC8   . 68 007F4500          PUSH BM.00457F00              ; "frameskip=%d
"
00457ECD   . FF75 FC              PUSH DWORD PTR SS:[EBP-4]		; file handle
00457ED0   . E8 699CFFFF          CALL BM.00451B3E				; write to ini frameskip line
00457ED5   . 83C4 0C              ADD ESP,0C					; restore the stack
00457ED8   . C745 F4 00000000     MOV DWORD PTR SS:[EBP-C],0	; replace original code
00457EDF   .^E9 3AE1FAFF          JMP BM.0040601E				; jmp to original location

00457F00     frameskip=%d,0x0A,0x00								; frameskip line
00457F10     ; Valid frameskip values from 1 to 16,0x0A,0x00 	; comment line
```
