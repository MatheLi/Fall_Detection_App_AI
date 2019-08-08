## The project:

Falls those leads to fatal injury have become a great challenge that cannot be neglected for elderly people. In the last few years, different kinds of approaches have been proposed in fall detection area, which can be explained and categorized into three types: wearable device based, ambience sensor based and vision based [1]. First of all, wearable devices usually take advantages of embedded sensors to detect the motion and location of the body, such as accelerometer, magnetometer and gyroscope [2, 3]. And the cost of wearable device based approach is quite low, as well as the installation and operation is not complicated for the elderly. Secondly, ambience based approach always use pressure sensors to detect and track body. This solution is also cost-effective and easy-deployment [4, 5]. However, the possibility of sensing objects other than human bodies posts a remarkable challenge to the detection accuracy of this approach. Last but not least, vision based solution makes full use of deployed cameras to monitor all the objects within the range, including human bodies [6, 7]. There is less intrusion into people’s daily life than the above two approaches, while the observation space is limited and ubiquitous monitoring can’t be achieved.
 
In this project, a surveillance system based on Arduino, Windows 10 and Microsoft Azure for real-time fall detection is proposed. Raw data of three-dimensional accelerometer are provided by Arduino with ADXL345. Windows 10 device makes use of this information to get the orientation of the subject by efficient data fusion and fall detection algorithms. Microsoft Azure services and Mobile/PC application are also designed to achieve seamless data processing, analyzing, storing and acquiring any time from any place as long as they have access to the Internet. The system architecture is shown is Fig. 1 as follows. 




![](https://hackster.imgix.net/uploads/image/file/119494/Fig2.png?auto=compress%2Cformat&w=1280&h=960&fit=max)

Image source: https://hackster.imgix.net/uploads/image/file/119494/Fig2.png?auto=compress%2Cformat&w=1280&h=960&fit=max




| [For more details click here](./posts/More details.md) | [Code of the first model](./posts/First_model.py) |
