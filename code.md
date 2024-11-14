按钮和压力传感器 arduino代码：/n
```C++
// 定义传感器和电磁铁引脚
const int sensor1Pin = A0;       // 第一个传感器连接到模拟引脚A0
const int sensor2Pin = A1;       // 第二个传感器连接到模拟引脚A1
const int electromagnetPin = 7;  // 电磁铁连接到数字引脚7

void setup() {
  // 设置引脚模式
  pinMode(electromagnetPin, OUTPUT); // 电磁铁为输出

  // 初始化串口通信
  Serial.begin(115200);
  delay(2000);  // 等待2秒，以确保串口监视器准备好
}

void loop() {
  // 使电磁铁通电
  digitalWrite(electromagnetPin, HIGH);
  delay(1000);  // 保持通电状态1秒
  digitalWrite(electromagnetPin, LOW);

  // 读取传感器1的值
  int sensor1Value = analogRead(sensor1Pin);
  // 读取传感器2的值
  int sensor2Value = analogRead(sensor2Pin);

  // 如果第一个传感器的值大于特定阈值，发送1
  if (sensor1Value > 1000) {
    Serial.println(1);
  }

  // 如果第二个传感器的值大于特定阈值，发送2
  if (sensor2Value > 1000) {
    Serial.println(2);
  }

  delay(500);  // 每500毫秒读取一次
}
```
unity代码 电路开始后电磁铁自动工作（工作1秒停0.5秒）压力传感器1 数值大于1000 输出1 可以在后面改 unity接收到1 做xx交互动作 压力传感器2数值大于1000 输出2
```C#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System.IO.Ports;

public class ArduinoController : MonoBehaviour
{
    SerialPort serialPort = new SerialPort("COM12", 115200); // 请确保端口和波特率与Arduino设置匹配

    public GameObject cube;   // 拖动并设置你的立方体对象
    public GameObject sphere; // 拖动并设置你的球体对象

    void Start()
    {
        OpenConnectionToArduino();
    }

    void Update()
    {
        if (serialPort.IsOpen)
        {
            try
            {
                string message = serialPort.ReadLine(); // 试图读取一行数据
                HandleMessage(message);
            }
            catch (System.Exception)
            {
                // 捕捉任何通信错误
            }
        }
    }

    void OpenConnectionToArduino()
    {
        if (!serialPort.IsOpen)
        {
            serialPort.Open();
            serialPort.ReadTimeout = 100;
            Debug.Log("串口已打开");
        }
    }

    void HandleMessage(string message)
    {
        Debug.Log("收到消息: " + message);

        if (message == "1" && !cube.activeSelf)
        {
            EnableCube();
        }
        else if (message == "2" && !sphere.activeSelf)
        {
            EnableSphere();
        }
    }

    void EnableCube()
    {
        cube.SetActive(true); // 启用立方体
    }

    void EnableSphere()
    {
        sphere.SetActive(true); // 启用球体
    }

    void OnApplicationQuit()
    {
        if (serialPort.IsOpen)
        {
            serialPort.Close();
            Debug.Log("串口已关闭");
        }
    }
}
```
两个led灯arduino代码：接受到a led2亮0.3秒 接收到b led1亮0.3秒
```C++
// 引脚定义
const int redPin1 = 9;  // LED1 的红色引脚
const int greenPin1 = 10;  // LED1 的绿色引脚
const int bluePin1 = 11;  // LED1 的蓝色引脚

const int redPin2 = 5;  // LED2 的红色引脚
const int greenPin2 = 3;  // LED2 的绿色引脚
const int bluePin2 = 6;  // LED2 的蓝色引脚


void setup() {
  // 初始化引脚模式
  pinMode(redPin1, OUTPUT);
  pinMode(greenPin1, OUTPUT);
  pinMode(bluePin1, OUTPUT);
  pinMode(redPin2, OUTPUT);
  pinMode(greenPin2, OUTPUT);
  pinMode(bluePin2, OUTPUT);

  // 初始化串口
  Serial.begin(115200);  // 设置串口波特率为9600
}

void loop() {
  // 检查串口是否有数据
  if (Serial.available() > 0) {
    // 读取串口数据（字符）
    char receivedChar = Serial.read();
    
    // 根据接收到的字符控制 LED
    if (receivedChar == 'a') {
      // 如果收到 'a'，亮起 LED1 红色
      digitalWrite(redPin1, LOW);    // 开启红色引脚
      digitalWrite(greenPin1, HIGH);   // 关闭绿色引脚
      digitalWrite(bluePin1, HIGH);    // 关闭蓝色引脚

      // 保持 LED2 的原始颜色（不改变它）
      
      delay(300);  // LED1 持续亮起 0.3 秒
      // 熄灭 LED1
      digitalWrite(redPin1, LOW);
      digitalWrite(greenPin1, LOW);
      digitalWrite(bluePin1, LOW);
    }
    else if (receivedChar == 'b') {
      digitalWrite(redPin2, HIGH);    // 开启红色引脚
      digitalWrite(greenPin2, LOW);   // 关闭绿色引脚
      digitalWrite(bluePin2, LOW);    // 关闭蓝色引脚


      delay(300);  // LED2 保持原色，持续 0.3 秒

      digitalWrite(redPin2, LOW);
      digitalWrite(greenPin2, LOW);
      digitalWrite(bluePin2, LOW);
    }
  }
}
```
unity代码
```C#
using System.Collections;
using System.IO.Ports;
using UnityEngine;

public class BlinkObjects : MonoBehaviour
{
    public float cueInterval = 1f;  // 控制字符间隔时间
    private SerialPort serialPort;   // 串口对象

    private void Start()
    {
        // 初始化串口（修改为你实际的串口号）
        serialPort = new SerialPort("COM8", 115200);  // 修改为你实际的串口号
        serialPort.Open();  // 打开串口连接
        serialPort.ReadTimeout = 100;  // 设置读取超时

        // 开始协程，模拟闪烁效果并发送数据
        StartCoroutine(BlinkObjectsCoroutine());
    }

    private IEnumerator BlinkObjectsCoroutine()
    {
        while (true)
        {
            // 发送 'a' 到 Arduino
            if (serialPort.IsOpen)
            {
                serialPort.Write("a");
                Debug.Log("a");  // 控制台输出已发送的字符
            }

            // 等待指定的间隔时间
            yield return new WaitForSeconds(cueInterval);

            // 发送 'b' 到 Arduino
            if (serialPort.IsOpen)
            {
                serialPort.Write("b");
                Debug.Log("b");  // 控制台输出已发送的字符
            }

            // 等待指定的间隔时间
            yield return new WaitForSeconds(cueInterval);
        }
    }

    private void OnApplicationQuit()
    {
        // 程序退出时关闭串口
        if (serialPort.IsOpen)
        {
            serialPort.Close();
        }
    }
}
```
