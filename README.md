# ledmatrix
#include <SoftwareSerial.h>
#define LEDTime 200 // how long the LED stays on, in microseconds

const int buttonPin1 = A5;
const int buttonPin2 = A4;
const int buttonPin3 = A3;
const int buttonPin4 = A2;
const int buttonPin5 = A1;
const int buttonPin6 = A0;
const int buttonPin7 = 2;
const int buttonPin8 = 12;
const int buttonPin9 = 13;
int buttonState1 = 0;
int buttonState2 = 0;
int buttonState3 = 0;
int buttonState4 = 0;
int buttonState5 = 0;
int buttonState6 = 0;
int buttonState7 = 0;
int buttonState8 = 0;
int buttonState9 = 0;
int c=9;
int level=168;
int RedLEDPins[] = {11, 10, 9};
int GreenLEDPins[] = {6, 5, 3};
int CathodePins[] = {8, 7, 4};
static int red[]={0,0,0,0,0,0,0,0,0};
static int green[]={0,0,0,0,0,0,0,0,0};

void setup()
{
  
  Serial.begin(38400);

   pinMode(buttonPin1, INPUT);
 pinMode(buttonPin2, INPUT);
 pinMode(buttonPin3, INPUT);
 pinMode(buttonPin4, INPUT);
 pinMode(buttonPin5, INPUT);
 pinMode(buttonPin6, INPUT);
 pinMode(buttonPin7, INPUT);
 pinMode(buttonPin8, INPUT);
 pinMode(buttonPin9, INPUT);
 pinMode(RedLEDPins[0], OUTPUT); 
 pinMode(RedLEDPins[1], OUTPUT); 
 pinMode(RedLEDPins[2], OUTPUT);  
 pinMode(GreenLEDPins[0], OUTPUT);
 pinMode(GreenLEDPins[1], OUTPUT); 
 pinMode(GreenLEDPins[2], OUTPUT);
 pinMode(CathodePins[0], OUTPUT);
 pinMode(CathodePins[1], OUTPUT);
 pinMode(CathodePins[2], OUTPUT);
  
   
   
}

void lightLED(word LEDOnOff, word LEDColour)
{
  
  

     digitalWrite(CathodePins[0], HIGH);
        digitalWrite(CathodePins[1], HIGH); 
           digitalWrite(CathodePins[2], HIGH);  
  for (int i=0;i<9;i++)
  {
    if (LEDOnOff & 1)
    {     
      if (LEDColour & 1)
      {
         
         digitalWrite(RedLEDPins[i/3], HIGH);
         digitalWrite(CathodePins[i%3], LOW);
         
         delayMicroseconds(LEDTime);
         
         digitalWrite(RedLEDPins[i/3], LOW);
         digitalWrite(CathodePins[i%3], HIGH);
        
      } else 
      {
         
         digitalWrite(GreenLEDPins[i/3], HIGH);
         digitalWrite(CathodePins[i%3], LOW);
         
         delayMicroseconds(LEDTime);
         
         digitalWrite(GreenLEDPins[i/3], LOW);
                digitalWrite(CathodePins[i%3], HIGH);
      }
      
    }
    LEDOnOff = LEDOnOff >> 1;
    LEDColour = LEDColour >> 1;
  }
}


word checkWin(word GridOnOff, word GridColour, boolean Turn)
{
 
  
word winArray[] = {7, 56, 73, 84, 146, 273, 292, 448};  
  
if (Turn)        // red's turn, check for green
   {
     for (int i=0;i<8;i++)
     {
       if ( ((GridOnOff & ~GridColour) & winArray[i]) == winArray[i])
       {
          return winArray[i];
       }
     }
     return 0;
   } else        // green's turn, check for red
     {
      for (int i=0;i<8;i++)
     {
         if ( ((GridOnOff & GridColour) & winArray[i]) == winArray[i])
        {      
            return winArray[i];
         }
      }
 return 0;
  }
}


void loop()
{
  word Pressed=0;
  word WinCondition = 0;
  byte NoOfTurns = 0;
  word GridOnOff = 0;
  word GridColour = 0;
  boolean Turn = 1;    
        
  
  do
  { 
    if(Serial.available())
   { c=Serial.read();
    Serial.print(c);
   
    if(c==49)
 buttonState1 =HIGH;
  else if(c==50)
 buttonState2 =HIGH;
  else if(c==51)
 buttonState3 =HIGH;
  else if(c==52)
 buttonState4 =HIGH;
 else if(c==53)
 buttonState5 =HIGH;
  else if(c==54)
 buttonState6 =HIGH;
  else if(c==55)
 {buttonState7 =HIGH;
 Serial.print(c);}
 else if(c==56)
 buttonState8 =HIGH;
 else if(c==57)
{ buttonState9 =HIGH;
 Serial.print(c);}
 }
    Pressed = readGrid();      // take a reading
    if (Pressed & ~GridOnOff)    // if an empty space is selected
    {
      GridOnOff = GridOnOff | Pressed;  // light up the space
        if (Turn)                         // set colour according to whose turn it is
        {
          GridColour = GridColour | Pressed;
          
         
        } 
        Turn=!Turn;                            
        NoOfTurns+=1;
        WinCondition = checkWin(GridOnOff, GridColour, Turn);  // check if there's a win for player
    }
    lightLED(GridOnOff,GridColour);    
 
 
}while ((WinCondition == 0) && (NoOfTurns < 9));     

}
word readGrid()
{
  
if(buttonState1==HIGH)
{buttonState1=LOW;
Serial.print(Serial.read());
  return 1<<2;
  
}
if(buttonState2==HIGH)
{buttonState2=LOW;
  return 1<<5;
  
}
if(buttonState3==HIGH)
{buttonState3=LOW;
  return 1<<8;
  
}
if(buttonState4==HIGH)
{buttonState4=LOW;
  return 1<<1;
  
}
if(buttonState5==HIGH)
{buttonState5=LOW;
  return 1<<4;
  
}
if(buttonState6==HIGH)
{buttonState6=LOW;
  return 1<<7;
  
}
if(buttonState7==HIGH)
{buttonState7=LOW;
Serial.print(c);
  return 1<<0;
  
}
if(buttonState8==HIGH)
{buttonState8=LOW;
  return 1<<3;
  
}
if(buttonState9==HIGH)
{buttonState9=LOW;
Serial.print(c);
  return 1<<6;
  
}
else
return 0;
}
