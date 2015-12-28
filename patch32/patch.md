## Patch32 v1.0

### New functionality

`00019F` - change .text section characteristics from Executable, Readable to Executable, Readable, Writeable (File Offset) 
- From : `60`
- To   : `E0`

`007C10` - change identifier of "Exit" button to 5 instead of 2 as 2 was also used by Windows 'X' button (File Offset)
- From : `02`
- To   : `05`

`007C16` - change unicode string of "Exit" button to "Undo" (File Offset)
- From : `45007800690074`
- To   : `55006E0064006F`

`401634` - change jump table address for "Patch" button from 4015EB to 406B50
- From : `EB1540`
- To   : `506B40`

`401644` - add jump table address for new "Undo" button as 406B50
- From : `CCCCCCCC`
- To   : `506B4000`

`4015DF` - change the switch statement to handle new identifier 5 for "Undo" button (returns as 4 in eax)
- From : `83F803 cmp eax, 00000003`
- To   : `83F804 cmp eax, 00000004`

`406B50` - additional code to modify the buffers used by the patching code when checking the target file for the #chek bytes and when writing the #repl bytes to the target. Unpatching the file uses the #repl buffer when checking the target and writes to the files using the #chek buffer

### Disassembly of New Code (OllyDbg)

```
00406B50   > 833D 30804000 >CMP DWORD PTR DS:[408030],0	
00406B57   . 74 21          JE SHORT COPY_OF_.00406B7A	; jump if P32 file was not selected
00406B59   . 803D 508E4000 >CMP BYTE PTR DS:[408E50],0
00406B60   . 74 18          JE SHORT COPY_OF_.00406B7A	; jump if target file was not selected
00406B62     83F8 00        CMP EAX,0			
00406B65   . 75 18          JNZ SHORT COPY_OF_.00406B7F	; jump if "Undo" button was clicked
00406B67     C605 7C104000 >MOV BYTE PTR DS:[40107C],0C	; edit the code	to use the #chek buffer
00406B6E   . C605 9F104000 >MOV BYTE PTR DS:[40109F],2C ; edit the code to use the #repl buffer
00406B75   > E8 86A4FFFF    CALL COPY_OF_.00401000	; call the patching code
00406B7A   > 33C0           XOR EAX,EAX			; return from the DlgProc
00406B7C   . C2 1000        RETN 10
00406B7F     C605 7C104000 >MOV BYTE PTR DS:[40107C],2C	; edit the code to use the #repl buffer
00406B86   . C605 9F104000 >MOV BYTE PTR DS:[40109F],0C ; edit the code to use the #chek buffer
00406B8D   .^EB E6          JMP SHORT COPY_OF_.00406B75
```