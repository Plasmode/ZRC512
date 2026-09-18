# Driving WS2812 NeoPixel LED with ZRC512
The timing spec for WS2812 is fairly loose; it is nominally 800KHz 33%-67% pulse-width modulation. The 1.25uS period is divided into 3 phases. The first phase, nominally 400nS, is always high; the 2nd phase is also 400nS in duration and contains the actual information such that high denotes '1' or low denotes '0'; the third phase is 450nS and always low. The timing is fairly loose with tolerance of +/-150nS.

ZRC512 is designed for 22MHz clock which is too slow to software bit-bang WS2812 without hardware help. ZRC512 CPLD has enough spare logic to provide hardware assistance to generate timely output to drive WS2812. The schematic below shows a return-to-zero pulse generator that either generate a 363nS high pulse if D[7]=0, or a 726nS high pulse if D[7]=1. Ideally the software provides a new D[7] value every 1250nS, but WS2812 is fairly tolerant of timing variation in low pulse. The following routine will provide 1260nS pulse period when outputting the 8-bit color value, and 1935nS pulse period between 8-bit of colors. 1260nS period is exactly the correct timing and 1935nS appears to be adequate.
```
doGrn:
    out (WS2812),a    ;11
    rla               ;4
    djnz doGrn        ;13 or 8 B==0

    ld b,8            ;7
    inc hl            ;6
    ld a,(hl)         ;7
doRed:
    out (WS2812),a
    rla
    djnz doRed

    ld b,8
    inc hl
    ld a,(hl)
doBlue:
    out (WS2812),a
    rla
    djnz doBlue

    ld b,8
    inc hl
    ld a,(hl)
;more LED, so on...
```
### TO DO
upload picture of ZRC512 driving WS2812B

upload demo program
