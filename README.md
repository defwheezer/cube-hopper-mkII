Coin hopper coin sensor is high when no coin, goes low when coin present. See the scope trace in the images folder.

Count coin at fall from 5v, then stop motor when coin clears (sensor raises back to 5v). A slight wait (50 ms) is added to allow the coin to clear the slot after the fall in sensed.  Ideally the motor would be stopped when a rise is detected, but since the signal is floating high, I have no idea how to do that and thus I am using the falling edge to detect coin.

For Arduino:
-  attach on interrupt as follows: attachInterrupt(0, countCoins, FALLING); // interrupt 0, call fxn 'countCoins', when signal is FALLING
-  Use internal pull-up resistor: digitalWrite(coinHopperPin, INPUT_PULLUP); //set internal pull up resistor
-  Set the right pin mode: pinMode(buttonPin, INPUT_PULLUP);
