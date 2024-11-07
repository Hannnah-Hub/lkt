按钮和压力传感器 arduino代码：/n
```C++
// 定义按钮引脚
const int buttonPin = 2;  // 按钮连接到数字引脚2
const int sensorPin = A0; // 传感器连接到模拟引脚A0

// 定义变量来保存按钮的状态
int buttonState = 0;         // 当前按钮状态
int lastButtonState = 0;     // 上一个按钮状态

void setup() {
  // 设置按钮引脚为输入，并启用内部上拉电阻
  pinMode(buttonPin, INPUT_PULLUP);

  // 初始化串口通信
  Serial.begin(115200);
  delay(2000);  // 等待2秒，以确保串口监视器准备好
}

void loop() {
  // 读取按钮的当前状态
  buttonState = digitalRead(buttonPin);

  // 检查按钮状态是否发生变化
  if (buttonState == LOW && lastButtonState == HIGH) {
    // 按钮被按下时，发送 1（进入 V 模式）
    Serial.println(1);
  }

  // 更新上一个按钮状态
  lastButtonState = buttonState;

  // 读取模拟传感器的值（0-1023）
  int sensorValue = analogRead(sensorPin);

  // 如果传感器的值大于1000，发送2（进入 B 模式）
  if (sensorValue > 1000) {
    Serial.println(2);
  }

  delay(500);  // 每500毫秒读取一次
}
```
