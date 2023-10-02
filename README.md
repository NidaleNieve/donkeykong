# Donkey Kong Spilaútskýring
Spilið er mjög svipað spilinu "Snakes and Ladders", sá sem kemst að "END" reitnum fyrst vinnur.
leikmaður ýtir á Græna takkann til þess að kasta teningnum og fer jafn marga reiti áfram og talan segir. Ef að maður lendir á Stiga þá ferðu upp hann og ef að þú lendir á banana farðu á síðasta "banana-reit" bakvið þig. 
Ef að öll ljósin og ljósið á takkanum blikka þá er það special og maður fer að næsta stiga og ferð upp hann. 

# Höfundar
Höfundarnir eru Daníel og Leó

## Hönnunar teikningin
![BoardGame Final](https://github.com/NidaleNieve/donkeykong/assets/62642672/06542175-10d3-49e1-aa3a-68c5db186e89)

## Ljósmynd af borðspilinu

![inní](https://github.com/NidaleNieve/donkeykong/assets/62642672/c809f128-e7f9-4703-8e00-62a3d926a9d4)

![utan](https://github.com/NidaleNieve/donkeykong/assets/62642672/7df25e81-dc0a-401a-9173-4529c54fa47a)

## Ljósmynd af lóðunn

![lóðun vírar](https://github.com/NidaleNieve/donkeykong/assets/62642672/1caed11a-09ac-497b-9138-bc9556f6b440)
![lóðun front](https://github.com/NidaleNieve/donkeykong/assets/62642672/e80ee339-2273-423d-aaf2-4ea9c0619e94)
![Lóðun bak](https://github.com/NidaleNieve/donkeykong/assets/62642672/8fd2d305-dfcb-4483-8759-f70776a1da81)

## Mynband af okkur að spila spilið

[Þetta er linkur](https://youtu.be/C6F_-i7GbiU)


## Kóði
```python
from machine import Pin, PWM
import random
import time
from utime import sleep

led1 = PWM(Pin(41), 10000)
led2 = PWM(Pin(11), 10000)
led3 = PWM(Pin(13), 1000)
led4 = PWM(Pin(4), 1000)
led5 = PWM(Pin(5), 1000)
led6 = PWM(Pin(35), 1000)
led7 = PWM(Pin(18), 1000)
led8 = Pin(16, Pin.OUT)
led9 = Pin(7, Pin.OUT)
led10 =Pin(1, Pin.OUT)

takki = Pin(2, Pin.IN, Pin.PULL_UP)
last_button_press_time = 0

buzzer = PWM(Pin(21))

sensor1 = Pin(48, Pin.IN, Pin.PULL_UP)
sensor2 = Pin(9, Pin.IN, Pin.PULL_UP)

game_started = False
game_finished = False

def play_mario_theme(buzzer):
    tones = {
    "B0": 31,"C1": 33,"CS1": 35,"D1": 37,"DS1": 39,"E1": 41,"F1": 44,"FS1": 46,
    "G1": 49,"GS1": 52,"A1": 55,"AS1": 58,"B1": 62,"C2": 65,
    "CS2": 69,"D2": 73,"DS2": 78,"E2": 82,"F2": 87,"FS2": 93,"G2": 98,
    "GS2": 104,"A2": 110,"AS2": 117,"B2": 123,"C3": 131,"CS3": 139,
    "D3": 147,"DS3": 156,"E3": 165,"F3": 175,"FS3": 185,
    "G3": 196,"GS3": 208,"A3": 220,"AS3": 233,"B3": 247,"C4": 262,"CS4": 277,"D4": 294,"DS4": 311,
    "E4": 330,"F4": 349,"FS4": 370,"G4": 392,"GS4": 415,"A4": 440,"AS4": 466,"B4": 494,"C5": 523,"CS5": 554,"D5": 587,"DS5": 622,"E5": 659,"F5": 698,
    "FS5": 740,"G5": 784,"GS5": 831,"A5": 880,"AS5": 932,"B5": 988,"C6": 1047,"CS6": 1109,"D6": 1175,"DS6": 1245,"E6": 1319,"F6": 1397,"FS6": 1480,"G6": 1568,"GS6": 1661,
    "A6": 1760,"AS6": 1865,"B6": 1976,"C7": 2093,"CS7": 2217,"D7": 2349,"DS7": 2489,"E7": 2637,"F7": 2794,"FS7": 2960,"G7": 3136,"GS7": 3322,"A7": 3520,
    "AS7": 3729,"B7": 3951,"C8": 4186,"CS8": 4435,"D8": 4699,"DS8": 4978
    }

    mario = ["C4", 0, "C4", 0, "A4", 0, 0, 0, 0, "F4", 0, "G4", 0, "F4", 0, "D4", 0, 0, 0, 0,
             "D4", 0, "D4", 0, "AS4", 0, 0, 0, 0, "A4", 0, "AS4", 0, "A4", 0, "E4", 0, 0, 0, 0,
             "E4", 0, "E4", 0, "C5", 0, 0, 0, 0, "A4", 0, "AS4", 0, "C5", 0, "D5", 0, 0, 0, 0,
             "F4", 0, "G4", 0, "A4", 0, 0, 0, 0, "D4", 0, "E4", 0, "F4", 0, "D4"]

    def playtone(frequency):
        buzzer.duty_u16(4000)
        buzzer.freq(frequency)

    def bequiet():
        buzzer.duty_u16(0)

    def playsong(mysong, pause_duration=0.11):
        for i in range(len(mysong)):
            if mysong[i] == 0:
                bequiet()
                # Shorten the pause duration
                sleep(pause_duration / 20)  # Adjust this value to your desired pause duration
            else:
                playtone(tones[mysong[i]])
            # Maintain the original note duration
            sleep(pause_duration)
        bequiet()

    playsong(mario)

def GameStart():
    global game_started
    if not game_started:
        tones = {
            "B0": 31,"C1": 33,"CS1": 35,"D1": 37,"DS1": 39,"E1": 41,"F1": 44,"FS1": 46,
            "G1": 49,"GS1": 52,"A1": 55,"AS1": 58,"B1": 62,"C2": 65,
            "CS2": 69,"D2": 73,"DS2": 78,"E2": 82,"F2": 87,"FS2": 93,"G2": 98,
            "GS2": 104,"A2": 110,"AS2": 117,"B2": 123,"C3": 131,"CS3": 139,
            "D3": 147,"DS3": 156,"E3": 165,"F3": 175,"FS3": 185,
            "G3": 196,"GS3": 208,"A3": 220,"AS3": 233,"B3": 247,"C4": 262,"CS4": 277,"D4": 294,"DS4": 311,
            "E4": 330,"F4": 349,"FS4": 370,"G4": 392,"GS4": 415,"A4": 440,"AS4": 466,"B4": 494,"C5": 523,"CS5": 554,"D5": 587,"DS5": 622,"E5": 659,"F5": 698,
            "FS5": 740,"G5": 784,"GS5": 831,"A5": 880,"AS5": 932,"B5": 988,"C6": 1047,"CS6": 1109,"D6": 1175,"DS6": 1245,"E6": 1319,"F6": 1397,"FS6": 1480,"G6": 1568,"GS6": 1661,
            "A6": 1760,"AS6": 1865,"B6": 1976,"C7": 2093,"CS7": 2217,"D7": 2349,"DS7": 2489,"E7": 2637,"F7": 2794,"FS7": 2960,"G7": 3136,"GS7": 3322,"A7": 3520,
            "AS7": 3729,"B7": 3951,"C8": 4186,"CS8": 4435,"D8": 4699,"DS8": 4978}

        mario = ["G3", "G3", "G3", 0, "A4", "A4", 0, "AS4", "AS4", "AS4", 0, "G3", "G3", "G3", 0,
                 "FS4", "G4", "FS4", "G4", "FS4", "G4", "FS4", "G4", "FS4", "G4", "FS4", "G4", "FS4", "G4", "G4", "G4", "G4"]

        def playtone(frequency):
            buzzer.duty_u16(4000)
            buzzer.freq(frequency)

        def bequiet():
            buzzer.duty_u16(0)

        def playsong(mysong, pause_duration=0.11):
            for i in range(len(mysong)):
                if mysong[i] == 0:
                    bequiet()
                    # Shorten the pause duration
                    sleep(pause_duration / 20)  # Adjust this value to your desired pause duration
                else:
                    playtone(tones[mysong[i]])
                # Maintain the original note duration
                sleep(pause_duration)
            bequiet()

        playsong(mario)
        game_started = True
def play_game_over():
    global game_finished
    if not game_finished:
        tones = {
        "B0": 31,"C1": 33,"CS1": 35,"D1": 37,"DS1": 39,"E1": 41,"F1": 44,"FS1": 46,
        "G1": 49,"GS1": 52,"A1": 55,"AS1": 58,"B1": 62,"C2": 65,
        "CS2": 69,"D2": 73,"DS2": 78,"E2": 82,"F2": 87,"FS2": 93,"G2": 98,
        "GS2": 104,"A2": 110,"AS2": 117,"B2": 123,"C3": 131,"CS3": 139,
        "D3": 147,"DS3": 156,"E3": 165,"F3": 175,"FS3": 185,
        "G3": 196,"GS3": 208,"A3": 220,"AS3": 233,"B3": 247,"C4": 262,"CS4": 277,"D4": 294,"DS4": 311,
        "E4": 330,"F4": 349,"FS4": 370,"G4": 392,"GS4": 415,"A4": 440,"AS4": 466,"B4": 494,"C5": 523,"CS5": 554,"D5": 587,"DS5": 622,"E5": 659,"F5": 698,
        "FS5": 740,"G5": 784,"GS5": 831,"A5": 880,"AS5": 932,"B5": 988,"C6": 1047,"CS6": 1109,"D6": 1175,"DS6": 1245,"E6": 1319,"F6": 1397,"FS6": 1480,"G6": 1568,"GS6": 1661,
        "A6": 1760,"AS6": 1865,"B6": 1976,"C7": 2093,"CS7": 2217,"D7": 2349,"DS7": 2489,"E7": 2637,"F7": 2794,"FS7": 2960,"G7": 3136,"GS7": 3322,"A7": 3520,
        "AS7": 3729,"B7": 3951,"C8": 4186,"CS8": 4435,"D8": 4699,"DS8": 4978
        }

        game_over_tune = ["C4", "C4", "C4", 0, 0, "G4", "A4", "B4", "B4", "G4", 0, 0, 0, "G4", "A4", "B4", "B4", "G4"]

        def playtone(frequency):
            buzzer.duty_u16(4000)
            buzzer.freq(frequency)

        def bequiet():
            buzzer.duty_u16(0)
        
        def playsong(mysong, pause_duration=0.11):
            for i in range(len(mysong)):
                if mysong[i] == 0:
                    bequiet()
                    # Shorten the pause duration
                    sleep(pause_duration / 20)  # Adjust this value to your desired pause duration
                else:
                    playtone(tones[mysong[i]])
                # Maintain the original note duration
                sleep(pause_duration)
            bequiet()

        playsong(game_over_tune)
        game_finished = True
        
def rollDice():
    result = random.randint(1, 7)
    print("You rolled:", result)
    
    if result == 1:
        einn()
    elif result == 2:
        tveir()
    elif result == 3:
        thrir()
    elif result == 4:
        fjorir()
    elif result == 5:
        fimm()
    elif result == 6:
        sex()
    elif result == 7:
        special()

def animation():
    for _ in range(3):
        led1.duty(200)
        led2.duty(200)
        led3.duty(200)
        led4.duty(0)
        led5.duty(0)
        led6.duty(0)
        led7.duty(0)
        led8.value(0)
        led9.value(0)
        led10.value(0)
        time.sleep(0.1)
        led1.duty(700)
        led2.duty(700)
        led3.duty(700)
        led4.duty(0)
        led5.duty(0)
        led6.duty(0)
        led7.duty(0)
        led8.value(0)
        led9.value(0)
        led10.value(1)
        time.sleep(0.1)
        led1.duty(200)
        led2.duty(200)
        led3.duty(200)
        led4.duty(200)
        led5.duty(200)
        led6.duty(200)
        led7.duty(0)
        led8.value(0)
        led9.value(0)
        led10.value(0)
        time.sleep(0.1)
        led1.duty(0)
        led2.duty(0)
        led3.duty(0)
        led4.duty(700)
        led5.duty(700)
        led6.duty(700)
        led7.duty(0)
        led8.value(0)
        led9.value(0)
        led10.value(1)
        time.sleep(0.1)
        led1.duty(0)
        led2.duty(0)
        led3.duty(0)
        led4.duty(200)
        led5.duty(200)
        led6.duty(200)
        led7.duty(200)
        led8.value(1)
        led9.value(1)
        led10.value(0)
        time.sleep(0.1)
        led1.duty(0)
        led2.duty(0)
        led3.duty(0)
        led4.duty(0)
        led5.duty(0)
        led6.duty(0)
        led7.duty(700)
        led8.value(1)
        led9.value(1)
        led10.value(1)
        time.sleep(0.1)
        led1.duty(0)
        led2.duty(0)
        led3.duty(0)
        led4.duty(0)
        led5.duty(0)
        led6.duty(0)
        led7.duty(200)
        led8.value(1)
        led9.value(1)
        led10.value(0)



def einn():
    led1.duty(0)
    led2.duty(0)
    led3.duty(0)
    led4.duty(0)
    led5.duty(500)
    led6.duty(0)
    led7.duty(0)
    led8.value(0)
    led9.value(0)

def tveir():
    led1.duty(700)
    led2.duty(0)
    led3.duty(0)
    led4.duty(0)
    led5.duty(0)
    led6.duty(0)
    led7.duty(0)
    led8.value(0)
    led9.value(1)
    
def thrir():
    led1.duty(700)
    led2.duty(0)
    led3.duty(0)
    led4.duty(0)
    led5.duty(700)
    led6.duty(0)
    led7.duty(0)
    led8.value(0)
    led9.value(1)
    
def fjorir():
    led1.duty(700)
    led2.duty(0)
    led3.duty(700)
    led4.duty(0)
    led5.duty(0)
    led6.duty(0)
    led7.duty(700)
    led8.value(0)
    led9.value(1)
    
def fimm():
    led1.duty(700)
    led2.duty(0)
    led3.duty(700)
    led4.duty(0)
    led5.duty(700)
    led6.duty(0)
    led7.duty(700)
    led8.value(0)
    led9.value(1)
    
def sex():
    led1.duty(700)
    led2.duty(0)
    led3.duty(700)
    led4.duty(700)
    led5.duty(0)
    led6.duty(700)
    led7.duty(700)
    led8.value(0)
    led9.value(1)
    
def special():
    for _ in range(6):
        led1.duty(0)
        led2.duty(0)
        led3.duty(0)
        led4.duty(0)
        led5.duty(0)
        led6.duty(0)
        led7.duty(0)
        led8.value(0)
        led9.value(0)
        led10.value(0)
        time.sleep(0.2)
        led1.duty(700)
        led2.duty(700)
        led3.duty(700)
        led4.duty(700)
        led5.duty(700)
        led6.duty(700)
        led7.duty(700)
        led8.value(1)
        led9.value(1)
        led10.value(1)
        time.sleep(0.2)


play_mario_theme(buzzer)
while True:
    if not takki.value():
        current_time = time.ticks_ms()
        if current_time - last_button_press_time >= 1000:
            last_button_press_time = current_time
            led1.duty(0)
            led2.duty(0)
            led3.duty(0)
            led4.duty(0)
            led5.duty(0)
            led6.duty(0)
            led7.duty(0)
            led8.value(0)
            led9.value(0)
            led10.value(0)
            animation()
            rollDice()
    if not sensor1.value():
        GameStart()
    if not sensor2.value():
        play_game_over()        
```
