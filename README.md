/*
 * Written by: R-STEM (rufina michael)
 * Created: 6-5-2015, Last edited: 7-8-2015
 * Project:  Bluetooth RC Car
 *           Direction: 8-direction pad around 's' key
 *           Variable motor speed: '1'-'4'
             
 *           BONUS:
 *           Lights: Turn signals, brake lights, headlights, anything
 *                   programmed in any way that you want!
 *           Buzzer: Horn that plays in any pattern that you would like
 */


// Motors - IC
int LightCounter = 0; 
int LeftOn = 0;
int RightOn = 0;
const int Frontrightlight = 13; //headlight#1
const int Frontleftlight = 12;//headlight#2
const int leftBackward  = 5;  // Pin  2 of L293
const int leftForward  = 6;  // Pin  7 of L293
const int rightBackward  = 10; // Pin 10 of L293
const int rightForward  = 11;  // Pin 14 of L293

// State provided by serial monitor/bluetooth activates car
char state = 's'; // default = 's' = stop

// Motor speed (0-255)
int vSpeed = 200; // Default speed

// Array of possible direction keys
char directions[] = {'q','w','e','a','d','z','x','c'};
//'q' forword left
//'w' forward
//'e' forwardright
//'a' left
//'s' stop
//'d' right
//'z' backwards left
//'x' backwards 
//'c' backwards right

// Counter to keep direction for more fluid motion
int directionCount = 0;


void setup() {
  
  // Outputs
  pinMode(Frontrightlight, OUTPUT);
  pinMode(Frontleftlight, OUTPUT);
  pinMode(leftBackward, OUTPUT);
  pinMode(leftForward, OUTPUT);
  pinMode(rightBackward, OUTPUT);
  pinMode(rightForward, OUTPUT);
  
  // Add bonus things (lights, buzzer) here. Are they inputs or outputs?
  
  Serial.begin(115200);
  
}//setup

void loop() {
  
  // Stops car when no commands given
  if (!in(directions,state) || directionCount >= 15){
    state = 's';
    directionCount = 0; //
  }//if ! a direction command or loop has iterated 15x with direction command
  else{
    directionCount++;
  }//else
  
  // Communication with serial monitor/bluetooth
  if (Serial.available() > 0) {
    state = Serial.read();
  }//if serial comunication is available

  // A switch statement checks its parameter (state) to see if it
  // equals the value following each 'case' keyword. In its basic
  // form, it is a series of if-statements.
  switch(state) {
  
  // Changes speed of the motors (1-4)
  case '1': vSpeed = 100;
            break;
  case '2': vSpeed = 150; 
            break;
  case '3': vSpeed = 200;
            break;
  case '4': vSpeed = 255; // Max speed
            break;
            
  // Lights - ' '
  // choose a letter key to turn on/off your LEDs
  case 'p': digitalWrite(Frontleftlight, !digitalRead(Frontleftlight)); //does the opposite of whatever the light is doing
            break; 
  case 'l': digitalWrite(Frontrightlight, !digitalRead(Frontrightlight)); //does the opposite of whatever the light is doing   
            break;
  case 'r': digitalWrite(Frontleftlight, LOW);
            LeftOn = !LeftOn; //
            break; 
  case 'b': digitalWrite(Frontrightlight, LOW);
            RightOn = !RightOn; //
            break;   
            
  // Buzzer - ' '
  // choose a letter key to honk your horn
  case 'f': //Write any sound pattern of your choice 

  
            break;
  
  // Forward - 'w'
  case 'w': analogWrite(leftForward, vSpeed);
            analogWrite(rightForward, vSpeed);
    
            analogWrite(leftBackward, 0);
            analogWrite(rightBackward, 0);
            break;
  
  // Forward left - 'q'
  case 'q': analogWrite(leftForward, vSpeed / 2);
            analogWrite(rightForward, vSpeed);
     
            analogWrite(leftBackward, 0); 
            analogWrite(rightBackward, 0);
            break;
  
  // Forward right - 'e'
  case 'e': analogWrite(leftForward, vSpeed);
            analogWrite(rightForward, vSpeed / 2);
    
            analogWrite(leftBackward, 0); 
            analogWrite(rightBackward, 0);
            break;
            
  // Backward - 'x'
  case 'x': analogWrite(leftForward, 0);
            analogWrite(rightForward, 0);
    
            analogWrite(leftBackward, vSpeed);
            analogWrite(rightBackward, vSpeed);   
            break;
  
  // Backward left - 'z'
  case 'z': analogWrite(leftForward, 0);
            analogWrite(rightForward, 0);
          
            analogWrite(leftBackward, vSpeed / 2);     
            analogWrite(rightBackward, vSpeed);   
            break;
  
  // Backward right - 'c'
  case 'c': analogWrite(leftForward, 0);
            analogWrite(rightForward, 0);
            
            analogWrite(leftBackward, vSpeed);   
            analogWrite(rightBackward, vSpeed / 2);
            break;
  
  // Left - 'a'
  case 'a': analogWrite(leftForward, 0);
            analogWrite(rightForward, vSpeed);
            
            analogWrite(leftBackward, vSpeed);   
            analogWrite(rightBackward, 0); 
            break;
  
  // Right - 'd'
  case 'd': analogWrite(leftForward, vSpeed);
            analogWrite(rightForward, 0);
    
            analogWrite(leftBackward, 0);   
            analogWrite(rightBackward, vSpeed);     
            break;

  // Stops the car - 's'
  case 's': analogWrite(leftForward, 0);
            analogWrite(rightForward, 0);
        
            analogWrite(leftBackward, 0);  
            analogWrite(rightBackward, 0);
            break;
            
  default:  Serial.println("Input error");
  
  }//switch
  
  delay(30); // Necessary for keydown to register
  
  if(LeftOn|| RightOn) {
    LightCounter = LightCounter+1; 
    if(LeftOn) {
      if(LightCounter > 10){
        digitalWrite(Frontleftlight,!digitalRead(Frontleftlight));
        LightCounter = 0;
      }
     }
  
    if(RightOn){
      if(LightCounter > 10){
        digitalWrite(Frontrightlight,!digitalRead(Frontrightlight));
        LightCounter = 0;
      }
    }
  }
}//loop

// Function written for directions[]
// Checks if char check is found within array[]
bool in(char array[], char check){
  
   for (int x = 0; x < 8; x++){
     if (check == array[x]){
       return 1;
     }//if found
   }//for x
   
   return 0;

}//in
