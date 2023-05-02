# boe-bot-line-follow-single-qtr
Parallax Boe-Bot line following using a single QTR sensor.

Hardware

    Parallax Boe-Bot
    2 x Servo Motors [pins 12 and 13]
    Line Sensors [pin 3]
    Red LED [pin 10]
    Piezo Speaker [pin 4]
    
Code

    ' Line Follow

    ' {$STAMP BS2}
    ' {$PBASIC 2.5}

    DEBUG "Program Start..."

    repeat         VAR Byte ' repeat, used for various features
    counter        VAR Byte ' count, used for various features
    alarmcounter   VAR Byte ' count, used for various features
    flag           VAR Bit  ' found line flag
    scantime       VAR Word ' detected scan time value
    mult           VAR Byte ' seek multiplier
    threshold      VAR Byte ' sensor threshold
    lastdir        VAR Bit  ' last successful seek direction

    ' start warning sound
    FOR alarmcounter = 1 TO 4
      FREQOUT 4, 100, 3000
      PAUSE 200
    NEXT

    threshold = 60

    DO
      HIGH 3
      RCTIME 3, 1, scantime

      IF (scantime > threshold) THEN
        HIGH 10
        PULSOUT 13, 770
        PULSOUT 12, 730
        PAUSE 20
        mult = 1
      ELSE
        LOW 10
        flag = 0
        counter = 8 * mult
        DO
    '      FREQOUT 4, 100, 3000
          IF lastdir = 0 THEN
            PULSOUT 13, 740
            PULSOUT 12, 740
          ELSE
            PULSOUT 13, 760
            PULSOUT 12, 760
          ENDIF
          PAUSE 5
          HIGH 3
          RCTIME 3, 1, scantime
          IF (scantime > threshold) THEN
            flag = 1
            IF lastdir = 0 THEN
              lastdir = 0
            ELSE
              lastdir = 1
            ENDIF
          ENDIF
          PAUSE 50
          counter = counter - 1
        LOOP UNTIL flag = 1 OR counter = 0
        IF flag = 0 THEN
          counter = 8 * mult * 2
          DO
    '        FREQOUT 4, 100, 3000
            IF lastdir = 1 THEN
              PULSOUT 13, 740
              PULSOUT 12, 740
            ELSE
              PULSOUT 13, 760
              PULSOUT 12, 760
            ENDIF
            PAUSE 5
            HIGH 3
            RCTIME 3, 1, scantime
            IF (scantime > threshold) THEN
              flag = 1
              IF lastdir = 0 THEN
                lastdir = 1
              ELSE
                lastdir = 0
              ENDIF
            ENDIF
            PAUSE 50
            counter = counter - 1
          LOOP UNTIL flag = 1 OR counter = 0
        ENDIF
        mult = mult + 1
      ENDIF
    LOOP

    END
