// ESP32C6 will connect to your WiFi per provided credentials
// WiFi interface info will be printed to the Serial Monitor
// API for controlling the NeoPixel ring LEDs
// http://<your ip address>/led/update?status=on      (fade the white LEDs on to full brightness)
// http://<your ip address>/led/update?status=off     (fade the white LEDs to off)
// http://<your ip address>/led/update?status=rainbow (run a raindbow pattern around the neopixel ring)

#include <WiFi.h>
#include <WebServer.h>
#include <Adafruit_NeoPixel.h>

// Which GPIO pin is connected to the NeoPixels?
#define LED_PIN    D6

// How many NeoPixels are attached to the board?
#define LED_COUNT  12

// NeoPixel brightness, 0 (min) to 255 (max)
#define BRIGHTNESS 50 // Set BRIGHTNESS to about 1/5 (max = 255)

// Declare our NeoPixel strip object:
Adafruit_NeoPixel strip(LED_COUNT, LED_PIN, NEO_GRBW + NEO_KHZ800);
// Argument 1 = Number of pixels in NeoPixel strip
// Argument 2 = Arduino pin number (most are valid)
// Argument 3 = Pixel type flags, add together as needed:
//   NEO_KHZ800  800 KHz bitstream (most NeoPixel products w/WS2812 LEDs)
//   NEO_KHZ400  400 KHz (classic 'v1' (not v2) FLORA pixels, WS2811 drivers)
//   NEO_GRB     Pixels are wired for GRB bitstream (most NeoPixel products)
//   NEO_RGB     Pixels are wired for RGB bitstream (v1 FLORA pixels, not v2)
//   NEO_RGBW    Pixels are wired for RGBW bitstream (NeoPixel RGBW products)

const char* ssid = "<YOUR WIFI SSID>";
const char* password = "<YOUR WIFI PASSWORD>";

WebServer server(80);
String rgbwState = "off";
String currentState = "off";

void setup() {
  // Intialize NeoPixel ring
  strip.begin();           // INITIALIZE NeoPixel strip object (REQUIRED)
  strip.show();            // Turn OFF all pixels ASAP
  strip.setBrightness(BRIGHTNESS);

  // Connect to Wi-Fi
  Serial.begin(115200);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
  Serial.print("hostname:    ");
  Serial.println(WiFi.getHostname());
  Serial.print("MAC address: ");
  Serial.println(WiFi.macAddress());
  Serial.print("IP address:  ");
  Serial.println(WiFi.localIP());

  // Set up a web listener
  server.on("/led/update", handleLEDUpdate);
  server.onNotFound(handleNotFound);
  server.begin();

  // Set up the rgbwLED control loop on the second core
  xTaskCreatePinnedToCore(
    [] (void* param) {
      int count = 0;
      while (true) {
        while (rgbwState == "on") {
          handleLEDs("on");
        }
        while (rgbwState == "off") {
          handleLEDs("off");
        }
        handleLEDs(rgbwState);
      }
    },
    "rgbwLED Control",
    4096,
    NULL,
    1,
    NULL,
    1
  );
}

void loop() {
  // Handle client requests
  server.handleClient();
}

void handleLEDUpdate() {
  if (server.method() == HTTP_GET && server.hasArg("status")) {
    String status = server.arg("status");
    // validate the input
    status.toLowerCase();
    if ((status == "on") || (status == "off") || (status == "rainbow")) {
      rgbwState = status;
      server.send(200, "text/plain", "Status updated to \"" + rgbwState + "\"");
      Serial.print("rgbwState: ");
      Serial.println(rgbwState);
    } else {
      server.send(400, "text/plain", "Invalid status value");
    }
  } else {
    server.send(405, "text/plain", "Method Not Allowed");
  }
}

void handleNotFound() {
  server.send(404, "text/plain", "Not Found");
}

// Handle LEDs based on state
void handleLEDs(String state) {
  if (state == "off" && currentState == "on") {
    whiteFadeOff(5);     // fade the white LEDs to off
  } else if (state == "off" && currentState != "off") {
    whiteOff();          // turn all LEDs off immediately
  } else if (state == "on" && currentState == "off") {
    whiteFadeOn(5);     // fade the white LEDs on to full brightness
  } else if (state == "on" & currentState != "on") {
    whiteOn();          // turn white LEDs on immediately
  } else if (state == "rainbow") {
    rainbowPlusWhite(50, 1);
  }
  currentState = state;
}

void whiteOn() {
  strip.fill(strip.Color(0, 0, 0, strip.gamma8(255)));
  strip.show();
}

void whiteOff() {
  strip.fill(strip.Color(0, 0, 0, strip.gamma8(0)));
  strip.show();
}

void whiteFadeOn(uint8_t wait) {
  for(int j=0; j<256; j++) { // Ramp up from 0 to 255
    // Fill entire strip with white at gamma-corrected brightness level 'j':
    strip.fill(strip.Color(0, 0, 0, strip.gamma8(j)));
    strip.show();
    delay(wait);
  }
}

void whiteFadeOff(uint8_t wait) {
  for(int j=255; j>0; j--) { // Ramp down from 255 to 0
    // Fill entire strip with white at gamma-corrected brightness level 'j':
    strip.fill(strip.Color(0, 0, 0, strip.gamma8(j)));
    strip.show();
    delay(wait);
  }
}

void rainbowPlusWhite(int whiteSpeed, int whiteLength) {

  if(whiteLength >= strip.numPixels()) whiteLength = strip.numPixels() - 1;

  int      head          = whiteLength - 1;
  int      tail          = 0;
  int      loops         = 3;
  int      loopNum       = 0;
  uint32_t lastTime      = millis();
  uint32_t firstPixelHue = 0;

  for(;;) { // Repeat forever (or until a 'break' or 'return')
    for(int i=0; i<strip.numPixels(); i++) {  // For each pixel in strip...
      if(((i >= tail) && (i <= head)) ||      //  If between head & tail...
         ((tail > head) && ((i >= tail) || (i <= head)))) {
        strip.setPixelColor(i, strip.Color(0, 0, 0, 255)); // Set white
      } else {                                             // else set rainbow
        int pixelHue = firstPixelHue + (i * 65536L / strip.numPixels());
        strip.setPixelColor(i, strip.gamma32(strip.ColorHSV(pixelHue)));
      }
    }

    strip.show(); // Update strip with new contents
    // There's no delay here, it just runs full-tilt until the timer and
    // counter combination below runs out.

    firstPixelHue += 40; // Advance just a little along the color wheel

    if((millis() - lastTime) > whiteSpeed) { // Time to update head/tail?
      if(++head >= strip.numPixels()) {      // Advance head, wrap around
        head = 0;
        if(++loopNum >= loops) return;
      }
      if(++tail >= strip.numPixels()) {      // Advance tail, wrap around
        tail = 0;
      }
      lastTime = millis();                   // Save time of last movement
    }
  }
}
