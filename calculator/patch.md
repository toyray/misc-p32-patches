## Calculator v5.00.1764.1 (Func.)

### New Functionality

Set Calculator to always display Scientific view

`10016B9` - bypass code to call GetProfileInt to get the value of Layout for SciCalc app in win.ini
- From  : `FF1528100001` call dword ptr [01001028]
- To    : `83C40B33C090` add esp, 12 -> xor eax, eax -> nop

Set Calculator to always use Hexadecimal number system

`1005DD7` - code to move identifier of Dec radio button into esi
- From  : `8BF7` mov esi, edi - code to load esi with resource identifier of Dec radio button in edi
- To    : `89D6` mov esi, edx

### Disassembly of New Code (W32Dasm)

```
* Possible Reference to String Resource ID=00001: "C"
                                  |
:010016AD 6A01                    push 00000001                 ; param 3 [DefaultValue]
:010016AF 687C120001              push 0100127C                 ; param 2 [KeyName - "Layout"]
	
* Possible StringData Ref from Data Obj ->"SciCalc"
                                  |
:010016B4 6828300101              push 01013028                 ; param 1 [AppName - "SciCalc"]

* Reference To: KERNEL32.GetProfileIntA, Ord:0147h
                                  |
:010016B9 FF1528100001            Call dword ptr [01001028]     ; function call to get value of "Layout" in SciCalc
									  in win.ini	
* Possible Reference to String Resource ID=00001: "C"
                                  |
:010016BF 6A01                    push 00000001
:010016C1 A34C3D0101              mov dword ptr [01013D4C], eax ; mov [LayoutValue] into [LayoutFlag]
:010016C6 E8BA050000              call 01001C85
:010016CB 6A69                    push 00000069
:010016CD FF35483D0101            push dword ptr [01013D48]

_______________________________________________________________________________________________________________


* Referenced by a CALL at Addresses:
|:01001E58   , :01002BF1   
|
:01005DA9 53                      push ebx
:01005DAA 56                      push esi
:01005DAB 8B74240C                mov esi, dword ptr [esp+0C]		; mov [FunctionCallFlag] into esi (10 = Init, others 									  = Runtime)
:01005DAF 57                      push edi
:01005DB0 8BC6                    mov eax, esi                  ; mov [FunctionCallFlag] into eax
:01005DB2 BA8F000000              mov edx, 0000008F             ; mov [HexId] into edx
:01005DB7 48                      dec eax			
:01005DB8 BF8E000000              mov edi, 0000008E             ; mov [DecId] into edi
:01005DBD 48                      dec eax	
:01005DBE B98C000000              mov ecx, 0000008C             ; mov [BinId] into ecx
:01005DC3 741D                    je 01005DE2                   ; check if eax is equal to 0
:01005DC5 83E806                  sub eax, 00000006
:01005DC8 7411                    je 01005DDB                   ; check if eax is equal to 0
:01005DCA 48                      dec eax
:01005DCB 48                      dec eax
:01005DCC 7409                    je 01005DD7                   ; check if eax is equal to 0
:01005DCE 83E806                  sub eax, 00000006
:01005DD1 7511                    jne 01005DE4                  ; check if eax is not equal to 0 (jumps here)
:01005DD3 8BF2                    mov esi, edx                  ; mov [HexId] into [CurrentId]
:01005DD5 EB0D                    jmp 01005DE4

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005DCC(C)
|
:01005DD7 8BF7                    mov esi, edi                  ; mov [DecId] into [CurrentId] (executes only if [FunctionCallFlag] = 10)
:01005DD9 EB09                    jmp 01005DE4

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005DC8(C)
|
:01005DDB BE8D000000              mov esi, 0000008D             ; mov [OctId] into [CurrentId]
:01005DE0 EB02                    jmp 01005DE4

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005DC3(C)
|
:01005DE2 8BF1                    mov esi, ecx                  ; mov [BinId] into [CurrentId]

* Referenced by a (U)nconditional or (C)onditional Jump at Addresses:
|:01005DD1(C), :01005DD5(U), :01005DD9(U), :01005DE0(U)
|

* Possible Reference to String Resource ID=00003: "Backspace"
                                  |
:01005DE4 6A03                    push 00000003                 ; push [CurrentSizeType] onto stack (0 = Bin, 1 = Oct, 2 = Hex and 3 = Dec)
:01005DE6 3BF7                    cmp esi, edi				          ; check if [CurrentId] is not equal to [DecId]
:01005DE8 5B                      pop ebx                       ; pop [CurrentSizeType] into ebx
:01005DE9 7508                    jne 01005DF3	
:01005DEB 8B3D503D0101            mov edi, dword ptr [01013D50] ; mov 0 into edi
:01005DF1 EB08                    jmp 01005DFB

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005DE9(C)
|
:01005DF3 8B3D543D0101            mov edi, dword ptr [01013D54] ; mov edi
:01005DF9 33DB                    xor ebx, ebx                  ; clear ebx (CurrentSizeType = 0)

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005DF1(U)
|
:01005DFB 3BF1                    cmp esi, ecx                  ; check if [CurrentId] is less than [BinId]
:01005DFD 721D                    jb 01005E1C
:01005DFF 56                      push esi                      ; param 4 [CheckId - DefaultId]
:01005E00 52                      push edx                      ; param 3 [LastId  - HexId]
:01005E01 51                      push ecx                      ; param 2 [FirstId - BinId]
:01005E02 FF356C3D0101            push dword ptr [01013D6C]     ; param 1 [HwndDlg - Hwnd of Calc.exe] 

* Reference To: USER32.CheckRadioButton, Ord:0036h
                                  |
:01005E08 FF1578110001            Call dword ptr [01001178]     ; call CheckRadioButton to check radio button of [DefaultId]
:01005E0E 8B04B580300101          mov eax, dword ptr [4*esi+01013080]	; mov [NumberSystem] into eax
:01005E15 A31C300101              mov dword ptr [0101301C], eax ; mov [NumberSystem] into [NumberSystemFlag]
:01005E1A EB16                    jmp 01005E32

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005DFD(C)
|
:01005E1C 6A00                    push 00000000
:01005E1E 52                      push edx
:01005E1F 51                      push ecx
:01005E20 FF356C3D0101            push dword ptr [01013D6C]

* Reference To: USER32.CheckRadioButton, Ord:0036h
                                  |
:01005E26 FF1578110001            Call dword ptr [01001178]
:01005E2C 89351C300101            mov dword ptr [0101301C], esi

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005E1A(U)
|
:01005E32 81C792000000            add edi, 00000092			        ; mov 146 into edi (SizeId)
:01005E38 57                      push edi				              ; param 4 [CheckId - SizeId]	
:01005E39 6894000000              push 00000094                 ; param 3 [LastId  - ByteId]	
:01005E3E 6892000000              push 00000092                 ; param 2 [FirstId - DwordId]	
:01005E43 FF356C3D0101            push dword ptr [01013D6C]     ; param 1 [HwndDlg - Hwnd of Calc.exe]

* Reference To: USER32.CheckRadioButton, Ord:0036h
                                  |
:01005E49 FF1578110001            Call dword ptr [01001178]     ; call CheckRadioButton to check radio button of [SizeId]
:01005E4F 33FF                    xor edi, edi                  ; clear edi
:01005E51 8D349D283C0101          lea esi, dword ptr [4*ebx+01013C28]	; mov [Type] into esi

* Referenced by a (U)nconditional or (C)onditional Jump at Address:
|:01005E74(C)
|	
:01005E58 FF36                    push dword ptr [esi]          ; param 3 [String - "Dword","Word","Byte"]
:01005E5A 8D8792000000            lea eax, dword ptr [edi+00000092]	; mov [currentSizeId] into eax
:01005E60 50                      push eax                      ; param 2 [CurrentSizeId]
:01005E61 FF356C3D0101            push dword ptr [01013D6C]     ; param 1 [HwndDlg - Hwnd of Calc.exe]

* Reference To: USER32.SetDlgItemTextA, Ord:022Ch
                                  |
:01005E67 FF1540110001            Call dword ptr [01001140]     ; call SetDlgItemText to set caption of SizeId's radio buttons
:01005E6D 47                      inc edi				; inc edi
:01005E6E 83C604                  add esi, 00000004             ; add 4 to esi (to get address of next caption in list)
:01005E71 83FF03                  cmp edi, 00000003             ; check if all three radio buttons do not have captions
:01005E74 7CE2                    jl 01005E58	
:01005E76 E852EDFFFF              call 01004BCD
:01005E7B E882FEFFFF              call 01005D02
:01005E80 E880E0FFFF              call 01003F05
:01005E85 5F                      pop edi
:01005E86 5E                      pop esi
:01005E87 5B                      pop ebx
:01005E88 C20400                  ret 0004
```