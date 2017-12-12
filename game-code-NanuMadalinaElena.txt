#include <LiquidCrystal.h>
#include <LedControl.h> //  need the library
//#include<binary.h>

#define X_pin A0 // analog pin connected to X output
#define Y_pin A1 // analog pin connected to Y output
#define SW_pin 8 // digital pin connected to switch output

//pin for lcd
#define v0 9
#define rs 1
#define E 2
#define d4 4
#define d5 5
#define d6 6
#define d7 7

/*//millis function
unsigned long previousMillis=0; // millis() returns an unsigned long
unsigned long previousMillis2=0; // millis() returns an unsigned long
unsigned long interval=500; // the time we need to wait
unsigned long interval2=1000; // the time we need to wait
*/


int matrice[8][8]=
    {
      //matrix that determines the road
        {1,1,1,1,1,1,2,1},
        {1,1,0,0,0,0,0,1},
        {1,1,0,0,1,1,1,1},
        {1,0,0,0,0,0,0,1},
        {1,0,0,0,0,0,0,1},
        {1,1,1,1,0,0,0,1},
        {1,0,0,0,0,0,0,1},
        {1,1,1,1,1,1,1,1},
    };

//initial position
int xPos=6,yPos=6;

bool isTrue=false;
int numberLives=3; //this variable holds the number of lives
//int lastSwitchState = LOW;        // previous switch state

LedControl lc=LedControl(12,11,10,1);  
LiquidCrystal lcd(rs,E,d4,d5,d6,d7);


/*
void readJoystick(){
  //int pressed; //this variable will determine whether joystick has been pressed down (selected)
 
  if(pressed == LOW)
    {
      pressed = 0;
      isTrue=true;
    }

   else
   {
    pressed = 1;
   }


} //end readJoystick()
*/

void setup() {
  //matrice
  pinMode(SW_pin, INPUT);

  //digitalWrite(SwButtonPin, HIGH);//sets the SW button HIGH
  //Serial.begin(9600);

  lc.shutdown(0,false);// turn off power saving, enables display
  lc.setIntensity(0,8);// sets brightness (0~15 possible values)
  lc.clearDisplay(0);// clear screen

  lcd.begin(16,2);
  analogWrite(v0,100);

}//end setup()


void firstStep()
{
   
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Press the button ");
  lcd.setCursor(0,1);
  lcd.print("to start ");
  delay(2000);
  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Good luck! ");


}//end firstStep()

void secondStep()
{
  int indexi;
  int indexj;

  int  xVal = analogRead(X_pin);
  int  yVal = analogRead(Y_pin);
  xVal = map(xVal,0,1023,1,300);
  yVal = map(yVal,0,1023,1,300);

  if(xVal<100)
  {
    if(xPos == 7)
        {
        xPos = 0;
        //loveste perete
        numberLives--;
         
        }
     else  
        xPos+=1;
  }
  else
    if (xVal>200)
    {
      if(xPos == 0)
      {
          xPos = 7;
          numberLives--;
          //loveste perete
      }
      else
          xPos-=1;
    }
    
  if(yVal<100)
  {
    if(yPos == 0)
    {
        yPos = 7;
        numberLives--;
         //loveste perete
    }
    else
        yPos -= 1;
  }
  else 
    if (yVal > 200)
      if(yPos == 7)
      {
        //daca e diferit de "Won"
        if(xPos !=6)
        {
          yPos = 0;
          numberLives--;
        }
        //altfel ai pierdut
        //nu e posibil sa vi prin partea opusa la iesirea din labirint
        else
        {
          yPos == 6;
          xPos == 6;
        }
         //loveste perete
      }
      else
        yPos += 1;
 
  lc.setLed(0,yPos,xPos,true);
  delay(200);
  lc.clearDisplay(0);
    //lc.setRow(0,1,b,B10110000); 


//----------------the road------------------
for(indexi=0;indexi<8;indexi++)
{  for(indexj=0;indexj<8;indexj++)
      {
        if(matrice[indexi][indexj]==1)
          {
            lc.setLed(0,indexi,indexj,true);
          }
      }
}


//---------------bushes = outline labyrinth---------------
if(matrice[yPos][xPos]==1)
{
  numberLives--;
  
}
else
  {
    if(matrice[yPos][xPos]==2)
    {
      lcd.clear();
      lcd.setCursor(0,0);
      //lcd.print(yPos + xPos);
      //yPos=0 xPos=6;
      lcd.print("You won!");

    }
  }

if(numberLives==0)
{

  lcd.clear();
  lcd.setCursor(0,0);
  lcd.print("Game over!");
  lcd.setCursor(0,1);
  lcd.print("Keep trying");

}
  
  
}//end secondStep()

void loop() {
  int pressed=digitalRead(SW_pin); //reads whether joystick has been pressed down (selected) or not 

if(isTrue==false)
{
  firstStep();
 // readJoystick();//calls this function which reads the digital input button SW
   if(pressed == LOW)
    {
      isTrue=true;
    }
}
//intra pe cazul isTrue=false;
else
  {
      secondStep();

  }
}//end function loop()

