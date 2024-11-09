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
