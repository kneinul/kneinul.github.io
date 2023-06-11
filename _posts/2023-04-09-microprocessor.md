---
title: Microprocessor
author: LJY
date: 2023-04-09 21:46:00 +0900
categories: [etc.]
tags: []
pin: true
---

## AVR 소개
* Atmel사에서 제조한 modified harvard architecture 8-bit RISC 마이크로 컨트롤러로서 1996년에 개발
* AVR(Alf(Bogen) Vergard(Wollan) RISC)은 제작자의 이름을 본따 만들었다.
* AVR 제품군 중에서 가장 많이 사용되고 있는 제품 중의 하나인 AVR Atmega128 프로세서를 다룬다.

## Atmega128의 특징
* 향상된 RISC 구조
    + 133개의 명령어가 대부분 단일 clock cycle에 수행―고속 MCU
    + 32 X 8의 범용 레지스터와 주변장치 제어 레지스터
    + Fully Static Operation
        - 16MHz clock에서 16MIPS 이상의 처리속도
    + 2 cycle에 수행되는 곱셈기가 내장
* 메모리
    + 128K Bytes의 프로그램 저장용 내부 플래쉬 메모리(10,000번 쓰고 지우기 가능)
    + 4K Bytes 내부 EEPROM (100,000번 쓰고 지우기 가능)
    + 4K Bytes 내부 SRAM
    + 최대 64K Bytes의 외부 SRAM 확장 가능
    + In-System Programming(ISP)를 위해 Serial Peripheral Interface(SPI) 사용
        - ISP : EEPROM, 플래시 메모리 등의 비휘발성 메모리 등을 보드(혹은 시스템)에 이미 장착한 이후에, 그 내용물을 프로그래밍 하는 것
        - 제조회사가 프로그래밍된 칩을 다른 회서로부터 사지 않고, 자신의 회사 시스템 생산 과정에서 프로그래밍
        - SPI : SPI는 Serial Peripheral Interface의 약자로 MCU와 메모리, 센서 등 주변장치 간에 사용할 수 있는 시리얼 통신 규약 중 하나. Motorola사에서 처음 고안.


.
.
.

컴구 복습 후 마프 1주차 강의자료 다시 정리해야 함...

.
.
.

## Atmega128의 사용 방법 및 관련 특징
1. 소스 코드 작성 및 컴파일과 링크 작업을 호스트 PC에서 진행
    + 호스트 PC : 프로그램 개발을 진행하는 PC 환경
    + IDE 종류로는 WinAVR, AVR Studio가 있다. 모두 AVR용 C언어 컴파일러인 avr-gcc 및 다양한 개발 도구들을 제공
2. 실행 파일을 타겟 보드에 다운로드한 뒤 실행
    + 타겟 보드 : 프로그램이 실제로 실행되는 임베디드 환경
    + 링커를 통해서 만들어진 실행 파일을 AVR 칩의 프로그래밍 방법인 ISP를 통해 전송
    + FTDI Chip에서 제공하는 VCP(Virtual COM port) 드라이버와 관련된 인터페이스 장치를 사용

* Cross Compiler
    + A cross compiler is a compiler capable of creating executable code for a platform other than the one on which the compiler is running 
        - E.g., a compiler that runs on a PC but generates code that runs on Android smartphone
    + A cross compiler is necessary to compile code for multiple platforms from one development host. Direct compilation on the target platform might be infeasible.
        - E.g., on embedded systems with limited computing resources

* Embedded C
    + Embedded C is a set of language extentions for the C programming language by the C standards Committee to address commonality issues that exist between C extentions for different embedded systems.
    + Embedded C programming typically requires nonstandard extentions to the C language in order to support enhanced microprocessor features such as fixed-point arithmetic, multiple distinct memory banks, and basic I/O operations.
    + ※ volatile in Embedded C
        - 최적화 등 컴파일러의 재량을 제한하는 역할 담당. volatile로 선언된 변수는 모두 온전히 컴파일되도록 강제.
    + ※ data-types for avr-gcc(컴파일러)
        - (signed/unsigned) char : 1byte
        - (signed/unsigned) short : 2byte
        - (signed/unsigned) int : 2byte
        - (signed/unsigned) long : 4byte
        - (signed/unsigned) long long : 8byte
        - float : 4byte (floating point)
        - double : alias to float
* <details>   
  <summary><u>전체 구조</u></summary>
  <div markdown="1">
  ![Atmega128 diagram](/assets/img/Microprocessor/Atmega128 diagram.png)
  </div>
  </details>
* I/O Port
    + On/Off 정보를 내어 놓거나 받아들이는 장치
    + 포트 A에서 포트 F까지(+G) 각 8bit인 6개의 입출력 포트
    + 최대 48bit의 디지털 입출력 신호를 주고 받을 수 있다.
    + Direction Register(DDR?)
        - 데이터의 입출력 방향 설정
        - 각 비트에 0을 쓰면 핀은 포트 방향을 입력으로 설정, 1을 쓰면 출력으로 설정
    + Data Register(PORT?)
        - DDR? 포트에서 출력으로 설정되어있는 핀의 데이터를 출력하는 레지스터
        - 각 비트에 0을 쓰면 핀에서는 low가 출력, 1을 쓰면 high가 출력
    + Input Pin Register(DDR?)
        - DDR? 포트에서 입력으로 설정되어있는 핀의 데이터를 입력하는 레지스터

## Atmega128에 내장된 각종 기능 이용 code
* ※ External BUS Interface
    + 항상 비활성화로 초기화 되어 있음―사용 시 활성화 필요(MCU Control Register)
    + 외부 메모리 접근
    + 출력만 가능
    + ```c
      MCUCR=0X??
      ```
* 내/외부 LED
    + 내부 LED―I/O Port D
        - DDRD, PORTD 설정으로 동작
        - ```c
          ...
          DDRD=0xc0;
          PORTD=0X40; // 0번(위쪽) LED On
          PORTD=0x80; // 1번(아래쪽) LED On
          ...

          ```
    + 외부 LED―External BUS Interface
        - 출력만 가능
        - ```c
          #define EX_LED (*(volatile unsigned char *) 0x8008)
          ...
          MCUCR = 0x80; // 외부 LED 이용 위해 MCUCR의 MSB 1로 설정
          EX_LED = 0xc0; // MSB부터 2개 LED On
          ...
          ```
    + + Brightness Control
        - How? PWM(Pulse Width Modulation)!
        - <details>   
          <summary><u>example</u></summary>
          <div markdown="1">
          ```c
          /* 메모리 주소 0x8008에 매핑된 LED의 좌측부터 차례대로 
             밝기가 서서히 변하는 프로그램을 작성하시오.*/
          // 초기값: *(0x8008) = 0x00
          // 완전히 켜진 LED는 꺼진 후, 다음 LED와 동작 동기화
          #if 1

          #include <avr/io.h>
          #define EX_LED (*(volatile unsigned char *) 0x8008)
          #define BRIGHTNESS 255

          void delay(long int i){
              volatile char k;
            while(i--){
                  for(k=0; k<30; k++);
              }
          }

          void TurnOff(volatile int input){
              for(unsigned int i=0; i<BRIGHTNESS; i++){
                  EX_LED=0x00;
                  delay(i);
                  EX_LED=input;
                  delay(BRIGHTNESS-i);
             }
          }

          void TurnOn(volatile int input){
              for(unsigned int i=0; i<BRIGHTNESS; i++){
                  EX_LED=input;
                  delay(i);
                  EX_LED=0x00;
                  delay(BRIGHTNESS-i);
              }
          }
          
          int main(void){
              
              MCUCR=0x80;
              EX_LED=0x00;
              volatile int input;
              
              while(1){
                  input=0x80;
                  for(int i=0; i<8; i++){
                      TurnOn(input);
                      TurnOff(input);
                      input=input>>1;
                      input=input|0x80;
                  }
              }
          }

          #endif
          ```
          </div>
          </details>
* 스위치―I/O Port B
    + DDRB, PINB 설정으로 동작
    + ```c
      ...
      DDRB=0x00;
      if(PINB & i) {
      ...
      ```
* 7-segment―External BUS Interface
    + ```c
      #define SS_DATA (*(volatile unsigned char *) 0x8002)  // Data
      #define SS_SEL (*(volatile unsigned char *) 0x8003)   // Digit
      ...
      MCUCR = 0x80;
      SS_SEL=0b11110111;    // 하위 4bit MSB에 위치한 Digit[0] 선택. 
                            // 4개의 7-segment 중 가장 왼쪽부터 Digit[3]. 
                            // low:Enable, high:Disable
      SS_DATA=0b00000110;   // 8bit MSB부터 Data[0]. Data[5], Data[6] led on. □□□1 출력됨.
                            // Data[0]:dot, Data[1]:G ~ Data[7]:A. 
                            // 위 막대부터 시계방향으로 A~F. 가운데 막대 G.
      ...
      ```
* Dot matrix―External BUS Interface
    + ```c
      #define DM_SEL (*(volatile unsigned char *) 0x8004)
      #define DM_SELR (*(volatile unsigned char *) 0x8005)
      #define DM_DATA (*(volatile unsigned char *) 0x8006)
      #define DM_DATAR (*(volatile unsigned char *) 0x8007)
      ...
      MCUCR=0x80;
      DM_SEL=0x01;  // 첫번째(맨 위) 행 sel[0] 선택.
                    // 상위 8bit 중 LSB부터 2bit는 sel[8], sel[9].
                    // low:Disable, high:Enable
      DM_DATA=0xff; 
      DM_DATAR=0x01;
      // 맨 오른쪽 열부터 9개의 led, Data[0]~Data[8] led on. ○●●●●●●●●● 출력됨.
      DM_DATA=0x01ff;
      /* 이렇게 한 번에 할당할 수도 있으나 위 #define 에서 변수 선언 시
         data-type을 char이 아닌 short나 int로 바꿔
         길이를 2byte로 늘려야 한다
      */
      ...
      ```


## 출처
- 마이크로프로세서 수업 한창희 교수님 강의자료
- https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=eztcpcom&logNo=220193667359