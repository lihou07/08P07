#define PIN_LED 9
#define PIN_TRIG 12
#define PIN_ECHO 13

// configurable parameters
#define SND_VEL 346.0
#define INTERVAL 25          
#define PULSE_DURATION 10

#define _DIST_MIN 100.0      
#define _DIST_MAX 300.0    
#define _DIST_CENTER 200.0  

#define TIMEOUT ((INTERVAL / 2) * 1000.0)
#define SCALE (0.001 * 0.5 * SND_VEL)

unsigned long last_sampling_time;

// measure distance using ultrasonic sensor
float USS_measure(int TRIG, int ECHO) {
  unsigned long duration;

  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);

  digitalWrite(TRIG, HIGH);
  delayMicroseconds(PULSE_DURATION);
  digitalWrite(TRIG, LOW);

  duration = pulseIn(ECHO, HIGH, TIMEOUT);

  return duration * SCALE;
}

void setup() {
  pinMode(PIN_LED, OUTPUT);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);

  digitalWrite(PIN_TRIG, LOW);

  // LED OFF
  analogWrite(PIN_LED, 255);

  Serial.begin(57600);

  last_sampling_time = 0;
}

void loop() {
  float distance;
  int brightness;

  // wait until next sampling time
  if (millis() < (last_sampling_time + INTERVAL))
    return;

  // measure distance
  distance = USS_measure(PIN_TRIG, PIN_ECHO);

  // measurement failed or object is farther than 300 mm
  if ((distance == 0.0) || (distance > _DIST_MAX)) {
    distance = _DIST_MAX + 10.0;
    brightness = 255;             // LED 켜짐
  }

  // object is closer than 100 mm
  else if (distance < _DIST_MIN) {
    distance = _DIST_MIN - 10.0;
    brightness = 255;             // LED 꺼짐
  }

  // 100 mm ~ 200 mm
  else if (distance <= _DIST_CENTER) {

    // 100mm -> 255 (OFF)
    // 150mm -> about 128 (50%)
    // 200mm -> 0 (maximum brightness)

    brightness =
      255 - (int)((distance - _DIST_MIN)
                  * 255.0
                  / (_DIST_CENTER - _DIST_MIN));
  }

  // 200 mm ~ 300 mm
  else {

    // 200mm -> 0 (maximum brightness)
    // 250mm -> about 128 (50%)
    // 300mm -> 255 (OFF)

    brightness =
      (int)((distance - _DIST_CENTER)
            * 255.0
            / (_DIST_MAX - _DIST_CENTER));
  }

  // control LED brightness
  analogWrite(PIN_LED, brightness);

  // output for Serial Plotter
  Serial.print("Min:");
  Serial.print(_DIST_MIN);

  Serial.print(",distance:");
  Serial.print(distance);

  Serial.print(",Max:");
  Serial.println(_DIST_MAX);

  // update sampling time
  last_sampling_time += INTERVAL;
}
