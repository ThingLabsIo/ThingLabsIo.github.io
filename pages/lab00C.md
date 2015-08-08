# Getting your IP Address on your Yun

The simplest way to get your IP address on your Yun is to look at your network hub but the reality is that we rarely own the hub and can log in and look at it. 

The more sure fire way to do it is to run ifconfig on the linux side of your Yun which you can do with the sketch below. 

## Writing the ifconfig sketch. 

#include <Process.h>
<pre>
void setup() {
  // Initialize Bridge
  Bridge.begin();

  // Initialize Serial
  Serial.begin(9600);
  
  //Wait on the serial port to connect
  while (!Serial);

  Serial.println("Ready.");
}

void loop() {
  // Wait until a Serial Monitor is connected.
  if (Serial)
  {
    Serial.println("running ifconfig.");
    // run various example processes
    runifconfig();
    
    delay(10000);
  }
}

void runifconfig() {
  // Launch "ifconfig" command and get Arduino ascii art logo from the network
  // ifconfig is command line program that gives you your network configuration information.
  Process p;        // Create a process and call it "p"
  p.begin("ifconfig");  // Process that launch the "curl" command
  p.run();      // Run the process and wait for its termination

  // Print arduino logo over the Serial
  // A process output can be read with the stream methods
  while (p.available()>0) {
    char c = p.read();
    Serial.print(c);
  }
  // Ensure the last bit of data is sent.
  Serial.flush();
}
</pre>